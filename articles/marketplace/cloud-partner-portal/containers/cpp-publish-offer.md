---
title: Erbjudandet publicera avbildningserbjudande för Azure Containers | Azure Marketplace
description: Så här publicerar du ett Azure-behållarerbjudande.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 8003bf1cb7905d5decf8a6267ea397f1b2c01222
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270170"
---
# <a name="publish-container-offer"></a>Erbjudande om att skapa containrar

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Container-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett Azure Container-erbjudande](https://aka.ms/CreateContainerOffer) för att hantera dina migrerade erbjudanden.

 När du har skapat ett nytt erbjudande på sidan **Nytt erbjudande** kan du publicera erbjudandet. Välj **Publicera** om du vill starta publiceringsprocessen.

Följande diagram visar de viktigaste stegen i publiceringsprocessen för ett erbjudande om att "publicera".

![Publiceringssteg för behållarerbjudande](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publiceringssteg

I följande tabell beskrivs varje publiceringssteg. En beräknad tid att avsluta varje steg ges också.


|  **Publiceringssteg**           | **Tid**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validera förutsättningar         | 15 min   | Erbjudandeinformation och erbjudandeinställningar valideras.                        |
| Certifiering                  | 1 vecka | Erbjudandet analyseras av Azure Certification Team. Erbjudandet genomsöks efter virus, skadlig kod, säkerhetskrav och säkerhetsproblem. Erbjudandet kontrolleras för att se till att det uppfyller alla villkor för berättigande. Mer information finns i [förutsättningar](./cpp-prerequisites.md) och [förbereda dina tekniska tillgångar](./cpp-create-technical-assets.md). Feedback ges om ett problem hittas. |
| Paketering | 1 timme  | Erbjudandets tekniska tillgångar är paketerade för kundanvändning och leadsystemen är konfigurerade och konfigurerade. |
|  Signering av utgivare             |  -        | Slutlig utgivare granskning och bekräftelse innan erbjudandet går live. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i stegen för erbjudandeinformation) för att kontrollera att det uppfyller alla dina krav.  Välj **Gå live** så att ditt erbjudande kan gå vidare till nästa steg. |
| Paketering                 | 1 timme | Det färdiga erbjudandet replikeras i marknadsplatsproduktionssystem och regioner. | 
| Live                           | 4 dagar |Erbjudandet frisläpps, replikeras till de regioner som krävs och görs tillgängligt för allmänheten. |

Tillåt upp till 10 arbetsdagar för publiceringsprocessen att slutföras och erbjudandet släpps. När du är klar med publiceringsprocessen visas ditt behållarerbjudande på [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Nästa steg

[Uppdatera ett befintligt behållarerbjudande på Azure Marketplace](./cpp-update-existing-offer.md)
