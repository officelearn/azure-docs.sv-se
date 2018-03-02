---
title: Anvisningar om hur du distribuerar Azure-webbappar med mallar | Microsoft Docs
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
ms.openlocfilehash: eff0b0e6258217fa8fbf7f15606f98d70fecd7c5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="guidance-on-deploying-web-apps-with-azure-resource-manager-templates"></a>Riktlinjer för att distribuera webbprogram med Azure Resource Manager-mallar

Den här artikeln innehåller rekommendationer för att skapa mallar för Azure Resource Manager för att distribuera lösningar för Apptjänst. Dessa rekommendationer kan hjälpa dig att undvika vanliga problem.

## <a name="define-dependencies"></a>Definiera beroenden

Definiera beroenden för Web Apps kräver en förståelse av hur resurserna i en Webbapp samverkar. Du kan leda till distributionsfel eller skapa ett konkurrenstillstånd som stannar distributionen om du anger beroenden i felaktig ordning.

> [!WARNING]
> Om du inkludera ett MS Deploy plats tillägg i mallen anger du configuration resurser som är beroende av MS Deploy resursen. Konfigurationsändringar orsaka webbplatsen kan du starta om asynkront. Genom att göra configuration resurser beroende MS Deploy kan kontrollera du MS Deploy är klar startar platsen. Utan dessa beroenden platsen kanske måste startas om under MS Deploy distribueringen. En exempelmall finns i [Wordpress-mallen med Web distribuera beroende](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Följande bild visar beroendeordningen för olika resurser i Apptjänst.

![Web app beroenden](media/web-sites-rm-template-guidance/web-dependencies.png)

Du kan distribuera resurser i följande ordning:

**Nivå 1**
* App Service-plan
* Relaterade resurser som databaser eller storage-konton

**Nivå 2**
* Webbapp - beror på apptjänstplan
* Application Insights som är riktade till servergruppen - beror på apptjänstplan

**Nivå 3**
* Kontroll - är beroende av webbprogram
* MSDeploy webbplatstillägg - är beroende av webbprogram
* Application Insights som är riktade till servergruppen - är beroende av webbprogram

**Nivå 4**
* Certifikat för App Service - beror på källkontrollen eller MSDeploy om antingen är närvarande. Annars är beroende av webbprogram
* Konfigurationsinställningar (anslutningssträngar, web konfigurationsvärden, app-inställningar) - beror på källkontrollen eller MSDeploy om antingen är närvarande. Annars är beroende av webbprogram

**Nivå 5**
* Värd namn bindningar - beror på certifikatet om det finns; Annars en högre nivå resurs
* Webbplatsens tillägg - beror på konfigurationsinställningar om det finns; Annars en högre nivå resurs

Din lösning innehåller vanligtvis endast vissa av dessa resurser och nivåer. Mappa lägre resurser till nästa högre nivå för saknade nivåer.

I följande exempel visas en del av en mall. Anslutningen configuration strängvärdet beror på tillägget MSDeploy. Tillägget MSDeploy beror på webbappen och databasen.

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
2. Navigera till mappen `D:\home\LogFiles\SiteExtensions\MSDeploy`.
3. Leta efter den *appManagerStatus.xml* och *appManagerLog.xml* filer. Den första filen loggar status. Den andra filen loggar information om felet. Om felet inte är att förstå kan du inkludera den när du frågar efter hjälp för forumet.

## <a name="unique-web-app-name"></a>Unik webbprogrammets namn

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