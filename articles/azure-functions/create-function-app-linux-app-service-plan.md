---
title: Skapa en funktionsapp på Linux från Azure-portalen
description: Lär dig hur du skapar din första Azure-funktion för serverfri körning i Azure Portal.
ms.topic: quickstart
ms.date: 02/28/2019
ms.openlocfilehash: 1492188f72eb4a691ddceb78aa269601f192e467
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76963843"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Skapa en funktionsapp på Linux i en Azure App Service-plan

Med hjälp av Azure Functions kan Linux användas som värd för funktionerna i en Azure App Service-standardcontainer. I den här artikeln får du lära dig hur du använder [Azure-portalen](https://portal.azure.com) för att skapa en Linux-värdfunktionsapp som körs i en [App Service-plan](functions-scale.md#app-service-plan). Du kan även [använda en egen anpassad container](functions-create-function-linux-custom-image.md).

![Skapa en funktionsapp i Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com> med ditt Azure-konto.

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner i Linux. Funktionsappen är en miljö för körning av funktionskoden. Det låter dig gruppera funktioner som en logisk enhet för enklare hantering, distribution, skalning och delning av resurser. I den här artikeln skapar du en App Service-plan när du skapar din funktionsapp.

1. Välj knappen **Skapa en resurs** längst upp till vänster i Azure-portalen och sedan **Beräkning** > **Funktionsapp**.

    ![Skapa en funktionsapp i Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Använd funktionsappinställningarna enligt tabellen under bilden.

    ![Definiera nya funktionsappinställningar](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Appnamn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den nya funktionsappen skapas under. | 
    | **[Resursgrupp](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Namnet på den nya resursgrupp där du vill skapa funktionsappen. |
    | **Operativsystem** | Linux | Funktionsappen körs på Linux. |
    | **Publicera** | Kod | Standardbehållaren för Linux för din **Runtime Stack** används. Allt du behöver ange är din funktion app projektkod. Ett annat alternativ är att publicera en anpassad [Docker-bild](functions-create-function-linux-custom-image.md). |
    | **[Hosting plan](functions-scale.md)** | App Service-plan | Värdplan som definierar hur resurser allokeras till din funktionsapp. När du kör i en apptjänstplan kan du styra [skalningen av funktionsappen](functions-scale.md).  |
    | **App Service-plan/plats** | Skapa plan | Välj **Skapa nytt** och ange ett namn på **apptjänstplanen.** Välj en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller i närheten av andra tjänster som dina funktioner har åtkomst till. Välj önskad **[prisnivå](https://azure.microsoft.com/pricing/details/app-service/linux/)**. <br/>Du kan inte köra både Linux- och Windows-funktionsappar i samma App Service-plan. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. Välj **.NET** för C#- och F#-funktioner. |
    | **[Lagring](../storage/common/storage-account-create.md)** |  Globalt unikt namn |  Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto, som måste uppfylla [kraven för ett lagringskonto](storage-considerations.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Enabled | Application Insights är inaktiverat som standard. Vi rekommenderar att du aktiverar integrering av Application Insights nu och väljer en värdplats nära din apptjänstplanplats. Om du vill göra detta senare läser du [Övervaka Azure Functions](functions-monitoring.md).  |

3. Välj **Skapa** för att etablera och distribuera funktionsappen.

4. Välj meddelandeikonen i det övre högra hörnet av portalen och titta efter meddelandet **Distribueringen lyckades**.

    ![Definiera nya funktionsappinställningar](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Välj **Gå till resurs** att visa den nya funktionsappen.

Därefter skapar du en funktion i den nya funktionsappen. Även efter att din funktionsapp är tillgänglig kan det ta några minuter att initiera.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Skapa en HTTP-utlöst funktion

I det här avsnittet visas hur du skapar en funktion i din nya funktionsapp i portalen.

> [!NOTE]
> Portalutvecklingsupplevelsen kan vara användbar för att prova Azure Functions. I de flesta scenarier bör du överväga att utveckla dina funktioner lokalt och publicera projektet i funktionsappen med visual [studiokod](functions-create-first-function-vs-code.md#create-an-azure-functions-project) eller [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. I den nya funktionsappen väljer du fliken **Översikt** och väljer + **Ny funktion**när den har läses in .

    ![Skapa en ny funktion på fliken Översikt](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. Välj **In-portal**på fliken **Snabbstart** och välj **Fortsätt**.

    ![Välj din funktionsutvecklingsplattform.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

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
