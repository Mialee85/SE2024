# Umgang mit CI/CD-Pipelines (hier: GitLab-CI/CD)

## Fragen
Sinnvoll außerhalb des GitLap Runners?
Muss das außerhalb liegen? Nein.
Ein Verzeichnis als Volume in ein dockjer Container rein stellen. Sinnvoll-> Nein, warum nur auf dem System hängen auf anderem Runner läuft es nicht mehr. Auf Entwicklungsmaschine und nicht auf eigenentlichem System. 
Zugehörigen Artefakte zum Prod? Auf GitLab gespeichert, daher weiter benutzen! Modifizierte Version einchecken und auf dem Prod auschecken


## Schritt 2 

### GitLab Projekt erstellen:
Ein neues Projekt kann direkt über den GitLab Server erstellt werden. 

[!Image create a new Projekt](images/abbildung_1_1)

Shell Befehl, um ein neues Git Repository zu erstellen:
```
git init 
```

#### GitLab Pository hinzufügen
```
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
```

**Folgende Fehlermeldung:**
```
Unable to download repo config from: https://packages.gitlab.com/install/repositories/runner/gitlab-runner/config_file.list?os=Ubuntu&dist=kinetic&source=script



This usually happens if your operating system is not supported by 

packagecloud.io, or this script's OS detection failed.



You can override the OS detection by setting os= and dist= prior to running this script.

You can find a list of supported OSes and distributions on our website: https://packages.gitlab.com/docs#os_distro_version



For example, to force Ubuntu Trusty: os=ubuntu dist=trusty ./script.sh



If you are running a supported OS, please email support@packagecloud.io and report this.

```
Hierzu musste der Befehl um die Angabe ```os=ubuntu dist=trusty``` ergänzt werden.

```
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo os=ubuntu dist=trusty bash
```
Achtung: Wenn es dann immer noch nicht funktioniert, kann das Paket auch manuell herunter geladen und installiert werden (falls es immer noch nicht geht):

```
curl -LJO "https://gitlab-runner-downloads.s3.amazonaws.com/latest/deb/gitlab-runner_amd64.deb"

```

```
 sudo dpkg -i gitlab-runner_amd64.deb 
```
Ab hier normal weiter:

#### GitLab Runner installieren

```
sudo apt-get install gitlab-runner
```
#### GitLab Runner registrieren
```
sudo gitlab-runner register
```
Die URL und token des Repository sind im GitLab Projekt in den Settings CD/CI zu finden:
[!Setting in GitLap](images/abbildung_2_1)



## Schritt 2 - Erstellung einer "Hello World!"-Pipeline 
Ziel
Neues Issue und Merge-Request im Projekt. Erstellung einer "Hello World!"-Pipeline unter Zuhilfenahme einer zu erstellenden .gitlab-ci.yml im Projekt. Im GitLab-Flow können wir ein Issue, einen Merge-Request und einen Branch so verknüpfen, dass aus dem Issue heraus ein zugehöriger Merge-Request und Branch erstellt werden können und beim Mergen des Merge-Requests das Issue geschlossen und der Branch gelöscht werden. Dies bildet das Vorgehen bei der Softwareentwicklung so ab, dass es
- zu jedem Feature ein Issue gibt,
- Features auf eigenen Branches, sogenannten Feature-Branches, entwickelt werden,
- Branches nach Fertigstellung und Test in den main-Branch gemerged werden und
- das Issue nach Fertigstellung der Implementierung geschlossen wird.
Außerdem können wir Pipelines so definieren, dass nach jedem Commit und jedem Merge automatisch Pipeline-Skripte ausgeführt werden, welche
- aus dem Quellcode die fertige Software erstellen (bauen)
- die fertige Software testen und
- die fertige Software auf einen Staging- oder Produktionsserver ausliefern (deployen)



### Neues Issue anlegen
In der **Sidebar** im Menüpunkt **Issues** den Unterpunkt **New Issue** auswählen. Die Eingabemaske für das neue Issue wird geöffnet und kann entsprechend der Angaben angepasst werden:
[!Issue anlegen](images/abbildung_2_2)

#### Titel 

```
Testat_3_Schritt_2_Erstellen Sie eine erste Pipeline in der GitLab-UI
```
#### Description

```
Erstellen Sie einen ersten Job, der erst Hello, world! in eine Datei schreibt und dann den Inhalt dieser Beispieldatei auf der Kommandozeile ausgibt.
Sehen Sie sich die Ausgaben der Pipeline in GitLab an und notieren Sie sich Besonderheiten in der Ausgabe, insbesondere die, die Rückschlüsse auf das Hostsystem des Runners zulassen.
Überprüfen Sie Ihren Laborrechner anschließend auf die Existenz der Beispieldatei. Hierfür empfiehlt sich ein auffälliger Dateiname, sodass Sie anschließend auf dem System mit Suchwerkzeugen arbeiten können.
```
Abschließend wiurd das Issue mit dem Button **Create issue** angelegt. 

