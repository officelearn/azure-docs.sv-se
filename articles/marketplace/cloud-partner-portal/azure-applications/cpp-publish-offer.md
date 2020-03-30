---
title: Publicera Azure-programerbjudande | Azure Marketplace
description: Beskriver processen och stegen för att publicera ett Azure-programerbjudande på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: dsindona
ms.openlocfilehash: 9f89e31c2d17ef74971d2057ba58e9572e92184c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280107"
---
# <a name="publish-azure-application-offer"></a>Publicera ett erbjudande för Azure-program

När du har skapat ett erbjudande genom att ange informationen på sidan **Nytt erbjudande** kan du publicera erbjudandet. Välj **Publicera** om du vill starta publiceringsprocessen.

Följande diagram visar de viktigaste stegen i publiceringsprocessen för ett erbjudande om att "publicera".

![Steg för publicering av erbjudande](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publiceringssteg

I följande tabell visas och beskrivs varje publiceringssteg och en tidsuppskattning för att slutföra varje steg.  Uppskattningar av tider i "dagar" definieras som arbetsdagar, som utesluter helger och helgdagar.

|  **Publiceringssteg**           | **Tid**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validera förutsättningar         | < 15 min    | Erbjudandeinformation och erbjudandeinställningar valideras.                        |
| Validera intätverkade intäktsinställningar | < 15 min  | Attribution för Azure-resursanvändning för erbjudandet kontrolleras.             |
| Certifiering                  | < 1 dag     | Erbjudandet analyseras av Azure Certification Team. Erbjudandet genomsöks efter virus, skadlig kod, säkerhetskrav och säkerhetsproblem. Erbjudandet kontrolleras för att se till att det uppfyller alla villkor för berättigande. Mer information finns i [förutsättningar](./cpp-prerequisites.md). Feedback ges om ett problem hittas. |
| Validering av testkörning          | < 2 timmar   | (Valfritt) Om det finns en provkörning verifierar Microsoft att den kan distribueras och replikeras.  |
| Registrering av förpackning och leadgenerering | < 1 timme  | Erbjudandets tekniska tillgångar paketeras för kundanvändning och leadsystemen konfigureras och distribueras. |
|  Signering av utgivare             |  Manuell    | Slutlig utgivare granskning och bekräftelse innan erbjudandet går live. Erbjudandet är nu tillgängligt för förhandsversion.  Du kan distribuera ditt erbjudande i de valda prenumerationerna (i stegen för erbjudandeinformation) för att kontrollera att det uppfyller alla dina krav.  När du har verifierat erbjudandet väljer du **Gå live** så att erbjudandet kan gå vidare till nästa steg. |
| Microsoft recension                | 7 - 14 dagar | Microsoft granskar holistiskt ditt Azure-program och e-postmeddelanden du om problem upptäcks.  Längden på det här steget beror på programmets komplexitet, de problem som upptäckts och hur snabbt du svarar på dem.  |
| Live                           | < 1 dag | Erbjudandet frisläpps, replikeras till de angivna regionerna och görs tillgängligt för allmänheten. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Du kan övervaka publiceringsprocessen på fliken **Status** för ditt erbjudande i Cloud Partner Portal.

![Fliken Status för ett Azure-apperbjudande](./media/offer-status-tab.png)

När du är klar med publiceringsprocessen visas ditt erbjudande i [programkategorin Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>Opt-in (Cloud Solution Providers) partnerkanal är nu tillgänglig.  Se [Cloud Solution Providers](../../cloud-solution-providers.md) för mer information om marknadsföring ditt erbjudande via Microsoft CSP partnerkanaler.

## <a name="errors-and-review-feedback"></a>Fel och granska feedback

Förutom att visa publiceringsstatus för ditt erbjudande visar fliken **Status** också felmeddelanden och feedback från alla publiceringssteg där ett problem uppstår.  Om problemet är kritiskt avbryts publiceringen.  Du måste sedan korrigera de rapporterade problem och publicera erbjudandet igen.  Eftersom **Microsofts granskningssteg** representerar en omfattande granskning av ditt erbjudande och dess associerade tekniska resurser (särskilt Azure Resource Manager-mallen), presenteras problem vanligtvis som pull request(PR)-länkar.  En förklaring av hur du visar och svarar på dessa PRs, se [Hantera feedback.](./cpp-handling-review-feedback.md)


## <a name="next-steps"></a>Nästa steg

Om du stötte på fel i ett eller flera av publiceringsstegen måste du korrigera dem och publicera om erbjudandet.  Om kritiska problem uppstår i **Microsofts granskningssteg** måste du [hantera granskningsfeedbacken](./cpp-handling-review-feedback.md) genom att komma åt Microsofts granskningsteams Azure DevOps-databas.

När en Azure-app har publicerats kan du [uppdatera det befintliga erbjudandet](./cpp-update-existing-offer.md) för att återspegla förändrade affärs- eller tekniska krav. 
