---
title: Skapa en Azure HPC-cache
description: Så här skapar du en Azure HPC cache-instans
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: c3d14eaefaa1f317cb061273866ffee83747f12b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036850"
---
# <a name="configure-aggregated-namespace"></a>Konfigurera sammanställd namnrymd
<!-- change link in GUI -->

Med Azure HPC cache får klienterna åtkomst till en mängd olika lagrings system via ett virtuellt namn område som döljer information om Server dels lagrings systemet.

När du lägger till ett lagrings mål anger du klient Sök vägen. Klient datorer monterar denna fil Sök väg. Du kan ändra det lagrings mål som är associerat med den sökvägen. Du kan till exempel ersätta ett maskin varu lagrings system med moln lagring utan att behöva skriva över klientbaserade procedurer.

## <a name="aggregated-namespace-example"></a>Exempel på sammanställd namnrymd

Planera det sammansatta namn området så att klient datorerna enkelt kan komma åt den information de behöver, och administratörer och arbets flödes tekniker kan lätt skilja Sök vägarna.

Anta till exempel ett system där en Azure HPC cache-instans används för att bearbeta data som lagras i Azure blob. För analysen krävs mallfiler som lagras i ett lokalt Data Center.

Mal lin data lagras i ett Data Center och den information som krävs för det här jobbet lagras i följande under kataloger:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Data Center Storage-systemet visar dessa exporter: 

    /
    /goldline
    /goldline/templates

De data som ska analyseras har kopierats till en Azure Blob Storage-behållare med namnet "sourcecollection" med hjälp av [CLFSLoad-verktyget](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Överväg att skapa lagrings mål med dessa sökvägar för virtuella namn områden för att tillåta enkel åtkomst via cachen:

| Server delens NFS-sökväg eller BLOB-behållare | Sökväg för virtuell namnrymd |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source               |

Eftersom NFS-källans sökvägar är under kataloger för samma export, måste du definiera flera namn områdes Sök vägar från samma lagrings mål. 

| Värdnamn för lagrings mål  | Sökväg till NFS-export      | Sökväg till under Katalog | Sökväg till namnrymd    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP-adress eller värdnamn* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *IP-adress eller värdnamn* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Ett klient program kan montera cachen och enkelt komma åt de sammanställda namn rymderna/Source,/templates/sku798 och/templates/sku980.

## <a name="next-steps"></a>Nästa steg

När du har bestämt dig för att konfigurera det virtuella fil systemet [skapar du lagrings mål](hpc-cache-add-storage.md) för att mappa Server dels lagringen till dina klientbaserade virtuella fil Sök vägar.
