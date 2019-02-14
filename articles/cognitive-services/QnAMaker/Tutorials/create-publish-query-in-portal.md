---
title: Skapa, publicera, besvara i QnA Maker
titleSuffix: Azure Cognitive Services
description: Den här portalbaserade självstudien vägleder dig genom att programmatiskt skapa och publicera en kunskapsbas och sedan besvara en fråga från kunskapsbasen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: b78c19d4fa6b2e5b69e5d7f46cea51d943b808fa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882156"
---
# <a name="tutorial-create-a-knowledge-base-then-answer-question-via-the-qna-maker-portal"></a>Självstudier: Skapa en kunskapsbas och besvara sedan fråga via QnA Maker-portalen

Den här självstudien vägleder dig genom att skapa och publicera en kunskapsbas och sedan besvara en fråga från kunskapsbasen.

I den här guiden får du lära dig att: 

> [!div class="checklist"]
* Skapa en kunskapsbas i QnA Maker-portalen
* Granska, spara och träna kunskapsbasen
* Publicera kunskapsbasen
* Använda Curl för att köra frågor mot kunskapsbasen

> [!NOTE] 
> Den programmässiga versionen av den här självstudien finns med en kompletta lösning från GitHub-lagringsplatsen [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien kräver en befintlig [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). 

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas 

1. Logga in i [QnA Maker](https://www.qnamaker.ai)-portalen. 

1. Välj **Skapa en kunskapsbas** på den översta menyn.

    ![Steg 1 i skapandeprocessen för kunskapsbas](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Hoppa över det första steget eftersom du kommer att använda din befintliga QnA Maker-tjänst. 

1. Välj de befintliga inställningarna i nästa steg:  

    |Inställning|Syfte|
    |--|--|
    |Microsoft Azure Directory Id|Ditt _Id för Microsoft Azure Directory_ är associerat med det konto som används för att logga in på Azure-portalen och QnA Maker-portalen. |
    |Prenumerationsnamn för Azure|Faktureringskontot du skapade QnA Maker-resursen i.|
    |Azure QnA Service|Din befintliga QnA Maker-resurs.|

    ![Steg 2 i skapandeprocessen för kunskapsbas](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. I nästa steg anger du namnet på din kunskapsbas, `My Tutorial kb`.

    ![Steg 3 i skapandeprocessen för kunskapsbas](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. I nästa steg ska du fylla i din kunskapsbas med följande inställningar:  

    |Inställningsnamn|Inställningsvärde|Syfte|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Innehållet i vanliga frågor och svar på den URL:en är formaterad med en fråga som följs av ett svar. QnA Maker kan tolka det här formatet för att extrahera frågor och tillhörande svar.|
    |Fil |_används inte i den här självstudien_|Det här laddar upp filer för frågor och svar. |
    |Kommunikationspersonlighet|Vännen|Detta ger en informell och avslappnad personlighet för vanliga frågor och svar. Du kan redigera dessa frågor och svar senare. |

    ![Steg 4 i skapandeprocessen för kunskapsbas](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Välj alternativet för att **skapa kunskapsbasen** i skapandeprocessen.

    ![Steg 5 i skapandeprocessen för kunskapsbas](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-kb-save-and-train"></a>Granska kunskapsbas, spara och träna

1. Granska frågorna och svaren. Den första sidan är frågor och svar från URL: en. 

    ![Spara och öva](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Välj den sista sidan med frågor och svar längst ned i tabellen. På sidan visas frågor och svar från kommunikationspersonligheten. 

1. Välj metadataikonen från verktygsfältet ovanför listan med frågor och svar. Detta visar metadatataggar för varje fråga och svar. Kommunikationsfrågor har redan metadata **editorial: chit-chat** (redaktionellt: kommunikation) angett. Dessa metadata returneras till klientprogrammet tillsammans med det valda svaret. Klientprogrammet, till exempel en chattrobot, kan använda dessa filtrerade metadata för att fastställa ytterligare bearbetning eller interaktioner med användaren.

    ![Visa metadatataggar](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Välj alternativet för att **spara och träna** på den övre menyraden.

## <a name="publish-to-get-kb-endpoints"></a>Publicera för att få KB-slutpunkter

Välj knappen **Publicera** på den övre menyn. När du är på publiceringssidan väljer du **Publicera** bredvid knappen **Avbryt**.

![Publicera](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

När kunskapsbasen har publicerats visas slutpunkten

![Publicera sidans slutpunktsinställningar](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

## <a name="use-curl-to-query-for-an-faq-answer"></a>Använd curl för att fråga efter ett FAQ-svar

1. Välj fliken **Curl**. 

    ![Curl-kommando](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Kopiera texten för fliken **Curl** och kör i en Curl-aktiverad terminal eller på eller kommandoraden. Auktoriseringsrubrikens värde innehåller texten `Endpoint ` med ett avslutande blanksteg och därefter nyckeln.

1. Ersätt `<Your question>` med `How large can my KB be?`. Det här är nära frågan `How large a knowledge base can I create?` men inte exakt densamma. QnA Maker tillämpar bearbetning av naturligt språk för att fastställa att de två frågorna är desamma.     

1. Köra CURL-kommando och få JSON-svar inklusive poäng och svar. 

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Använda curl för att fråga efter ett kommunikationssvar

1. I den Curl-aktiverade terminalen ersätter du `How large can my KB be?` med en robots konversationsavslutande instruktion från användaren, till exempel `Thank you`.   

1. Köra CURL-kommando och få JSON-svar inklusive poäng och svar. 

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

    Eftersom frågan `Thank you` exakt matchar en kommunikationsfråga är QnA Maker helt säker med poängen 100. QnA Maker returnerade även alla relaterade frågor samt den metadata-egenskap som innehåller information om metadatataggen för kommunikation.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Använda curl för att fråga efter standardsvar

Alla frågor som QnA Maker inte är säker på i ett svar får standardsvaret. Det här svaret har konfigurerats i Azure-portalen. 

1. I den Curl-aktiverade terminalen ersätter du `Thank you` med `x`. 

1. Köra CURL-kommando och få JSON-svar inklusive poäng och svar. 

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
    
    QnA Maker returnerade ett resultat på 0 vilket betyder att inget förtroende, men även standardsvaret returnerades. 

## <a name="next-steps"></a>Nästa steg

Se [Datakällor som stöds](../Concepts/data-sources-supported.md) för mer information om filformat som stöds. 

Mer information om [kommunikationspersonligheter](../Concepts/best-practices.md#chit-chat).

Mer information om standardsvaret finns i [Ingen matchning hittades](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Kunskapsbasbegrepp](../Concepts/knowledge-base.md)