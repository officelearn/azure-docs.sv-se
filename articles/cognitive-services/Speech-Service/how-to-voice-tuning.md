---
title: Finjustera text till tal resultat – Speech Services
titleSuffix: Azure Cognitive Services
description: 'Aktivera loggning i SDK: N för tal.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 2d56aba6d62d70c482ed1489a09276eeaa11f956
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025850"
---
# <a name="fine-tune-text-to-speech-output"></a>Finjustera text till tal-utdata

Azure Speech Services kan du justera hastighet, uttal, volym, försäljningsargument och konturen för text till tal-utdata med [tal syntes Markup Language (SSML)](speech-synthesis-markup.md). SSML är en XML-baserade märkspråk som använder taggar för att informera tjänsten om vilken funktion kräver justering. SSML-meddelandet skickas i meddelandetexten för varje begäran till tjänsten text till tal. För att förenkla anpassningsprocessen Speech Services nu erbjuda en [röst justering](https://aka.ms/voicetuning) verktyg som gör att du kan granska och finjustera visuellt text till tal matar ut i realtid.

Justering av Voice-verktyget stöder Microsofts [standard](language-support.md#standard-voices), [neural](language-support.md#text-to-speech), och [anpassade röster](how-to-customize-voice-font.md).

## <a name="get-started-with-the-voice-tuning-tool"></a>Kom igång med verktyget röst justering

Innan du kan börja finjustera text till tal-utdata med verktyget röst justering, behöver du att slutföra dessa steg:

1. Skapa en [kostnadsfritt Microsoft-konto](https://account.microsoft.com/account) om du inte redan har ett.
2. Skapa en [kostnadsfritt Azure-konto](https://azure.microsoft.com/en-us/free/) om du inte redan har ett. Klicka på **starta kostnadsfri**, och skapa en ny Azure-konto med ditt Microsoft-konto.

3. Skapa en Speech Services-prenumeration i Azure-portalen. Stegvisa instruktioner för [så här skapar du en tal-resurs](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) är tillgängliga.
   >[!NOTE]
   >När du skapar en tal-resurs i Azure portal, måste Azure platsinformationen matcha med text till tal-voice-region. Neural text till tal-röst stöder en sub uppsättning Azure-platser. En fullständig lista över support finns i [regioner](regions.md#text-to-speech).

   >[!NOTE]
   >Du måste ha en F0 eller en S0-nyckel som skapats i Azure-portalen innan du kan använda tjänsten. Röst justering **inte** stöder den [30-dagars kostnadsfri utvärderingsversion nyckeln](https://review.docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Logga in på den [röst justering](https://aka.ms/voicetuning) -portalen och ansluta din Speech Services-prenumeration. Välj en enda Speech Services-prenumeration och sedan skapa ett projekt.
5. Välj **nya justering**. Följ dessa steg:

   * Leta upp och välj **alla prenumerationer**.  
   * Välj **ansluta befintliga prenumeration**.  
     ![Ansluta en befintlig prenumeration](./media/custom-voice/custom-voice-connect-subscription.png).
   * Ange din prenumerationsnyckel för Azure Speech Services från Azure-portalen och välj sedan **Lägg till**. Dina prenumerationsnycklar är tillgängliga i Azure-portalen från fönstret resurshantering.  Du kan också få nycklarna från prenumerationslista i portalen röst justering.
   * Om du har mer än en Speech Services-prenumeration som du planerar att använda kan du upprepa dessa steg för varje prenumeration.

## <a name="customize-the-text-to-speech-output"></a>Anpassa text till tal-utdata

Nu när du har skapat konton och länkade prenumerationen, kan du justera text till tal-utdata.

1. Välj en röst.
2. Ange den text som du vill redigera.
3. Spela upp ljud för att få utdata innan du börjar att göra ändringar.
4. Välj word/mening som du vill förfina och börja experimentera med olika SSML-baserade funktioner.

>[!TIP]
> Detaljerad information om justera SSML och justera röst utdata finns i [tal syntes Markup Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Begränsningar

Justering av neurala röst är något annorlunda än inställningen för Standard och anpassad röster.

* Intonation stöds inte för Neurala röster.
* Ton och volym funktioner fungerar bara med fullständiga meningar. Dessa funktioner är inte tillgängliga på nivån word.
* För pris, vissa Neural röster kan ställa in baserat på ord, medan andra kräver att du kan välja hela meningar.

> [!TIP]
> Verktyget röst justering ger detaljerad information om funktioner och justering.

## <a name="next-steps"></a>Nästa steg
* [Skapa en tal-resurs i Azure](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Starta röst justering](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md)
