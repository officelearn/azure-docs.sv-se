---
title: Skapa en Azure HPC-cache
description: Så här skapar du en Azure HPC cache-instans
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582182"
---
# <a name="plan-the-aggregated-namespace"></a>Planera det aggregerade namnområdet

Med Azure HPC cache får klienterna åtkomst till en mängd olika lagrings system via ett virtuellt namn område som döljer information om Server dels lagrings systemet.

När du lägger till ett lagrings mål anger du sökvägen till klientens fil Sök väg. Klient datorer monterar den här fil Sök vägen och kan göra fil läsnings begär anden till cacheminnet i stället för att montera lagrings systemet direkt.

Eftersom Azure HPC cache hanterar det här virtuella fil systemet kan du ändra lagrings målet utan att ändra sökvägen till klienten. Du kan till exempel ersätta ett maskin varu lagrings system med moln lagring utan att behöva skriva över klientbaserade procedurer.

## <a name="aggregated-namespace-example"></a>Exempel på sammanställd namnrymd

Planera det sammansatta namn området så att klient datorerna enkelt kan komma åt den information de behöver, och så att administratörer och arbets flödes tekniker enkelt kan särskilja Sök vägarna.

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

| Server dels lagrings system <br/> (NFS-filsökväg eller BLOB-behållare) | Sökväg för virtuell namnrymd |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source               |

Ett NFS-lagrings mål kan ha flera sökvägar för virtuella namn områden, så länge var och en refererar till en unik export Sök väg.

Eftersom NFS-källans sökvägar är under kataloger för samma export, måste du definiera flera namn områdes Sök vägar från samma lagrings mål.

| Värdnamn för lagrings mål  | Sökväg till NFS-export      | Sökväg till under Katalog | Sökväg till namnrymd    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP-adress eller värdnamn* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *IP-adress eller värdnamn* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Ett klient program kan montera cachen och enkelt komma åt de sammansatta Sök vägarna för namn områden ``/source``, ``/templates/sku798``och ``/templates/sku980``.

## <a name="next-steps"></a>Nästa steg

När du har bestämt dig för att konfigurera det virtuella fil systemet [skapar du lagrings mål](hpc-cache-add-storage.md) för att mappa Server dels lagringen till dina virtuella sökvägar till klienten.
