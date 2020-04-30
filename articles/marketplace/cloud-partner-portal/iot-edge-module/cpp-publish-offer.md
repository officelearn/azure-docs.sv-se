---
title: Erbjudande om att publicera Azure IoT Edge modul | Azure Marketplace
description: Så här publicerar du ett erbjudande för IoT Edge-modulen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ef73956b1b0d3e7bed6e91cde0b92bcc3e432795
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141847"
---
# <a name="publish-iot-edge-module-offer"></a>Publicera erbjudande för IoT Edge-moduler

>[!Important]
>Från och med 13 april 2020 kommer vi att börja flytta hantering av IoT Edge modul-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett IoT Edge modul-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) för att hantera dina migrerade erbjudanden.

 När du har skapat ett nytt erbjudande genom att ange informationen på sidan **ny erbjudande** kan du publicera erbjudandet. Välj **publicera** för att starta publicerings processen.

Följande diagram visar de huvudsakliga stegen i publicerings processen för ett erbjudande till "Go Live".

![Publicerings steg för IoT Edge modul-erbjudande](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publicerings steg

I följande tabell beskrivs varje publicerings steg, med en tids uppskattning (maximum) för att slutföra varje steg.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publicerings steg**           | **Tid**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Verifiera krav         | 15 min   | Information om erbjudandet och erbjudande inställningarna verifieras.                        |
| Certifiering                  | 2 veckor | Erbjudandet analyseras av Azure-certifierings teamet. I det här steget utförs genomsökningar efter virus, skadlig kod, säkerhets krav och säkerhets problem. Det kontrollerar också att det här IoT Edge modul-erbjudandet uppfyller alla villkor för berättigande (se [krav](./cpp-prerequisites.md) och [förbereda dina tekniska till gångar](./cpp-create-technical-assets.md)). Feedback ges om ett problem påträffas. |
| Paketering | 1 timme  | Erbjudandets tekniska till gångar är paketerade för kund användning och lead-systemen konfigureras och konfigureras. |
|  Utgivarens signering             |  -        | Slutlig utgivar granskning och bekräftelse innan erbjudandet går live. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i stegen för att erbjuda information) för att kontrol lera att det uppfyller alla krav.  Välj **Go Live** så att ditt erbjudande kan gå vidare till nästa steg. |
| Paketering                 | 1 timme | Det slutgiltiga erbjudandet replikeras i produktions system och regioner i Marketplace. | 
| Live                           | 4 dagar |Erbjudandet släpps, replikeras till de regioner som krävs och görs tillgängliga för allmänheten. |

Tillåt upp till 10 arbets dagar för publicerings processen att slutföras och erbjudandet släpps. När du har slutfört publicerings processen visas IoT Edge-modulens erbjudande i [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt IoT Edge modul-erbjudande på Azure Marketplace](./cpp-update-existing-offer.md)
