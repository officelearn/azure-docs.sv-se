---
title: Skapa erbjudande för virtuell dator i Azure Marketplace | Microsoft Docs
description: Visar de steg som krävs för att skapa en ny virtuell dator (VM) erbjuder för Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 0a875b69a10f6305b4ffe32b63d3db5b508faf01
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49640095"
---
# <a name="create-virtual-machine-offer"></a>Skapa erbjudande för virtuell dator

Det här avsnittet visas de steg som krävs för att skapa en ny virtuell dator (VM) erbjudandet begäran för Azure Marketplace.  Varje erbjudande visas som en egen enhet på Azure Marketplace och är associerad med en eller flera SKU: er.  Erbjudande om en virtuell dator består av följande grupper i tillgångar och stödtjänster: 

![Tillgångar till en virtuell dator](./media/publishvm_002.png)

Där:

|  **Grupp**   |  **Beskrivning**  |
|  ---------------   |  ---------------  |
|    SKU:er            |  Den minsta köpbara enheten i ett erbjudande. Ett erbjudande (produkten klassen) kan ha flera SKU: er som är associerade med den att skilja mellan funktioner som stöds och VM-avbildningstyper faktureringsmodeller. |
|  Marketplace       | Innehåller marknadsföring, juridiska och inverka management tillgångar och specifikationer.  <ul><li> Marknadsföring-material inkluderar erbjudandenamn, beskrivning och logotyper</li> <li> Juridiska tillgångar är en sekretesspolicy, användningsvillkor och andra juridisk dokumentation</li>  <li> Lead princip kan du ange hur du hanterar leads från Azure Marketplace slutanvändarportal.</li> </ul> |
| Support            | Innehåller information om support kontakta och princip |
| Test Drive         | Definierar tillgångar som gör det möjligt för användare att testa ditt erbjudande innan de köper det |
|  |  |


## <a name="new-offer-form"></a>Nytt erbjudande formulär

När din logga in på den [Cloud Partner Portal](http://cloudpartner.azure.com/), klickar du på den **+ nytt erbjudande** objekt i den vänstra menyraden. I den resulterande menyn, klickar du på **virtuella datorer** att visa den **nytt erbjudande** formar och starta processen med att definiera tillgångar till en ny virtuell dator. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Nytt erbjudande användaren gränssnittet val av virtuell dator](./media/publishvm_003.png)

> [!WARNING]
> Om den **virtuella datorer** alternativet inte visas eller inte är aktiverat och sedan ditt konto har inte behörighet att skapa den här erbjudandetypen.  Kontrollera att du uppfyller alla de [krav](./cpp-prerequisites.md) för den här erbjudandetypen, inklusive registrering för ett utvecklarkonto.


## <a name="next-steps"></a>Nästa steg

I efterföljande avsnitt i det här avsnittet spegling av flikarna i den **nytt erbjudande** (för en typ av erbjudande virtuell dator).  Varje artikeln förklarar hur du använder fliken associerade definiera tillgångar och stödtjänster till din nya VM-erbjudandet.

- [fliken Erbjudandeinställningar](./cpp-offer-settings-tab.md)
- [fliken SKU:er](./cpp-skus-tab.md)
- [Test Drive-fliken](./cpp-test-drive-tab.md)
- [fliken Marketplace](./cpp-marketplace-tab.md)
- [fliken Stöd](./cpp-support-tab.md)