>Hinweis: Git-Flow ->  Issue erstellen, aus dem Issue Merge-Request und Branch erzeugen, auf den Branch wechseln und dort die Aufgabe bearbeiten, nach Abschluss Merge-Request mergen.

### Merge-Request aus Issue erstellen
[!Merge-Request erstellen](images/abbildung_2_3)

### Branch erzeugen und wechseln
Durch den Merge-Request wird ein  neuer Branch erstellt. Im Menüpunkt **Repository** kann ein neuer Branch manuell erstellt werden. In diesem Fall wird auf den erstellten Branch gewechselt.

### Pipeline erzeugen
Über die Schaltfläche "CI / CD" gelangt man zur Pipeline-Übersicht. Zur Erstellung des Jobs in einer Pipeline wird eine Datei 
```
.gitlab-ci.yml
```
mit dem folgenden Inhalt (Pipeline-Konfiguration) angelegt: 
```
# .gitlab-ci.yml

# Definition des Jobs mit dem Namen "hello_world"
hello_world:
    # Ausführung eines Shell-Skripts
  script:
      # echo gibt den Inhalt des mitgegebenen Strings oder der Variable auf der console aus. mit der Spitzen-Klammer wird die Ausgabe in die angegebene Textdatei hello.txt umgeleitet
    - echo "Hello, world!" > hello.txt
    #Ausgabe des Inhalts der angegebenen Textdatei
    - cat hello.txt
```
Erläuterung: In diesem Code wird ein Job mit dem Namen "hello_world" definiert. Dann wird ein Shell-Skript ausgeführt, dass "Hello, world!" in die Datei "hello.txt" schreibt. Der Inhalt der Datei wird mit dem N´Befehl cat auf der Kommandozeile ausgegeben. 

Sobald diese .gitlab-ci.yml Datei im Repository hochgeladen wurde und die Pipeline ausgelöst wird, führt GitLab den Job aus und schreibt den Text "Hello, world!" in die Datei "hello.txt" speichern. Der Inhalt der Datei wird dann auf der Kommandozeile ausgegeben.


### Abschließend Merge-Request schließen


## Schritt 3 - Erstellung einer Pipeline mit mehreren Stages und Nutzung von vordefinierten Variablen

Ziel
Erstellung einer Pipeline mit mehreren Stages und Nutzung von vordefinierten Variablen. Die einfache HelloWorld-Pipeline soll so erweitert werden, dass Sie zwei Stages enthält: Bauen der Software und Deployment. Vorerst entstehen dabei nur Dateien im Filesystem des Entwicklungsrechners (dort, wo der GitLab-Runner läuft). Wir simulieren das Verhalten also erstmal nur anstatt wirklich Quellcode zu übersetzen und auf Server auszuliefern.

Aufgabe
Teilen Sie die Pipeline in eine Build-Stage (Dateiinhalt schreiben) und eine Deploy-Stage (Dateiinhalt ausgeben) ein.
Die Ausgabe des Deploy-Jobs soll nun den Zweignamen enthalten. Im Build-Job soll entsprechend Hello from <branch>! in die Datei geschrieben werden. In der Dokumentation der GitLab-CI/CD werden vordefinierte Variablen erläutert, die Sie hierfür verwenden können. Sollten Sie Probleme durch die Aufteilung in zwei Stages erhalten, schauen Sie Sich die Doku zu Artefakten an.
Bei Erfolg schließen Sie die Arbeit an dem Issue ab.

## Schritt 4

Ziel
Die simulierte Pipeline soll jetzt so angepasst werden, dass ein realitätsnäheres Szenario entsteht: Wir nehmen an, dass unser Softwareprojekt eine zentrale index.html enthält, die der Einstiegspunkt für die Benutzung der Software durch die Enduser ist. Die index.html des Softwareprojekts soll nach erfolgreichem Ausführen der Pipeline von einem Nginx-Server, der in einem Docker-Container läuft, ausgeliefert werden. Der Docker-Container läuft auf dem Entwicklungsrechner. Das ist ein zum Entwicklungszeitpunkt übliches Vorgehen. In realen Produktionsumgebungen gibt es weitere Pipelines, welche die Software in Docker-Container installieren, die wiederum auf den Produktivservern laufen. Beachten Sie, dass der Docker-Container nur einmal gestartet wird und durch die Pipeline die erforderlichen Dateien dann ersetzt werden. Der Container wird in unserem Beispiel also nicht durch die Pipeline erzeugt und gestartet.
Als index.html nehmen wir die standardmäßig im Nginx enthaltene Datei. Es wird also keine neue index.html im Projekt angelegt, sondern Sie finden heraus, wo die Dateien des Nginx standardmäßig liegen (s. u.).

