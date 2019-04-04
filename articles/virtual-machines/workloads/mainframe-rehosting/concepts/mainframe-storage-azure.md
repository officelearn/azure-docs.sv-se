---
title: Flytta lagringen för stordatorprogram till Azure Storage
description: Stora möjligheter till Azure storage-resurser kan hjälpa organisationer att stordatorprogram-baserade migrera och modernisera IBM z14 program.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 4fea787e7fe20d60de91761811e1b69bebf3a010
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896478"
---
# <a name="move-mainframe-storage-to-azure"></a>Flytta lagringen för stordatorprogram till Azure

För att köra stordatorprogram arbetsbelastningar på Microsoft Azure, som du behöver veta hur dina stordatorprogram funktioner jämfört med Azure. Stora möjligheter till storage-resurser kan hjälpa organisationer börjar modernisera utan att överge de program som de förlitar sig på.

Azure tillhandahåller stordatorprogram-liknande funktioner och lagringskapacitet som är jämförbar med IBM z14-baserade system (den mest aktuella modellen när detta skrivs). Den här artikeln visar hur du hämtar jämförbara resultat på Azure.

## <a name="mainframe-storage-at-a-glance"></a>Stordatorprogram lagring på ett ögonblick

IBM-stordatorer karaktäriserar lagring på två sätt. Först är en direkt åtkomst lagringsenhet (DASD). Andra är sekventiella lagring. För att hantera lagring, ger stordatorprogram Data anläggning Storage Management undersystem (DFSMS). Den hanterar åtkomst till olika lagringsenheter.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) refererar till en separat enhet för sekundär lagring av (inte i minnet) som gör att en unik adress som ska användas för direkt åtkomst av data. Ursprungligen, termen DASD som används för att skapa diskar, magnetiska FAT eller dataceller. Nu termen kan också gälla Solid State-lagringsenheter (solid-state drive), storage area network (SAN), nätverksansluten lagring (NAS) och optiska enheter. I det här dokumentet betecknas avser DASD centrifugeringsdiskar, SAN-nätverk och SSD.

Till skillnad från DASD lagring refererar sekventiella lagring på en stordatorprogram till enheter som bandenheter där data nås från en startpunkt och sedan läsas eller skrivas i en rad.

Lagringsenheter är vanligtvis kopplade med hjälp av en fiber-anslutning (FICON) eller kan nås direkt på stordatorprogram IO bus med [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), en IBM-teknik för höghastighetsnätverk kommunikation mellan partitioner på en server med en hypervisor-programmet.

De flesta stordatorprogram system separata lagringsutrymmen i två typer:

- *Onlinelagring* (även kallade frekvent lagring) krävs för dagliga verksamhet. DASD lagring används vanligtvis för detta ändamål. Sekventiella lagring, till exempel daglig säkerhetskopiering till band (logisk eller fysisk), kan dock också användas för detta ändamål.

- *Arkivlagring* (även kallat kall lagring) är inte säkert att monteras vid en given tidpunkt. Det är i stället monteras och komma åt efter behov. Arkivlagring implementeras ofta med sekventiella bandsäkerhetskopiering (logisk eller fysisk) för lagring.

## <a name="mainframe-versus-io-latency-and-iops"></a>Stordatorprogram jämfört med i/o-svarstid och IOPS

Stordatorer används ofta för program som kräver hög i/o-prestanda och låg latens för i/o. De kan göra detta med hjälp av FICON anslutningar till i/o-enheter och HiperSockets. När HiperSockets används för att ansluta program och enheter direkt till en stordatorprogram-i/o-kanal, kan svarstid i mikrosekunder uppnås.

## <a name="azure-storage-at-a-glance"></a>Azure storage på ett ögonblick

Azure infrastructure-as-a-service ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) alternativ för lagring ger jämförbara stordatorprogram kapacitet.

