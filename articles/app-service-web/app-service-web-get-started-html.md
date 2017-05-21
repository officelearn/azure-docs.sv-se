---
title: "Skapa en statisk HTML-webbapp i Azure på fem minuter | Microsoft Docs"
description: "Distribuera en exempelapp och se hur enkelt det är att köra webbappar i App Service."
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/08/2017
ms.author: riande
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 895906e1ab4bc50093ed3b18f043c3dd515ca054
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>Skapa en statisk HTML-webbapp i Azure på fem minuter

Den här snabbstarten visar hur du distribuerar en enkel HTML+CSS-webbplats till Azure. Du kommer att köra appen med en [Azure App Service-plan](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) och skapa en webbapp i den med hjälp av [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli). Du kan använda Git för att distribuera appen till Azure. Det tar cirka fem minuter att slutföra självstudiekursen när de nödvändiga komponenterna har installerats.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="prerequisites"></a>Krav

Innan du skapar det här exemplet måste du hämta och installera följande komponenter:

- [Git](https://git-scm.com/)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Hämta exemplet

Klona databasen för exempelappen till den lokala datorn i ett terminalfönster:

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="view-the-html"></a>Visa HTML

Navigera till den katalog som innehåller HTML-exemplet. Öppna filen *index.html* i webbläsaren.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [login-to-azure](../../includes/login-to-azure.md)] 
[!INCLUDE [configure-deployment-user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [app-service-web-quickstart1](../../includes/app-service-web-quickstart1.md)] 

Skapa en [webbapp](app-service-web-overview.md) i `quickStartPlan` App Service-planen. Webbappen ger dig ett lagringsutrymme för din kod och du får en URL så att du kan visa den distribuerade appen.

[!INCLUDE [app-service-web-quickstart2](../../includes/app-service-web-quickstart2.md)] 

Sidan körs som en Azure App Service-webbapp:

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="update-and-redeploy-the-app"></a>Uppdatera och distribuera om appen

Öppna filen *index.html*. Gör ändringar i koden. Du kan till exempel ändra `Hello world!` till `Hello Azure!`

Spara ändringarna på Git och skicka sedan kodändringarna till Azure.

```bash
git commit -am "updated HTML"
git push azure master
```

När distributionen är klar väljer du att uppdatera i webbläsaren så att ändringarna visas.

[!INCLUDE [manage-azure-web-app](../../includes/manage-azure-web-app.md)]


[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

- Utforska [exempelskipt för Web Apps CLI](app-service-cli-samples.md).
- Läs om hur du [mappar ett anpassat domännamn](app-service-web-tutorial-custom-domain.md), till exempel contoso.com, till en [App Service-app](app-service-web-tutorial-custom-domain.md).
