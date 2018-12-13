---
title: Publicera erbjudande för Azure SaaS-program | Microsoft Docs
description: Publicera processen och stegen för ett SaaS-program erbjuder på Azure Marketplace.
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
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 25c9ab0008e7f056789536d8cc737b69e83d6db5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197464"
---
# <a name="publish-a-saas-application-offer"></a>Publicera ett erbjudande för SaaS-program

När du har skapat ett nytt erbjudande genom att tillhandahålla informationen om den **nytt erbjudande** kan du publicera erbjudandet. Välj **publicera** att starta publiceringsprocessen.

I följande diagram visas de övergripande stegen för att publicera ett nytt erbjudande för SaaS-program.

![Publicera erbjudande-steg](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publicering av steg

I följande tabell beskrivs varje publishing steg, med en Uppskattad tidsåtgång (max) att slutföra varje steg.

|     **Steg**       |     **tid**      |  **Beskrivning**  |
|  ---------------   |  ---------------  |  ---------------  |
|         Certifiering           |       2 veckor            |          Erbjudandet analyseras av Azure-certifiering-teamet. Det här steget utför genomsökningar efter virus, skadlig kod, säkerhet och säkerhetsproblem. Den kontrollerar också att det här erbjudandet uppfyller alla urvalskriterier (se [krav](./cpp-prerequisites.md)). Feedback tillhandahålls om ett problem hittas.         |
|           Packas         |       1 timme            |       Erbjudandets tekniska resurser som är packade för kundens användning och lead-system har konfigurerats och konfigurera.            |
|        Utgivaren signering            |         -          |        Sista publisher granska och bekräfta innan erbjudandet lanseras. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i erbjudandet information steg) för att kontrollera att den uppfyller dina krav. Välj **Go Live** så att ditt erbjudande kan flytta till nästa steg.           |
|        Packas            |        1 timme           |        Slutförda erbjudandet replikeras i regionerna och marketplace produktionssystem.           |
|        Live            |       fyra dagar            |         Erbjudandet är publicerat, replikeras till de nödvändiga regionerna och blir tillgängliga för allmänheten.          |

Tillåt för upp till 10 arbetsdagar att slutföra publiceringsprocessen och erbjudandet släpps. När du har slutfört publiceringsprocessen ditt SaaS-erbjudande visas i den [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Nästa steg

[Uppdatera ett befintligt erbjudande](./cpp-update-existing-offer.md)
