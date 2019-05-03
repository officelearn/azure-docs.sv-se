---
title: Inspektera datakvaliteten för anpassat tal – Speech Services
titlesuffix: Azure Cognitive Services
description: Custom Speech tillhandahåller verktyg som gör att du kan inspektera visuellt igenkänning av kvaliteten på en modell genom att jämföra ljuddata med motsvarande igenkänningsresultatet. Med Custom Speech-portal kan du spela upp ljudet överförda och avgöra om den angivna igenkänningsresultatet är korrekt.  Det här verktyget kan du snabbt granska kvaliteten på Microsofts baslinje tal till text modell eller en anpassad träningsmodell utan att behöva transkribera alla ljuddata.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 682f3df362a7fbb0e95a07aa8a8f3a068367eef2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025824"
---
# <a name="inspect-custom-speech-data"></a>Granska data för anpassat tal

> [!NOTE]
> Den här sidan förutsätter att du har läst [förbereda testdata för anpassat tal](how-to-custom-speech-test-data.md) och har laddat upp en datauppsättning för granskning.

Custom Speech tillhandahåller verktyg som gör att du kan inspektera visuellt igenkänning av kvaliteten på en modell genom att jämföra ljuddata med motsvarande igenkänningsresultatet. Med Custom Speech-portal kan du spela upp ljudet överförda och avgöra om den angivna igenkänningsresultatet är korrekt. Det här verktyget kan du snabbt granska kvaliteten på Microsofts baslinje tal till text modell eller en anpassad träningsmodell utan att behöva transkribera alla ljuddata.

I det här dokumentet lär du dig att visuellt inspektera kvaliteten på en modell med träningsdata som du tidigare har laddats upp.

På den här sidan lär du dig att visuellt inspektera kvaliteten på Microsofts baslinje tal till text modellen och/eller en anpassad modell som du har tränat. Du ska använda de data som du laddade upp till den **Data** fliken för testning.

## <a name="create-a-test"></a>Skapa ett test

Följ dessa instruktioner för att skapa ett test:

1. Gå till **tal till text > anpassat tal > Testa**.
2. Klicka på **Lägg till Test**.
3. Välj **inspektera kvalitet (enbart ljud data)**. Ge testet ett namn, beskrivning, och välj din ljud datauppsättning.
4. Välj upp till två modeller som du vill testa.
5. Klicka på **Skapa**.

När ett test har skapats, kan du jämföra modeller sida vid sida.

## <a name="side-by-side-model-comparisons"></a>Sida-vid-sida-modellen jämförelser

När test-statusen är *lyckades*, klickar på objektnamn test visas detaljerad information om testet. Den här sidan listar alla uttryck i din datauppsättning som anger igenkänningsresultat av de två modellerna tillsammans med transkription från den överförda datamängden.

För att inspektera sida-vid-sida-jämförelse, kan du växla olika feltyper inklusive infogning och borttagning av ersättningen. Genom att lyssna på ljudet och jämföra resultat i varje kolumn (visar mänskliga etikett transkription och resultatet av två modeller för tal-till-text), kan du bestämma vilken modell som passar dina behov och var förbättringar som krävs.

Kontrollera kvalitetstestning är användbar för att verifiera om kvaliteten på en slutpunkt för taligenkänning räcker för ett program.  För ett servicenivåmål mått av Precision, kräver transkriberade ljud, följer du instruktionerna i testning: Utvärdera precision.

## <a name="next-steps"></a>Nästa steg

* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbereda testdata för anpassat tal](how-to-custom-speech-test-data.md)
