---
title: Förbättra kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: ''
author: diberry
manager: nitinme
displayName: active learning, suggestion, dialog prompt, train api, feedback loop, autolearn, auto-learn, user setting, service setting, services setting
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 6feb521aa47ca813b3067451c8c77111deb60e73
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874013"
---
# <a name="use-active-learning-to-improve-knowledge-base"></a>Använda aktiv inlärning för att förbättra kunskapsbasen

Aktiv inlärning kan du förbättra kvaliteten på din kunskapsbas genom att föreslå alternativa frågor, baserat på användare-bidrag till dina frågor och svar-par. Du kan granska dessa förslag, antingen lägga till dem i befintliga frågor eller avvisa dem. 

Kunskapsbasen ändras inte automatiskt. Du måste acceptera förslag för att ändringar ska börja gälla. Förslagen lägga till frågor men inte ändra eller ta bort befintliga frågor.

## <a name="active-learning"></a>Aktiv inlärning

QnA Maker lär sig nya fråga variationer med implicit och explicit feedback.
 
* Implicit feedback – rankningen förstår när en användare fråga har flera svar med resultat som är mycket nära och ser detta som feedback. 
* Explicit feedback – när flera svar med lite skillnad i resultat returneras i kunskapsbasen klientprogrammet ombeds användaren vilken fråga är rätt fråga. Explicit Användarfeedback skickas till QnA Maker med Train-API. 

Båda metoderna ger rankningen med liknande frågor som är klustrade.

När liknande frågor är klustrade föreslår QnA Maker användarbaserade frågorna kunskapsbas Designer ska godkänna eller avvisa.

## <a name="how-active-learning-works"></a>Hur active learning fungerar

Aktiv inlärning utlöses baserat på poäng för övre några svar returnerades av QnA Maker för en viss fråga. Om skillnaderna poäng ligger inom ett mindre intervall kommer frågan betraktas som ett möjligt _förslag_ för var och en av de möjliga svar. 

Alla förslag är klustrade tillsammans efter likhet och övre förslag på alternativa frågor visas baserat på åtkomstfrekvensen för de specifika frågorna av slutanvändare. Aktiv inlärning ger bästa möjliga förslagen i fall där slutpunkterna får ett rimligt antal och mängd användningsfrågor.

## <a name="upgrade-version-to-use-active-learning"></a>Uppgradera versionen om du vill använda aktiv inlärning

Aktiv inlärning stöds i körningsversion 4.4.0 och senare. Om din kunskapsbas skapades på en tidigare version [uppgradera din runtime](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) att använda den här funktionen. 

## <a name="best-practices"></a>Bästa praxis

Metodtips när du använder aktiv inlärning finns [bästa praxis](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Poäng nära mellan kunskapsbas frågor

När en fråga poängen är mycket säker på, till exempel 80%, är en uppsättning resultat som anses för aktiv inlärning breda, cirka inom 10%. Eftersom förtroendepoäng minskar, till exempel 40%, minskar antal poäng, cirka inom % 4. 

Algoritm och fastställa närhet är inte en enkel beräkning. Intervall i föregående exempel är inte avsedda att åtgärdas, men bör användas som en vägledning för att förstå effekten av algoritmen endast.

## <a name="turn-on-active-learning"></a>Aktivera aktiv inlärning

Aktiv inlärning är inaktiverat som standard. Aktivera den föreslagna frågor. 

1. Om du vill aktivera active learning på, klicka på din **namn**går du till [ **tjänstinställningar** ](https://www.qnamaker.ai/UserSettings) i QnA Maker-portalen i det övre högra hörnet.  

    ![På sidan för inställningar av tjänsten växla på aktiv inlärning](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Hitta QnA Maker-tjänsten och sedan växla **aktiv inlärning**. 

    [![På sidan för inställningar av tjänsten växla på aktiv inlärning](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    En gång **aktiv inlärning** är aktiverad, kunskap föreslår nya frågor med jämna mellanrum baserat på Användarinitierat frågor. Du kan inaktivera **aktiv inlärning** genom att ändra inställningen igen.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Lägg till aktiv inlärning förslag till kunskapsbas

1. Om du vill se de föreslagna frågorna på den **redigera** kunskapsbas markerar **visa förslag**. 

    [![Växla knappen Visa förslag på inställningssidan för tjänsten](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrera kunskapsbas med frågor och svar-par till att bara visa förslag genom att välja **filtrera efter förslag**.

    [![Filtrera efter förslag för att se på tjänstinställningssidan, bara de fråga/svar par](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Varje fråga med förslag visar nya frågor med en bock `✔` , för att godkänna frågan eller en `x` att avvisa förslagen. Klicka på bockmarkeringen för att lägga till frågan. 

    [![På sidan för inställningar av tjänsten växla på aktiv inlärning](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Du kan lägga till eller ta bort _alla förslag_ genom att välja **Lägg till alla** eller **avvisa alla**.

1. Välj **spara och träna** att spara ändringarna i kunskapsbasen.


## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>Fastställa bästa valet när flera frågor har liknande resultat

När en fråga är för nära andra frågor i poäng, kan klient-programmets utvecklare du be för information.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>Använd egenskapen längst upp i GenerateAnswer begäran

När du skickar en fråga till QnA Maker efter svar, kan en del av JSON-texten för att returnera fler än ett övre svar:

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

När klientprogrammet (till exempel en chattrobot) tar emot svaret, returneras på 3 viktiga frågor:

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Klienten programmet uppföljning när frågor har liknande resultat

Visar alla frågor i klientprogrammet med ett alternativ för att användaren väljer frågan som representerar sin avsikt. 

När användaren väljer en av de befintliga frågorna. Feedback från användare skickas till QnA Maker [träna](http://www.aka.ms/activelearningsamplebot) API för att fortsätta aktiv inlärning feedback-loop. 

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

Mer information om hur du använder aktiv inlärning med en [Azure Bot C# exempel](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot)

## <a name="next-steps"></a>Nästa steg
 
> [!div class="nextstepaction"]
> [Använd API för QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
