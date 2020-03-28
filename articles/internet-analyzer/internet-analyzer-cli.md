---
title: Skapa ett Internet Analyzer-test med CLI | Microsoft-dokument
description: I den här artikeln kan du läsa om hur du skapar ditt första Internet Analyzer-test.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: d474442086e2a114f26df279ab2682cd7628a5f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74184268"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Skapa ett Internet Analyzer-test med CLI (Preview)

Det finns två sätt att skapa en Internet Analyzer-resurs – med hjälp av [Azure-portalen](internet-analyzer-create-test-portal.md) eller med CLI.There are two ways to create an Internet Analyzer resource - using the Azure portal or using CLI. Det här avsnittet hjälper dig att skapa en ny Azure Internet Analyzer-resurs med hjälp av vår CLI-upplevelse. 


> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Den offentliga förhandsversionen är tillgänglig för användning globalt. Datalagring är dock begränsad till *US West 2* under förhandsversionen.

## <a name="object-model"></a>Objektmodell
Internet Analyzer CLI exponerar följande typer av resurser:
* **Tester** - Ett test jämför slutanvändarens prestanda för två internetslutpunkter (A och B) över tiden.
* **Profiler** - Tester skapas under en Internet Analyzer-profil. Profiler gör det möjligt att gruppera relaterade tester. en enda profil kan innehålla ett eller flera tester.
* **Förkonfigurerade slutpunkter** - Vi har ställt in slutpunkter med en mängd olika konfigurationer (regioner, accelerationsteknik osv.). Du kan använda någon av dessa förkonfigurerade slutpunkter i dina tester.
* **Styrkort** - Ett styrkort ger snabba och meningsfulla sammanfattningar av mätresultat. Se [Tolka ditt styrkort](internet-analyzer-scorecard.md).
* **Tidsserie -** En tidsserie visar hur ett mått ändras över tid.

## <a name="profile-and-test-creation"></a>Profil- och testskapande
1. Få åtkomst till förhandsversionen av Internet [Analyzer](internet-analyzer-faq.md)genom att följa **instruktionerna för hur deltar jag i förhandsversionen?**
2. [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
3. Kör `login` kommandot för att starta en CLI-session:
    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standardwebbläsare kommer den att göra det och läsa in en Azure-inloggningssida.
    Annars öppnar du en https://aka.ms/devicelogin webbläsarsida och anger auktoriseringskoden som visas i terminalen.

4. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

5. Välj ditt prenumerations-ID som har beviljats åtkomst till den offentliga förhandsversionen av Internet Analyzer.

    När du har loggat in visas en lista över prenumerationer som är kopplade till ditt Azure-konto. Prenumerationsinformationen `isDefault: true` med är den aktuella aktiverade prenumerationen efter inloggning. Om du vill välja en annan prenumeration använder du kommandot [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) med prenumerations-ID:n för att växla till. Mer information om prenumerationsval finns i [Använda flera Azure-prenumerationer](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).

    Det finns några olika sätt att logga in som inte är interaktiva. Läs mer i [Logga in med Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

6. **[Valfritt]** Skapa en ny Azure Resource Group:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Installera Azure CLI Internet Analyzer-tillägget:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Skapa en ny Internet Analyzer-profil:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Lista alla förkonfigurerade slutpunkter som är tillgängliga för den nyskapade profilen:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Skapa ett nytt test under den nyligen skapade InternetAnalyzer-profilen:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    Kommandot ovan förutsätter `www.contoso.com` att `www.microsoft.com` båda och är värd för en pixel-bild[(trans.gif)](https://fpc.msedge.net/apc/trans.gif)under anpassade banor. Om en objektsökväg inte uttryckligen anges används `/apc/trans.gif` Internet Analyzer som objektsökväg som standard, vilket är där de förkonfigurerade slutpunkterna är värd för enpixelbilden. Observera också att schemat (https/http) inte behöver anges. Internet Analyzer stöder endast HTTPS-slutpunkter, så HTTPS antas.

11. Det nya testet ska visas under Internet Analyzer-profilen:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Exempel på utdata:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. För att börja generera mätningar måste JavaScript-filen som testets **scriptFileUri** pekar på bäddas in i webbprogrammet. Specifika instruktioner finns på sidan [Embed Internet Analyzer Client.](internet-analyzer-embed-client.md)

13. Du kan övervaka testets framsteg genom att hålla reda på dess "status"-värde:
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Du kan inspektera testets insamlade resultat genom att generera tidsserier eller styrkort för det:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Nästa steg

* Bläddra i [CLI-referensen](https://docs.microsoft.com/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest) för Internet Analyzer för en fullständig lista över kommandon och användningsexempel som stöds.
* Läs [vanliga frågor om Internet Analyzer](internet-analyzer-faq.md).
* Läs mer om hur du bäddar in [Internet Analyzer-klienten](internet-analyzer-embed-client.md) och skapar en [anpassad slutpunkt](internet-analyzer-custom-endpoint.md). 
