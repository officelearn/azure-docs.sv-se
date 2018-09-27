---
title: Vanliga frågor och svar - Content Moderator
titlesuffix: Azure Cognitive Services
description: Få svar på vanliga frågor och svar om Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: faq
ms.date: 11/21/2016
ms.author: sajagtap
ms.openlocfilehash: ce61a89069caf90695a2cb6c54f61de8fa8cb112
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226944"
---
# <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

#### <a name="what-does-my-content-moderator-subscription-include"></a>Vad kostar Mina Content Moderator prenumeration innehålla?
Content Moderator-prenumeration ingår tillgång till verktyg för granskning och API: erna. Du kan bestämma om du vill använda en eller den andra eller både och, beroende på dina affärsbehov.

#### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Vilka är gränserna/begränsningarna för innehåll som kan modereras med hjälp av API:et?
När du använder API: et, måste avbildningar ha minst 128 bildpunkter och en maximal filstorlek på 4 MB. Text får vara högst 1024 tecken. Det finns ingen gräns på videons längd.

#### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>Vad händer om innehållet som skickas till text-API:et eller bild-API:et överskrider storleksgränserna?
API för textöversättning returnerar en felkod som meddelar att texten är längre än tillåtet. API för bildhantering returnerar också en felkod som meddelar att bilden inte uppfyller storlekskraven.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Håller du bilder, text och videor som skickas för moderering?
Innehållet är ditt eget och inte ska bevaras av Microsoft utan ditt medgivande. Microsoft använder branschledande säkerhetsåtgärder för att skydda ditt innehåll.

#### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Kan jag använda Content Moderator till skärmen för Ogiltigt underordnat utnyttjande avbildningar?
Nej. Men kvalificerade organisationer kan använda den [PhotoDNA molntjänst](https://www.microsoft.com/photodna "molntjänst från Microsoft PhotoDNA") till skärmen för den här typen av innehåll.

#### <a name="up-to-how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Upp till hur många granska team kan en användare ansluta till? Du kan växla mellan team?
En användare kan ansluta till ett team i taget.

#### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Vilken typ av teammedlem roller som stöds av granskningsverktyget? Hur skiljer de sig åt?
Studio kan för närvarande du tilldela rollerna Administratör och granskare. Administratörer kan bjuda in andra användare och har åtkomst till konfigurationsinställningar medan granskare kan endast granska resultaten och tagg eller ta bort taggen innehåll.

#### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>Vad är en tagg? Stöder anpassade taggar i granskningsverktyget?
En tagg är en eller två bokstäver kort kod som anger en flagga för moderering, till exempel ”a” för vuxna, ”r” för vågat och så vidare. Administratörer kan definiera nya taggar för sin verksamhet efter behov.

#### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Hur många teammedlemmar kan jag ha i mitt granskningsteam?
Du kan ha upp till fem teammedlemmar, inklusive administratören i ett team.
