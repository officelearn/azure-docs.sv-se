---
title: Skapa en Function-app på Linux från Azure Portal
description: Lär dig hur du skapar din första Azure-funktion för serverfri körning i Azure Portal.
ms.topic: quickstart
ms.date: 02/28/2019
ms.openlocfilehash: fdc2d9c7b5945e48cc87f3edd918498c3d45f55e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233094"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Skapa en Function-app i Linux i ett Azure App Service plan

Med hjälp av Azure Functions kan Linux användas som värd för funktionerna i en Azure App Service-standardcontainer. Den här artikeln beskriver hur du använder [Azure Portal](https://portal.azure.com) för att skapa en Linux-värdbaserad Function-app som körs i en [App Service plan](functions-scale.md#app-service-plan). Du kan även [använda en egen anpassad container](functions-create-function-linux-custom-image.md).

![Skapa en funktionsapp i Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com> med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner i Linux. Funktionsappen är en miljö för körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. I den här artikeln skapar du en App Service plan när du skapar din Function-app.

1. Välj knappen **skapa en resurs** i det övre vänstra hörnet av Azure Portal och välj sedan **Compute** > **Funktionsapp**.

    ![Skapa en funktionsapp i Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Använd funktionsappinställningarna enligt tabellen under bilden.

    ![Definiera nya funktionsappinställningar](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. | 
    | **[Resursgrupp](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **OS** | Linux | Function-appen körs på Linux. |
    | **Publicera** | Kod | Standard-Linux-behållaren för **körnings stacken** används. Allt du behöver ange är din projekt kod för Function-appen. Ett annat alternativ är att publicera en anpassad [Docker-avbildning](functions-create-function-linux-custom-image.md). |
    | **[Värdplan](functions-scale.md)** | App Service-plan | Värdplan som definierar hur resurser allokeras till din funktionsapp. När du kör i ett App Service plan kan du kontrol lera [skalningen för din Function-app](functions-scale.md).  |
    | **App Service plan/plats** | Skapa plan | Välj **Skapa nytt** och ange ett **App Service plan** namn. Välj en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som dina funktioner har åtkomst till. Välj önskad **[pris nivå](https://azure.microsoft.com/pricing/details/app-service/linux/)** . <br/>Du kan inte köra både Linux-och Windows-funktionella appar i samma App Service plan. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. |
    | **[Storage](../storage/common/storage-quickstart-create-account.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto, som måste uppfylla [kraven för ett lagringskonto](functions-scale.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Enabled | Application Insights är inaktiverat som standard. Vi rekommenderar att du aktiverar Application Insights integration nu och väljer en värd plats nära din App Service plan plats. Om du vill göra detta senare, se [övervaka Azure Functions](functions-monitoring.md).  |

3. Välj **Skapa** för att etablera och distribuera funktionsappen.

4. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

    ![Definiera nya funktionsappinställningar](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Välj **Gå till resurs** att visa den nya funktionsappen.

Därefter skapar du en funktion i den nya funktionsappen. Även efter att din Function-app är tillgänglig kan det ta några minuter innan den är helt initierad.

## <a name="create-function"></a>Skapa en HTTP-utlöst funktion

Det här avsnittet visar hur du skapar en funktion i din nya Function-app i portalen.

> [!NOTE]
> Portal utvecklings upplevelsen kan vara användbar för att testa Azure Functions. I de flesta fall bör du överväga att utveckla dina funktioner lokalt och publicera projektet till din Function-app med hjälp av [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) eller [Azure Functions Core tools](functions-run-local.md#create-a-local-functions-project).  

1. I din nya Function-app väljer du fliken **Översikt** och när den har lästs in helt väljer du **+ ny funktion**.

    ![Skapa en ny funktion från fliken Översikt](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. På fliken **snabb start** väljer du **i-portalen**och väljer **Fortsätt**.

    ![Välj funktions utvecklings plattform.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. Välj **WebHook + API** och välj sedan **Skapa**.

    ![Snabbstart för funktioner i Azure Portal.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

En funktion skapas med hjälp av en språkspecifik mall för en HTTP-utlöst funktion.

Nu kan du köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. I den nya funktionen klickar du på **</> Hämta funktionswebbadress** längst upp till höger och väljer **Standard (funktionsnyckel)** . Sedan klickar du på **Kopiera**. 

    ![Kopiera funktionswebbadressen från Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngvärdet `&name=<yourname>` i slutet av den här webbadressen och tryck på knappen `Enter` på tangentbordet för att utföra begäran. Du bör se svaret som returnerades av funktionen som visas i webbläsaren.  

    I följande exempel visas svaret i webbläsaren:

    ![Funktionssvar i webbläsaren.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    Begäransadressen innehåller en nyckel som krävs för åtkomst till din funktion över HTTP.

3. När din funktion körs skrivs spårningsinformation till loggarna. Om du vill visa spårningsinformationen från föregående körning återgår du till funktionen i portalen och klickar på pilen längst ned på skärmen så att **Loggar** expanderas.

   ![Funktionsloggvisning i Azure Portal.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har nu skapat en funktionsapp med en enkel HTTP-utlöst funktion.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Mer information finns i [Azure Functions HTTP bindings](functions-bindings-http-webhook.md) (HTTP-bindningar i Azure Functions).
