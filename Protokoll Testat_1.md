## Schritt 1 - In Docker einlesen

Syntax der docker Befehle

Wichtige Befehle

docker ps
cd
docker stop 
docker rm
docker logs -f id

Wietere Informationen: https://docs.docker.com/get-started/overview/

## Schritt 2 - Docker installieren
....

2. Installation überprüfen mit Docker-Image docker/getting-started

```
git clone https://github.com/docker/getting-started.git

```

## Schritt 3 - Anwendung mit Docker starten

1. Dockerfile erstellen

```
nano Dockerfile_App-starten
```
Inhalt:
    # syntax=docker/dockerfile:1
   
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000

Bedeutung:

2. Container Image bauen

```
docker build -t getting-started .
```

3. Container starten

```
docker run -dp 3000:3000 getting-started
```

## Schritt 4 -Anwendung verändern und neu starten

Mit dem Befehl **docker ps** können alle laufenden Container ermittelt werden.

Die laufende getting-started App kann mit dem remove Befehl beendet werden:

```
docker rm -f <ID>
```

Die App soll aktualisiert werden. Der vorhandene Text soll mit einer deutschen Übersetzung ersetzt werden.

Anschließend muss die App neu gebaut (**docker build**) und gestartet (**docker run**) werden.

## Schritt 5 -Daten persistieren

```
docker volume create NAME
```

```

docker run -dp 3000:3000 --mount type=volume,src=NAME,target=/etc/todos getting-started
```

```
docker volume inspect name
```
## Schritt 6 - selbstdefinierter Speicherort des Volumes

```
docker run -d -p 3000:3000 \
    -w /app --mount type=bind,src="$(pwd)",target=/app \
    node:18-alpine \
    sh -c "yarn install && yarn run dev"
```