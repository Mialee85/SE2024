pwd print working directory -> Schreibt ds aktuelle Verzeichnis!


## Wichtige Befehle 
|Docker Grund Befehl| Optionen |Bedeutung der Optionen
|docker run <image>|
|docker ps|
|docker stop|||
${variable_name}

Dockerfile erstellen:
FROM
COPY
RUN
EXPOSE
ENTRYPOINT

docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
OPTIONS:
-a attach
-c cpu-shares
-d detach RUN Container in background and print container id-> beim testen ungeeignet
--env -e set envoirement variables
--expose Expose a port or a range of ports
--help print usage
--hostname container hostname
-ip ipv4 adress
-ip6 ipv6 adress
--mount Attach a filesystem mount to a container
--network connect a container to a network
--network-alias Add network-scop alias for the container
--pull Pull image before running [always, missing, never]


|Docker starten
'''docker run -dp 3000:3000 getting-started'''
--restart Restart Container
--tty -t -it allocate a pseudo-tty
--volume , -v		Bind mount a volume
--volume-driver		Optional volume driver for the container
--workdir , -w		Working directory inside the container
