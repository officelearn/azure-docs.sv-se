---
title: "Anvisningar om hur du distribuerar Azure-webbappar med hjälp av mallar | Microsoft Docs"
description: "Rekommendationer för att skapa mallar för Azure Resource Manager för att distribuera webbprogram."
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: tomfitz
ms.openlocfilehash: dc816bb6e95d2800d79124dfac60b55e88eaa500
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Riktlinjer för att distribuera webbprogram med hjälp av Azure Resource Manager-mallar

Den här artikeln innehåller rekommendationer för att skapa mallar för Azure Resource Manager för att distribuera lösningar för Azure App Service. Dessa rekommendationer kan hjälpa dig att undvika vanliga problem.

## <a name="define-dependencies"></a>Definiera beroenden

Definiera beroenden för web apps kräver en förståelse av hur resurserna i en webbapp samverkar. Du kan leda till distributionsfel eller skapa ett konkurrenstillstånd som stannar distributionen om du anger beroenden i felaktig ordning.

> [!WARNING]
> Om du inkludera filnamnstillägget MSDeploy plats i mallen, anger du configuration resurser som är beroende av MSDeploy-resursen. Konfigurationsändringar orsaka webbplatsen kan du starta om asynkront. Genom att göra configuration resurser beroende MSDeploy kan du se till att MSDeploy slutförs innan platsen startar om. Utan dessa beroenden platsen kanske måste startas om under distribueringen av MSDeploy. En exempelmall finns i [WordPress-mallen med Web distribuera beroende](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Följande bild visar beroendeordningen för olika resurser i Apptjänst:

![Web app beroenden](media/web-sites-rm-template-guidance/web-dependencies.png)

Du kan distribuera resurser i följande ordning:

**Nivå 1**
* App Service-plan.
* Alla andra relaterade resurser, t.ex. databaser eller storage-konton.

**Nivå 2**
* Web app--beror på App Service-plan.
* Azure Application Insights-instans som riktar sig till servergruppen--beror på App Service-plan.

**Nivå 3**
* Kontroll--beror på webbprogrammet.
* MSDeploy webbplatstillägg--beror på webbprogrammet.
* Insikter programinstansen som riktar sig till servergruppen--beror på webbprogrammet.

**Nivå 4**
* Apptjänst certifikat--beror på källkontrollen eller MSDeploy om båda finns. I annat fall beror det på webbprogrammet.
* Konfigurationsinställningar (anslutningssträngar, web.config-värden, app-inställningar)--beror på källkontrollen eller MSDeploy om antingen finns. I annat fall beror det på webbprogrammet.

**Nivå 5**
* Värd för namnet bindningar--beror på certifikatet om det finns. I annat fall beror det på en högre nivå resurs.
* Platsen tillägg--beror på konfigurationsinställningar om den finns. I annat fall beror det på en högre nivå resurs.

Din lösning innehåller vanligtvis endast vissa av dessa resurser och nivåer. Mappa lägre resurser till nästa högre nivån för saknade nivåer.

I följande exempel visas en del av en mall. Värdet i strängen konfigurationen beror på tillägget MSDeploy. Tillägget MSDeploy beror på webbappen och databasen.

```json
{
    "name": "[parameters('name')]",
    "type": "Microsoft.Web/sites",
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'))]",
            "[concat('SuccessBricks.ClearDB/databases/', parameters('databaseName'))]"
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

## <a name="find-information-about-msdeploy-errors"></a>Hitta information om MSDeploy-fel

Om Resource Manager-mallen använder MSDeploy, kan distribution felmeddelanden vara svåra att förstå. Prova följande steg för att få mer information efter en misslyckad distribution:

1. Gå till webbplatsens [Kudu konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Bläddra till mappen D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Söka efter appManagerStatus.xml och appManagerLog.xml filer. Den första filen loggar status. Den andra filen loggar information om felet. Om felet inte är att förstå, kan du inkludera den när du begär hjälp om forumet.

## <a name="choose-a-unique-web-app-name"></a>Välj ett unikt webbprogrammets namn

Måste vara globalt unikt namn för ditt webbprogram. Du kan använda en namngivningskonvention som kan förväntas vara unikt och du kan använda den [uniqueString funktionen](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) att hjälpa till med att generera ett unikt namn.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="next-steps"></a>Nästa steg

* En självstudiekurs om att distribuera webbprogram med en mall finns [etablera och distribuera mikrotjänster förutsägbart i Azure](app-service-deploy-complex-application-predictably.md).