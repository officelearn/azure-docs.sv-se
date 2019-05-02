---
title: Publicera erbjudande för Azure IoT Edge-modulen | Azure Marketplace
description: Hur du publicerar ett erbjudande för IoT Edge-modulen.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: c853bd3bad9f02f6824c26fb5d18e9e59d921fe8
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942051"
---
# <a name="publish-iot-edge-module-offer"></a>Publicera erbjudande för IoT Edge-moduler

 När du har skapat ett nytt erbjudande genom att tillhandahålla informationen om den **nytt erbjudande** kan du publicera erbjudandet. Välj **publicera** att starta publiceringsprocessen.

I följande diagram visas de viktigaste stegen i publiceringsprocessen i ett erbjudande ”gå live”.

![Publicera anvisningar som beskriver IoT Edge-modul erbjuder](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publicering av steg

I följande tabell beskrivs varje publishing steg, med en Uppskattad tidsåtgång (max) att slutföra varje steg.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Publiceringen**           | **Time**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Verifiera förutsättningar         | 15 min   | Ger information och erbjuder inställningarna verifieras.                        |
| Certifiering                  | 2 veckor | Erbjudandet analyseras av Azure-certifiering-teamet. Det här steget utför genomsökningar efter virus, skadlig kod, säkerhet och säkerhetsproblem. Den kontrollerar också att erbjudandet IoT Edge-modulen uppfyller alla urvalskriterier (se [krav](./cpp-prerequisites.md) och [förbereda dina tekniska resurser](./cpp-create-technical-assets.md)). Feedback tillhandahålls om ett problem hittas. |
| Paketering | 1 timme  | Erbjudandets tekniska resurser som är packade för kundens användning och lead-system har konfigurerats och konfigurera. |
|  Utgivaren signering             |  -        | Sista publisher granska och bekräfta innan erbjudandet lanseras. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i erbjudandet information steg) för att kontrollera att den uppfyller dina krav.  Välj **Go Live** så att ditt erbjudande kan flytta till nästa steg. |
| Paketering                 | 1 timme | Slutförda erbjudandet replikeras i regionerna och marketplace produktionssystem. | 
| Live                           | fyra dagar |Erbjudandet är publicerat, replikeras till de nödvändiga regionerna och blir tillgängliga för allmänheten. |

Tillåt för upp till 10 arbetsdagar att slutföra publiceringsprocessen och erbjudandet släpps. När du har slutfört publiceringsprocessen erbjudandet IoT Edge-modulen visas i den [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Nästa steg

- [Uppdatera en befintlig IoT Edge-modulen erbjudandet på Azure Marketplace](./cpp-update-existing-offer.md)
