<properties
   pageTitle="Azure Container Service-hantering av behållare med Docker Swarm | Microsoft Azure"
   description="Distribuera behållare till Docker Swarm i Azure Container Service"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Containers, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/13/2016"
   ms.author="nepeters"/>

# Behållarhantering med Docker Swarm

Med Docker Swarm skapas en miljö för distribuering av arbetsbelastningar i behållare över en pooluppsättning med Docker-värdar. Docker Swarm använder interna Docker API. Arbetsflödet för att hantera behållare i Docker Swarm är ungefär detsamma som det skulle ha varit på en enskild behållarvärd. Det här dokumentet innehåller enkla exempel på distribution av arbetsbelastningar i behållare i en Azure Container Service-instans av Docker Swarm. Mer detaljerad dokumentation om Docker Swarm finns i [Docker Swarm på Docker.com](https://docs.docker.com/swarm/).

Förutsättningar för att kunna göra övningarna i det här dokumentet:

[Skapa ett Swarm-kluster i Azure Container Service](container-service-deployment.md)

[Anslut till Swarm-klustret i Azure Container Service](container-service-connect.md)

## Distribuera en ny behållare

Använd kommandot `docker run` för att skapa en ny behållare i Docker Swarm. I det här exemplet skapas en behållare från avbildningen `yeasy/simple-web`:


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

När behållaren har skapats kan du använda `docker ps` för att returnera information om behållaren. Observera att Swarm-agenten som är värd för behållaren anges:


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Du kan nu komma åt programmet som körs i den här behållaren via det offentliga DNS-namnet på Swarm-agentens belastningsutjämnare. Du hittar den här informationen i Azure Portal:  


![Verklighetstrogna besöksresultat](media/real-visit.jpg)  

## Distribuera flera behållare

Eftersom flera behållare har startats i Docker Swarm-klustret kan du använda kommandot `docker ps` för att se vilka värdar som behållarna körs på. I det här exemplet är tre behållare jämnt fördelade över de tre Swarm-agenterna:  


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## Distribuera behållare med Docker Compose

Du kan använda Docker Compose för att automatisera distribution och konfiguration av flera behållare. Om du vill göra det måste du kontrollera att en SSH-tunnel (Secure Shell) har skapats och att variabeln DOCKER_HOST har angetts.

Skapa filen docker-compose.yml på ditt lokala system. Använd det här [exemplet](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml) för att göra det.

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Kör `docker-compose up -d` för att starta behållardistributionerna:


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Slutligen returneras listan över behållare som körs. Den här listan visar de behållare som distribuerades med Docker Compose:


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

## Nästa steg

[Mer information om Docker Swarm](https://docs.docker.com/swarm/)



<!--HONumber=Jun16_HO2-->


