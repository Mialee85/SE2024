## Schritt 1 - Docker Netzwerk einrichten und mit einen Container verbinden
<!--#Aufgabe: 
#Datenbank ToDo-App durch MariaDB ersetzen
#Hinweis: Jeder Container nur eine Aufgabe
#Zwei Container einrichten (DB + ToD-App)
#Netzwerk erstellen
#MariaDB Container starten
    Beachten:
    mit Ntzwerk verbinden
    Option --network-alias Container alias geben
    Daten unter /var/lib/mysql
    Mittels --env bzw. -e Umgebungsvariablen für DB setzen, bei Start soll DB angelegt werden + Namen DB notieren
    ```SHOW DATABASE;```

                                                     Alles hier drüber, mit Ausnahme der Überschrift, wird nachher gelöscht-->

# MariaDB installieren

```
docker pull mariadb:latest
```

Mit dem Befehl **docker pull** können vorgefertigte Images heruntergeladen werden. Wird keine Version angegeben, dann wird die neueste runtergeladen. Im oben stehenden Code haben wir mit **:latest** die neueste Version ausgewählt.

# Netzwerk erstellen

Mit **docker network** werden Netzwerke verwaltet. Für den Beginn sind die folgenden Befehle wichtig:

| Befehl | Erläuterung |
| --- | --- |
| docker network create | Erzeugt ein Netzwerk |
| docker network connect | Verbindet einen Container mit einem Netzwerk |
| docker network ls | Listet aktive Netzwerke auf |
| docker network rm | Entfernt ein oder mehrere Netzwerke |

Für den Versuchsaufbau wird ein Netzwerk zum verbinden der Container für die MariaDB und die ToDo-App benötigt. Das Netzwerk bekommt den Namen **my-network**. Einige für wichtig erachtete Optionen werden nachstehend aufgelistet:

| Option | Beschreibung | Eingabemöglichkeit |
| --- | --- | --- |
| ---driver, -d | Verwaltet das Netzwerk | bridge, overlay |
| --attachable | Aktiviert manuelles Anhängen von Containern | - |
| --internal | Externen Zugriff beschränken | - |
| --label | Metadaten des Netzwerks | - |
| --opt, -o | Treiberspezifische Optionen | - |
| --scope | Umfang des Netzwerks | - |
| --subnet | Subnetz (Netzwerksegment, CIDR-Format) | - |

Hinweis: Bridge-Netzwerke sind isolierte Netzwerke in einer einzelnen Engine-Installation. Für mehrer Docker-Hosts, mit jeweils einer Engine, wird ein overlay Netzwerk benötigt und ein WARM-Modus muss aktiviert werden. Für den Versuchsaufbau ist das nicht notwendig.


```
docker network create -d bridge my-network
```

```
docker network ls
```

# MariaDB starten und mit Netzwerk verbinden

Ein Container kann über Namen oder ID mit dem Netzwerk verbunden werden. Container innerhalb eines Netzwerks können miteinander kommunizieren. 


```
docker run --name my-mariadb --env MARIADB_USER=ich --env MARIADB_PASSWORD=1234 --env MARIADB_ROOT_PASSWORD=1234 mariadb
```

**docker run** startet den Container. Mit **name** wird der Name der Datenbank festgelegt. Die Umgebungsvariablen können mit **env** oder **e** angelegt werden. In dem Versuchsaufbau wird hiermit ein User und das dazugehörige Passwort angelegt. 

Anschließend kann eine Verbindung mit dem Netzwerk hergestellt werden.

```
docker network connect my-network my-mariadb
```


<!--
```docker run --name my-first-mariadb --env MARIADB_USER=marita --env MARIADB_PASSWORD=1234 --env MARIADB_ROOT_PASSWORD=1234  mariadb:latest```

>mariadb mit einem Server verbinden

```docker run -itd --network=my-network my-first-mariadb```
-->



# Verbindung testen

Mit dem folgenden Befehl kann überprüft werden, ob eine Verbindung mit einem oder mehreren Containern besteht:

```
docker network inspect my-network
```

Der Zugriff auf die Datenbank erfolgt in zwei Schritten. Zuerst wird eine bash innerhalb des Container geöffnet und anschließend die Verbindung mithilfe der User Daten.

```
docker exec -it my-mariadb bash
```

```
mysql -uroot -p
```

Datenbank anzeigen

```
SHOW DATABASES;
``` 

## Schritt 2 - Container im Docker Netzwerk identifizieren

Zum ermitteln der IP Adresse des Container kann der docker inspect Befehl aus Schritt 1 genutzt werden. Die Id des Containers kann mit **docker ps** angezeigt werden.

# Id des Containers ermitteln

```
docker ps
```

# IP-Adresse des Containers ermitteln
```
docker inspect <Container-ID> | grep "IPAddress"
```

HINWEIS: **<Container-ID>** ist eine Variable, die durch die ermittelte Container-Id ersetzt werden muss! **IPAdresse** ist der Suchbergiff zur Ermittlung der IP-Adresse des Container mit der gewählten ID.
<!--Ergebnis: "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.3",
                    "IPAddress": "172.17.0.3",
                    "IPAddress": "172.18.0.2",
