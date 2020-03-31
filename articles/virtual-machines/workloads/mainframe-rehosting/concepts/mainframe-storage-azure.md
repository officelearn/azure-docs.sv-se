---
title: Flytta stordatorlagring till Azure Storage
description: Massivt skalbara Azure-lagringsresurser kan hjälpa stordatorbaserade organisationer att migrera och modernisera IBM z14-program.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288922"
---
# <a name="move-mainframe-storage-to-azure"></a>Flytta stordatorlagring till Azure

Om du vill köra stordatorarbetsbelastningar på Microsoft Azure måste du veta hur stordatorns funktioner står sig i jämförelse med Azure. De enormt skalbara lagringsresurserna kan hjälpa organisationer att börja modernisera utan att överge de program de förlitar sig på.

Azure tillhandahåller stordatorliknande funktioner och lagringskapacitet som är jämförbar med IBM z14-baserade system (den senaste modellen när den här skrivningen skrivs). I den här artikeln får du information om hur du får jämförbara resultat på Azure.

## <a name="mainframe-storage-at-a-glance"></a>Stordatorlagring i korthet

IBM-stordatorn kännetecknar lagring på två sätt. Den första är en DASD-enhet (Direct Access Storage Device). Den andra är sekventiell lagring. För hantering av lagring tillhandahåller stordatorn delsystemet För hantering av datafunktioner (DFSMS). Den hanterar dataåtkomst till de olika lagringsenheterna.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) refererar till en separat enhet för sekundär lagring (inte i minnet) som gör att en unik adress kan användas för direkt åtkomst av data. Ursprungligen termen DASD tillämpas på spinning diskar, magnetiska trummor, eller dataceller. Men nu termen kan också gälla för solid-state lagringsenheter (SSD), lagringsnätverk (TEN), nätverksansluten lagring (NAS) och optiska enheter. I det här dokumentet avses med DASD spinningdiskar, SSD och SSD.For the of this document, DASD refers to spinning disks, S.N.For the of this document, DASD refers to spinning disks, S.N, and SSD.

I motsats till DASD-lagring refererar sekventiell lagring på en stordator till enheter som bandenheter där data nås från en utgångspunkt och sedan läs- eller skrivet på en rad.

Lagringsenheter är vanligtvis anslutna med hjälp av en fiberanslutning (FICON) eller nås direkt på stordatorns IO-buss med [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), en IBM-teknik för höghastighetskommunikation mellan partitioner på en server med en hypervisor.

De flesta stordatorsystem separerar lagring i två typer:

- *Onlinelagring* (även som kallas varmlagring) behövs för dagliga operationer. DASD-lagring används vanligtvis för detta ändamål. Men sekventiell lagring, till exempel dagliga bandsäkerhetskopior (logiska eller fysiska), kan också användas för detta ändamål.

- *Arkivlagring* (även känd som kylförvaring) är inte garanterad att monteras vid en viss tidpunkt. Istället är den monterad och nås efter behov. Arkivlagring implementeras ofta med sekventiella bandsäkerhetskopior (logiska eller fysiska) för lagring.

## <a name="mainframe-versus-io-latency-and-iops"></a>Stordator kontra IO-latens och IOPS

Stordatorer används ofta för program som kräver hög prestanda IO och låg IO latens. De kan göra detta med ficon-anslutningarna till IO-enheter och HiperSockets. När HiperSockets används för att ansluta program och enheter direkt till en stordators IO-kanal kan latens i mikrosekonerna uppnås.

## <a name="azure-storage-at-a-glance"></a>Azure-lagring i korthet

