---
title: Aktiva inlärningsförslag - QnA Maker
description: Med aktiva inlärningsförslag kan du förbättra kvaliteten på din kunskapsbas genom att föreslå alternativa frågor, baserat på användarinlämningar, på din fråga och svarspar.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: edbe06b12fbb97473b28ccca968fd3e7d8366152
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804229"
---
# <a name="active-learning-suggestions"></a>Förslag på aktivt lärande

Med funktionen _Aktiva inlärningsförslag_ kan du förbättra kvaliteten på din kunskapsbas genom att föreslå alternativa frågor, baserat på användarinlämningar, på ditt fråga- och svarspar. Du granskar dessa förslag, antingen lägga till dem i befintliga frågor eller avvisa dem.

Din kunskapsbas ändras inte automatiskt. För att en ändring ska börja gälla måste du acceptera förslagen. Dessa förslag lägger till frågor men ändrar inte eller tar bort befintliga frågor.

## <a name="what-is-active-learning"></a>Vad är aktivt lärande?

QnA Maker lär sig nya frågevariationer med implicit och explicit feedback.

* [Implicit feedback](#how-qna-makers-implicit-feedback-works) – Rankern förstår när en användarfråga har flera svar med poäng som är mycket nära och betraktar detta som feedback. Du behöver inte göra något för att det här ska hända.
* [Explicit feedback](#how-you-give-explicit-feedback-with-the-train-api) – När flera svar med liten variation i poäng returneras från kunskapsbasen frågar klientprogrammet användaren vilken fråga som är rätt fråga. Användarens explicita feedback skickas till QnA Maker med [Train API](../How-to/improve-knowledge-base.md#train-api).

Båda metoderna ger rangordningen liknande frågor som är klustrade.

## <a name="how-active-learning-works"></a>Så här fungerar aktivt lärande

Aktiv inlärning utlöses baserat på poängen för de få svar som returneras av QnA Maker. Om poängskillnaderna mellan QnA-par som matchar frågan ligger inom ett litet intervall, betraktas frågan som ett möjligt förslag (som en alternativ fråga) för vart och ett av de möjliga QnA-paren. När du har accepterat den föreslagna frågan för ett specifikt QnA-par avvisas den för de andra paren. Du måste komma ihåg att spara och träna, efter att ha accepterat förslag.

Aktiv inlärning ger bästa möjliga förslag i de fall där slutpunkterna får en rimlig mängd och olika användningsfrågor. När 5 eller fler liknande frågor grupperas, var 30:e minut, föreslår QnA Maker de användarbaserade frågorna till kunskapsbasdesignern att acceptera eller avvisa. Alla förslag grupperas tillsammans efter likhet och de vanligaste förslagen för alternativa frågor visas baserat på frekvensen av de specifika frågorna av slutanvändare.

När frågor har föreslagits i QnA Maker-portalen måste du granska och acceptera eller avvisa dessa förslag. Det finns inget API för att hantera förslag.

## <a name="turn-on-active-learning"></a>Aktivera aktiv inlärning

Som standard är aktiv inlärning **inaktiverad**.
Så här använder du aktiv inlärning:
* Du måste [aktivera aktiv inlärning](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) så att QnA Maker samlar in alternativa frågor till din kunskapsbas.
* Om du vill se de föreslagna alternativa frågorna [använder du Visa alternativ](../How-To/improve-knowledge-base.md#view-suggested-questions) på sidan Redigera.

## <a name="how-qna-makers-implicit-feedback-works"></a>Så här fungerar QnA Maker implicita återkopplingar

QnA Maker implicita feedback använder en algoritm för att bestämma poäng närhet sedan gör aktiva lärande förslag. Algoritmen för att bestämma närhet är inte en enkel beräkning. Intervallen i följande exempel är inte avsedda att vara fasta, men bör användas som en guide för att förstå effekten av algoritmen endast.

När en frågas poäng är mycket säker, till exempel 80%, är intervallet av poäng som anses för aktivt lärande brett, ungefär inom 10%. När konfidenspoängen minskar, till exempel 40 %, minskar också poängintervallet, ungefär inom 4 %.

I följande JSON svar från en fråga till QnA Maker's generateAnswer, poängen för A, B och C är nära och skulle betraktas som förslag.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker vet inte vilket svar som är det bästa svaret. Använd QnA Maker-portalens lista med förslag för att välja det bästa svaret och träna igen.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Hur du ger explicit feedback med Train API

QnA Maker behöver tydlig feedback om vilket av svaren som var det bästa svaret. Hur det bästa svaret bestäms är upp till dig och kan innehålla:

* Feedback från användare genom att välja ett av svaren.
* Affärslogik, till exempel bestämma ett godtagbart poängintervall.
* En kombination av både användarfeedback och affärslogik.

Använd [Train API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) för att skicka rätt svar till QnA Maker, när användaren har valt det.

## <a name="next-step"></a>Nästa steg

> [!div class="nextstepaction"]
> [Fråga kunskapsbasen](query-knowledge-base.md)