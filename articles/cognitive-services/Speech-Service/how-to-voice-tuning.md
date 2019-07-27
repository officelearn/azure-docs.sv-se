---
title: Finjustera text till tal-utdata-tal-tjänsten
titleSuffix: Azure Cognitive Services
description: 'Aktivera loggning i tal-SDK: n.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4cf2338d76ce31f44eaf3fb235e5f8796602d819
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562744"
---
# <a name="fine-tune-text-to-speech-output"></a>Finjustera text till tal-utdata

Med Azures tal tjänster kan du justera hastighet, uttal, volym, färgdjup och profil för text till tal-utdata med hjälp av [tal syntes markerings språk (SSML)](speech-synthesis-markup.md). SSML är ett XML-baserat kodspråk som använder taggar för att informera tjänsten om vilken funktion som kräver justering. SSML-meddelandet skickas sedan i bröd texten för varje begäran till text till tal-tjänsten. För att förenkla anpassnings processen erbjuder tal tjänsterna nu ett [röst justerings](https://aka.ms/voicetuning) verktyg som gör det möjligt att visuellt inspektera och finjustera text till tal-utdata i real tid.

Verktyget för röst justering stöder Microsofts [standard](language-support.md#standard-voices)-, [neurala](language-support.md#text-to-speech)-och [anpassade röster](how-to-customize-voice-font.md).

## <a name="get-started-with-the-voice-tuning-tool"></a>Kom igång med röst justerings verktyget

Innan du kan börja finjustera text till tal-utdata med röst justerings verktyget måste du utföra följande steg:

1. Skapa en [kostnads fri Microsoft-konto](https://account.microsoft.com/account) om du inte redan har en.
2. Skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) om du inte redan har ett. Klicka på **Starta kostnads fritt**och skapa ett nytt Azure-konto med hjälp av din Microsoft-konto.

3. Skapa en Speech Services-prenumeration i Azure Portal. Stegvisa instruktioner för [hur du skapar en tal resurs](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) är tillgängliga.
   >[!NOTE]
   >När du skapar en tal resurs i Azure Portal måste Azures plats information matcha med röst regionen TTS. Neurala TTS-röst har stöd för en under uppsättning av Azure-platser. En fullständig lista över support finns i [regioner](regions.md#text-to-speech).

   >[!NOTE]
   >Du måste ha en F0-eller S0-nyckel som skapats i Azure Portal innan du kan använda tjänsten. Röst justering har **inte** stöd för den [30 dagars kostnads fria utvärderings nyckeln](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Logga in på [röst justerings](https://aka.ms/voicetuning) portalen och Anslut din röst tjänst prenumeration. Välj en prenumeration på en enda tal tjänst och skapa sedan ett projekt.
5. Välj **Ny justering**. Följ sedan dessa steg:

   * Leta upp och välj **alla prenumerationer**.  
   * Välj **ansluta befintliga prenumeration**.  
     ![Anslut en befintlig prenumeration](./media/custom-voice/custom-voice-connect-subscription.png).
   * Ange din prenumerations nyckel för Azure Speech Services och välj sedan **Lägg till**. Dina prenumerations nycklar är tillgängliga i anpassnings portalen för tal på [prenumerations sidan](https://go.microsoft.com/fwlink/?linkid=2090458). Du kan också hämta nycklarna från fönstret resurs hantering i [Azure Portal](https://portal.azure.com/).
   * Om du har fler än en röst tjänst prenumeration som du planerar att använda upprepar du dessa steg för varje prenumeration.

## <a name="customize-the-text-to-speech-output"></a>Anpassa text till tal-utdata

Nu när du har skapat konton och länkat din prenumeration kan du börja justera text till tal-utdata.

1. Välj en röst.
2. Mata in den text som du vill redigera.
3. Innan du börjar göra redigeringarna kan du spela upp ljudet för att få en känsla för utdata.
4. Välj ordet/meningen som du vill förfina och börja experimentera med olika SSML-baserade funktioner.

>[!TIP]
> Detaljerad information om hur du justerar SSML och justerar röst resultatet finns i [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md).

## <a name="limitations"></a>Begränsningar

Neurala röst justering skiljer sig något från justering för standard-och anpassade röster.

* Intonation stöds inte för neurala-röster.
* Funktionerna för färgdjup och volym fungerar bara med fullständiga meningar. Dessa funktioner är inte tillgängliga på ordet-nivå.
* I takt med detta kan vissa neurala-röster justeras baserat på ord, medan andra kräver att du väljer hela meningar.

> [!TIP]
> Verktyget röst justering ger sammanhangsbaserad information om funktioner och justeringar.

## <a name="next-steps"></a>Nästa steg
* [Skapa en tal resurs i Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Starta röst justering](https://speech.microsoft.com/app.html#/VoiceTuning)
* [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md)