Azure infrastructure-as-a-service[(IaaS)](https://azure.microsoft.com/overview/what-is-iaas/)alternativ för lagring ger jämförbar stordatorkapacitet.

Microsoft erbjuder petabytes värde av lagring för program som finns i Azure, och du har flera lagringsalternativ. Dessa sträcker sig från SSD-lagring för hög prestanda till billig bloblagring för masslagring och arkiv. Dessutom tillhandahåller Azure ett dataredundansalternativ för lagring – något som tar större ansträngning för att konfigurera i en stordatormiljö.

Azure-lagring är tillgänglig som [Azure Disks](/azure/virtual-machines/windows/managed-disks-overview), [Azure Files](/azure/storage/files/storage-files-introduction)och [Azure Blobbar](/azure/storage/blobs/storage-blobs-overview) som följande tabell sammanfattar. Läs mer om [när du ska använda varje](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Typ</th><th>Beskrivning</th><th>Använd när du vill:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Tillhandahåller ett SMB-gränssnitt, klientbibliotek och ett <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST-gränssnitt</a> som ger åtkomst från var som helst till lagrade filer.
</td>
<td><ul>
<li>Lyft och flytta ett program till molnet när programmet använder de inbyggda api:erna för filsystem för att dela data mellan det och andra program som körs i Azure.</li>
<li>Lagra utvecklings- och felsökningsverktyg som måste nås från många virtuella datorer.</li>
</ul>
</td>
</tr>
<tr><td>Azure-blobar
</td>
<td>Tillhandahåller klientbibliotek och ett <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST-gränssnitt</a> som gör att ostrukturerade data kan lagras och nås i stor skala i blockblobar. Stöder även <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> för lösningar för stordataanalys för företag.
</td>
<td><ul>
<li>Stöd för scenarier för direktuppspelning och slumpmässig åtkomst i ett program.</li>
<li>Ha tillgång till programdata var som helst.</li>
<li>Skapa en företagsdatasjö på Azure och utför stordataanalys.</li>
</ul></td>
</tr>
<tr><td>Azure-diskar
</td>
<td>Tillhandahåller klientbibliotek och ett <a href="https://docs.microsoft.com/rest/api/compute/disks">REST-gränssnitt</a> som gör att data kan lagras och kommas åt från en ansluten virtuell hårddisk.
</td>
<td><ul>
<li>Lyft och flytta program som använder inbyggda api:er för filsystem för att läsa och skriva data till beständiga diskar.</li>
<li>Lagra data som inte behöver nås från utanför den virtuella datorn som disken är ansluten till.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure hot (online) och cold (archive) lagring

Vilken typ av lagring för ett visst system beror på kraven i systemet, inklusive lagringsstorlek, dataflöde och IOPS. För DASD-lagring på en stordator använder program på Azure vanligtvis Azure Disks-enhetslagring i stället. För lagring av stordatorer används blob-lagring på Azure.

SSD ger högsta lagringsprestanda på Azure. Följande alternativ är tillgängliga (från och med skrivningen av detta dokument):

| Typ         | Storlek           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB till 64 TB  | 1 200 till 160 000 IOPS |
| Premium SSD  | 32 GB till 32 TB | 12 till 15 000 IOPS     |
| Standard SSD | 32 GB till 32 TB | 12 till 2 000 IOPS      |

Blob-lagring ger den största volymen lagringsutrymme på Azure. Förutom lagringsstorlek erbjuder Azure både hanterad och ohanterad lagring. Med hanterad lagring tar Azure hand om att hantera underliggande lagringskonton. Med ohanterat lagringsutrymme tar användaren ansvar för att konfigurera Azure-lagringskonton av lämplig storlek för att uppfylla lagringskraven.

## <a name="next-steps"></a>Nästa steg

- [Stordatormigrering](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Stordatorsåterhosting på virtuella Azure-datorer](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Flytta stordatorberäkning till Azure](mainframe-compute-Azure.md)
- [Bestämma när Azure Blobbar, Azure-filer eller Azure-diskar ska användas](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Standard SSD-hanterade diskar för Azure VM-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM-resurser

- [Parallell Sysplex på IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS och kopplingsmöjligheten: Bortom grunderna](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Skapa nödvändiga användare för en Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - Skapa förekomst, kommando](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale klustrade databaslösning](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government-molnet för stordatorprogram](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft och FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Fler migreringsresurser

- [Azure Virtual Data Center Lyft och Skift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
