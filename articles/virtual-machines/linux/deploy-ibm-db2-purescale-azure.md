---
title: Distribuera IBM DB2 pureScale på Azure
description: Lär dig hur du distribuerar en exempelarkitektur som nyligen användes för att migrera ett företag från IBM DB2-miljön som körs på z/OS till IBM DB2 pureScale på Azure.
author: njray
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 98e912894a4d93a057a2f6a2153d0690deaed250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968899"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Distribuera IBM DB2 pureScale på Azure

I den här artikeln beskrivs hur du distribuerar en [exempelarkitektur](ibm-db2-purescale-azure.md) som en företagskund nyligen använde för att migrera från IBM DB2-miljön som körs på z/OS till IBM DB2 pureScale på Azure.

Om du vill följa stegen som används för migreringen läser du installationsskripten i [DB2onAzure-databasen](https://aka.ms/db2onazure) på GitHub. Dessa skript baseras på arkitekturen för en typisk, medelstor onlinetransaktionsbearbetning (OLTP) arbetsbelastning.

## <a name="get-started"></a>Komma igång

Om du vill distribuera den här arkitekturen hämtar och kör du det deploy.sh skriptet som finns i [DB2onAzure-databasen](https://aka.ms/db2onazure) på GitHub.

Databasen har också skript för att konfigurera en Grafana-instrumentpanel. Du kan använda instrumentpanelen för att fråga Prometheus, övervaknings- och varningssystemet med öppen källkod som ingår i DB2.

> [!NOTE]
> Det deploy.sh skriptet på klienten skapar privata SSH-nycklar och skickar dem till distributionsmallen via HTTPS. För större säkerhet rekommenderar vi att du använder [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) för att lagra hemligheter, nycklar och lösenord.

## <a name="how-the-deployment-script-works"></a>Så här fungerar distributionsskriptet

Skriptet deploy.sh skapar och konfigurerar Azure-resurser för den här arkitekturen. Skriptet frågar dig för Azure-prenumerationen och virtuella datorer som används i målmiljön och utför sedan följande åtgärder:

-   Ställer in resursgruppen, det virtuella nätverket och undernäten på Azure för installationen.

-   Ställer in nätverkssäkerhetsgrupper och SSH för miljön.

-   Ställer in flera nätverkskort på både den delade lagringen och db2-virtuella datorerna i renskala.

-   Skapar virtuella lagringsdatorer. Om du använder Storage Spaces Direct eller en annan lagringslösning läser du [översikt över Storage Spaces Direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

-   Skapar den virtuella startrutan.

-   Skapar db2 pureScale virtuella datorer.

-   Skapar vittnet virtuell maskin som DB2 pureScale pingar. Hoppa över den här delen av distributionen om din version av Db2 pureScale inte kräver ett vittne.

-   Skapar en virtuell Windows-dator som ska användas för testning, men installerar ingenting på den.

Därefter konfigurerade distributionsskripten ett iSCSI-nätverk för virtuellt lagringsutrymme (vSAN) för delad lagring på Azure. I det här exemplet ansluter iSCSI till det delade lagringsklustret. I den ursprungliga kundlösningen användes GlusterFS. IBM stöder dock inte längre den här metoden. För att behålla ditt stöd från IBM måste du använda ett iSCSI-kompatibelt filsystem som stöds. Microsoft erbjuder Storage Spaces Direct (S2D) som tillval.

Den här lösningen ger dig också möjlighet att installera iSCSI-målen som en enda Windows-nod. iSCSI tillhandahåller ett delat blocklagringsgränssnitt över TCP/IP som gör att inställningsproceduren DB2 pureScale kan använda ett enhetsgränssnitt för att ansluta till delad lagring.

Distributionsskripten kör följande allmänna steg:

1.  Konfigurera ett delat lagringskluster på Azure. Det här steget omfattar minst två Linux-noder.

2.  Konfigurera ett iSCSI Direct-gränssnitt på mål-Linux-servrar för det delade lagringsklustret.

3.  Konfigurera iSCSI-initieraren på virtuella Linux-datorer. Initieraren kommer åt det delade lagringsklustret med hjälp av ett iSCSI-mål. Information om inställningar finns i [Så här konfigurerar du en iSCSI-mål och initierare i Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) i RootUsers-dokumentationen.

4.  Installera det delade lagringslagret för iSCSI-gränssnittet.

När skripten har skapat iSCSI-enheten är det sista steget att installera DB2 pureScale. Som en del av DB2 pureScale-installationen kompileras [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (tidigare gpfs) och installeras på GlusterFS-klustret. Detta klustrade filsystem gör det möjligt för DB2 pureScale att dela data mellan de virtuella datorer som kör DB2 pureScale-motorn. Mer information finns i [IBM Spectrum Scale-dokumentationen](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) på IBM:s webbplats.

## <a name="db2-purescale-response-file"></a>DB2 pureScale svarsfil

GitHub-arkivet innehåller DB2server.rsp, en svarsfil (.rsp) som gör att du kan generera ett automatiserat skript för DB2 pureScale-installationen. I följande tabell visas alternativen DB2 pureScale som svarsfilen använder för installationen. Du kan anpassa svarsfilen efter behov för din miljö.

> [!NOTE]
> Ett exempel svar fil, DB2server.rsp, ingår i [DB2onAzure](https://aka.ms/db2onazure) arkivet på GitHub. Om du använder den här filen måste du redigera den innan den kan fungera i din miljö.

| Skärmnamn               | Field                                        | Värde                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Välkommen                   |                                              | Ny installation                                                                                           |
| Välj en produkt          |                                              | DB2 Version 11.1.3.3. Serverutgåvor med DB2 pureScale                                              |
| Konfiguration             | Katalog                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Välj installationstyp                 | Typiska                                                                                               |
|                           | Jag godkänner IBM-villkoren                     | Markerad                                                                                               |
| Instansägare            | Befintlig användare till exempel, Användarnamn        | DB2sdin1 (PÅ ANDRA)                                                                                              |
| Inspärrad användare               | Befintlig användare, Användarnamn                     | DB2sdfe1                                                                                              |
| Klusterfilsystem       | Sökväg till delad diskpartitionsenhet            | /dev/dm-2                                                                                             |
|                           | Montera punkt                                  | /DB2sd\_1804a                                                                                         |
|                           | Delad disk för data                         | /dev/dm-1                                                                                             |
|                           | Monteringspunkt (data)                           | /DB2fs/datafs1                                                                                        |
|                           | Delad disk för logg                          | /dev/dm-0                                                                                             |
|                           | Monteringspunkt (logg)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 Klustertjänster Tiebreaker. Enhetssökväg | /dev/dm-3                                                                                             |
| Värdlista                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | Önskad primär CF                         | jfr1                                                                                                   |
|                           | Önskad sekundär CF                       | jfr2                                                                                                   |
| Svarsfil och sammanfattning | första alternativet                                 | Installera DB2 Server Edition med IBM DB2 pureScale-funktionen och spara mina inställningar i en svarsfil |
|                           | Namn på svarsfil                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Anteckningar om den här distributionen

- Värdena för /dev-dm0, /dev-dm1, /dev-dm2 och /dev-dm3 kan ändras efter en omstart på den virtuella datorn där installationen sker (d0 i det automatiserade skriptet). Om du vill hitta rätt värden kan du utfärda följande kommando innan du slutför svarsfilen på servern där installationen ska köras:

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

- Installationsskripten använder alias för iSCSI-diskarna så att de faktiska namnen lätt kan hittas.

- När inställningsskriptet körs på d0 kan **\* /dev/dm-värdena** vara olika på d1, cf0 och cf1. Skillnaden i värden påverkar inte DB2 pureScale-installationen.

## <a name="troubleshooting-and-known-issues"></a>Felsökning och kända problem

GitHub-repoen innehåller en kunskapsbas som författarna upprätthåller. Den listar potentiella problem som du kan ha och lösningar som du kan prova. Kända problem kan till exempel inträffa när:

-   Du försöker nå gateway-IP-adressen.

-   Du sammanställer Allmän offentlig licens (GPL).

-   Säkerhetshandskakningen mellan värdar misslyckas.

-   DB2-installationsprogrammet identifierar ett befintligt filsystem.

-   Du installerar IBM Spectrum Scale manuellt.

-   Du installerar DB2 pureScale när IBM Spectrum Scale redan har skapats.

-   Du tar bort DB2 pureScale och IBM Spectrum Scale.

Mer information om dessa och andra kända problem finns i kb.md-filen i [DB2onAzure-repoen.](https://aka.ms/DB2onAzure)

## <a name="next-steps"></a>Nästa steg

-   [Skapa nödvändiga användare för en DB2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - Skapa förekomst, kommando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale Kluster Data lösning](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Azure Virtual Data Center Lyft och Skift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
