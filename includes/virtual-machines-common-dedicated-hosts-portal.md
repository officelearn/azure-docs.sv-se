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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128622"
---
## <a name="limitations"></a>Begränsningar

- Skalningsuppsättningar för virtuella datorer stöds för närvarande inte på dedikerade värdar.
- Storleken och maskinvarutyperna som är tillgängliga för dedikerade värdar varierar mellan olika regioner. Läs [värdprissidan](https://aka.ms/ADHPricing) om du vill veta mer.

## <a name="create-a-host-group"></a>Skapa en värdgrupp

En **värdgrupp** är en ny resurs som representerar en samling dedikerade värdar. Du skapar en värdgrupp i en region och en tillgänglighetszon och lägger till värdar i den. När du planerar för hög tillgänglighet finns det ytterligare alternativ. Du kan använda ett eller båda av följande alternativ med dina dedikerade värdar: 
- Sträcker sig över flera tillgänglighetszoner. I det här fallet måste du ha en värdgrupp i var och en av de zoner som du vill använda.
- Span över flera feldomäner som mappas till fysiska rack. 
 
I båda fallen måste du ange antalet feldomäner för värdgruppen. Om du inte vill spänna över feldomäner i gruppen använder du ett antal feldomäner på 1. 

Du kan också välja att använda både tillgänglighetszoner och feldomäner. 

I det här exemplet skapar vi en värdgrupp med 1 tillgänglighetszon och 2 feldomäner. 


1. Öppna [Azure-portalen](https://portal.azure.com).
1. Välj **Skapa en resurs** i det övre vänstra hörnet.
1. Sök efter **värdgrupp** och välj sedan **Värdgrupper** från resultaten.
1. På sidan **Värdgrupper** väljer du **Skapa**.
1. Välj den prenumeration som du vill använda och välj sedan **Skapa ny** för att skapa en ny resursgrupp.
1. Skriv *myDedicatedHostsRG* som **namn** och välj sedan **OK**.
1. Skriv *myHostGroup*för **värdgruppsnamn**.
1. För **Plats**väljer du **Östra USA**.
1. För **tillgänglighetszon**väljer du **1**.
1. För **antal feldomäner**väljer du **2**.
1. Välj **Granska + skapa** och vänta sedan på validering.
1. När du ser **meddelandet Validering har du** valt **Skapa** för att skapa värdgruppen.

Det bör bara ta en stund att skapa värdgruppen.

## <a name="create-a-dedicated-host"></a>Skapa en dedikerad värd

Skapa nu en dedikerad värd i värdgruppen. Förutom ett namn för värden måste du ange SKU för värden. Host SKU fångar den VM-serien som stöds samt maskinvarugenereringen för din dedikerade värd.

Mer information om värdsskrona och priser finns i [Azure Dedicated Host pricing](https://aka.ms/ADHPricing).

Om du anger ett antal feldomäner för värdgruppen blir du ombedd att ange feldomänen för värden.  

1. Välj **Skapa en resurs** i det övre vänstra hörnet.
1. Sök efter **dedikerad värd** och välj sedan **Dedikerade värdar** från resultaten.
1. På sidan **Dedikerade värdar** väljer du **Skapa**.
1. Välj den prenumeration som du vill använda.
1. Välj *myDedicatedHostsRG* som **resursgrupp**.
1. I **Instansinformation**skriver du *myHost* för **namnet** och väljer *östra USA* för platsen.
1. I **maskinvaruprofil**väljer du *Standard Es3 - Typ 1* för **storleksfamiljen,** väljer *myHostGroup* för **värdgruppen** och väljer sedan *1* för **feldomänen**. Lämna standardvärdena för resten av fälten.
1. När du är klar väljer du **Granska + skapa** och väntar på validering.
1. När du ser **meddelandet Validering har du** valt **Skapa** för att skapa värden.


