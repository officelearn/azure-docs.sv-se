---
title: Skapa, publicera, besvara i QnA Maker
titleSuffix: Azure Cognitive Services
description: Skapa en ny kunskapsbas med frågor och svar från en offentlig webbaserade vanliga frågor och svar. Spara, träna och publicera i knowledge base. När kunskapsbasen har publicerats kan skicka en fråga och ta emot ett svar med ett CURL-kommando. Sedan skapa en robot och testa bot med samma fråga.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: diberry
ms.openlocfilehash: 299dd61055503f0b5a11cbe97e137e4760edadda
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266961"
---
# <a name="tutorial-from-qna-maker-portal-create-a-knowledge-base"></a>Självstudier: Med QnA Maker portal kan du skapa en kunskapsbas

Skapa en ny kunskapsbas med frågor och svar från en offentlig webbaserade vanliga frågor och svar. Spara, träna och publicera i knowledge base. När kunskapsbasen har publicerats kan skicka en fråga och ta emot ett svar med ett Curl-kommando. Sedan skapa en robot och testa bot med samma fråga. 

I den här guiden får du lära dig att: 

> [!div class="checklist"]
> * Skapa en kunskapsbas i QnA Maker-portalen
> * Granska, spara och träna kunskapsbasen
> * Publicera kunskapsbasen
> * Använda Curl för att köra frågor mot kunskapsbasen
> * Skapa en robot
> 
> [!NOTE]
> Den programmässiga versionen av den här självstudien finns med en kompletta lösning från GitHub-lagringsplatsen [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien kräver en befintlig [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md). 

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas 

1. Logga in i [QnA Maker](https://www.qnamaker.ai)-portalen. 

1. Välj **Skapa en kunskapsbas** på den översta menyn.

    ![Steg 1 i skapandeprocessen för kunskapsbas](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Hoppa över det första steget eftersom du kommer att använda din befintliga QnA Maker-tjänst. 

1. Välj de befintliga inställningarna i nästa steg:  

    |Inställning|Syfte|
    |--|--|
    |Microsoft Azure Directory Id|Din _ID för Microsoft Azure Directory_ är associerad med det konto som används för att logga in på Azure-portalen och QnA Maker-portalen. |
    |Prenumerationsnamn för Azure|Faktureringskontot du skapade QnA Maker-resursen i.|
    |Azure QnA Service|Din befintliga QnA Maker-resurs.|

    ![Steg 2 i skapandeprocessen för kunskapsbas](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. I nästa steg anger du namnet på din kunskapsbas, `My Tutorial kb`.

    ![Steg 3 i skapandeprocessen för kunskapsbas](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. I nästa steg ska du fylla i din kunskapsbas med följande inställningar:  

    |Inställningsnamn|Inställningsvärde|Syfte|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Innehållet i vanliga frågor och svar på den URL:en är formaterad med en fråga som följs av ett svar. QnA Maker kan tolka det här formatet för att extrahera frågor och tillhörande svar.|
    |Fil |_inte används i den här självstudien_|Det här laddar upp filer för frågor och svar. |
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

Stäng inte det här **publicera** kan du använda den för att skapa en robot senare under kursen. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Använd Curl till att fråga efter ett svar på vanliga frågor och svar

1. Välj fliken **Curl**. 

    ![Curl-kommando](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Kopiera texten för fliken **Curl** och kör i en Curl-aktiverad terminal eller på eller kommandoraden. Auktoriseringsrubrikens värde innehåller texten `Endpoint` med ett avslutande blanksteg och därefter nyckeln.

1. Ersätt `<Your question>` med `How large can my KB be?`. Det här är nära frågan `How large a knowledge base can I create?` men inte exakt densamma. QnA Maker tillämpar bearbetning av naturligt språk för att fastställa att de två frågorna är desamma.     

1. Köra Curl-kommando och få JSON-svar inklusive poäng och svar. 

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Använd Curl till att fråga efter en Chit-chatt-svar

1. I terminalen Curl-aktiverade ersätter `How large can my KB be?` med en bot konversationen slut instruktion från användare, till exempel `Thank you`.   

1. Köra Curl-kommando och få JSON-svar inklusive poäng och svar. 

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

## <a name="use-curl-to-query-for-the-default-answer"></a>Använd Curl till att fråga efter Standardsvar

Alla frågor som QnA Maker inte är säker på i ett svar får standardsvaret. Det här svaret har konfigurerats i Azure-portalen. 

1. I den Curl-aktiverade terminalen ersätter du `Thank you` med `x`. 

1. Köra Curl-kommando och få JSON-svar inklusive poäng och svar. 

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
    
    QnA Maker returnerade ett resultat på `0`, vilket innebär att inga förtroende, men den också returnerade standard-svaret. 

## <a name="create-a-knowledge-base-bot"></a>Skapa en kunskapsbas-robot

Mer information finns i [skapa en chattrobot med den här kunskapsbas](create-qna-bot.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med knowledge base-robot, tar du bort resursgruppen, `my-tutorial-rg`, för att ta bort alla Azure-resurser skapas i bot-processen.

När du är klar med kunskapsbas i QnA Maker-portalen väljer du **min kunskapsbaser**, välj sedan kunskapsbasen, **Mina självstudien kb**, välj ikonen Ta bort längst till höger på den raden.  

## <a name="next-steps"></a>Nästa steg

Mer information finns i [datakällor som stöds](../Concepts/data-sources-supported.md) för mer information om support-filformat. 

Mer information om [kommunikationspersonligheter](../Concepts/best-practices.md#chit-chat).

Mer information om standardsvaret finns i [Ingen matchning hittades](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Skapa en chattrobot med den här kunskapsbas](create-qna-bot.md)