Microsoft erbjuder petabyte tillhandahåller lagring för program på Azure och du har flera lagringsalternativ. Dessa allt från SSD-lagring för hög prestanda till låg kostnad blob storage för masslagringsenheter och Arkiv. Dessutom Azure tillhandahåller ett alternativ för dataredundans för lagring, något som tar mer arbete för att ställa in i en miljö med stordatorprogram.

Azure storage är tillgängligt som [Azure Disks](/azure/virtual-machines/windows/managed-disks-overview), [Azure Files](/azure/storage/files/storage-files-introduction), och [Azure Blobs](/azure/storage/blobs/storage-blobs-overview) som i följande tabell sammanfattas. Läs mer om [när du ska använda varje](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>Beskrivning</th><th>Använd när du vill:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Tillhandahåller ett gränssnitt för SMB, klientbibliotek och en <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> gränssnitt som möjliggör åtkomst överallt till lagrade filer.
</td>
<td><ul>
<li>Flytta över ett program till molnet när programmet använder filsystemet interna API: er för att dela data mellan dem och andra program som körs i Azure.</li>
<li>Store utvecklings- och felsökningsverktyg som måste kunna nås från många virtuella datorer.</li>
</ul>
</td>
</tr>
<tr><td>Azure-Blobar
</td>
<td>Tillhandahåller klientbibliotek och en <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> gränssnitt som gör att Ostrukturerade data kan lagras och används i massiv skala i blockblobar. Stöder också <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> för enterprise lösningar för analys av stordata.
</td>
<td><ul>
<li>Stöd direktuppspelning och direktåtkomst scenarier i ett program.</li>
<li>Ha åtkomst till programdata från var som helst.</li>
<li>Skapa ett data lake för enterprise på Azure och utföra analyser av stordata.</li>
</ul></td>
</tr>
<tr><td>Azure-diskar
</td>
<td>Tillhandahåller klientbibliotek och en <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> gränssnitt som gör att data kan lagras beständigt och nås från en ansluten virtuell hårddisk.
</td>
<td><ul>
<li>Flytta över program som använder interna filsystemet API: er för att läsa och skriva data till beständiga diskar.</li>
<li>Store data som inte behövs för åtkomst från utanför den virtuella datorn som disken är ansluten.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure frekvent (online) och kallagring (Arkiv)

Typ av lagring för ett visst system är beroende av kraven i systemet, inklusive lagringsstorlek, dataflöde och IOPS. För DASD-type lagring på en stordatorprogram använder program på Azure vanligtvis Azure Disks enhet-lagring i stället. För stordatorprogram archive storage används blob storage på Azure.

SSD ger högsta lagringsprestanda på Azure. Följande alternativ är tillgängliga (från och med skrivning av det här dokumentet):

| Type         | Storlek           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB till 64 TB  | 1 200 160,000 IOPS |
| Premium SSD  | 32 GB till 32 TB | 12 till 15 000 IOPS     |
| Standard SSD | 32 GB till 32 TB | 12 – 2 000 IOPS      |

Bloblagring är den största mängden lagringsutrymme på Azure. Förutom lagringsstorleken erbjuder Azure både hanterade och ohanterade lagring. Med hanterad lagring hand Azure tar om att hantera de underliggande lagringskontona. Med ohanterade storage ansvarar användaren för att konfigurera Azure storage-konton av rätt storlek för att uppfylla lagringskraven.

## <a name="next-steps"></a>Nästa steg

- [Stordatormigrering](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Stordatorprogram rehosting på Azure Virtual Machines](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Flytta stordatorprogram beräkning till Azure](mainframe-compute-Azure.md)
- [Avgöra när du ska använda Azure Blobs, Azure Files eller Azure-diskar](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Standard SSD Managed Disks för Azure VM-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM-resurser

- [Parallell Sysplex på IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS och funktionen koppling: Bortom grunderna](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Skapa nödvändiga användare för en Db2-pureScale funktionsinstallation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - skapa instans-kommando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale klustrade databaslösning](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government-molnet för stordatorprogram](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft och FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Fler migreringsresurser

- [Plattformen modernisering Alliance: IBM Db2 i Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure Virtual Datacenter Lift and Shift-Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
