---
title: Konfigurera innehåll kontrollant granskningsinställningar | Microsoft Docs
description: Konfigurera eller hämta ditt team, taggar, kopplingar, arbetsflöden och autentiseringsuppgifter.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: a3432a1d8f424fbe78570f47b774c6e7942e16b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351507"
---
# <a name="about-review-tool-settings"></a>Om verktyget granska inställningar #

Med fliken Inställningar på instrumentpanelen granska verktyget är det enkelt att definiera och ändra många komponenter.

![Innehåll kontrollant granskningsinställningar](images/settings-1.png)

## <a name="team-and-subteams"></a>Teamet och Subteams ## 

Hantera ditt team och subteams från den här fliken. Du kan bara ha ett team, men du kan [skapa flera subteams](subteams.md) och skicka inbjudningar för framtida medlemmar. När du har skickat ut inbjudan har skickats ut kan du övervaka dem, ändra behörigheter för gruppmedlemmar och bjuda in ytterligare användare. När gruppmedlemmar har accepterat inbjudan, kan du tilldela olika subteams medlemmar. Du kan ange gruppmedlemmar roller vara administratörer eller granskare: Administratörer kan bjuda in andra användare, medan det går inte att granskare.

![Inställningar för kontrollant Team-innehåll](images/settings-2-team.png)

## <a name="tags"></a>Taggar ##

Det är där du kan [definiera anpassade taggar](tags.md) genom att ange kort kod, namn och beskrivning för taggarna. När du har skapat den, är den tillgänglig under granskningar. Du kan använda olika taggar för olika granskningar genom att aktivera och stänga synlighet.

![Innehåll kontrollant taggar inställningar](images/settings-3-tags.png)

## <a name="connectors"></a>Anslutningar ##

Arbetsflöden lägger till funktioner genom att använda kopplingar för att kommunicera med verktyget granska. Granska verktyget anropar API: er för innehåll-kontrollant med standardarbetsflöde för att kontrollera innehåll. När du registrerar dig för verktyget granska den automatiskt etablerar kontrollant API-autentiseringsuppgifter för. Det stöder också integrera andra connector API: er, så länge en koppling är tillgänglig. Vi har gjort några kopplingar direkt.

Den [kopplingar fliken](connectors.md) är där du hanterar kopplingar. Du kan lägga till eller ta bort kopplingar och hitta din prenumeration nyckel för en viss koppling. Klicka på Anslut för att lägga till dem till dina anpassade arbetsflöden. 

![Innehåll kontrollant kopplingar inställningar](images/settings-4-connectors.png)

## <a name="workflows"></a>Arbetsflöden ##

Hantera arbetsflöden från fliken arbetsflöden. Du kan testa arbetsflöden genom att ladda upp en exempelfil. Du kan också [definiera anpassade arbetsflöden](workflows.md) för bilden och texten med hjälp av de tillgängliga API-kopplingar (finns på fliken kopplingar). 

![Innehåll kontrollant inställningar för arbetsflöde](images/settings-5-workflows.png)

## <a name="credentials"></a>Autentiseringsuppgifter ##

Den här fliken ger snabb åtkomst till ditt innehåll kontrollant prenumeration nyckeln behöver du använda API: er som inkluderas med innehåll kontrollant (bild Avbrottsmoderering, Text Avbrottsmoderering, hantering av listor, arbetsflöde och granska API: er).
 
![Innehåll kontrollant autentiseringsuppgifter](images/settings-6-credentials.png)
