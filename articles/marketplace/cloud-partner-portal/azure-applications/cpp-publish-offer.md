---
title: Publicera erbjudande för Azure-program | Azure Marketplace
description: Beskriver processen och stegen för att publicera ett erbjudande för Azure-program på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pabutler
ms.openlocfilehash: 0005760a16f5109ca3555df5c5c5137facc84c40
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826153"
---
# <a name="publish-azure-application-offer"></a>Publicera ett erbjudande för Azure-program

När du har skapat ett erbjudande genom att ange informationen på sidan **ny erbjudande** kan du publicera erbjudandet. Välj **publicera** för att starta publicerings processen.

Följande diagram visar de huvudsakliga stegen i publicerings processen för ett erbjudande till "Go Live".

![Erbjudande publicerings steg](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publicerings steg

I följande tabell visas och beskrivs varje publicerings steg, och en tids uppskattning visas för att slutföra varje steg.  Uppskattningar inom "dagar" definieras som arbets dagar, som inte omfattar helger och helgdagar.

|  **Publicerings steg**           | **Tid**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Verifiera krav         | < 15 min    | Information om erbjudandet och erbjudande inställningarna verifieras.                        |
| Validera inställningar för påverkan på intäkter | < 15 min  | Azure-resursanvändning för erbjudandet är markerat.             |
| Certifiering                  | < 1 dag     | Erbjudandet analyseras av Azure-certifierings teamet. Erbjudandet genomsöks efter virus, skadlig kod, säkerhets krav och säkerhets problem. Erbjudandet kontrol leras för att se att det uppfyller alla villkor för berättigande. Mer information finns i [krav](./cpp-prerequisites.md). Feedback ges om ett problem påträffas. |
| Verifiering av test enhet          | < 2 timmar   | Valfritt Om det finns en testen het, verifierar Microsoft att den kan distribueras och replikeras.  |
| Registrering av paketering och skapande av lead | < 1 timme  | Erbjudandets tekniska till gångar är paketerade för kund användning och lead-systemen konfigureras och distribueras. |
|  Utgivarens utloggning             |  Bok    | Slutlig utgivar granskning och bekräftelse innan erbjudandet går live. Nu är erbjudandet tillgängligt för förhands granskning.  Du kan distribuera ditt erbjudande i de valda prenumerationerna (i stegen för att erbjuda information) för att kontrol lera att det uppfyller alla krav.  När du har verifierat erbjudandet väljer du **Go Live** så att ditt erbjudande kan gå vidare till nästa steg. |
| Microsoft-granskning                | 7-14 dagar | Microsoft granskar ditt Azure-program och skickar ett e-postmeddelande till dig om problem har identifierats.  Längden på det här steget beror på programmets komplexitet, de problem som har påträffats och hur du svarar på dem.  |
| Live                           | < 1 dag | Erbjudandet släpps, replikeras till de angivna regionerna och görs tillgängligt för allmänheten. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Du kan övervaka publicerings processen på fliken **status** för ditt erbjudande i Cloud Partner Portal.

![Fliken status för ett erbjudande om Azure App](./media/offer-status-tab.png)

När du har slutfört publicerings processen visas ditt erbjudande i [kategorin Microsoft Azure Marketplace program](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>Partner kanal för Cloud solution providers (CSP) är nu tillgängligt.  Se [leverantörer av moln lösningar](../../cloud-solution-providers.md) för mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner-kanaler.

## <a name="errors-and-review-feedback"></a>Fel och granska feedback

Förutom att Visa publicerings statusen för ditt erbjudande visar fliken **status** också fel meddelanden och feedback från alla publicerings steg där ett problem påträffas.  Om problemet är kritiskt avbryts publiceringen.  Du måste sedan korrigera de rapporterade problemen och publicera om erbjudandet.  Eftersom **Microsoft gransknings** steget representerar en omfattande granskning av ditt erbjudande och dess tillhör ande tekniska till gångar (särskilt Azure Resource Manager mal len) visas problem vanligt vis som pull-begäranden (PR).  En förklaring av hur du kan visa och svara på dessa pull finns i [hantera granskning av feedback](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Nästa steg

Om du har påträffat fel i ett eller flera publicerings steg måste du korrigera dem och publicera erbjudandet på annat sätt.  Om kritiska problem påträffas i **Microsoft Review** -steget måste du [Hantera gransknings feedbacken](./cpp-handling-review-feedback.md) genom att gå till Microsoft Review Teams Azure DevOps-lagringsplats.

När en Azure-app har publicerats kan du [uppdatera det befintliga erbjudandet](./cpp-update-existing-offer.md) för att avspegla förändringar av affärs kraven eller de tekniska kraven. 
