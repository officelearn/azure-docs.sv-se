---
title: 'Självstudie: skapa, publicera och svara i QnA Maker'
description: Den här självstudien visar hur du skapar en ny kunskaps bas med frågor och svar från en offentlig webbaserad vanliga frågor och svar. Spara, träna och publicera kunskaps basen. När kunskaps basen har publicerats kan du skicka en fråga och få ett svar med ett spiral kommando. Skapa sedan en robot och testa roboten med samma fråga.
ms.topic: tutorial
ms.date: 01/28/2020
ms.openlocfilehash: 07a92b0f24aa95fc4a5188f54c7194fd342409fb
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844214"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Självstudie: skapa en kunskaps bas från QnA Maker Portal

Skapa en ny kunskaps bas med frågor och svar från en offentlig webbaserad vanliga frågor och svar. Spara, träna och publicera kunskaps basen. När kunskaps basen har publicerats kan du skicka en fråga och få ett svar med ett spiral kommando. Skapa sedan en robot och testa roboten med samma fråga.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en kunskaps bas i QnA Maker portalen.
> * Granska, Spara och träna kunskaps basen.
> * Publicera kunskaps basen.
> * Använd sväng för att fråga kunskaps basen.
> * Skapa en robot.


> [!NOTE]
> Den programmässiga versionen av den här självstudien finns med en kompletta lösning från GitHub-lagringsplatsen [**Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Krav

Den här självstudien kräver en befintlig [QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md).

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

1. Logga in i [QnA Maker](https://www.qnamaker.ai)-portalen.

1. Välj **Skapa en kunskapsbas** på den översta menyn.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av QnA Maker Portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Hoppa över det första steget eftersom du kommer att använda din befintliga QnA Maker-tjänst.

1. Välj dina befintliga inställningar:

    |Inställning|Syfte|
    |--|--|
    |Microsoft Azure katalog-ID|Detta ID är kopplat till det konto som du använder för att logga in på Azure Portal och QnA Maker portalen. |
    |Prenumerationsnamn för Azure|Det fakturerings konto där du skapade QnA Maker-resursen.|
    |Azure QnA Service|Din befintliga QnA Maker-resurs.|
    |Språk|Språk som används för alla kunskaps banker i tjänsten. Blanda inte språk i en enskild resurs. Det kommer att skada svars kvaliteten.|

    > [!div class="mx-imgBorder"]
    > ![skärm bild av QnA Maker Portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Ange namnet på din kunskaps bas `My Tutorial kb`.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av QnA Maker Portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Fyll i kunskaps basen med följande inställningar:

    |Inställningsnamn|Inställningsvärde|Syfte|
    |--|--|--|
    |Aktivera extrahering på flera sätt|Markerad|Tillåter uppföljnings instruktioner.|
    |Standard svars text|`No good answer was found.`|Returneras när det inte finns någon matchning till frågan.|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Innehållet i vanliga frågor och svar på den URL:en är formaterad med en fråga som följs av ett svar. QnA Maker kan tolka det här formatet för att extrahera frågor och tillhörande svar.|
    |Fil |_används inte i den här självstudien_|Det här laddar upp filer för frågor och svar. |
    |Kommunikationspersonlighet|Eget|Detta ger en användarvänlig och enhetlig [personlighet](../Concepts/best-practices.md#chit-chat) till vanliga frågor och svar. Du kan redigera dessa frågor och svar senare. |

    > [!div class="mx-imgBorder"]
    > ![skärm bild av QnA Maker Portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Välj alternativet för att **skapa kunskapsbasen** i skapandeprocessen.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av QnA Maker Portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Granska, spara och träna kunskapsbasen

1. Granska frågorna och svaren. Den första sidan är frågor och svar från URL: en.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av QnA Maker Portal](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Välj den sista sidan med frågor och svar längst ned i tabellen. På sidan visas frågor och svar från kommunikationspersonligheten.

1. Välj ikonen **visnings alternativ** i verktygsfältet ovanför listan med frågor och svar och välj sedan **Visa metadata**. Detta visar metadatataggar för varje fråga och svar. Kommunikationsfrågor har redan metadata **editorial: chit-chat** (redaktionellt: kommunikation) angett. Dessa metadata returneras till klient programmet, tillsammans med det valda svaret. Klientprogrammet, till exempel en chattrobot, kan använda dessa filtrerade metadata för att fastställa ytterligare bearbetning eller interaktioner med användaren.

    ![Skärm bild av QnA Maker Portal](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Välj alternativet för att **spara och träna** på den övre menyraden.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Publicera för att få kunskaps bas slut punkter

Välj knappen **Publicera** på den övre menyn. Välj **Publicera** på publiceringssidan.

![Skärm bild av QnA Maker Portal](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

När kunskaps basen har publicerats visas slut punkten.

![Skärm bild av slut punkts inställningar](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Stäng inte den här **publicerings** sidan. Du behöver den senare i självstudien för att skapa en bot.

## <a name="use-curl-to-query-for-an-faq-answer"></a>Använd klammer för att fråga efter svar på vanliga frågor

1. Välj fliken **Curl**.

    ![Skärm bild av fliken vändning](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Kopiera texten på fliken **vändning** och kör den i en sväng aktive rad terminal eller kommando rad. Authorization-huvudets värde inkluderar text `Endpoint`, med ett avslutande blank steg och sedan nyckeln.

1. Ersätt `<Your question>` med `How large can my KB be?`. Det här är nära frågan `How large a knowledge base can I create?` men inte exakt densamma. QnA Maker tillämpar bearbetning av naturligt språk för att fastställa att de två frågorna är desamma.

1. Kör kommandot vändning och ta emot JSON-svaret, inklusive poäng och svar.

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Använd sväng för att fråga efter ett "Chi2test"-svar

1. I den spiral-aktiverade terminalen ersätter du `How large can my KB be?` med en robot konversations slut kommentar från användaren, till exempel `Thank you`.

1. Kör kommandot vändning och ta emot JSON-svaret, inklusive poäng och svar.

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

    Eftersom frågan `Thank you` exakt matchar en kommunikationsfråga är QnA Maker helt säker med poängen 100. QnA Maker också returnerade alla relaterade frågor, samt egenskapen metadata som innehåller information om metadata tag gen för Chi2test.

## <a name="use-curl-to-query-for-the-default-answer"></a>Använd sväng för att fråga efter standard svaret

Alla frågor som QnA Maker inte är säkra på att ta emot standard svaret. Det här svaret har konfigurerats i Azure-portalen.

1. I den spiral-aktiverade terminalen ersätter du `Thank you` med `x`.

1. Kör kommandot vändning och ta emot JSON-svaret, inklusive poäng och svar.

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

    QnA Maker returnerade ett resultat av `0`, vilket innebär ingen exakthet. Den returnerade även standard svaret.

## <a name="create-a-knowledge-base-bot"></a>Skapa en kunskaps bas robot

Mer information finns i [skapa en chatt-robot med den här kunskaps basen](create-qna-bot.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med kunskaps bas roboten tar du bort resurs gruppen `my-tutorial-rg`för att ta bort alla Azure-resurser som skapats i bot-processen.

När du är färdig med kunskaps basen väljer du **Mina kunskaps**baser i QNA Maker-portalen. Välj sedan kunskaps basen, **min självstudie i KB**och välj Ta bort-ikonen längst till höger på raden.

## <a name="next-steps"></a>Nästa steg

Se [Datakällor som stöds](../Concepts/content-types.md) för mer information om filformat som stöds.

Mer information om [kommunikationspersonligheter](../Concepts/best-practices.md#chit-chat).

Mer information om standardsvaret finns i [Ingen matchning hittades](../Concepts/confidence-score.md#no-match-found).

> [!div class="nextstepaction"]
> [Skapa en chatt-robot med den här kunskaps basen](create-qna-bot.md)