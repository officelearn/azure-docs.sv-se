---
title: Skapa ett erbjudande för Azure-behållare | Microsoft Docs
description: Så här publicerar du ett nytt erbjudande för behållaren för Marketplace...
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 3b2a34a5ed2c6b57bfce0b9a4cf35cdf14cff9c9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473046"
---
# <a name="create-a-new-container-offer-with-the-cloud-partner-portal"></a>Skapa ett nytt erbjudande för behållare med partnerportalen i molnet

Den här artikeln beskriver hur du skapar och publicerar en post för container-erbjudande för Azure Marketplace. Varje erbjudande visas som en egen enhet på Azure Marketplace och är associerad med en eller flera SKU: er.  Erbjudande om en behållare består av följande grupper i tillgångar och stödtjänster:

|  **Grupp**   |  **Beskrivning**  |
|  ---------------   |  ---------------  |
|    SKU:er            |  Den minsta distribuerbar enheten i ett erbjudande. Ett erbjudande (produkten klassen) kan ha flera SKU: er som är associerade med erbjudandet. Du kan använda SKU: er för att skilja mellan funktioner som stöds och faktureringsmodeller. |
|  Marketplace       | Innehåller marknadsföring, juridiska och inverka management tillgångar och specifikationer.  <ul><li> Marknadsföring-material inkluderar erbjudandenamn, beskrivning och logotyper</li> <li> Juridiska tillgångar är en sekretesspolicy, användningsvillkor och andra juridisk dokumentation</li>  <li> Lead princip kan du ange hur du hanterar leads från Azure Marketplace-slutanvändarportal.</li> </ul> |
| Support            | Innehåller information om support kontakta och princip |


## <a name="new-offer-form"></a>Nytt erbjudande formulär 

Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/), och välj sedan **+ nytt erbjudande** på den vänstra menyraden. På ikonen nytt erbjuda meny, Välj **behållare** att visa den **nytt erbjudande** formar och starta processen med att definiera tillgångar till en ny behållare.

![Välj behållare alternativ för nytt erbjudande](./media/azure-container-offer.png)

## <a name="next-steps"></a>Nästa steg

Den **nytt erbjudande** sidan för typ av erbjudande behållaren innehåller en uppsättning av flikar och formulärfält som du använder för att skapa ett nytt erbjudande. Var och en av följande artiklar beskriver hur du använder fliken Definiera tillgångar och stödtjänster till din nya erbjudandet för behållaren.

- [fliken Erbjudandeinställningar](./cpp-offer-settings-tab.md)
- [fliken SKU:er](./cpp-skus-tab.md)
- [fliken Marketplace](./cpp-marketplace-tab.md)
- [fliken Stöd](./cpp-support-tab.md)
