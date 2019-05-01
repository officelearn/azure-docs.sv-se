---
title: Publicera erbjudande om Azure-behållare | Azure Marketplace
description: Så här publicerar du ett Azure container-erbjudande.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 9433673e464beb2df74eb4f49851e960d2e7f99c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942651"
---
# <a name="publish-container-offer"></a>Erbjudande om att skapa containrar

 När du har skapat ett nytt erbjudande med den **nytt erbjudande** kan du publicera erbjudandet. Välj **publicera** att starta publiceringsprocessen.

I följande diagram visas de viktigaste stegen i publiceringsprocessen i ett erbjudande ”gå live”.

![Publicera steg för behållaren erbjudande](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publicering av steg

I följande tabell beskrivs varje publiceringen. Det ges även en uppskattad tid att slutföra varje steg.


|  **Publiceringen**           | **Time**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Verifiera förutsättningar         | 15 min   | Ger information och erbjuder inställningarna verifieras.                        |
| Certifiering                  | 1 vecka | Erbjudandet analyseras av Azure-certifiering-teamet. Erbjudandet genomsöks efter virus, skadlig kod, säkerhet och säkerhetsproblem. Erbjudandet kontrolleras för att se att den uppfyller alla urvalskriterier. Mer information finns i [krav](./cpp-prerequisites.md) och [förbereda dina tekniska resurser](./cpp-create-technical-assets.md). Feedbacks tillhandahålls om ett problem hittas. |
| Paketering | 1 timme  | Erbjudandets tekniska resurser som är packade för kundens användning och lead-system har konfigurerats och konfigurera. |
|  Utgivaren signering             |  -        | Sista publisher granska och bekräfta innan erbjudandet lanseras. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i erbjudandet information steg) för att kontrollera att den uppfyller dina krav.  Välj **Go Live** så att ditt erbjudande kan flytta till nästa steg. |
| Paketering                 | 1 timme | Klar erbjudandet replikeras i regionerna och marketplace produktionssystem. | 
| Live                           | fyra dagar |Erbjudandet är publicerat, replikeras till de nödvändiga regionerna och blir tillgängliga för allmänheten. |

Tillåt för upp till 10 arbetsdagar att slutföra publiceringsprocessen och erbjudandet släpps. När du har slutfört publiceringsprocessen erbjudandet behållaren visas i den [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Nästa steg

[Uppdatera en befintlig behållare erbjudandet på Azure Marketplace](./cpp-update-existing-offer.md)
