---
title: Skapa en Azure HPC-cache
description: Skapa en Azure HPC-cacheinstans
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582182"
---
# <a name="plan-the-aggregated-namespace"></a>Planera det aggregerade namnområdet

Azure HPC Cache tillåter klienter att komma åt en mängd olika lagringssystem via ett virtuellt namnområde som döljer information om backend-lagringssystemet.

När du lägger till ett lagringsmål anger du sökvägen till klienten. Klientdatorer monterar den här filsökvägen och kan göra filläsningsbegäranden till cachen i stället för att montera lagringssystemet direkt.

Eftersom Azure HPC Cache hanterar det här virtuella filsystemet kan du ändra lagringsmålet utan att ändra den klientinriktade sökvägen. Du kan till exempel ersätta ett maskinvarulagringssystem med molnlagring utan att behöva skriva om klientinriktade procedurer.

## <a name="aggregated-namespace-example"></a>Aggregerat namnområdesexempel

Planera det aggregerade namnområdet så att klientdatorer enkelt kan nå den information de behöver och så att administratörer och arbetsflödestekniker enkelt kan skilja på sökvägarna.

Tänk dig till exempel ett system där en Azure HPC-cacheinstans används för att bearbeta data som lagras i Azure Blob. Analysen kräver mallfiler som lagras i ett lokalt datacenter.

Malldata lagras i ett datacenter och informationen som behövs för det här jobbet lagras i dessa underkataloger:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Datacentrets lagringssystem exponerar dessa exporter:

    /
    /goldline
    /goldline/templates

De data som ska analyseras har kopierats till en Azure Blob-lagringsbehållare med namnet "sourcecollection" med hjälp av [verktyget CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Om du vill tillåta enkel åtkomst via cachen bör du överväga att skapa lagringsmål med de här virtuella namnrymdsvägarna:

| Backend lagringssystem <br/> (NFS-filsökväg eller Blob-behållare) | Sökväg till virtuellt namnområde |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798 /goldline/templates/acme2017/sku798 /goldline/templates/acme2017/sku798 /gold     | /mallar/sku798      |
| /goldline/templates/acme2017/sku980     | /mallar/sku980      |
| källasamling                        | /källa/               |

Ett NFS-lagringsmål kan ha flera virtuella namnområdessökvägar, så länge var och en refererar till en unik exportsökväg.

Eftersom NFS-källsökvägarna är underkataloger för samma export måste du definiera flera namnområdessökvägar från samma lagringsmål.

| Värdnamn för lagringsmål  | NFS-exportsökväg      | Underkatalogsökväg | Namnområdessökväg    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP-adress eller värdnamn* | /goldline/mallar  | acme2017/sku798   | /mallar/sku798 |
| *IP-adress eller värdnamn* | /goldline/mallar  | acme2017/sku980   | /mallar/sku980 |

Ett klientprogram kan montera cacheminnet och enkelt komma ``/source`` ``/templates/sku798``åt ``/templates/sku980``de aggregerade namnrymdsfilsökvägarna , och .

## <a name="next-steps"></a>Nästa steg

När du har bestämt hur du konfigurerar det virtuella filsystemet [skapar du lagringsmål](hpc-cache-add-storage.md) för att mappa serverdelslagringen till klientinriktade virtuella filsökvägar.
