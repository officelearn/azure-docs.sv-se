---
title: Vad är en arbetsyta och projekt? – Anpassade Translator
titleSuffix: Azure Cognitive Services
description: En arbetsyta är en arbetsyta för att skapa och skapa dina anpassade översättningssystemet. En arbetsyta kan innehålla flera projekt, modeller och dokument. Ett projekt är en Omslutning för en modell, dokument och tester. Alla projekt som innehåller den automatiskt alla dokument som överförs till den arbetsytan som har rätt språk-par.
services: cognitive-services
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 1612c5ae6f6fd82386a4d4fba20f011621223a84
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214117"
---
# <a name="what-is-a-custom-translator-workspace"></a>Vad är en anpassad Translator-arbetsyta?

En arbetsyta är en arbetsyta för att skapa och skapa dina anpassade översättningssystemet. En arbetsyta kan innehålla flera projekt, modeller och dokument. Allt arbete du göra i anpassade Translator som finns i en viss arbetsyta.

Arbetsytan är privat för dig och de personer som du bjuder in på din arbetsyta. Mötesförfrågningarna personer har inte åtkomst till innehållet i din arbetsyta. Du kan bjuda in så många personer som du vill på din arbetsyta och ändra eller ta bort åtkomsten när som helst. Du kan också skapa en ny arbetsyta. Som standard innehåller en arbetsyta inte några projekt eller dokument som i dina andra arbetsytor.

## <a name="what-is-a-custom-translator-project"></a>Vad är en anpassad Translator-projekt?

Ett projekt är en Omslutning för en modell, dokument och tester. Alla projekt som innehåller den automatiskt alla dokument som överförs till den arbetsytan som har rätt språk-par. Om du har både en engelska till spanska projekt och en spanska till engelska projekt, till exempel tas samma dokument med i båda projekten. Varje projekt har en CategoryID kopplade till den som används vid frågor till den [V3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) för översättningar. CategoryID är parameter som används för att hämta översättningar från ett anpassat system som skapats med anpassade Translator.

## <a name="project-categories"></a>Projektkategorier

Kategorin identifierar domänen – området i terminologi och stil du vill använda – för ditt projekt. Välj kategorin som är mest relevant för dina dokument. I vissa fall påverkar ditt val av kategorin direkt beteendet för anpassad Translator.

Vi har två uppsättningar basmodeller. De är allmän och teknik. Om kategorin **teknik** är markerat, basmodeller tekniken används. För alla andra Kategorival används allmänt basmodeller. Teknik baslinje modellen har bra i teknik domän, men den visar lägre kvalitet om meningar som används för översättning inte faller inom teknik-domänen. Vi rekommenderar att kunder för att välja kategori teknik endast om meningar strikt faller inom teknik-domänen.

På samma arbetsyta kan du skapa projekt för samma språk par i olika kategorier. Anpassade Translator går inte att skapa en duplicerad projekt med samma språk-par och kategori. Tillämpa en etikett till ditt projekt kan du undvika den här begränsningen. Använd inte etiketter, såvida inte du bygger översättningssystem för flera klienter, som att lägga till en unik etikett till ditt projekt återspeglas i dina projekt CategoryID.

## <a name="project-labels"></a>Projekt-etiketter

Anpassade Translator kan du tilldela en etikett för projekt i projektet. Etikett för projekt skiljer mellan flera projekt med samma språk-par och kategori. Ett bra tips är att undvika att använda projekt etiketter, såvida inte behövs.

Projekt-etiketten används som en del av CategoryID. Om etiketten projekt lämnas Odefinierad eller ställts identiskt för projekt, sedan projekt med samma kategori och *olika* språkpar delar samma CategoryID. Den här metoden innebär stora fördelar eftersom det gör att du eller dina kunder att växla mellan språk när du använder Translator Text API utan att behöva bekymra dig om en CategoryID som är unik för varje projekt.

Om jag vill aktivera översättningar i teknik domänen från engelska till franska och från Franska till engelska jag till exempel skapar två projekt: en för engelska –\> franska och en för franska -\> engelska. Jag skulle ange samma kategori (teknik) för både och lämna det tomt projekt etiketten. CategoryID för båda projekten matchar, så att jag kan ställa frågor API: et för både engelska och franska översättningar utan att behöva ändra min CategoryID.

Om du är tjänstleverantör och språk och vill ge flera kunder med olika modeller som behåller samma kategori och språk-par, skulle det vara klokt beslut att sedan använda en etikett för projektet för att skilja mellan kunder.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [utbildnings- och modellen](training-and-model.md) veta hur du skapar en översättningsmodellen effektivt.
