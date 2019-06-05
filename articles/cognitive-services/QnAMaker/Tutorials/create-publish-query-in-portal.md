---
title: Skapa, publicera och besvara i QnA Maker
titleSuffix: Azure Cognitive Services
description: Skapa en ny kunskapsbas med frågor och svar från en offentlig webbaserade vanliga frågor och svar. Spara, träna och publicera i knowledge base. När kunskapsbasen har publicerats kan skicka en fråga och ta emot ett svar med ett cURL-kommando. Sedan skapa en robot och testa bot med samma fråga.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a13e0cb0e594571344b16d007ef13475b384b73d
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693022"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Självstudier: Skapa en kunskapsbas från QnA Maker-portalen

Skapa en ny kunskapsbas med frågor och svar från en offentlig webbaserade vanliga frågor och svar. Spara, träna och publicera i knowledge base. När kunskapsbasen har publicerats kan skicka en fråga och ta emot ett svar med ett cURL-kommando. Sedan skapa en robot och testa bot med samma fråga. 

I den här guiden får du lära dig att: 

> [!div class="checklist"]
> * Skapa en kunskapsbas i QnA Maker-portalen.
> * Granska, spara och tränar kunskapsbasen.
> * Publicera i knowledge base.
> * Använda cURL för att söka i knowledge base.
> * Skapa en robot.
 

> [!NOTE]
> Den programmässiga versionen av den här självstudien finns med en kompletta lösning från GitHub-lagringsplatsen [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien kräver en befintlig [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). 

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas 

1. Logga in i [QnA Maker](https://www.qnamaker.ai)-portalen. 

1. Välj **Skapa en kunskapsbas** på den översta menyn.

    ![Skärmbild av QnA Maker-portalen](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Hoppa över det första steget, eftersom du kommer att använda din befintliga QnA Maker-tjänsten. 

1. Välj dina befintliga inställningar:  

    |Inställning|Syfte|
    |--|--|
    |Microsoft Azure Directory ID|Detta ID är associerad med det konto som används för att logga in på Azure-portalen och QnA Maker-portalen. |
    |Prenumerationsnamn för Azure|Faktureringskonto där du skapade QnA Maker-resursen.|
    |Azure QnA Service|Din befintliga QnA Maker-resurs.|

    ![Skärmbild av QnA Maker-portalen](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Ange namnet på din kunskapsbas `My Tutorial kb`.

    ![Skärmbild av QnA Maker-portalen](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Fyll i kunskapsbasen med följande inställningar:  

    |Inställningsnamn|Inställningsvärde|Syfte|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Innehållet i vanliga frågor och svar på den URL:en är formaterad med en fråga som följs av ett svar. QnA Maker kan tolka det här formatet för att extrahera frågor och tillhörande svar.|
    |Fil |_används inte i den här självstudien_|Det här laddar upp filer för frågor och svar. |
    |Kommunikationspersonlighet|Eget|Detta ger en vänlig och en Vardaglig [personlighet](../Concepts/best-practices.md#chit-chat) till vanliga frågor och svar. Du kan redigera dessa frågor och svar senare. |

    ![Skärmbild av QnA Maker-portalen](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Välj alternativet för att **skapa kunskapsbasen** i skapandeprocessen.

    ![Skärmbild av QnA Maker-portalen](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Granska, spara och träna kunskapsbasen

1. Granska frågorna och svaren. Den första sidan är frågor och svar från URL: en. 

    ![Skärmbild av QnA Maker-portalen](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Välj den sista sidan med frågor och svar längst ned i tabellen. På sidan visas frågor och svar från kommunikationspersonligheten. 

1. I verktygsfältet ovanför listan med frågor och svar, Välj den **Visningsalternativ** ikonen och välj sedan **visa metadata**. Detta visar metadatataggar för varje fråga och svar. Kommunikationsfrågor har redan metadata **editorial: chit-chat** (redaktionellt: kommunikation) angett. Den här metadata som returneras till klientprogrammet, tillsammans med det valda svaret. Klientprogrammet, till exempel en chattrobot, kan använda dessa filtrerade metadata för att fastställa ytterligare bearbetning eller interaktioner med användaren.

    ![Skärmbild av QnA Maker-portalen](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Välj alternativet för att **spara och träna** på den övre menyraden.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Publicera för att få kunskapsbas slutpunkter

Välj knappen **Publicera** på den övre menyn. Välj **Publicera** på publiceringssidan.

![Skärmbild av QnA Maker-portalen](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

När kunskapsbasen har publicerats visas slutpunkten.

![Skärmbild av endpoint-inställningar](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Stäng inte det här **publicera** sidan. Du behöver den senare i guiden, för att skapa en robot. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Använd cURL för att fråga efter ett svar på vanliga frågor och svar

1. Välj fliken **Curl**. 

    ![Skärmbild av Curl fliken](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Kopiera texten för den **Curl** fliken och köra den i ett cURL-aktiverade terminal eller kommandoraden. Auktorisering sidhuvudets värde innehåller texten `Endpoint`, med ett avslutande blanksteg och sedan nyckeln.

1. Ersätt `<Your question>` med `How large can my KB be?`. Det här är nära frågan `How large a knowledge base can I create?` men inte exakt densamma. QnA Maker tillämpar bearbetning av naturligt språk för att fastställa att de två frågorna är desamma.     

1. Köra cURL-kommando och få JSON-svar, inklusive poäng och svar. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    QnA Maker är ganska trygg med poängen 42,81 %.  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Använda cURL för att fråga efter en Chit-chatt-svar

1. I terminalen cURL-aktiverade ersätter `How large can my KB be?` med en bot konversationen slut instruktion från användare, till exempel `Thank you`.   

1. Köra cURL-kommando och få JSON-svar, inklusive poäng och svar. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    Eftersom frågan `Thank you` exakt matchar en kommunikationsfråga är QnA Maker helt säker med poängen 100. QnA Maker returneras alla relaterade frågor, samt metadata-egenskap som innehåller Chit-chatt taggen metadatainformation.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Använda cURL till frågan för standardsvar

Alla frågor som QnA Maker inte är säker på om får Standardsvar. Det här svaret har konfigurerats i Azure-portalen. 

1. I terminalen cURL-aktiverade ersätter `Thank you` med `x`. 

1. Köra cURL-kommando och få JSON-svar, inklusive poäng och svar. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    QnA Maker returnerade ett resultat på `0`, vilket innebär att inga förtroende. Den returnerade även standard-svaret. 

## <a name="create-a-knowledge-base-bot"></a>Skapa en kunskapsbas-robot

Mer information finns i [skapa en chattrobot med den här kunskapsbas](create-qna-bot.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med knowledge base-robot, tar du bort resursgruppen, `my-tutorial-rg`, för att ta bort alla Azure-resurser skapas i bot-processen.

När du är klar med kunskapsbas i QnA Maker-portalen väljer du **min kunskapsbaser**. Välj sedan kunskapsbasen, **Mina självstudien kb**, och välj ikonen Ta bort längst till höger på den raden.  

## <a name="next-steps"></a>Nästa steg

Se [Datakällor som stöds](../Concepts/data-sources-supported.md) för mer information om filformat som stöds. 

Mer information om [kommunikationspersonligheter](../Concepts/best-practices.md#chit-chat).

Mer information om standardsvaret finns i [Ingen matchning hittades](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Skapa en chattrobot med den här kunskapsbas](create-qna-bot.md)