-->

## Schritt 3 - ToDo-App Container ins Netzwerk integrieren
Die ToDo App soll mit Umgebungsvariablen, die die MariaDB- Verbindungseinstellungen spezifizieren, gestartet werden. Zudem soll die App auf dem localhost:4200 erreichbar sein und die aktuelle LTS Version des Node.js Image als Grundlage nutzen.   

Der **docker build** Befehl muss um die nachstehenden Umgebungsvariablen ergänzt werden:

| Umgebungsvariable | Beschreibung |
| --- | --- |
| MYSQL_HOST| Hostname des MariaDB_Servers |
| MYSQL_USER | Benutzername für die Verbindung |
| MYSQL_PASSWORD | Passwort für die Verbindung |
| MYSQL_DB | Name zu nutzenden Datenbank |

# MariaDB starten

```
docker run \
  -d -v ~/swe/getting-started/app/:/var/lib/mysql \
  --name my-mariadb \
  --network-alias mariaNet \
  --network my-network \
  --env MYSQL_USER=root \
  --env MYSQL_ROOT_PASSWORD=1234 \
  --env MYSQL_DATABASE=todos \
  mariadb \
  /

```
Erläuterung: 
-v current working(Host):Container ->Wenn es nicht existiert, dann wird das host directory angelegt

Erläuterung --env MYSQL_USER=ich | --env MYSQL_PASSWORD=1234


# Todo App starten

```
docker run -dp 4200:3000 \
  --network my-network \
  --env MYSQL_HOST=mariaNet \
  --env MYSQL_USER=root \
  --env MYSQL_PASSWORD=1234 \
  --env MYSQL_DB=todos \
  todoApp \
  /
   ```
Erläuterung: 
mysql_db ist der Name der Datentabelle, in der MariaDb (my-first-mariadb) in der die Aufgaben gespeichert werden. 
MYSQL_HOST ist das Netzwerk-alias der MariaDB. 

<!--HOST = mariaDbNet
docker run -dp 4200:3000 
--network firstNetwork 
--env MYSQL_HOST=mariaDbNet 
--env MYSQL_DB=todos 
--env MYSQL_USER=root 
--env MYSQL_PASSWORD=root todonew


-->

Erläuterungen zum Befehl:
|||
| --- | --- |
| --detach, -d | Lässt Container im Hintergrund laufen |
| --ublish, -p | Veröffentlicht den Container Port an den HOST |
| 4200:3000 | HOST : Container |
| --env , -e | Setzt die Umgebungsvariablen |


HINWEIS: Falls der Hostname der MariaDB nicht bekannt ist, kann er abgefragt werden:

```
docker exec -it my-first-mariadb bash
```

```
mysql -uroot -p
#Enter password: 
```

>MariaDB [(none)]> SHOW VARIABLES WHERE Variable_name='hostname';



## Schritt 4 bis 8 - Docker Compose
# Start per Docker compose vorbereiten
Ins Wurzelverzeichnis der App wechseln

```
cd swe/getting-started/app 
```

Die Datei docker-compose.yml anlegen
```
nano docker-compose.yml
```

```
version: "3.8"
```
# App in die docker-compose.yml integrieren

```
version: "3.8"

services:
   app:
      image: Inginx:latest
      container_name: todoApp
      ports:
       -4200:3000
      enviroment:
        - MYSQL_HOST=db
        - MYSQL_DB=test
        - MYSQL_USER=ich
        - MYSQL_PASSWORD=1234
      build: 
       context:./app 
       dockerfile: dockerfile  
```
# Datenbank Container in die docker-compose.yml integrieren

```
version: "3.8"

services:
   app:
      image: Inginx:latest
      container_name: todoapp
      ports:
       -4200:3000
      enviroment:
        - MYSQL_HOST=db
        - MYSQL_DB=test
        - MYSQL_USER=ich
        - MYSQL_PASSWORD=1234
      build: 
        context:./app 
        dockerfile: dockerfile  
   db:
      image: mariadb:latest
    container_name: mariaContainer
    environment:
      - MYSQL_ROOT_PASSWORD=1234 
      - MYSQL_DATABASE=test
      - MYSQL_USER=ich
  myadmin:
    image: phpmyadmin
    container_name: myAdmin
    ports:
      - 8090:80
    environment:
      - PMA_HOST=db

volumes:
   -v ./swe/getting-started/app/:/var/lib/mysql


```
Hinweis: myadmin ist das anlegen eines phpMyAdmin Instanz für die MariaDB
Hinwies app->build: Entweder Image nutzen oder Image builden/bauen/erstellen. Hierzu wird der build Befehl mit docker-compose.yml reingeschrieben.

# Betriebsumgebung starten

```
docker compose up -d
```

**Logs überprüfen**

```
docker compose logs -f
```

**Anwendung beenden**

```
docker compose down [--volume]
```
mit der Option --volume wird die Volume gelöscht