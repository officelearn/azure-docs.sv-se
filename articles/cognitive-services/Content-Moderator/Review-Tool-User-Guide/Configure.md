---
title: Konfigurera verktygsinställningar för Content Moderator granska - Content Moderator
titlesuffix: Azure Cognitive Services
description: Konfigurera eller hämta ditt team, taggar, anslutningar, arbetsflöden och autentiseringsuppgifter.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 32563b429764a45efbef952310ab6615567d42f0
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210938"
---
# <a name="configure-review-tool-settings"></a>Konfigurera inställningar för granskning-verktyget

På fliken Inställningar på Granska verktyget instrumentpanelen, är det enkelt att definiera och ändra många komponenter.

![Content Moderator granskningsinställningar](images/settings-1.png)

## <a name="team-and-subteams"></a>Team och undergrupper

Hantera ditt team och undergrupper från den här fliken. Du kan bara ha ett team, men du kan [skapa flera undergrupper](subteams.md) och skicka inbjudningar för framtida medlemmar. När du har skickat ut inbjudningar kan du övervaka dem, ändra behörigheter för gruppmedlemmar och bjuda in fler användare. När gruppmedlemmar som har accepterat din inbjudan, kan du tilldela olika undergrupper dessa medlemmar. Du kan ställa in gruppmedlemmar roller ska vara administratörer eller granskare: Administratörer kan bjuda in andra användare, även om granskare kan inte.

![Content Moderator Teaminställningar](images/settings-2-team.png)

## <a name="tags"></a>Taggar

Det här är där du kan [definiera anpassade taggar](tags.md) genom att ange kort kod, namn och beskrivning för taggarna. När du har skapat den, är den tillgänglig under granskningar. Du kan använda olika taggar för olika granskning genom att aktivera synligheten ut och in.

![Content Moderator Tags Settings](images/settings-3-tags.png)

## <a name="connectors"></a>Anslutningar

Arbetsflöden kan du lägga till funktioner genom att använda kopplingar för att kommunicera med granskningsverktyget. Granskningsverktyget anropar API: er för innehåll-Moderator med standardarbetsflöde för att kontrollera innehåll. När du registrerar dig för verktyget för att granska den automatiskt etablerar Moderator API-autentiseringsuppgifter för dig. Det stöder även integrering av andra connector API: er, så länge som en anslutning är tillgänglig. Vi har gjort några anslutningar direkt.

Den [kopplingar fliken](connectors.md) är där du hanterar anslutningar. Du kan lägga till eller ta bort kopplingar och hitta din prenumerationsnyckel för en viss koppling. Klicka på Anslut för att lägga till dem i dina anpassade arbetsflöden. 

![Content Moderator kopplingar inställningar](images/settings-4-connectors.png)

## <a name="workflows"></a>Arbetsflöden

Hantera arbetsflöden från fliken arbetsflöden. Du kan testa arbetsflöden genom att ladda upp en exempelfil. Du kan också [definiera anpassade arbetsflöden](workflows.md) för bild- och med hjälp av de tillgängliga API-kopplingar (finns på fliken för anslutningar). 

![Content Moderator arbetsflödesinställningarna](images/settings-5-workflows.png)

## <a name="credentials"></a>Autentiseringsuppgifter

Den här fliken ger snabb åtkomst till din prenumerationsnyckel för Content Moderator som du måste använda API: er som ingår med Content Moderator (Bildmoderering, Textmoderering, hantering, arbetsflöde och granska API: er).
 
![Content Moderator-autentiseringsuppgifter](images/settings-6-credentials.png)
