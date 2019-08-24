---
title: Konfigurera inställningar för Azure-Funktionsapp | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för Azure Function-appar.
author: ggailey777
manager: gwallace
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 27e56c3bcaa08fee25c362cdfed64c61b92873cb
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982660"
---
# <a name="how-to-manage-a-function-app"></a>Så här hanterar du en Function-app

I Azure Functions tillhandahåller en Function-app körnings kontexten för dina enskilda funktioner. Function-appens beteenden gäller för alla funktioner som finns i en specifik Function-app. I det här avsnittet beskrivs hur du konfigurerar och hanterar dina funktions program i Azure Portal.

Börja genom att gå till [Azure Portal](https://portal.azure.com) och logga in på ditt Azure-konto. I sökfältet längst upp i portalen skriver du namnet på din funktionsapp. Välj den sedan i listan. När du har valt appen funktion visas följande sida:

![Översikt över Function-appen i Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Du kan navigera till allt du behöver för att hantera din Function-app från översikts sidan, i synnerhet **[program inställningar](#settings)** och **[plattforms funktioner](#platform-features)** .

## <a name="settings"></a>Program inställningar

Fliken **program inställningar** innehåller inställningar som används av din Function-app.

![Function app-inställningar i Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

De här inställningarna lagras krypterade och du måste välja **Visa värden** för att se värdena i portalen.

Om du vill lägga till en inställning väljer du **ny program inställning** och lägger till det nya nyckel/värde-paret.

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

När du utvecklar en Function-app lokalt, underhålls dessa värden i den lokala. Settings. JSON-projektfilen.

## <a name="platform-features"></a>Plattformsfunktioner

![Fliken funktioner i funktionen app Platform.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Function-appar körs i och underhålls av Azure App Services plattformen. Därför har dina funktions appar till gång till de flesta av funktionerna i Azures centrala webb värd plattform. På fliken **plattforms funktioner** får du till gång till många funktioner i den app service-plattform som du kan använda i dina Function-appar. 

> [!NOTE]
> Alla App Service funktioner är inte tillgängliga när en Function-App körs på förbruknings värd planen.

Resten av det här avsnittet fokuserar på följande App Service funktioner i Azure Portal som är användbara för funktioner:

+ [App Service redigerare](#editor)
+ [Konsol](#console)
+ [Avancerade verktyg (kudu)](#kudu)
+ [Distributions alternativ](#deployment)
+ [CORS](#cors)
+ [Autentisering](#auth)
+ [API-definition](#swagger)

Mer information om hur du arbetar med App Service inställningar finns i [konfigurera Azure App Service inställningar](../app-service/configure-common.md).

### <a name="editor"></a>App Service Editor

| | |
|-|-|
| ![Function app App Service Editor.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | App Services redigeraren är en avancerad i-Portal-redigerare som du kan använda för att ändra JSON-konfigurationsfiler och kommandofiler. Om du väljer det här alternativet startas en separat flik i webbläsaren med en grundläggande redigerare. På så sätt kan du integrera med git-lagringsplatsen, köra och felsöka kod och ändra programmets inställningar. Den här redigeraren ger en förbättrad utvecklings miljö för dina funktioner jämfört med bladet standard program för funktion.    |

![App Service redigeraren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>Konsolomdirigering

| | |
|-|-|
| ![Function app-konsolen i Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | Konsolen i-portalen är ett idealiskt utvecklingsverktyg när du föredrar att interagera med din Function-app från kommando raden. Vanliga kommandon är att skapa och navigera mellan kataloger och filer, samt att köra kommandofiler och skript. |

![Function app-konsol](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Avancerade verktyg (kudu)

| | |
|-|-|
| ![Function app-kudu i Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | De avancerade verktygen för App Service (kallas även kudu) ger till gång till avancerade administrativa funktioner i din Function-app. Från kudu kan du hantera system information, appinställningar, miljövariabler, plats tillägg, HTTP-rubriker och servervariabler. Du kan också starta **kudu** genom att bläddra till SCM-slutpunkten för din Function-app, t. ex.`https://<myfunctionapp>.scm.azurewebsites.net/` |

![Konfigurera kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Distributions alternativ

| | |
|-|-|
| ![Funktions program distributions alternativ i Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Med Functions kan du utveckla din funktions kod på den lokala datorn. Sedan kan du ladda upp ditt lokala Function-appaket till Azure. Förutom traditionell FTP-uppladdning kan du distribuera din Function-app med populära lösningar för kontinuerlig integrering, t. ex. GitHub, Azure DevOps, Dropbox, BitBucket och andra. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md). Om du vill ladda upp manuellt med FTP eller lokal git måste du också [Konfigurera autentiseringsuppgifterna för distributionen](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Function app CORS i Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | För att förhindra körning av skadlig kod i dina tjänster, App Service blockerar dina funktions program från externa källor. Functions stöder resurs delning mellan ursprung (CORS) så att du kan definiera en "vitlista" av tillåtna ursprung som dina funktioner kan använda för att acceptera fjärrbegäranden.  |

![Konfigurera Funktionsapps CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Anspråksautentisering

| | |
|-|-|
| ![Function app-autentisering i Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | När funktioner använder en HTTP-utlösare kan du kräva att anrop först ska autentiseras. App Service stöder Azure Active Directory autentisering och logga in med sociala leverantörer, till exempel Facebook, Microsoft och Twitter. Mer information om hur du konfigurerar vissa autentiseringsproviders finns i [Översikt över Azure App Service autentisering](../app-service/overview-authentication-authorization.md). |

![Konfigurera autentisering för en Function-app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>API-definition

| | |
|-|-|
| ![Swagger-definition för Function app API i Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Functions har stöd för Swagger som gör det lättare för klienter att använda HTTP-utlösta funktioner. Mer information om hur du skapar API-definitioner med Swagger finns [i värd a RESTful API with CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md). Du kan också använda Functions-proxyservrar för att definiera en enskild API-yta för flera funktioner. Mer information finns i [arbeta med Azure Functions-proxyservrar](functions-proxies.md). |

![Konfigurera Funktionsapp s API](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Nästa steg

+ [Konfigurera Azure App Service inställningar](../app-service/configure-common.md)
+ [Löpande distribution för Azure Functions](functions-continuous-deployment.md)



