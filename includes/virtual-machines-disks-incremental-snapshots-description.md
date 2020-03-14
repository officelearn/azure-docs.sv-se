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
ms.openlocfilehash: 1f094cd78e6708fec4bda1e5510379b11df14dcd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299213"
---
Stegvisa ögonblicks bilder är tidpunkter för säkerhets kopiering av hanterade diskar som, när de tas, endast består av alla ändringar sedan den senaste ögonblicks bilden. När du försöker hämta eller på annat sätt använda en stegvis ögonblicks bild, används hela den virtuella hård disken. Den här nya funktionen för ögonblicks bilder av hanterade diskar kan eventuellt vara mer kostnads effektivt, eftersom du inte behöver lagra hela disken med varje enskild ögonblicks bild, om du inte väljer att göra det. Precis som vanliga ögonblicks bilder kan stegvisa ögonblicks bilder användas för att antingen skapa en fullständig hanterad disk eller för att göra en vanlig ögonblicks bild.

Det finns några skillnader mellan en stegvis ögonblicks bild och en vanlig ögonblicks bild. Stegvisa ögonblicks bilder använder alltid standard lagring av hård diskar, oavsett diskens lagrings typ, medan vanliga ögonblicks bilder kan använda Premium-SSD. Om du använder vanliga ögonblicks bilder på Premium Storage för att skala upp VM-distributioner rekommenderar vi att du använder anpassade avbildningar på standard lagring i det [delade avbildnings galleriet](../articles/virtual-machines/linux/shared-image-galleries.md). Det hjälper dig att få en mer enorm skala med lägre kostnad. Dessutom kan stegvisa ögonblicks bilder erbjuda bättre tillförlitlighet med [zon redundant lagring](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Om ZRS är tillgängligt i den valda regionen kommer en stegvis ögonblicks bild att använda ZRS automatiskt. Om ZRS inte är tillgängligt i regionen kommer ögonblicks bilden att vara [Lokalt Redundant lagring](../articles/storage/common/storage-redundancy-lrs.md) (LRS) som standard. Du kan åsidosätta det här beteendet och välja ett manuellt, men vi rekommenderar inte detta.

Stegvisa ögonblicks bilder erbjuder också en differentiell funktion som bara är tillgänglig för hanterade diskar. De gör att du kan hämta ändringarna mellan två stegvisa ögonblicks bilder av samma hanterade diskar, ned till block nivån. Du kan använda den här funktionen för att minska dina data när du kopierar ögonblicks bilder över flera regioner.
