---
title: "Skapa en serverlösa app i Visual Studio | Microsoft Docs"
description: "Kom igång med din första serverlösa app med den här guiden för att skapa, distribuera och hantera appen i Visual Studio."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: d3cd509a0073e45bf910e9a74a279d678ff03b63
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>Skapa en serverlösa app i Visual Studio med Logic Apps och funktioner

Tillåt för snabb utveckling och distribution av molnprogram serverlösa verktyg och funktioner i Azure.  Det här dokumentet fokuserar på hur du kommer igång i Visual Studio skapar ett program.  En översikt över serverlösa i Azure [kan hittas i den här artikeln](logic-apps-serverless-overview.md).

## <a name="getting-everything-ready"></a>Förbereder allt

Här följer de förutsättningar som krävs för att skapa ett program från Visual Studio:

* [Visual Studio 2017](https://www.visualstudio.com/vs/) eller Visual Studio 2015 - Community, Professional och Enterprise
* [Logic Apps Tools för Visual Studio](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [Den senaste Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 eller senare)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* [Azure Functions grundläggande verktyg](https://www.npmjs.com/package/azure-functions-core-tools) felsöka fungerar lokalt
* Tillgång till Internet när du använder den inbäddade Logikapp designern

## <a name="getting-started-with-a-deployment-template"></a>Komma igång med en Distributionsmall

Hantera resurser i Azure är klar inom en resursgrupp.  En resursgrupp är en logisk gruppering av resurser.  Resursgrupper kan distribution och hantering av en samling resurser.  För ett program i Azure våra resursgruppen som innehåller både Azure Logic Apps och Azure Functions.  Med hjälp av resursgrupp-projekt i Visual Studio, kommer du att utveckla, hantera och distribuera hela programmet som ett enskilt objekt.

### <a name="create-a-resource-group-project-in-visual-studio"></a>Skapa en resursgrupp-projekt i Visual Studio

1. I Visual Studio klickar du på Lägg till en **nytt projekt**
1. I den **moln** kategori, väljer att skapa en Azure **resursgruppen** projekt  
 * Om du inte ser kategori eller projekt i listan, bör du se till Azure SDK för Visual Studio
1. Ge projektet namn och plats och välj **Ok** uppmanas för att välja en mall för att skapa Visual Studio.  Du kan välja att starta från tomt, börja med en Logikapp eller andra resurser.  Men använder i det här fallet vi en mall för Azure Quickstart få oss igång med en serverlösa app.
1. Välj för att visa mallar från **Azure Quickstart** ![att välja Azure Quickstart mallar][1]
1. Välj mallen serverlösa Snabbstart: **101-logic-app-and-function-app** och på **Ok**

Snabbstart skapas en Distributionsmall i resursgruppsprojektet.  Mallen innehåller en enkel Logikapp som anropar en Azure-funktioner, och returnerar resultatet.  Om du öppnar den `azuredeploy.json` filen i Solution Explorer kan du se resurser för serverlösa appen.

## <a name="deploying-the-serverless-application"></a>Distribuera programmet serverlösa

Innan du kan öppna den visuella designern Logikapp i Visual Studio, behöver det finnas en redan distribuerad Azure-resursgrupp.  Detta gör att skapa och använda anslutningar till resurser och tjänster i logikappen.  Om du vill komma igång behöver vi bara distribuera lösning som har skapats.

1. Högerklicka på projektet i Visual Studio, markera **distribuera**, och skapa en **ny** distribution ![välja ny resurs-distribution][2]
1. Välj en giltig Azure-prenumeration och resursgrupp
1. Välja **distribuera** lösningen
1. Ange namn på Logikappen och Funktionsapp Azure.  Namnet på Azure-funktionen måste vara globalt unika

Serverlösa lösningen distribueras till den angivna resursgruppen.  Om du tittar på det **utdata** i Visual Studio kan du se statusen för distributionen.

## <a name="editing-the-logic-app-in-visual-studio"></a>Redigera logikappen i Visual Studio

När lösningen har distribuerats i valfri resursgrupp, kan den visuella designern användas för att redigera och göra ändringar i logikappen.

1. Högerklicka på den `azuredeploy.json` fil i Solution Explorer och markera **öppna med Logic Apps Designer**
1. Välj den **resursgruppen** och **plats** lösningen har distribuerats till och välj **OK**

Den visuella designern Logikapp ska nu visas med Visual Studio.  Du kan fortsätta att lägga till steg, ändra arbetsflödet och spara ändringarna.  Du kan också skapa logikappar från Visual Studio.  Om du högerklickar på den **resurser** i Navigatören mallen kan du lägga till en **Logikapp** i projektet.  Tom logikappar läsa in den visuella designern utan en fördistribuera i en resursgrupp.

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>Hantera och visa körningshistorik för en distribuerad logikapp

Du kan också hantera och visa körningshistorik för logikappar i Azure.  Om du öppnar den **Cloud Explorer** verktyg i Visual Studio kan du högerklicka på alla Logikapp och välja att redigera, inaktivera, visa egenskaperna för eller visa körningshistorik.  Klicka på Redigera kan du också hämta en publicerad logikapp till en resursgrupp för Visual Studio-projekt.  Det innebär att även om du startade skapa din logikapp i Azure-portalen, du kan fortfarande importera och hantera den från Visual Studio.

## <a name="developing-an-azure-function-in-visual-studio"></a>Utveckla en Azure-funktion i Visual Studio

En Distributionsmall distribuerar de Azure-funktioner som ingår i lösningen för git-lagringsplats som anges i den `azuredeploy.json` variabler.  Om du skapar en funktion-projekt i lösningen checkar in i källkontrollen (GitHub, Visual Studio Team Services osv.) och uppdatera den `repo` variabel, mallen ska distribuera Azure-funktion.

### <a name="creating-an-azure-function-project"></a>Skapa ett projekt för Azure-funktion

Om använder JavaScript, Python, F #, Bash, Batch eller PowerShell följa den [stegen i funktioner CLI](../azure-functions/functions-run-local.md) att skapa ett projekt.  Om du utvecklar en funktion i C#, kan du använda en [C#-klassbiblioteket](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) i den aktuella lösningen för Azure-funktion.

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du skapar en serverlösa sociala instrumentpanel](logic-apps-scenario-social-serverless.md)
* [Hantera en logikapp från Visual Studio Cloud Explorer](manage-logic-apps-with-visual-studio.md)
* [Språk i Arbetsflödesdefinitionen för logik App](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png
