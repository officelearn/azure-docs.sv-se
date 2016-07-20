<properties
   pageTitle="Installera DC/OS CLI | Microsoft Azure"
   description="Installera DC/OS CLI."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Containers, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] Det här är för att arbeta med DC/OS-baserade ACS-kluster. Du behöver inte göra det här för Swarm-baserade ACS-kluster.

[Anslut först till ditt DC/OS-baserade ACS-kluster](../articles/container-service/container-service-connect.md). När du har gjort det, kan du installera DC/OS CLI på din klientdator med nedanstående kommandon:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Om du använder en äldre version av Python, kan du få en del "InsecurePlatformWarnings". Du kan ignorera dem.

För att komma igång utan att starta om gränssnittet, kör du:

```bash
source ~/.bashrc
```

Det här steget är inte nödvändigt när du startar nya gränssnitt.

Nu kan du bekräfta att CLI är installerad:

```bash
dcos --help
```


<!--HONumber=Jun16_HO2-->


