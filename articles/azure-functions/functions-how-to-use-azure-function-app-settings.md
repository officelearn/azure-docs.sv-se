---
title: "Konfigurera Azure funktionen App-inställningar | Microsoft Docs"
description: "Lär dig hur du konfigurerar Azure funktionen app-inställningar."
services: 
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 04/23/2017
ms.author: glenga
ms.openlocfilehash: 2aa35cf814f8de2af89c6f7e0eda1f919301d6a3
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Så här hanterar du en funktionsapp i Azure-portalen 

I Azure Functions ger en funktionsapp körningskontexten för dina enskilda funktioner. Funktionen app beteenden gäller för alla funktioner hos en viss funktionsapp. Det här avsnittet beskriver hur du konfigurerar och hanterar dina appar för funktionen i Azure-portalen.

Börja, gå till den [Azure-portalen](http://portal.azure.com) och logga in på ditt Azure-konto. I sökfältet längst upp i portalen skriver du namnet på din funktionsapp. Välj den sedan i listan. När du har valt appen funktion, visas följande sida:

![Översikt över funktionen app i Azure-portalen](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="manage-app-service-settings"></a>Fliken för funktionen app-inställningar

![Funktionen app översikt i Azure-portalen.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Den **inställningar** är där du kan uppdatera funktioner körtidsversion som används av din funktionsapp. Det är också där du hanterar värd-nycklar som används för att begränsa HTTP-åtkomst till alla funktioner som appen funktionen som värd.

Funktioner stöder både förbrukning värd och Apptjänst som är värd för planer. Mer information finns i [väljer du rätt serviceplan för Azure Functions](functions-scale.md). För bättre förutsägbarhet i planen förbrukning funktioner kan du begränsa användning av plattform genom att ange en daglig användning kvot i GB-sekunder. När den dagliga kvoten har nåtts kan har funktionen appen stoppats. En funktionsapp stoppades på grund av når utgiftsgränsen kvoten kan aktiveras igen från samma kontext som upprätta dagligen utgifter kvoten. Finns det [Azure Functions sida med priser](http://azure.microsoft.com/pricing/details/functions/) information om fakturering.   

## <a name="platform-features-tab"></a>Plattform funktioner fliken

![Funktionen app plattform funktioner fliken.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Funktionen appar körs i och underhålls av Azure App Service-plattformen. Därför har funktionen-appar åtkomst till de flesta av funktionerna i Azures core webbvärd plattform. Den **plattformsfunktioner** fliken är där du kan komma åt de många funktionerna i Apptjänst-plattformen som du kan använda i dina appar för funktionen. 

> [!NOTE]
> Inte alla Apptjänst funktioner är tillgängliga när en funktionsapp körs på förbrukningen värd plan.

Resten av det här avsnittet fokuserar på följande Apptjänst funktioner i Azure portal som är användbara för funktioner:

+ [Redigeraren för Apptjänst](#editor)
+ [Programinställningar](#settings) 
+ [Konsol](#console)
+ [Avancerade verktyg (Kudu)](#kudu)
+ [Distributionsalternativ](#deployment)
+ [CORS](#cors)
+ [Autentisering](#auth)
+ [API-definition](#swagger)

Mer information om hur du arbetar med App Service-inställningar finns [konfigurera inställningarna för Azure App Service](../app-service/web-sites-configure.md).

### <a name="editor"></a>App Service Editor

| | |
|-|-|
| ![Funktionsapp Apptjänst-redigeraren.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | Redigeraren för Apptjänst är en avancerad i portalen redigerare som du kan använda för att ändra JSON-konfigurationsfiler och kodfiler likadan. Det här alternativet startar en separat webbläsarflik med ett grundläggande redigeringsprogram. På så sätt kan du integrera med Git-lagringsplatsen, köra och felsöka kod och ändra funktionen app-inställningar. Den här redigeraren ger en förbättrad utvecklingsmiljön för dina funktioner jämfört med bladet standard funktionen.    |

![Redigeraren för Apptjänst](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings">Programinställningar</a>

| | |
|-|-|
| ![Funktionen app programinställningar.](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | App Service **programinställningar** bladet är där du kan konfigurera och hantera framework-versioner, fjärrfelsökning, app-inställningar och anslutningssträngar. Du kan ändra de här inställningarna när du integrerar dina funktionsapp med andra Azure och tjänster från tredje part. Om du vill ta bort en inställning, bläddra till höger och markera den **X** längst till höger på raden (visas inte i följande bild).

![Konfigurera programinställningar](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="console"></a>Konsolen

| | |
|-|-|
| ![Funktionen app-konsolen i Azure-portalen](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | I portalen konsolen är ett perfekt developer verktyg när du föredrar att arbeta med funktionen appen från kommandoraden. Vanliga kommandon omfattar directory skapas och navigering, samt och köra batch-filer och skript. |

![Funktionen app-konsolen](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu">Avancerade verktyg (Kudu)</a>

| | |
|-|-|
| ![Funktionsapp Kudu i Azure-portalen](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | Avancerade verktyg för Apptjänst (även kallat Kudu) ger åtkomst till appen funktionen avancerade administrativa funktioner. Från Kudu Hantera Systeminformation, app-inställningar, miljövariabler, webbplatstillägg, HTTP-huvuden och servervariabler. Du kan också starta **Kudu** genom att bläddra till SCM-slutpunkten för din funktionsapp som `https://<myfunctionapp>.scm.azurewebsites.net/` |

![Konfigurera Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Distributionsalternativ

| | |
|-|-|
| ![Distributionsalternativ för funktionen app i Azure-portalen](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Funktioner kan du utveckla Funktionskoden på din lokala dator. Du kan sedan överföra dina lokala funktionen app-projekt till Azure. Utöver traditionella FTP överför funktioner kan du distribuera appen funktionen med hjälp av populära kontinuerlig integration lösningar som GitHub, VSTS, Dropbox, Bitbucket och andra. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md). Om du vill överföra manuellt med hjälp av FTP- eller lokal Git, du måste också [konfigurera dina autentiseringsuppgifter för distribution](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Funktionsapp CORS i Azure-portalen](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | För att förhindra att skadlig kod i dina tjänster, blockerar Apptjänst anrop till funktionen appar från externa källor. Functions stöder resursdelning för korsande ursprung (CORS) om du vill kan du definiera ”godkänd” tillåtna ursprung som dina funktioner kan acceptera fjärrbegäranden.  |

![Konfigurera Funktionsapp CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Autentisering

| | |
|-|-|
| ![Funktionen app autentisering i Azure-portalen](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | När funktioner använder en HTTP-utlösare kan kräva du anrop till först autentiseras. Apptjänst har stöd för Azure Active Directory-autentisering och logga in med sociala leverantörer, till exempel Facebook, Microsoft och Twitter. Mer information om hur du konfigurerar specifika autentiseringsproviders finns [översikt över Azure App Service-autentisering](../app-service/app-service-authentication-overview.md). |

![Konfigurera autentisering för en funktionsapp](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger">API-definition</a>

| | |
|-|-|
| ![Funktionen app API swagger-definition i Azure-portalen](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Functions stöder Swagger så att klienterna kan enkelt använda din HTTP-utlösta funktioner. Mer information om hur du skapar API-definitioner med Swagger finns [värd för en RESTful-API med CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md). Du kan också använda funktioner proxyservrar för att definiera en enda API-yta för flera funktioner. Mer information finns i [arbeta med Azure Functions proxyservrar](functions-proxies.md). |

![Konfigurera Funktionsapp-API](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Nästa steg

+ [Konfigurera inställningar för Azure App Service](../app-service/web-sites-configure.md)
+ [Löpande distribution för Azure Functions](functions-continuous-deployment.md)



