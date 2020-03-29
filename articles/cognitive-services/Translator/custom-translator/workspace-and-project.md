---
title: Vad är en arbetsyta och ett projekt? - Anpassad översättare
titleSuffix: Azure Cognitive Services
description: I den här artikeln förklaras skillnaderna mellan en arbetsyta och ett projekt samt projektkategorier och etiketter för tjänsten Custom Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d2f7903fa85c645357e46a753d1cb043e0893254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219438"
---
# <a name="what-is-a-custom-translator-workspace"></a>Vad är en anpassad översättare arbetsyta?

En arbetsyta är en arbetsyta för att komponera och bygga ditt anpassade översättningssystem. En arbetsyta kan innehålla flera projekt, modeller och dokument. Allt arbete du gör i Custom Translator finns i en viss arbetsyta.

Arbetsytan är privat för dig och de personer du bjuder in till din arbetsyta. Objudna personer har inte tillgång till innehållet på din arbetsyta. Du kan bjuda in så många personer du vill på arbetsytan och ändra eller ta bort deras åtkomst när som helst. Du kan också skapa en ny arbetsyta. Som standard innehåller en arbetsyta inga projekt eller dokument som finns inom dina andra arbetsytor.

## <a name="what-is-a-custom-translator-project"></a>Vad är ett Custom Translator-projekt?

Ett projekt är ett omslag för en modell, dokument och tester. Varje projekt innehåller automatiskt alla dokument som överförs till arbetsytan som har rätt språkpar. Om du till exempel har både ett engelskt till spanskt projekt och ett spanskt till engelskt projekt, inkluderas samma dokument i båda projekten. Varje projekt har ett KategoriID som är associerat med det som används vid frågor om [V3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) för översättningar. CategoryID är parameter som används för att få översättningar från ett anpassat system byggt med Custom Translator.

## <a name="project-categories"></a>Projektkategorier

Kategorin identifierar domänen – det terminologi- och stilområde som du vill använda – för projektet. Välj den kategori som är mest relevant för dina dokument. I vissa fall påverkar ditt val av kategori direkt beteendet hos den anpassade översättaren.

Vi har två uppsättningar baslinjemodeller. De är allmänna och teknologi. Om kategorin **Teknik** väljs används teknikbaslinjemodellerna. För alla andra kategorival används de allmänna baslinjemodellerna. Teknikbaslinjemodellen fungerar bra i teknikdomänen, men den visar lägre kvalitet, om de meningar som används för översättning inte faller inom teknikområdet. Vi föreslår att kunderna väljer kategori Teknik endast om meningar faller strikt inom teknikområdet.

På samma arbetsyta kan du skapa projekt för samma språkpar i olika kategorier. Anpassad översättare förhindrar att ett duplicerat projekt skapas med samma språkpar och kategori. Om du använder en etikett i projektet kan du undvika den här begränsningen. Använd inte etiketter om du inte bygger översättningssystem för flera klienter, eftersom om du lägger till en unik etikett i projektet återspeglas i projekten CategoryID.

## <a name="project-labels"></a>Projektetiketter

Med Custom Translator kan du tilldela projektet en projektetikett. Projektetiketten skiljer mellan flera projekt med samma språkpar och kategori. Undvik att använda projektetiketter om det inte är nödvändigt.

Projektetiketten används som en del av CategoryID. Om projektetiketten lämnas urkopplad eller anges på samma sätt i olika projekt, kommer projekt med samma kategori och *olika* språkpar att dela samma CategoryID. Den här metoden är fördelaktig eftersom det gör att du eller kunden kan växla mellan språk när du använder API:et för textöversättare utan att behöva oroa dig för ett KategoriID som är unikt för varje projekt.

Om jag till exempel ville möjliggöra översättningar inom teknikdomänen från engelska till franska och från\> franska till engelska skulle\> jag skapa två projekt: ett för engelska - franska och ett för franska - engelska. Jag skulle ange samma kategori (Teknik) för båda och lämna projektet etiketten tom. CategoryID för båda projekten skulle matcha, så jag kunde fråga API för både engelska och franska översättningar utan att behöva ändra min CategoryID.

Om du är en leverantör av språktjänster och vill betjäna flera kunder med olika modeller som behåller samma kategori och språkpar, då använda en projektetikett för att skilja mellan kunder skulle vara ett klokt beslut.

## <a name="next-steps"></a>Nästa steg

- Läs om [Utbildning och modell](training-and-model.md) att veta, hur man effektivt bygger en översättningsmodell.
