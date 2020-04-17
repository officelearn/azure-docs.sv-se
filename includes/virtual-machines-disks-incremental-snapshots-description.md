---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 83c391c0d92f8d4a0ed4b44bc3a90273db51b412
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539574"
---
Inkrementella ögonblicksbilder är tidssäkerhetskopior för hanterade diskar som, när de tas, endast består av alla ändringar sedan den senaste ögonblicksbilden. När du försöker hämta eller på annat sätt använda en inkrementell ögonblicksbild används den fullständiga virtuella hårddisken. Den här nya funktionen för hanterade diskögonblicksbilder kan göra det möjligt för dem att vara mer kostnadseffektiva, eftersom du inte behöver lagra hela disken med varje enskild ögonblicksbild om du inte väljer att lagra hela disken med varje enskild ögonblicksbild. Precis som vanliga ögonblicksbilder kan inkrementella ögonblicksbilder användas för att antingen skapa en fullständig hanterad disk eller för att skapa en vanlig ögonblicksbild.

Det finns några skillnader mellan en inkrementell ögonblicksbild och en vanlig ögonblicksbild. Inkrementella ögonblicksbilder använder alltid standard-hårddiskar lagring, oavsett lagringstypen på disken, medan vanliga ögonblicksbilder kan använda premium SSD.Inkrementell snapshots will always use standard HDDs storage, irrespective of the storage type of the disk, medan regelbundna ögonblicksbilder kan använda premium SSD.Inkrementell snapshots will always use standard HDDs storage, irrespective of the storage type of the disk, medan regelbundna ögonblicksbilder kan använda premium SSD Om du använder vanliga ögonblicksbilder på Premium Storage för att skala upp vm-distributioner rekommenderar vi att du använder anpassade avbildningar på standardlagring i [det delade avbildningsgalleriet](../articles/virtual-machines/linux/shared-image-galleries.md). Det kommer att hjälpa dig att uppnå en mer massiv skala med lägre kostnad. Dessutom kan inkrementella ögonblicksbilder potentiellt ge bättre tillförlitlighet med [zonupptraklig lagring](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Om ZRS är tillgängligt i det valda området används ZRS automatiskt i en inkrementell ögonblicksbild. Om ZRS inte är tillgängligt i regionen, kommer ögonblicksbilden som standard att [lokalt redundant lagring](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Du kan åsidosätta detta beteende och välja en manuellt, men vi rekommenderar inte det.

Inkrementella ögonblicksbilder erbjuder också en differentiell funktion, endast tillgänglig för hanterade diskar. De gör att du kan få ändringarna mellan två inkrementella ögonblicksbilder av samma hanterade diskar, ner till blocknivå. Du kan använda den här funktionen för att minska dataavtrycket när du kopierar ögonblicksbilder mellan regioner.  Du kan till exempel hämta den första inkrementella ögonblicksbilden som en basblob i en annan region. För de efterföljande inkrementella ögonblicksbilderna kan du bara kopiera ändringarna sedan den senaste ögonblicksbilden till basbloben. När du har kopierat ändringarna kan du ta ögonblicksbilder på basbloben som representerar din tidpunkts säkerhetskopiering av disken i en annan region. Du kan återställa disken antingen från basbloben eller från en ögonblicksbild på basbloben i en annan region.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagram som visar inkrementella ögonblicksbilder som kopierats mellan regioner. Ögonblicksbilder gör olika API-anrop tills sidblobar bildas så småningom per varje ögonblicksbild.":::

Du kan se den använda storleken på dina ögonblicksbilder genom att titta på [Azure-användningsrapporten](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). Om till exempel den använda datastorleken för en ögonblicksbild **daily** är 10 GiB visas 10 GiB/(31 dagar) = 0,3226 som förbrukad kvantitet.
