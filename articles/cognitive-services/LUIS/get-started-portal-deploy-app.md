---
title: 'Snabbstart: Distribuera app med LUIS-portalen'
titleSuffix: Language Understanding - Azure Cognitive Services
description: När appen är redo att gå tillbaka uttryck förutsägelser till ett klientprogram, till exempel en chattrobot måste du distribuera appen till slutpunkten för förutsägelse. I den här snabbstarten får du lära dig att distribuera ett program genom att skapa en förutsägelse slutpunktsresurs, tilldela resursen till appen, träna appen och publicera appen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cf7464188bb502d77c673284cba226ce91a5725
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259728"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Snabbstart: Distribuera en app i LUIS-portalen

När appen är redo att gå tillbaka uttryck förutsägelser till ett klientprogram, till exempel en chattrobot måste du distribuera appen till slutpunkten för förutsägelse. 

I den här snabbstarten får du lära dig att distribuera ett program genom att skapa en förutsägelse slutpunktsresurs, tilldela resursen till appen, träna appen och publicera appen. 

## <a name="prerequisites"></a>Förutsättningar

* [Azure-prenumeration](https://azure.microsoft.com/free).
* Slutför den [tidigare snabbstarten](get-started-portal-build-app.md) eller [hämta och importera appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json). 


## <a name="create-endpoint-resource"></a>Skapa slutpunktsresurs

Slutpunktsresurs förutsägelse skapas i Azure-portalen. Den här resursen bör endast användas för slutpunkt-förutsägelsefrågor. Använd inte den här resursen för redigering ändringar till appen. 

1. Logga in på den  **[Azure-portalen](https://ms.portal.azure.com/)**. 

1. Välj gröna **+** logga i den övre vänstra panelen och Sök efter `Cognitive Services` på marketplace, Välj den sedan. 

1. Konfigurera prenumerationen med följande inställningar:

    |Inställning|Värde|Syfte|
    |--|--|--|
    |Namn|`my-cognitive-service-resource`|Namnet på Azure-resursen. Du behöver det här namnet när du tilldelar resursen till appen i LUIS-portalen.|
    |Prenumeration|Din prenumeration|Välj en av de prenumerationer som är associerade med ditt konto.|
    |Plats|**USA, västra**|Azure-regionen för den här resursen.|
    |Prisnivå|**S0**|Standard som prisnivå för den här resursen.|
    |Resursgrupp|`my-cognitive-service-resource-group`|Skapa en ny resursgrupp för alla cognitive serviceresurser. När du är klar med resurser, kan du ta bort resursgrupp att rensa upp din prenumeration. | 

    ![Azure API-alternativ](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png) 

1. Välj **skapa** att skapa Azure-resursen. 

    Lär dig hur du ansluter den här nya resursen till en LUIS-app i LUIS-portalen i nästa avsnitt. 

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Tilldela Resursnyckeln LUIS-app i LUIS-portalen

Du måste tilldela resursen till LUIS-app varje gång du skapar en ny resurs för LUIS. När den är tilldelad, behöver du inte göra det här steget igen om du inte skapar en ny resurs. Du kan skapa en ny resurs att utöka regionerna som din app eller för ett högre antal förutsägelsefrågor. 

1. Logga in på den [LUIS portal](https://www.luis.ai), Välj den **myEnglishApp** app från applistan.

1. Välj **hantera** i menyn längst upp till höger och markera **nycklar och slutpunkter**.

1. För att lägga till LUIS, välja **Tilldela resurs +**.

    [![Tilldela en resurs till din app](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Välj namnet på din klient, prenumeration och resursgrupp och välj sedan **Tilldela resurs**. 

    ![Tilldela en resurs till din app](./media/get-started-portal-deploy-app/assign-resource.png)

1. Hitta den nya raden i tabellen och kopiera slutpunkts-URL. Det är korrekt konstruerat för att göra en HTTP GET-begäran för LUIS-API-slutpunkt för en förutsägelse. 

## <a name="train-and-publish-the-app"></a>Träna och publicera appen 

Du bör skapa när du är redo att testa den. När du vill att den för närvarande tränade versionen för att vara tillgängliga för klientprogram från slutpunkt-runtime förutsägelse bör du publicera appen. 

1. Om appen är omdöme väljer **träna** direkt från överst menyn.

1. Välj **publicera** direkt från överst menyn. Acceptera standardinställningarna för miljön och välj **publicera**.

1. När meddelandefältet grönt visas överst i fönstret väljer du den **finns i listan över slutpunkter** länk. 

    ![Publicerades app meddelandefältet i webbläsare](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Då kommer du till den **nycklar och slutpunkten inställningar** sidan. Listan över tilldelade resurser och motsvarande slutpunkt URL: er är längst ned på sidan. 

1. Välj slutpunkts-URL som är associerade med din nytt resursnamn. Då öppnas en webbläsare med en korrekt skapade URL: en att göra en **hämta** begäran till slutpunkten-körningen förutsägelse. 

1. Den `q=` i slutet av URL: en är kort för **fråga** och är där användarens uttryck läggs till GET-begäran. Efter den `q=`, ange den samma användare-uttryck som används i slutet av den förra snabbstarten:

    ```Is there a form named hrf-234098```

    Webbläsaren svaret är samma JSON klientprogrammet får:

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    Det här svaret får du mer information än standardvärdet **Test** fönstret i föregående självstudie. Om du vill se det här samma nivå av informationen i rutan måste appen publiceras. I rutan, markera **Jämför med publicerade**. Använd **visa JSON-vy** i testfönstret publicerad finns i samma JSON som i föregående steg. På så sätt kan du jämföra den aktuella appen som du arbetar med och den app som har publicerats till slutpunkten.

    [![Jämför redigerar jämfört med publicerade versionen av appen](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)


## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med den här snabbstarten, Välj **Mina appar** från den övre navigeringsmenyn. Sedan markerar appens vänstra kryssrutan i listan och välj **ta bort** från kontexten verktygsfältet ovanför listan. 

[![Ta bort appen från listan över Mina appar](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera vanliga avsikter och entiteter](luis-tutorial-prebuilt-intents-entities.md)