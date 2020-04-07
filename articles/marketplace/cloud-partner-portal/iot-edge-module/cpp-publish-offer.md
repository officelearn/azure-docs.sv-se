---
title: Publicera Azure IoT Edge-modulerbjudande | Azure Marketplace
description: Så här publicerar du ett IoT Edge-modulerbjudande.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 2220352f817b488b3992b88920428063edde006e
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745009"
---
# <a name="publish-iot-edge-module-offer"></a>Publicera erbjudande för IoT Edge-moduler

>[!Important]
>Från och med den 30 mars 2020 börjar vi flytta hanteringen av dina IoT Edge-modulerbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett IoT Edge-modulerbjudande](https://aka.ms/AzureCreateIoT) för att hantera dina migrerade erbjudanden.

 När du har skapat ett nytt erbjudande genom att ange informationen på sidan **Nytt erbjudande** kan du publicera erbjudandet. Välj **Publicera** om du vill starta publiceringsprocessen.

Följande diagram visar de viktigaste stegen i publiceringsprocessen för ett erbjudande om att "publicera".

![Publiceringssteg för IoT Edge-modulerbjudande](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publiceringssteg

I följande tabell beskrivs varje publiceringssteg, med en tidsuppskattning (maximal) för att slutföra varje steg.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publiceringssteg**           | **Tid**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validera förutsättningar         | 15 min   | Erbjudandeinformation och erbjudandeinställningar valideras.                        |
| Certifiering                  | 2 veckor | Erbjudandet analyseras av Azure Certification Team. Det här steget kommer att utföra genomsökningar efter virus, skadlig kod, säkerhetsefterlevnad och säkerhetsproblem. Det kommer också att kontrollera att detta IoT Edge-modulerbjudande uppfyller alla [behörighetskriterier](./cpp-prerequisites.md) (se förutsättningar och [förbereda dina tekniska tillgångar).](./cpp-create-technical-assets.md) Feedback ges om ett problem hittas. |
| Paketering | 1 timme  | Erbjudandets tekniska tillgångar är paketerade för kundanvändning och leadsystemen är konfigurerade och konfigurerade. |
|  Signering av publisher             |  -        | Slutlig utgivare granskning och bekräftelse innan erbjudandet går live. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i stegen för erbjudandeinformation) för att kontrollera att det uppfyller alla dina krav.  Välj **Gå live** så att ditt erbjudande kan gå vidare till nästa steg. |
| Paketering                 | 1 timme | Slutfört erbjudande replikeras i marknadsplatsproduktionssystem och regioner. | 
| Live                           | 4 dagar |Erbjudandet frisläpps, replikeras till de regioner som krävs och görs tillgängligt för allmänheten. |

Tillåt upp till 10 arbetsdagar för publiceringsprocessen att slutföras och erbjudandet släpps. När du är klar med publiceringsprocessen visas erbjudandet om IoT Edge-modul på [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt IoT Edge-modulerbjudande på Azure Marketplace](./cpp-update-existing-offer.md)
