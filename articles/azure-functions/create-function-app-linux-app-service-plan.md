---
title: Skapa en funktionsapp i Linux i Azure Portal | Microsoft Docs
description: Lär dig hur du skapar din första Azure-funktion för serverfri körning i Azure Portal.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: glenga
ms.custom: ''
ms.openlocfilehash: cc99bc4345c388f22e72957590f3917a85e214e0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126639"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Skapa en funktionsapp i Linux i en Azure App Service-plan

Med hjälp av Azure Functions kan Linux användas som värd för funktionerna i en Azure App Service-standardcontainer. Den här artikeln beskriver hur du använder den [Azure-portalen](https://portal.azure.com) att skapa en Linux-värd funktionsapp som körs i en [App Service-plan](functions-scale.md#app-service-plan). Du kan även [använda en egen anpassad container](functions-create-function-linux-custom-image.md).

![Skapa en funktionsapp i Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com> med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner i Linux. Funktionsappen är en miljö för körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. I den här artikeln skapar du en App Service plan när du skapar din funktionsapp.

1. Välj den **skapa en resurs** knappen hittades på det övre vänstra hörnet i Azure-portalen, sedan väljer **Compute** > **Funktionsapp**.

    ![Skapa en funktionsapp i Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Använd funktionsappinställningarna enligt tabellen under bilden.

    ![Definiera nya funktionsappinställningar](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appens namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. | 
    | **[Resursgrupp](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **OS** | Linux | Funktionsappen som körs på Linux. |
    | **Publicera** | Kod | Linux-behållare som standard för din **Körningsstack** används. Allt du behöver ange är din funktionsprojektkoden för app. Ett annat alternativ är att publicera en anpassad [Docker-avbildning](functions-create-function-linux-custom-image.md). |
    | **[Värdplan](functions-scale.md)** | App Service-plan | Värdplan som definierar hur resurser allokeras till din funktionsapp. När du kör i en App Service-plan, du kan styra den [skalning av funktionsappen](functions-scale.md).  |
    | **App Service-plan/plats** | Skapa plan | Välj **Skapa nytt** och ange en **App Service-plan** namn. Välj en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster åt dina funktioner. Välj din önskade  **[prisnivå](https://azure.microsoft.com/pricing/details/app-service/linux/)**. <br/>Du kan inte köra både Linux och Windows-funktionsappar i samma App Service-planen. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. [Stöd för Python](functions-reference-python.md) förhandsvisas just nu. |
    | **[Storage](../storage/common/storage-quickstart-create-account.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto, som måste uppfylla [kraven för ett lagringskonto](functions-scale.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Enabled | Application Insights är inaktiverad som standard. Vi rekommenderar att aktivera nu Application Insights-integrering och välja en värdplats nära din plats för App Service-plan. Om du vill göra det senare [övervaka Azure Functions](functions-monitoring.md).  |

3. Välj **Skapa** för att etablera och distribuera funktionsappen.

4. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

    ![Definiera nya funktionsappinställningar](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Välj **Gå till resurs** att visa den nya funktionsappen.

> [!TIP]
> Är det svårt att hitta dina funktionsappar i portalen kan du prova att [lägga till funktionsappar till dina favoriter i Azure-portalen](functions-how-to-use-azure-function-app-settings.md#favorite).

Därefter skapar du en funktion i den nya funktionsappen. Även när funktionsappen är tillgängligt, kan det ta ett par minuter måste initieras fullständigt.

## <a name="create-function"></a>Skapa en HTTP-utlöst funktion

Det här avsnittet visar hur du skapar en funktion i din nya funktionsapp i portalen.

> [!NOTE]
> Portalen utvecklingsupplevelse kan vara användbart för att prova Azure Functions. Överväg att utveckla dina funktioner lokalt och publicera projektet på din funktionsapp med hjälp av antingen för de flesta fall [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) eller [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. I din nya funktionsapp väljer du den **översikt** , och när den har lästs in helt Välj **+ ny funktion**.

    ![Skapa en ny funktion från fliken Översikt](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. I den **snabbstarten** fliken **i portalen**, och välj **Fortsätt**.

    ![Välj din plattform för utveckling av funktionen.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. Välj **WebHook + API** och välj sedan **Skapa**.

    ![Snabbstart för funktioner i Azure Portal.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

En funktion skapas med hjälp av en språkspecifik mall för en HTTP-utlöst funktion.

Nu kan du köra den nya funktionen genom att skicka en HTTP-begäran.

## <a name="test-the-function"></a>Testa funktionen

1. I den nya funktionen klickar du på **</> Hämta funktionswebbadress** längst upp till höger och väljer **Standard (funktionsnyckel)**. Sedan klickar du på **Kopiera**. 

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
