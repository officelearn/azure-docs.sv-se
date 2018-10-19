---
title: Skapa ett erbjudande för Azure IoT Edge-modulen | Microsoft Docs
description: Hur du publicerar en ny IoT Edge-modul för Marketplace.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: d7943119ed29e03afb6b089a913d4ba2baddc166
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431292"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Skapa ett nytt erbjudande för IoT Edge-modulen med partnerportalen i molnet

Den här artikeln beskriver hur du skapar och publicerar något erbjudande IoT Edge-modulen för Azure Marketplace. Varje erbjudande visas som en egen enhet på Azure Marketplace och är associerad med en eller flera SKU: er.  Ett erbjudande för IoT Edge-modulen består av följande grupper i tillgångar och stödtjänster:

|  **Grupp**   |  **Beskrivning**  |
|  ---------------   |  ---------------  |
|    SKU:er            |  Den minsta distribuerbar enheten i ett erbjudande. Ett erbjudande (produkten klassen) kan ha flera SKU: er som är associerade med erbjudandet. Du kan använda SKU: er för att skilja mellan funktioner som stöds och faktureringsmodeller. |
|  Marketplace       | Innehåller marknadsföring, juridiska och inverka management tillgångar och specifikationer.  <ul><li> Marknadsföring-material inkluderar erbjudandenamn, beskrivning och logotyper</li> <li> Juridiska tillgångar är en sekretesspolicy, användningsvillkor och andra juridisk dokumentation</li>  <li> Lead princip kan du ange hur du hanterar leads från Azure Marketplace-slutanvändarportal.</li> </ul> |
| Support            | Innehåller information om support kontakta och princip |


## <a name="new-offer-form"></a>Nytt erbjudande formulär 

Logga in på den [Cloud Partner Portal](http://cloudpartner.azure.com/), och välj sedan **+ nytt erbjudande** på den vänstra menyraden. Välj på menyn ny erbjuder **IoT Edge-moduler** att visa den **nytt erbjudande** formar och starta processen med att definiera tillgångar till en ny ioT Edge-modul. 

![Nya IoT Edge-modul erbjuder användarens gränssnittsval](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Nästa steg

Den **nytt erbjudande** sidan för typ av erbjudande IoT Edge-modulen innehåller en uppsättning av flikar och formulärfält som du använder för att skapa ett nytt erbjudande. Var och en av följande artiklar beskriver hur du använder fliken Definiera tillgångar och stödtjänster till din nya erbjudandet för IoT Edge-modulen.

- [Fliken Inställningar för erbjudandet](./cpp-offer-settings-tab.md)
- [Fliken SKU: er](./cpp-skus-tab.md)
- [Marketplace-fliken](./cpp-marketplace-tab.md)
- [Fliken stöd](./cpp-support-tab.md)
