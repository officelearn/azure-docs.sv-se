---
title: Publicera erbjudande för Azure-program – Azure Marketplace | Microsoft Docs
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
ms.date: 01/25/2019
ms.author: pbutlerm
ms.openlocfilehash: cafda8a48f9160c80edb02c3452035f912958bc7
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098229"
---
# <a name="publish-azure-application-offer"></a>Publicera erbjudande för Azure-program

När du har skapat ett erbjudande genom att tillhandahålla informationen om den **nytt erbjudande** kan du publicera erbjudandet. Välj **publicera** att starta publiceringsprocessen.

I följande diagram visas de viktigaste stegen i publiceringsprocessen i ett erbjudande ”gå live”.

![Publicera erbjudande-steg](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publicering av steg

I följande tabell listar och beskriver hur publicering och ger en Uppskattad tidsåtgång för att slutföra varje steg.  Tider beräkningar i ”dagar” definieras som arbetsdagar utesluta helger och semestrar.

|  **Publiceringen**           | **Time**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Verifiera förutsättningar         | < 15 min    | Ger information och erbjuder inställningarna verifieras.                        |
| Verifiera inställningar som har påverkat intäkter | < 15 min  | Azure-resurs användning attribution för erbjudandet kontrolleras.             |
| Certifiering                  | < 1 dag     | Erbjudandet analyseras av Azure-certifiering-teamet. Erbjudandet genomsöks efter virus, skadlig kod, säkerhet och säkerhetsproblem. Erbjudandet kontrolleras för att se att den uppfyller alla urvalskriterier. Mer information finns i [krav](./cpp-prerequisites.md). Feedback tillhandahålls om ett problem hittas. |
| Test Drive-verifiering          | < 2 timmar   | (Valfritt) Om en Test Drive finns verifierar Microsoft att den kan distribueras och replikeras.  |
| Paketering och registrering för lead-generering | < 1 timme  | Erbjudandets tekniska resurser som är packade för kundens användning och lead-system konfigureras och distribueras. |
|  Utgivaren signering             |  Manuell    | Sista publisher granska och bekräfta innan erbjudandet lanseras. Erbjudandet är nu tillgänglig för förhandsgranskning.  Du kan distribuera ditt erbjudande i de valda prenumerationerna (i erbjudandet information steg) för att kontrollera att den uppfyller dina krav.  När du har kontrollerat erbjudandet, Välj **Go Live** så att ditt erbjudande kan flytta till nästa steg. |
| Microsoft-granskning                | 7 – 14 dagar | Microsoft granskar dina Azure-program holistiskt och e-postmeddelande om problem upptäcks.  Längden på det här steget beror på hur komplex programmet problem vid och hur snabbt du svara på dem.  |
| Live                           | < 1 dag | Erbjudandet är publicerat, replikeras till de angivna regionerna och göras tillgängliga för allmänheten. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Du kan övervaka publiceringsprocessen i den **Status** fliken för ditt erbjudande i partnerportalen i molnet.

![Fliken status för ett erbjudande för Azure-app](./media/offer-status-tab.png)

När du har slutfört processen för att publicera ditt erbjudande visas i den [Microsoft Azure Marketplace-programkategori](https://azuremarketplace.microsoft.com/marketplace/apps/).


## <a name="errors-and-review-feedback"></a>Fel och granska feedback

Förutom att visa publiceringsstatus för ditt erbjudande i **Status** fliken visas även felmeddelanden och feedback från alla publishing steg där ett problem har påträffats.  Om problemet är kritiska kan avbryts sedan publicera.  Du måste sedan rätta till felen i rapporterade och publicera erbjudandet.  Eftersom den **Microsoft granska** steg representerar en omfattande granskning av ditt erbjudande och dess associerade tekniska resurser (särskilt Azure Resource Manager-mall), problem visas vanligtvis som pull-begäran (PR) länkar.  En förklaring av hur du visar och svara på dessa PR Se [hantering granska feedback](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Nästa steg

Om du har stött på fel i en eller flera av publishing stegen, måste du åtgärda dem och publicera ditt erbjudande.  Om allvarliga problem som uppstår i den **Microsoft granska** steg, måste du [hantera granska feedback](./cpp-handling-review-feedback.md) granska gruppens Azure DevOps-databasen genom att gå till Microsoft.

När en Azure-app har publicerats kan du [uppdatera befintligt erbjudande](./cpp-update-existing-offer.md) så att företag eller tekniska behoven. 
