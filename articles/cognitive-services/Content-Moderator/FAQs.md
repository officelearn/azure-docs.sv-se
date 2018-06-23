---
title: Vanliga frågor och svar för Azure innehåll kontrollant | Microsoft Docs
description: Få svar på vanliga frågor om innehåll kontrollanten.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 11/21/2016
ms.author: sajagtap
ms.openlocfilehash: 7bf6c67bd0a83d9455d14253f4f4b2becafd29ba
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351594"
---
# <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

#### <a name="what-does-my-content-moderator-subscription-include"></a>Vad gör min innehåll kontrollant prenumeration innehålla?
Prenumerationen innehåll kontrollant inbegriper tillgång till verktyget granskning och API: er. Du kan välja om du vill använda en eller den andra eller både och, beroende på dina affärsbehov.

#### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Vilka är gränserna/begränsningarna för innehåll som kan modereras med hjälp av API:et?
När du använder API: et, måste bilder ha minst 128 bildpunkter och en maximal storlek på 4 MB. Texten kan ha högst 1024 tecken. Det finns ingen gräns på video varaktighet.

#### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Vad händer om innehållet som skickas till text-API:et eller bild-API:et överskrider storleksgränserna?
Texten API returnerar en felkod som meddelar att texten är längre än tillåtet. Bilden API även returnerar en felkod som meddelar att bilden inte uppfyller storlekskraven.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Håller du bilder, text eller videor som skickas för gruppering?
Innehållet är din egen och kan inte behållas av Microsoft utan ditt medgivande. Microsoft använder branschledande säkerhetsåtgärder för att skydda ditt innehåll.

#### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Kan jag använda innehåll kontrollant till skärmen för Ogiltigt underordnat utnyttjande bilder?
Nej. Kvalificerade organisationer kan dock använda den [PhotoDNA Molntjänsten](https://www.microsoft.com/photodna "Molntjänsten i Microsoft PhotoDNA") till skärmen för den här typen av innehåll.

#### <a name="up-to-how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Upp till hur många granska team kan en användare ansluta till? Användaren kan växla mellan grupper?
En användare kan ansluta till ett team i taget.

#### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Vilken typ av teammedlem roller som stöds av verktyget granska? Hur skiljer de sig åt?
Studio kan för närvarande tilldela roller för administratör och granskning. Administratörer kan bjuda in andra användare och har åtkomst till konfigurationsinställningarna medan granskare kan bara granska avbrottsmoderering resultat och tagg eller ta bort märkord från innehållet.

#### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>Vad är en tagg? Stöder anpassade taggar i verktyget granska?
En tagg är en eller två bokstäver kort kod som anger en flagga i avbrottsmoderering, till exempel ”a” för vuxna, ”r” för dyr och så vidare. Administratörer kan definiera nya etiketter för verksamheten efter behov.

#### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Hur många medlemmar kan ha i min granska grupp?
Du kan ha upp till fem teammedlemmar, inklusive administratören i ett team.
