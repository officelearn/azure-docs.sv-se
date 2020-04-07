---
title: Skapa ett Azure IoT Edge-modulerbjudande | Azure Marketplace
description: Så här publicerar du en ny IoT Edge-modul för Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 112c2ba831ab27faaca755079e9d20e2aebfcf9f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744396"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Skapa ett nytt IoT Edge-modulerbjudande med Cloud Partner Portal

>[!Important]
>Från och med den 30 mars 2020 börjar vi flytta hanteringen av dina IoT Edge-modulerbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett IoT Edge-modulerbjudande](https://aka.ms/AzureCreateIoT) för att hantera dina migrerade erbjudanden.

I den här artikeln beskrivs hur du skapar och publicerar en IoT Edge-modul som erbjuder posten för Azure Marketplace. Varje erbjudande visas som sin egen enhet på Azure Marketplace och är associerat med en eller flera SKU:er.  Ett IoT Edge-modulerbjudande består av följande grupperingar av tillgångar och stödtjänster:

|  **Tillgångsgrupp**   |  **Beskrivning**  |
|  ---------------   |  ---------------  |
|    SKU: er            |  Den minsta deployable enheten i ett erbjudande. Ett enda erbjudande (produktklass) kan ha flera SKU:er kopplade till erbjudandet. Du kan använda SKU:er för att skilja mellan funktioner som stöds och faktureringsmodeller. |
|  Marketplace       | Innehåller marknadsförings-, legal- och lead management-tillgångar och specifikationer.  <ul><li> Marknadsföringstillgångar inkluderar erbjudandenamn, beskrivning och logotyper</li> <li> Juridiska tillgångar inkluderar en sekretesspolicy, användarvillkor och annan juridisk dokumentation</li>  <li> Med leadhanteringsprincipen kan du ange hur leads ska hanteras från slutanvändarens portal på Azure Marketplace.</li> </ul> |
| Support            | Innehåller supportkontakt- och policyinformation |


## <a name="new-offer-form"></a>Formulär för nytt erbjudande 

Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/)och välj sedan + Nytt **erbjudande** i den vänstra menyraden. På menyn Nytt erbjudande väljer du **IoT Edge Modules** för att visa formuläret **Nytt erbjudande** och startar processen med att definiera tillgångar för ett nytt ioT Edge Module-erbjudande. 

![Ny IoT Edge-modul erbjuder val av användargränssnitt](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Nästa steg

På sidan **Nytt erbjudande** för IoT Edge-modulens erbjudandetyp finns en uppsättning flikar och formulärfält som du ska använda för att skapa ett nytt erbjudande. Var och en av följande artiklar förklarar hur du använder fliken för att definiera tillgångsgrupper och stödtjänster för ditt nya IoT Edge-modulerbjudande.

- [fliken Erbjudandeinställningar](./cpp-offer-settings-tab.md)
- [fliken SKU:er](./cpp-skus-tab.md)
- [fliken Marketplace](./cpp-marketplace-tab.md)
- [fliken Stöd](./cpp-support-tab.md)
