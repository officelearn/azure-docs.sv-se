---
title: Distribuera IBM DB2 pureScale på Azure
description: Lär dig hur du distribuerar en exempel-arkitektur som använts nyligen migrera företaget från dess IBM DB2-miljö som körs på z/OS till IBM DB2 pureScale på Azure.
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
ms.openlocfilehash: 104730d94134d935f56fb95fd55d05b515e9f501
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245573"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Distribuera IBM DB2 pureScale på Azure

Den här artikeln beskriver hur du distribuerar en [exempel arkitektur](ibm-db2-purescale-azure.md) som en företagskund nyligen används för att migrera från dess IBM DB2-miljö som körs på z/OS till IBM DB2 pureScale på Azure.

Om du vill följa de steg som används för migreringen, se installationsskript i den [DB2onAzure](http://aka.ms/db2onazure) arkivet på GitHub. Dessa skript baseras på arkitekturen för en typisk, medelstora online transaction processing (OLTP) arbetsbelastning.

## <a name="get-started"></a>Kom igång

Om du vill distribuera arkitekturen genom att ladda ned och kör skriptet deploy.sh finns i den [DB2onAzure](http://aka.ms/db2onazure) arkivet på GitHub.

Databasen har också skript för att konfigurera en Grafana-instrumentpanel. Du kan använda instrumentpanelen för att fråga Prometheus, öppen källkod övervaknings- och aviseringssystemet som ingår i DB2.

> [!NOTE]
> Deploy.sh skriptet på klienten skapar privata SSH-nycklar och skickar dem till distributionsmallen via HTTPS. Av säkerhetsskäl bör du använda [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) att lagra hemligheter, nycklar och lösenord.

## <a name="how-the-deployment-script-works"></a>Så här fungerar distributionsskriptet

Skriptet deploy.sh skapar och konfigurerar Azure-resurser för den här arkitekturen. Skriptet frågar efter Azure-prenumeration och virtuella datorer som används i målmiljön och utför följande åtgärder:

-   Ställer in den resursgrupp, virtuellt nätverk och undernät i Azure för att installera

-   Ställer in nätverkssäkerhetsgrupper och SSH för miljön

-   Ställer in nätverkskorten på både GlusterFS och DB2 pureScale virtuella datorer

-   Skapar de virtuella datorerna för lagring av GlusterFS

-   Skapar den virtuella jumpbox-datorn

-   Skapar DB2 pureScale virtuella datorer

-   Skapar den virtuella datorn vittne de DB2 pureScale-ping

-   Skapar en virtuell dator i Windows för testning men installerar inte något på den

Distribueringsskripten konfigurera sedan en iSCSI-virtuellt SAN (vSAN) för delad lagring på Azure. I det här exemplet ansluter iSCSI till GlusterFS. Den här lösningen får du också alternativet att installera iSCSI-mål som en Windows-nod. iSCSI tillhandahåller ett gränssnitt för lagring av delade block över TCP/IP som tillåter DB2 pureScale installationsprocessen för ett enhetsgränssnitt för att ansluta till delad lagring. GlusterFS grunderna, finns det [arkitektur: Typer av volymer](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) avsnittet i Gluster Docs.

Distribueringsskripten kör dessa allmänna steg:

1.  Använd GlusterFS för att konfigurera ett kluster med delad lagring på Azure. Det här steget ska ha minst två Linux-noder. Installationsprogrammet information finns i [konfigurerar Red Hat Gluster Storage i Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) i Red Hat Gluster-dokumentationen.

2.  Ställ in en direkt iSCSI-gränssnittet på mål-Linux-servrar för GlusterFS. Installationsprogrammet information finns i [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) i GlusterFS Administration Guide.

3.  Konfigurera iSCSI-initieraren på Linux-datorer. Initieraren åt GlusterFS klustret genom att använda ett iSCSI-mål. Installationsprogrammet information finns i [hur du konfigurerar en iSCSI-mål och initierare i Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) i RootUsers-dokumentationen.

4.  Installera GlusterFS som lagringsskikt för iSCSI-gränssnittet.

När skripten skapar iSCSI-enhet kan är det sista steget att installera DB2 pureScale. Som en del av installationsprogrammet för DB2 pureScale [IBM Spectrum skala](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (kallades tidigare GPFS) samlas in och installeras i klustret GlusterFS. Den här klustrade filsystem kan DB2 pureScale att dela data mellan de virtuella datorer som kör DB2 pureScale motorn. Mer information finns i den [IBM Spectrum skala](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) dokumentationen på webbplatsen IBM.

## <a name="db2-purescale-response-file"></a>Svarsfilen för DB2 pureScale

GitHub-lagringsplatsen innehåller DB2server.rsp, en svarsfil (.rsp) där du kan generera ett automatiserat skript för DB2 pureScale installationen. I följande tabell visas de DB2 pureScale alternativ som svarsfilen används för installation. Du kan anpassa svarsfilen som behövs för din miljö.

> [!NOTE]
> En exempelfil för svar, DB2server.rsp, som ingår i den [DB2onAzure](http://aka.ms/db2onazure) arkivet på GitHub. Om du använder den här filen kan redigera du den innan de fungerar i din miljö.

| Skärmnamn               | Fält                                        | Värde                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Välkommen                   |                                              | Ny installation                                                                                           |
| Välj en produkt          |                                              | DB2 Version 11.1.3.3. Serverversioner med DB2 pureScale                                              |
| Konfiguration             | Katalog                                    | /Data1/OPT/IBM/DB2/v11.1                                                                              |
|                           | Välj installationstypen                 | Vanliga                                                                                               |
|                           | Jag samtycker till villkoren IBM                     | Markerad                                                                                               |
| Instans-ägare            | Befintliga For användarinstans, användarnamn        | DB2sdin1                                                                                              |
| Inhägnade användare               | Befintliga användare, användarnamn                     | DB2sdfe1                                                                                              |
| Kluster-filsystem       | Delad disk partition enhetens sökväg            | /dev/DM-2                                                                                             |
|                           | Monteringspunkt                                  | / DB2sd\_1804a                                                                                         |
|                           | Delad disk för data                         | /dev/DM-1                                                                                             |
|                           | Monteringspunkt (Data)                           | / DB2fs/datafs1                                                                                        |
|                           | Delad disk för logg                          | /dev/DM-0                                                                                             |
|                           | Monteringspunkt (loggning)                            | / DB2fs/logfs1                                                                                         |
|                           | DB2 Kluster Services Tiebreaker. Enhetens sökväg | /dev/DM-3                                                                                             |
| Värdlistan                 | D1 [eth1] d2 [eth1] cf1 [eth1] cf2 [eth1] |                                                                                                       |
|                           | Prioriterade primära CF                         | CF1                                                                                                   |
|                           | Prioriterade sekundära CF                       | cf2                                                                                                   |
| Svarsfilen och sammanfattning | första alternativet                                 | Installera DB2 Server Edition med funktionen pureScale IBM DB2 och spara mina inställningar i en svarsfil |
|                           | Svaret filnamn                           | /Root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Information om den här distributionen

- Värdena för /dev-dm0, /dev-dm1, /dev-dm2 och /dev-dm3 ändras efter en omstart på den virtuella datorn där installationen äger rum (d0 i automatiserade skript). För att hitta rätt värden, kan du utfärda följande kommando innan du slutför svarsfilen på servern där installationen körs:

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

- Installationsskripten Använd alias för iSCSI-diskar så att de faktiska namnen lätt kan hittas.

- När du kör installationsskriptet på d0, den **/dev/dm -\***  värden kan vara olika på d1 och cf0 cf1. Skillnaden i värden påverkar inte DB2 pureScale installationen.

## <a name="troubleshooting-and-known-issues"></a>Felsökning och kända problem

GitHub-lagringsplatsen innehåller en kunskapsbas som författarna underhållas. Den visar eventuella problem som du kanske och lösningar som du kan prova. Till exempel kända problem kan inträffa när:

-   Du försöker nå gatewayens IP-adress.

-   Du kompilerar General Public License (GPL).

-   Det går inte att säkerhetshandskakningen mellan värdar.

-   DB2-installationsprogrammet identifierar en befintlig filsystem.

-   Du installerar manuellt IBM Spectrum skala.

-   Du installerar DB2 pureScale när IBM Spectrum skala har redan skapats.

-   Du bort DB2 pureScale och IBM Spectrum skala.

Mer information om dessa och andra kända problem finns i filen kb.md i den [DB2onAzure](http://aka.ms/DB2onAzure) lagringsplatsen.

## <a name="next-steps"></a>Nästa steg

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Skapa nödvändiga användare för en DB2-pureScale funktionsinstallation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - skapa instans-kommando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale lösning för kluster](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Plattformen modernisering Alliance: IBM DB2 i Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Azure Virtual Datacenter Lift and Shift-Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