Aufgabe
Starten Sie auf dem Laborrechner einen Nginx-Server in einem Docker-Container und prüfen Sie die Netzwerkausgabe. Finden Sie heraus aus welcher Datei die Ausgabe stammt und wie Sie diese manipulieren können. Vermutlich müssen Sie entsprechende Parameter in den Docker-Kommandos nutzen.
Erste wenn der Docker Container läuft und Sie die Konfiguration des Nginx verstanden haben: Ändern Sie die Pipeline so, dass Sie die Beispieldatei aus den vorhergehenden Schritten an eine geeignete Stelle im Container kopieren, wodurch sie vom Nginx als Startseite bereitgestellt wird. Es empfiehlt sich vor der Umsetzung komplexer Pipelines das Vorhaben lokal zu testen, sodass dieses nicht in einer Vielzahl von fehlgeschlagenen Pipelines ausufert.
Erstellen Sie für Ihre Arbeitsschritte ein Issue mit entsprechendem Titel und einer passenden Beschreibung. Arbeiten Sie dieses und alle weiteren Implementierungsschritte ebenfalls nach dem Schema des GitLab-Flow ab und dokumentieren Sie diese.
Sollte Ihre Pipeline aufgrund von Zugriffsrechten fehlschlagen: Bringen Sie in Erfahrung, wie der GitLab-Runner in Ihrem Ubuntu realisiert wird und beachten Sie die Anweisungen aus dem Docker Getting Started Guide

## Schritt 5

Ziel
Meistens gibt es nicht nur einen Produktivserver, sondern zusätzlich noch einen Staging- und evtl. weitere Server zum Testen. Auf einem Staging-Server kann die Software unter Produktionsbedingungen ausgeführt werden ohne die Produktionsversion zu verändern. Dies dient dazu, Kunden eine Möglichkeit der Überprüfung und endgültigen Freigabe zur Übernahme in Produktion zu ermöglichen. Dabei können beispielsweise Mitarbeiter des Kunden involviert werden, die nicht Mitglied im Projektteam sind (z. B. Geschäftsleitung).
Es sollen jetzt zwei Nginx-Container auf dem Entwicklungsrechner betrieben werden. Einer enthält die aktuelle Entwicklungsversion, der andere die Staging-Version. Das Deployment auf die beiden Server wird über den Branch gesteuert: wenn es sich um den Branch staging handelt, wird auf Staging deployed, sonst auf Development.
Da alle Server nach wie vor auf derselben Maschine (Entwicklungsrechner) laufen, muss der Zugriff auf die beiden Server über einen Reverse Proxy gesteuert werden um Zugriffskonflikte zu vermeiden. Wir verwenden dafür den Reverse Proxy træfik, der ebenfalls als Docker-Container gestartet wird.
Außerdem sollen die beiden Server über GitLab-Environments ansprechbar sein.

Aufgabe
Ihnen ist beim Befolgen des GitLab-Flow sicherlich aufgefallen, dass sowohl die Pipelines des Main-Branches als auch der Feature-Branches die eine Beispieldatei aus den vorhergehenden Schritten überschreiben. Ändern Sie Ihre Pipeline so ab, dass verschiedene Nginx-Container genutzt werden, die über verschiedene URLs erreichbar und unter Deployments > Environments im GitLab-Projekt einsehbar sind.
Nutzen Sie dafür das gegebene docker-compose.yml File. Hier wird mittels træfik und nip.io eine Umgebung aus zwei verschiedenen Nginx-Containern geschaffen. Für eine Erfolgreiche Bearbeitung der Aufgabe ist ein tieferes Verständnis dieser beiden Dienste nicht zwingend erforderlich. Wir empfehlen aber, dass Sie sich auch mit diesen Diensten auseinandersetzen und versuchen die Funktionsweise nachzuvollziehen.

## Schritt 6

Ziel
Üblicherweise wird das fertige Softwareprodukt nach dem Bauen (build-Stage) und vor dem Deployment noch getestet. Dies erfolgt in einer eigenen Test-Stage. Wir wollen dies simulieren, indem die index.html des Projekts während der Build-Stage um den Branch-Namen erweitert und anschließend in der neuen Test-Stage mittels html-validate validiert wird.

Aufgabe
Nun sollen noch zusätzliche Tests in die Pipeline eingebaut werden. Erstellen Sie auch hier ein neues Issue und nutzen Sie Git-Flow. In diesem Schritt soll mit html-validate gearbeitet werden. Führen Sie die folgenden Schritte durch:

Falls noch nicht vorhanden: Installieren Sie Node.js

Installieren Sie html-validate

Fügen Sie Ihrem Projekt eine index.html mit dem folgenden Inhalt hinzu:


<p>
  <button>Click me!</button>
  <div id="show-me">
    Lorem ipsum
  </div>
</p>



Erweitern Sie Ihre Pipeline um eine Test-Stage und validieren Sie Ihre index.html.
Beheben Sie die angezeigten Fehler und ermöglichen Sie somit eine erfolgreiche Pipeline.
Ersetzen Sie in der build-stage mittels sed Lorem ipsum in der index.html durch Hello from <branch>!, bei Problemem mit der Variable beachten sie auch diesen Foreneintrag.
Liefern Sie Ihre index.html über die vorhandene Staging-Infrastruktur aus.
