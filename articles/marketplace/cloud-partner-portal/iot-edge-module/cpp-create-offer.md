---
title: Skapa ett erbjudande för Azure IoT Edge-modulen | Azure Marketplace
description: Publicera en ny IoT Edge-modul för Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d8f4300f45fc626c104055f987d123301ba8f39d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148068"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Skapa ett nytt IoT Edge modul-erbjudande med Cloud Partner Portal

>[!Important]
>Från och med 13 april 2020 kommer vi att börja flytta hantering av IoT Edge modul-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett IoT Edge modul-erbjudande](https://docs.microsoft.com//azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) för att hantera dina migrerade erbjudanden.

Den här artikeln beskriver hur du skapar och publicerar en IoT Edge-moduls erbjudande post för Azure Marketplace. Varje erbjudande visas som sin egen enhet i Azure Marketplace och är associerad med en eller flera SKU: er.  Ett IoT Edge modul-erbjudande består av följande grupperingar av till gångar och stöd tjänster:

|  **Till gångs grupp**   |  **Beskrivning**  |
|  ---------------   |  ---------------  |
|    SKU: er            |  Den minsta distributions bara enheten för ett erbjudande. Ett enda erbjudande (produkt klass) kan ha flera SKU: er kopplade till erbjudandet. Du kan använda SKU: er för att skilja mellan stödda funktioner och fakturerings modeller. |
|  Marketplace       | Innehåller till gångar och specifikationer för marknadsföring, juridiska och ledande hantering.  <ul><li> Marknadsförings till gångar innehåller namn, beskrivning och logo typer för erbjudandet</li> <li> Juridiska till gångar innehåller en sekretess policy, användnings villkor och annan juridisk dokumentation</li>  <li> Med princip för hantering av lead kan du ange hur du vill hantera leads från slut användar portalen för Azure Marketplace.</li> </ul> |
| Support            | Innehåller support kontakt och princip information |


## <a name="new-offer-form"></a>Nytt erbjudande formulär 

Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/)och välj sedan **+ nytt erbjudande** i den vänstra meny raden. På menyn ny erbjudande väljer du **IoT Edge moduler** för att visa det **nya erbjudande** formuläret och startar processen för att definiera till gångar för ett nytt erbjudande för IoT Edge-modulen. 

![Ny IoT Edge modul erbjuder val av användar gränssnitt](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Nästa steg

På sidan **ny erbjudande** för IoT Edge modulens erbjudande typ finns en uppsättning flikar och formulär fält som du använder för att skapa ett nytt erbjudande. I följande artiklar förklaras hur du använder fliken för att definiera till gångs grupper och stöd tjänster för ditt nya IoT Edge-modul-erbjudande.

- [Fliken Erbjudandeinställningar](./cpp-offer-settings-tab.md)
- [Fliken SKU:er](./cpp-skus-tab.md)
- [Fliken Marketplace](./cpp-marketplace-tab.md)
- [Fliken Stöd](./cpp-support-tab.md)
