---
title: Distribuera IBM Db2 pureScale på Azure
description: Lär dig hur du distribuerar en [exempel arkitektur](ibm-db2-purescale-azure.md) använts nyligen migrera företaget från sina IBM Db2-miljö som körs på z/OS till IBM Db2 pureScale på Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 24bdc9867af869437cc0e440a80e5bf4813abbae
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977682"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Distribuera IBM Db2 pureScale på Azure

Den här artikeln beskriver hur du distribuerar en [exempel arkitektur](ibm-db2-purescale-azure.md) använts nyligen migrera företaget från sina IBM Db2-miljö som körs på z/OS till IBM Db2 pureScale på Azure.

Om du vill följa de steg som används under migreringen, se installationsskript i [Db2onAzure](http://aka.ms/db2onazure) arkivet på GitHub. Dessa skript är baserade på den arkitektur som används för en typisk, medelstora online transaction processing (OLTP) arbetsbelastning.

## <a name="get-started"></a>Kom igång

Om du vill distribuera arkitekturen genom att ladda ned och kör skriptet deploy.sh finns i den [Db2onAzure](http://aka.ms/db2onazure) arkivet på GitHub.

Databasen innehåller också skript som du kan använda för att ställa in en Grafana-instrumentpanel som kan användas för att fråga Prometheus, öppen källkod övervaknings- och aviseringssystemet som ingår i Db2.

> [!NOTE]
> Deploy.sh skriptet på klienten skapar privata SSH-nycklar och skickar dem till distributionsmallen via HTTPS. Av säkerhetsskäl bör du använda [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) att lagra hemligheter, nycklar och lösenord.

## <a name="how-the-deployment-script-works"></a>Så här fungerar distributionsskriptet

Skriptet deploy.sh skapar och konfigurerar Azure-resurser som används i den här arkitekturen. Skriptet frågar efter Azure-prenumeration och virtuella datorer som används i målmiljön och utför följande åtgärder:

-   Ställer in den resursgrupp, virtuellt nätverk och undernät i Azure för installationen.

-   Ställer in NSG: er och SSH för miljön.

-   Ställer in flera nätverkskort i både GlusterFS och Db2 pureScale virtuella datorer.

-   Skapar GlusterFS storage virtuella datorer.

-   Skapar den virtuella jumpbox-datorn.

-   Skapar Db2 pureScale virtuella datorer.

-   Skapar den virtuella datorn vittne de Db2 pureScale pingar.

-   Skapar en virtuell dator i Windows för testning, men kan inte installeras något i den.

Distribueringsskripten konfigurera sedan iSCSI virtuella SAN-nätverk (vSAN) för delad lagring på Azure. I det här exemplet ansluter iSCSI till GlusterFS. Den här lösningen får du också alternativet att installera iSCSI-mål som en Windows-nod. (iSCSI ger en delad block lagringsgränssnittet över TCP/IP som tillåter Db2 pureScale installationsprocessen för ett enhetsgränssnitt för att ansluta till delad lagring.) GlusterFS grunderna, finns det [arkitektur: typer av volymer](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) avsnittet i komma igång med GlusterFS.

Distribueringsskripten kör dessa allmänna steg:

1.  Konfigurera ett kluster med delad lagring på Azure. GlusterFS används för att konfigurera klustret för delad lagring. Detta innebär att minst två Linux-noder. Installationsprogrammet information finns i [konfigurerar Red Hat Gluster Storage i Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) i Red Hat Gluster-dokumentationen.

2.  Ställ in en direkt iSCSI-gränssnittet på mål-Linux-servrar för GlusterFS. Installationsprogrammet information finns i [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) i GlusterFS Administration Guide.

3.  Konfigurera iSCSI-initierare på virtuella Linux-datorer som har åtkomst GlusterFS klustret med iSCSI-mål. Installationsprogrammet information finns i den [hur du konfigurerar en iSCSI-mål och initierare i Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) i RootUsers-dokumentationen.

4.  Installerar GlusterFS som lagringsskikt för iSCSI-gränssnittet.

När du har skapat iSCSI-enhet, är det sista steget att installera Db2 pureScale. Som en del av installationsprogrammet för Db2 pureScale [IBM Spectrum skala](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (kallades tidigare GPFS) samlas in och installeras i klustret GlusterFS. Den här klustrade filsystem kan Db2 pureScale att dela data mellan flera virtuella datorer som kör Db2 pureScale motorn. Mer information finns i den [IBM Spectrum skala](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) dokumentationen på webbplatsen IBM.

## <a name="db2-purescale-response-file"></a>Svarsfilen för Db2 pureScale

GitHub-lagringsplatsen innehåller Db2server.rsp, en svarsfil (.rsp) där du kan generera ett automatiserat skript för Db2 pureScale installationen. I följande tabell visas de Db2 pureScale alternativ som svarsfilen används för installation. Du kan anpassa svarsfilen som behövs för din installationsmiljö.

> [!NOTE]
> En exempelfil för svar, Db2server.rsp, som ingår i den [Db2onAzure](http://aka.ms/db2onazure) arkivet på GitHub. Om du använder den här filen kan redigera du den innan de fungerar i din miljö.

**Filalternativ för Db2 pureScale svar**

| Skärmnamn               | Fält                                        | Värde                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Välkommen                   |                                              | Ny installation                                                                                           |
| Välj en produkt          |                                              | Db2 Version 11.1.3.3. Serverversioner med Db2 pureScale                                              |
| Konfiguration             | Katalog                                    | /Data1/OPT/IBM/DB2/v11.1                                                                              |
|                           | Välj installationstypen                 | Vanliga                                                                                               |
|                           | Jag samtycker till villkoren IBM                     | Markerad                                                                                               |
| Instans-ägare            | Befintliga For användarinstans, användarnamn        | Db2sdin1                                                                                              |
| Inhägnade användare               | Befintliga användare, användarnamn                     | Db2sdfe1                                                                                              |
| Kluster-filsystem       | Delad disk partition enhetens sökväg            | /dev/DM-2                                                                                             |
|                           | Monteringspunkt                                  | / Db2sd\_1804a                                                                                         |
|                           | Delad disk för data                         | /dev/DM-1                                                                                             |
|                           | Monteringspunkt (Data)                           | / Db2fs/datafs1                                                                                        |
|                           | Delad disk för logg                          | /dev/DM-0                                                                                             |
|                           | Monteringspunkt (loggning)                            | / Db2fs/logfs1                                                                                         |
|                           | Db2-kluster Services Tiebreaker. Enhetens sökväg | /dev/DM-3                                                                                             |
| Värdlistan                 | D1 [eth1] d2 [eth1] cf1 [eth1] cf2 [eth1] |                                                                                                       |
|                           | Prioriterade primära CF                         | CF1                                                                                                   |
|                           | Prioriterade sekundära CF                       | cf2                                                                                                   |
| Svarsfilen och sammanfattning | första alternativet                                 | Installera Db2 Server Edition med funktionen pureScale IBM Db2 och spara mina inställningar i en svarsfil |
|                           | Svaret filnamn                           | /Root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Information om den här distributionen

-   Värdena för /dev-dm0, /dev-dm1, /dev-dm2 och /dev-dm3 ändras efter en omstart på den virtuella datorn där installationen äger rum (d0 i automatiserade skript). För att hitta rätt värden, kan du utfärda följande kommando innan du slutför svarsfilen på servern där installationen körs:

```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
```

-   Installationsskripten Använd alias för iSCSI-diskar så att de faktiska namnen lätt kan hittas.

-   När du kör installationsskriptet på d0, den **/dev/dm -\***  värden kan skilja sig på d1, cf0 och cf1. Db2 pureScale installationen bryr sig inte.

## <a name="troubleshooting-and-known-issues"></a>Felsökning och kända problem

GitHub-lagringsplatsen innehåller en kunskapsbas som underhålls av författarna. Den visar potentiella problem som kan uppstå och lösningar som du kan prova. Till exempel kända problem kan uppstå när:

-   Om du försöker nå gatewayens IP-adress.

-   Kompilerar GPL.

-   Det går inte att säkerhetshandskakningen mellan värdar.

-   Db2-installationsprogrammet identifierar en befintlig filsystem.

-   Installera GPFS manuellt.

-   Installera Db2 pureScale när GPFS redan har skapats.

-   Tar bort Db2 pureScale och GPFS.

Mer information om dessa och andra kända problem finns i filen kb.md i den [Db2onAzure](http://aka.ms/Db2onAzure) lagringsplatsen.

## <a name="next-steps"></a>Nästa steg

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Skapa nödvändiga användare för en Db2-pureScale funktionsinstallation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [Db2icrt - skapa instans-kommando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Db2 pureScale lösning för kluster](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Plattformen modernisering Alliance: IBM Db2 i Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Azure Virtual Datacenter Lift and Shift-Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
