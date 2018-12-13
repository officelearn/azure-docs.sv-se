---
title: Publicera erbjudande för Azure-program | Microsoft Docs
description: Beskriver processen och stegen för att publicera ett erbjudande för Azure-program på Azure Marketplace.
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
ms.date: 12/05/2018
ms.author: pbutlerm
ms.openlocfilehash: fff751d531864faee7bd234ddcf543ae2992a617
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197239"
---
# <a name="publish-azure-application-offer"></a>Publicera erbjudande för Azure-program

När du har skapat ett nytt erbjudande genom att tillhandahålla informationen om den **nytt erbjudande** kan du publicera erbjudandet. Välj **publicera** att starta publiceringsprocessen.

I följande diagram visas de viktigaste stegen i publiceringsprocessen i ett erbjudande ”gå live”.

  ![Publicera erbjudande-steg](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publicering av steg

I följande tabell beskrivs varje publishing steg, med en Uppskattad tidsåtgång (max) att slutföra varje steg.
I följande tabell beskrivs varje publiceringen. Det ges även en uppskattad tid att slutföra varje steg.


|  **Publiceringen**           | **tid**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Verifiera förutsättningar         | 15 min   | Ger information och erbjuder inställningarna verifieras.                        |
| Certifiering                  | 1 vecka | Erbjudandet analyseras av Azure-certifiering-teamet. Erbjudandet genomsöks efter virus, skadlig kod, säkerhet och säkerhetsproblem. Erbjudandet kontrolleras för att se att den uppfyller alla urvalskriterier. Mer information finns i [krav](./cpp-prerequisites.md). Feedbacks tillhandahålls om ett problem hittas. |
| Packas | 1 timme  | Erbjudandets tekniska resurser som är packade för kundens användning och lead-system har konfigurerats och konfigurera. |
|  Utgivaren signering             |  -        | Sista publisher granska och bekräfta innan erbjudandet lanseras. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i erbjudandet information steg) för att kontrollera att den uppfyller dina krav.  Välj **Go Live** så att ditt erbjudande kan flytta till nästa steg. |
| Packas                 | 1 timme | Klar erbjudandet replikeras i regionerna och marketplace produktionssystem. | 
| Live                           | fyra dagar |Erbjudandet är publicerat, replikeras till de nödvändiga regionerna och blir tillgängliga för allmänheten. |

Tillåt för upp till 10 arbetsdagar att slutföra publiceringsprocessen och erbjudandet släpps. När du har slutfört processen för att publicera ditt erbjudande visas i den [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).

## <a name="next-steps"></a>Nästa steg

[Uppdatera befintligt erbjudande](./cpp-update-existing-offer.md)