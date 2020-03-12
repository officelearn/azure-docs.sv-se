---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 427117fe47294a1db1fa8d3fa1e46ee1efb91b4d
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128622"
---
## <a name="limitations"></a>Begränsningar

- Skalnings uppsättningar för virtuella datorer stöds för närvarande inte på dedikerade värdar.
- De storlekar och maskin varu typer som är tillgängliga för dedikerade värdar varierar beroende på region. Mer information hittar du på [prissättnings sidan](https://aka.ms/ADHPricing) för värden.

## <a name="create-a-host-group"></a>Skapa en värdgrupp

En **värd grupp** är en ny resurs som representerar en samling dedikerade värdar. Du skapar en värd grupp i en region och en tillgänglighets zon och lägger till värdar i den. När du planerar för hög tillgänglighet finns det ytterligare alternativ. Du kan använda ett eller båda av följande alternativ med dina dedikerade värdar: 
- Sträck över flera tillgänglighets zoner. I så fall måste du ha en värd grupp i var och en av de zoner som du vill använda.
- Sträck över flera fel domäner som är mappade till fysiska rack. 
 
I båda fallen måste du ange antalet fel domäner för värd gruppen. Om du inte vill spänna över fel domäner i gruppen använder du antalet fel domäner 1. 

Du kan också välja att använda både tillgänglighets zoner och fel domäner. 

I det här exemplet ska vi skapa en värd grupp med hjälp av 1 tillgänglighets zon och 2 fel domäner. 


1. Öppna Azure [Portal](https://portal.azure.com).
1. Välj **skapa en resurs** i det övre vänstra hörnet.
1. Sök efter **värd grupp** och välj sedan **värd grupper** från resultaten.
1. På sidan **värd grupper** väljer du **skapa**.
1. Välj den prenumeration som du vill använda och välj sedan **Skapa ny** för att skapa en ny resurs grupp.
1. Skriv *myDedicatedHostsRG* som **namn** och välj sedan **OK**.
1. För **värd grupp namn**skriver du *myHostGroup*.
1. För **plats**väljer du **östra USA**.
1. För **tillgänglighets zon**väljer du **1**.
1. För **antal fel domäner**väljer du **2**.
1. Välj **Granska + skapa** och vänta sedan på verifiering.
1. När du ser meddelandet **valideringen som skickats** väljer du **skapa** för att skapa värd gruppen.

Det bör bara ta en stund att skapa värd gruppen.

## <a name="create-a-dedicated-host"></a>Skapa en dedikerad värd

Skapa nu en dedikerad värd i värd gruppen. Förutom ett namn för värden måste du ange SKU för värden. Värd-SKU: n samlar in de VM-serier som stöds samt maskin varu generering för den dedikerade värden.

Mer information om värd-SKU: er och priser finns i [prissättning för Azure-dedikerad värd](https://aka.ms/ADHPricing).

Om du anger ett fel domän antal för värd gruppen uppmanas du att ange fel domänen för värden.  

1. Välj **skapa en resurs** i det övre vänstra hörnet.
1. Sök efter **dedikerad värd** och välj sedan **dedikerade värdar** från resultaten.
1. På sidan **dedikerade värdar** väljer du **skapa**.
1. Välj den prenumeration som du vill använda.
1. Välj *myDedicatedHostsRG* som **resurs grupp**.
1. I **instans information**skriver du in *värd* för **namnet** och väljer *östra USA* för platsen.
1. I **maskin varu profil**väljer du *standard Es3 Family – typ 1* för **storleks familjen**, väljer *myHostGroup* för **värd gruppen** och väljer sedan *1* för **fel domänen**. Lämna standardvärdena för resten av fälten.
1. När du är färdig väljer du **Granska + skapa** och väntar på verifiering.
1. När du ser meddelandet **valideringen som skickats** väljer du **skapa** för att skapa värden.


