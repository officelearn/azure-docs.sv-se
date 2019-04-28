---
title: Konfigurera inställningar för Azure-funktion | Microsoft Docs
description: Lär dig mer om att konfigurera inställningar för Azure-funktion.
services: ''
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 7497255dcad55cea86e0c640e2f1423d7d763a7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738101"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Så här hanterar du en funktionsapp i Azure portal 

I Azure Functions tillhandahåller en funktionsapp körningssammanhang för dina enskilda funktioner. Funktionen app beteenden som gäller för alla funktioner som är värd för en viss funktionsapp. Det här avsnittet beskriver hur du konfigurerar och hanterar dina funktionsappar i Azure-portalen.

Börja, gå till den [Azure-portalen](https://portal.azure.com) och logga in på ditt Azure-konto. I sökfältet längst upp i portalen skriver du namnet på din funktionsapp. Välj den sedan i listan. När du har valt din funktionsapp, ser du följande sida:

![Översikt över funktionen i Azure portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="favorite"></a>Funktionerna i portalen 

Det kan ibland vara svårt att hitta dina resurser i den [Azure Portal]. Om du vill göra det enklare att hitta funktionsappar som du har skapat, att lägga till Funktionsappar i dina Favoriter på portalen. 

1. Logga in på [Azure Portal].

2. Klicka på pilen längst ned till vänster för att expandera alla tjänster, skriv `Functions` i fältet **Filter** och klicka sedan på stjärnan bredvid **Funktionsappar**.  
 
    ![Skapa en funktionsapp i Azure Portal](./media/functions-how-to-use-azure-function-app-settings/functions-favorite-function-apps.png)

    Detta gör att funktionsikonen läggs till på menyn till vänster på portalen.

3. Stäng menyn och bläddra ned till slutet så att du ser funktionsikonen. Klicka på ikonen för att visa alla dina funktionsappar. Klicka på funktionsappen för att arbeta med funktionerna i den här appen. 
 
    ![Funktionsappar i Favoriter](./media/functions-how-to-use-azure-function-app-settings/functions-function-apps-hub.png)
 
[Azure Portal]: https://portal.azure.com/

## <a name="manage-app-service-settings"></a>Fliken för funktionen app-inställningar

![Funktionen översikt över Azure-portalen.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Den **inställningar** fliken är där du kan uppdatera Functions runtime-versionen som används av din funktionsapp. Det är också där du hanterar värdnycklar som används för att begränsa HTTP-åtkomst till alla funktioner som är värd för funktionsappen.

Functions har stöd för både som är värd för förbrukning och App Service som är värd för planer. Mer information finns i [Välj korrekt tjänsteplan för Azure Functions](functions-scale.md). Funktioner för bättre förutse förbrukningsplanen kan du begränsa plattformsanvändningen genom att ange en kvot för daglig användning i gigabyte-sekunder. När dagskvoten uppnås stoppas funktionsappen. En funktionsapp som stoppats på grund av når utgiftsgränsen kvoten kan återaktiveras från samma kontext som upprättar dagliga utgiftskvot. Se den [Azure Functions sidan med priser](https://azure.microsoft.com/pricing/details/functions/) mer information om fakturering.   

## <a name="platform-features-tab"></a>Plattformen funktioner fliken

![Funktionen app-plattformen funktioner fliken.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Funktionsappar körs i och hanteras av Azure App Service-plattformen. Dina funktionsappar har därför åtkomst till de flesta av funktionerna i Azure core webbvärdtjänster plattform. Den **plattformsfunktioner** fliken är där du kan komma åt de många funktionerna i App Service-plattformen som du kan använda i dina funktionsappar. 

> [!NOTE]
> Inte alla App Service-funktioner är tillgängliga när en funktionsapp som körs på förbrukningen som är värd för planen.

Resten av det här avsnittet fokuserar på de följande App Service-funktioner i Azure-portalen som är användbara för funktioner:

+ [App Service editor](#editor)
+ [programinställningar](#settings) 
+ [Konsol](#console)
+ [Avancerade verktyg (Kudu)](#kudu)
+ [Distributionsalternativ](#deployment)
+ [CORS](#cors)
+ [Autentisering](#auth)
+ [API-definition](#swagger)

Mer information om hur du arbetar med App Service-inställningar finns i [konfigurera Azure App Service-inställningarna](../app-service/web-sites-configure.md).

### <a name="editor"></a>App Service Editor

| | |
|-|-|
| ![Funktionsapp App Service editor.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | App Service-redigeraren är en avancerad redigerare i portalen som du kan använda för att ändra JSON-konfigurationsfiler och kodfiler både. Det här alternativet startar en separat webbläsarflik med en grundläggande redigerare. På så sätt kan du integrera med Git-lagringsplats, köra och felsöka kod och ändra funktionsappinställningarna. Den här redigeraren ger en förbättrad utvecklingsmiljö för dina funktioner jämfört med standard-funktionen appbladet.    |

![App Service-redigeraren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings"></a>programinställningar

| | |
|-|-|
| ![Programinställningar för funktionen app.](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | App Service- **programinställningar** bladet kan du konfigurera och hantera framework-versioner, fjärrfelsökning, appinställningar och anslutningssträngar. När du integrerar din funktionsapp med andra Azure och tjänster från tredje part, kan du ändra dessa inställningar här. Ta bort en inställning genom att bläddra till höger och välj den **X** ikonen längst till höger på raden (visas inte i följande bild).

![Konfigurera programinställningar](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="console"></a>Konsolen

| | |
|-|-|
| ![Funktionen app-konsolen i Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | Konsolen i portalen är ett perfekt utvecklarverktyget när du föredrar att interagera med din funktionsapp från kommandoraden. Vanliga kommandon är directory och skapa en fil och navigering, samt köra batch-filer och skript. |

![Funktionen app-konsolen](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Avancerade verktyg (Kudu)

| | |
|-|-|
| ![Kudu-funktionsapp i Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | Avancerade verktyg för App Service (även kallat Kudu) ger åtkomst till avancerade administrativa funktioner i din funktionsapp. Från Kudu hanterar du Systeminformation, app-inställningar, miljövariabler, webbplatstillägg, HTTP-huvuden och servervariabler. Du kan också starta **Kudu** genom att bläddra till SCM-slutpunkten för din funktionsapp, som `https://<myfunctionapp>.scm.azurewebsites.net/` |

![Konfigurera Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Distributionsalternativ

| | |
|-|-|
| ![Funktionen app-distributionsalternativ i Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Functions kan du utveckla Funktionskoden på den lokala datorn. Du kan sedan överföra ditt lokala funktionsappsprojekt till Azure. Utöver traditionella FTP-överföring kan du distribuera funktionsappen med hjälp av populära kontinuerlig integreringslösningar, som GitHub, Azure DevOps, Dropbox, Bitbucket och andra funktioner. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md). Om du vill ladda upp manuellt med hjälp av FTP- eller lokal Git måste du också måste [konfigurera dina autentiseringsuppgifter för distribution](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Funktionsapp CORS i Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | App Service blockerar anrop till dina funktionsappar från externa källor för att förhindra körning av skadlig kod i dina tjänster. Functions har stöd för cross-origin resource sharing (CORS) om du vill kan du definiera en ”lista över tillåtna” tillåtna ursprung som dina funktioner kan acceptera fjärr-förfrågningar.  |

![Konfigurera Funktionsappens CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Autentisering

| | |
|-|-|
| ![Funktionen app-autentisering i Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | När funktioner använder en HTTP-utlösare kan kräva du anrop till först autentiseras. App Service har stöd för Azure Active Directory-autentisering och logga in med sociala leverantörer, till exempel Facebook, Microsoft och Twitter. Mer information om hur du konfigurerar specifika autentiseringsproviders finns i [översikt över Azure App Service-autentisering](../app-service/overview-authentication-authorization.md). |

![Konfigurera autentisering för en funktionsapp](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>API-definition

| | |
|-|-|
| ![Funktionen app API swagger-definition i Azure portal](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Functions har stöd för Swagger ska tillåta klienter att enkelt använda din HTTP-utlösta funktioner. Mer information om hur du skapar API-definitioner med Swagger finns [vara värd för en RESTful-API med CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md). Du kan också använda Functions Proxies för att definiera en enda API-yta för flera funktioner. Mer information finns i [arbeta med Azure Functions Proxies](functions-proxies.md). |

![Konfigurera Funktionsapp-API](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Nästa steg

+ [Konfigurera inställningar för Azure App Service](../app-service/web-sites-configure.md)
+ [Löpande distribution för Azure Functions](functions-continuous-deployment.md)



