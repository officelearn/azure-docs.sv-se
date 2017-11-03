---
title: "Skapa mallar för distribution för Azure Logic Apps | Microsoft Docs"
description: "Skapa Azure Resource Manager-mallar för logic apps för hantering av distribution och version"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 9cfbb294010d48deaf4b4c78c6a6bcd59a387d87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-templates-for-logic-apps-deployment-and-release-management"></a>Skapa mallar för logikappar för hantering av distribution och version

När du har skapat en logikapp kanske du vill skapa den som en Azure Resource Manager-mall.
På så sätt kan du kan enkelt distribuera logikappen till miljö eller resursgrupp som du kanske behöver.
Mer information om Resource Manager-mallar finns i [skapa mallar för Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) och [distribuera resurser med hjälp av Azure Resource Manager-mallar](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Mall för distribution av logik-app

En logikapp har tre huvudkomponenter:

* **Logik app resurs**: innehåller information om till exempel priser plan, plats och arbetsflödesdefinitionen.
* **Arbetsflödesdefinitionen**: Beskriver steg i din logikapp arbetsflöde och hur Logic Apps motorn ska köra arbetsflödet.
Du kan visa den här definitionen i din logikapp **kodvy** fönster.
Resursen logik appen hittar den här definitionen i den `definition` egenskapen.
* **Anslutningar**: refererar till separata resurser som på ett säkert sätt lagra metadata om någon koppling anslutningar, till exempel en anslutningssträng och ett åtkomst-token.
I resursen logik app logikappen som refererar till dessa resurser i den `parameters` avsnitt.

Du kan visa dessa delar av befintliga logikappar med hjälp av ett verktyg som [resursutforskaren Azure](http://resources.azure.com).

Om du vill skapa en mall för en logikapp som ska användas med distributionen av resursgrupper, måste du definiera resurserna och parameterstyra efter behov.
Till exempel om du distribuerar en utveckling och test produktionsmiljön, vill du förmodligen använda olika anslutningssträngar till en SQL-databas i varje miljö.
Eller så kanske du vill distribuera i olika prenumerationer eller resursgrupper.  

## <a name="create-a-logic-app-deployment-template"></a>Skapa en logik app Distributionsmall

Det enklaste sättet att ha en giltig logik app Distributionsmall är att använda den [Visual Studio Tools för Logic Apps](logic-apps-deploy-from-vs.md).
Visual Studio-verktygen Generera en giltig Distributionsmall som kan användas på alla prenumerationen eller platsen.

Några andra verktyg kan hjälpa dig när du skapar en logik app Distributionsmall.
Du kan skriva manuellt, det vill säga med hjälp av de resurser som redan beskrivs här om du vill skapa parametrar efter behov.
Ett annat alternativ är att använda en [logik som skapar appen mallen](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell-modulen. Den här modulen för öppen källkod utvärderas först logikappen och alla anslutningar som använder, och genererar Mallresurser med parametrarna som krävs för distributionen.
Till exempel om du har en logikapp som tar emot ett meddelande från en Azure Service Bus-kö och läggs data till en Azure SQL database, verktyget bevarar alla orchestration-logik och parameterizes SQL och Service Bus-anslutning strängar så att de kan anges på distribuera Räkna upp.

> [!NOTE]
> Anslutningar måste finnas inom samma resursgrupp som logikappen.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Installera PowerShell-modulen logik app mall
Det enklaste sättet att installera modulen är via den [PowerShell-galleriet](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), med hjälp av kommandot `Install-Module -Name LogicAppTemplate`.  

Dessutom kan du installera PowerShell-modulen manuellt:

1. Hämta den senaste versionen av den [logik som skapar appen mallen](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Extrahera mappen för din PowerShell-modul (vanligtvis `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Att arbeta med klient- och prenumeration åtkomst-modulen token, rekommenderar vi att du använder den med den [ARMClient](https://github.com/projectkudu/ARMClient) kommandoradsverktyget.  Detta [blogginlägget](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) ARMClient beskrivs i detalj.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Generera en logik app mall med hjälp av PowerShell
När du har installerat PowerShell kan du generera en mall med hjälp av följande kommando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId`är Azure prenumerations-ID. Den här raden först hämtar en åtkomst-token via ARMClient, och sedan kommer det via till PowerShell-skript och skapar sedan mallen i en JSON-fil.

## <a name="add-parameters-to-a-logic-app-template"></a>Lägga till parametrar till en app logik-mall
Du kan fortsätta att lägga till eller ändra parametrarna som du kan behöva när du har skapat mallen logik app. Om din definition innehåller en resurs-ID till en Azure-funktion eller inkapslat arbetsflöde som du planerar att distribuera i en enkel distribution, kan du lägga till fler resurser i mallen och parameterstyra ID: N efter behov. Detsamma gäller för alla referenser till anpassade API: er eller Swagger slutpunkter som du förväntar dig att distribuera med varje resursgrupp.

## <a name="deploy-a-logic-app-template"></a>Distribuera en app logik-mall

Du kan distribuera mallen med hjälp av verktyg som PowerShell, REST-API [Visual Studio Team Services versionshantering](#team-services), och malldistribution via Azure-portalen.
Även om du vill lagra värdena för parametrarna, rekommenderar vi att du skapar en [parameterfilen](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Lär dig hur du [distribuera resurser med Azure Resource Manager-mallar och PowerShell](../azure-resource-manager/resource-group-template-deploy.md) eller [distribuera resurser med Azure Resource Manager-mallar och Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Godkänna OAuth-anslutningar

Efter distributionen fungerar logikappen slutpunkt till slutpunkt med giltiga parametrar.
Du måste dock fortfarande auktorisera OAuth-anslutningar för att skapa en åtkomst-token.
Öppna logikappen i Logic Apps Designer för att godkänna OAuth-anslutningar och godkänna dessa anslutningar. Eller för automatisk distribution, kan du använda ett skript för att godkänna varje OAuth-anslutning.
Det finns ett exempelskript på GitHub under den [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projekt.

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Visual Studio Team Services versionshantering

Ett vanligt scenario för att distribuera och hantera en miljö är att använda ett verktyg som versionshantering i Visual Studio Team Services med en logik app Distributionsmall. Visual Studio Team Services innehåller en [distribuera Azure-resursgrupp](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) uppgift att du kan lägga till alla build-versionen eller släpp pipeline. Du måste ha en [tjänstens huvudnamn](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) för auktorisering för att distribuera, och du kan generera release-definition.

1. Välj i versionshantering, **tom** så att du skapar en tom definition.

    ![Skapa en tom definition][1]

2. Välj alla resurser som du behöver för att göra detta troligen inklusive logik appmallen som skapas manuellt eller som en del av skapar.
3. Lägg till en **Azure Resource distribution** aktivitet.
4. Konfigurera med en [tjänstens huvudnamn](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/), och refererar till mallen och mallparametrar filer.
5. Fortsätta att bygga ut steg i processen versionen för alla andra miljö, automatiserat test och godkännare efter behov.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
