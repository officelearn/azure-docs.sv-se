<properties
   pageTitle="Ansluta till ett Azure Container Service-kluster | Microsoft Azure"
   description="Ansluta till ett Azure Container Service-kluster med en SSH-tunnel."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, behållare, Micro-tjänster, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>



# Ansluta till ett Azure Container Service-kluster

DC/OS- och Docker Swarm-kluster som distribueras med Azure Container Service exponerar REST-slutpunkter. De här slutpunkterna är dock inte öppna för allmänheten. Du måste skapa en SSH-tunnel (Secure Shell) för att kunna hantera de här slutpunkterna. När en SSH-tunnel har upprättats kan du köra kommandon mot klusterslutpunkter och visa klustergränssnittet via en webbläsare i ditt system. I det här dokumentet får du anvisningar om hur du skapar en SSH-tunnel i Linux, OS X och Windows.

>[AZURE.NOTE] Du kan skapa en SSH-session med ett klusterhanteringssystem. Men vi det rekommenderar inte. Om du arbetar direkt i ett hanteringssystem finns det en risk att konfigurationsändringar görs oavsiktligt.   

## Skapa en SSH-tunnel i Linux eller OS X

Det första du ska göra när du ska skapa en SSH-tunnel i Linux eller OS X är att leta upp det offentliga DNS-namnet på belastningsutjämnade huvudservrar. Det gör du genom att expandera resursgruppen så att alla resurser visas. Leta upp och markera huvudserverns offentliga IP-adress. Då öppnas ett blad som innehåller information om den offentliga IP-adressen, och här ingår DNS-namnet. Spara det här namnet för senare användning. <br />


![Offentligt DNS-namn](media/pubdns.png)

Öppna ett gränssnitt och kör följande kommando där:

**PORT** är porten för den slutpunkt som du vill exponera. För Swarm är det 2375. Använd port 80 för DC/OS.  
**USERNAME** är det användarnamn som angavs när du distribuerade klustret.  
**DNSPREFIX** är det DNS-prefix som du angav när du distribuerade klustret.  
**REGION** är den region där resursgruppen finns.  
**PATH_TO_PRIVATE_KEY** [VALFRITT] är den sökväg till den privata nyckeln som motsvarar den offentliga nyckeln som du angav när du skapade behållartjänstklustret. Använd det här alternativet med flaggan -i.

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> SSH-anslutningsporten är 2200, inte standardporten 22.

## DC/OS-tunnel

Om du vill öppna en tunnel till de DC/OS-relaterade slutpunkterna kör du ett kommando som liknar följande:

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Du kan nu komma åt de DC/OS-relaterade slutpunkterna på:

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

På samma sätt kan du nå REST API:erna för alla program via den här tunneln.

## Swarm-tunnel

Om du vill öppna en tunnel till Swarm-slutpunkten kör du ett kommando som liknar följande:

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Du kan nu ange din DOCKER_HOST-miljövariabel på följande sätt. Du kan fortsätta att använda ditt Docker-kommandoradsgränssnitt (CLI) som vanligt.

```bash
export DOCKER_HOST=:2375
```

## Skapa en SSH-tunnel i Windows

Det finns flera olika sätt att skapa SSH-tunnlar i Windows. I det här dokumentet beskrivs hur du använder PuTTY för att göra det.

Hämta PuTTY till Windows-systemet och kör programmet.

Ange ett värdnamn som består av klustrets adminanvändarnamn och det offentliga DNS-namnet på den första huvudservern i klustret. **Värdnamnet** ska se ut så här: `adminuser@PublicDNS`. Ange 2200 som **Port**.

![PuTTY-konfiguration 1](media/putty1.png)

Välj **SSH** och **Autentisering**. Lägg till filen för privat nyckel för autentisering.

![PuTTY-konfiguration 2](media/putty2.png)

Välj **Tunnlar** och konfigurera följande vidarebefordrade portar:
- **Källport:** Enligt önskemål – använd 80 för DC/OS eller 2375 för Swarm.
- **Mål:** Använd localhost:80 för DC/OS eller localhost:2375 för Swarm.

Exemplen nedan har konfigurerats för DC/OS, men det ser ungefär likadant ut för Docker Swarm.

>[AZURE.NOTE] Port 80 får inte användas för något annat när du skapar den här tunneln.

![PuTTY-konfiguration 3](media/putty3.png)

När du är klar sparar du anslutningskonfigurationen och ansluter PuTTY-sessionen. När du ansluter kan du se portkonfigurationen i PuTTY-händelseloggen.

![PuTTY-händelselogg](media/putty4.png)

När du har konfigurerat tunneln för DC/OS kan du komma åt den relaterade slutpunkten på:

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

När du har konfigurerat tunneln för Docker Swarm kan du komma åt Swarm-klustret via Docker CLI. Du måste först konfigurera en Windows-miljövariabel som heter `DOCKER_HOST` med värdet ` :2375`.

## Nästa steg

Distribuera och hantera behållare med DC/OS eller Swarm:

- [Arbeta med Azure Container Service och DC/OS](container-service-mesos-marathon-rest.md)
- [Arbeta med Azure Container Service och Docker Swarm](container-service-docker-swarm.md)



<!--HONumber=Sep16_HO3-->


