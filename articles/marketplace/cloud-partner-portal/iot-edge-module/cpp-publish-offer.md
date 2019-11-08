---
title: Erbjudande om att publicera Azure IoT Edge modul | Azure Marketplace
description: Så här publicerar du ett erbjudande för IoT Edge-modulen.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 2310d7a7cad16009bbb58469190a77eedb0619f8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813836"
---
# <a name="publish-iot-edge-module-offer"></a>Publicera erbjudande för IoT Edge-moduler

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
| Paketering | 1 timme  | Erbjudandets tekniska till gångar är paketerade för kund användning och lead-systemen konfigureras och konfigureras. |
|  Utgivarens signering             |  -        | Slutlig utgivar granskning och bekräftelse innan erbjudandet går live. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i stegen för att erbjuda information) för att kontrol lera att det uppfyller alla krav.  Välj **Go Live** så att ditt erbjudande kan gå vidare till nästa steg. |
| Paketering                 | 1 timme | Det slutgiltiga erbjudandet replikeras i produktions system och regioner i Marketplace. | 
| Live                           | 4 dagar |Erbjudandet släpps, replikeras till de regioner som krävs och görs tillgängliga för allmänheten. |

Tillåt upp till 10 arbets dagar för publicerings processen att slutföras och erbjudandet släpps. När du har slutfört publicerings processen visas IoT Edge-modulens erbjudande i [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt IoT Edge modul-erbjudande på Azure Marketplace](./cpp-update-existing-offer.md)
