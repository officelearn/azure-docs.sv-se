---
title: Vägledning för att distribuera Azure-webbappar med hjälp av mallar | Microsoft Docs
description: Rekommendationer för att skapa Azure Resource Manager-mallar för att distribuera web apps.
services: app-service
documentationcenter: app-service
author: tfitzmac
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: tomfitz
ms.openlocfilehash: c2f600d86965e1115d4be1370da8f7c8e1b67f05
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927680"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Vägledning för att distribuera webbappar med hjälp av Azure Resource Manager-mallar

Den här artikeln innehåller rekommendationer för att skapa Azure Resource Manager-mallar för distribution av Azure App Service-lösningar. Dessa rekommendationer kan hjälpa dig att undvika vanliga problem.

## <a name="define-dependencies"></a>Definiera beroenden

Definiera beroenden för web apps kräver en förståelse för hur resurser i en webbapp interagerar. Om du anger beroenden i en felaktig ordning, kan du orsaka distributionsfel eller skapa ett konkurrenstillstånd som stannar distributionen.

> [!WARNING]
> Om du inkluderar en MSDeploy-webbplatstillägg i mallen, måste du ange konfigurationen resurser som är beroende av MSDeploy-resursen. Ändringar i konfigurationen orsaka platsen att starta om asynkront. Genom att göra resurserna som konfiguration beroende MSDeploy du se till att MSDeploy är klar startar platsen. Utan dessa beroenden platsen kanske måste startas om under distribution av MSDeploy. Läs en exempelmall [WordPress-mall med Web distribuera beroende](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Följande bild visar beroendeordningen för olika App Service-resurser:

![Web app-beroenden](media/web-sites-rm-template-guidance/web-dependencies.png)

Du distribuerar resurser i följande ordning:

**Nivå 1**
* App Service-plan.
* Andra relaterade resurser, t.ex. databaser eller storage-konton.

**Nivå 2**
* Web app – är beroende av App Service-planen.
* Azure Application Insights-instans som riktar sig till servergruppen--beror på App Service-planen.

**Nivå 3**
* Källkontroll--beror på webbappen.
* MSDeploy-platstillägget--beror på webbappen.
* Application Insights-instans som riktar sig till servergruppen--beror på webbappen.

**Nivå 4**
* App Service certificate--beroende källkontroll eller MSDeploy om antingen finns. I annat fall beror det på webbappen.
* Konfigurationsinställningar (anslutningssträngar, web.config-värden, appinställningar)--beror på källkontroll eller MSDeploy om antingen finns. I annat fall beror det på webbappen.

**Nivå 5**
* Vara värd för namn-bindningar--beror på certifikatet om det finns. I annat fall beror det på en högre nivå resurs.
* Platstillägg – är beroende av konfigurationsinställningar om den är tillgänglig. I annat fall beror det på en högre nivå resurs.

Din lösning innehåller normalt endast vissa av dessa resurser och nivåer. Mappa lägre resurser till nästa högre nivån för nivåerna för saknas.

I följande exempel visas en del av en mall. Värdet för strängen anslutningskonfiguration är beroende av MSDeploy-tillägget. MSDeploy-tillägget är beroende av webbappen och databasen. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Ett är klara och kör exempel som använder koden ovan, se [mall: skapa en enkel Umbraco-Webbapp](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Hitta information om MSDeploy-fel

Om Resource Manager-mallen använder MSDeploy, kan felmeddelanden distribution vara svåra att förstå. Om du vill ha mer information när du har en misslyckad distribution kan du prova följande steg:

1. Gå till platsen [Kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Bläddra till mappen på D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Leta efter appManagerStatus.xml och appManagerLog.xml-filer. Den första filen loggar status. Den andra filen loggar information om felet. Om felet inte är att förstå, kan du inkludera den när du begär hjälp i forumet.

## <a name="choose-a-unique-web-app-name"></a>Välj ett unikt webbappens namn

Namn för din webbapp måste vara globalt unikt. Du kan använda en namngivningskonvention som troligen kommer att vara unikt och du kan använda den [uniqueString funktionen](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) som hjälper till med att generera ett unikt namn.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Distribuera webbappcertifikat från Key Vault

Om mallen innehåller en [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) för SSL-bindning och certifikatet som är lagrad i ett Nyckelvalv, måste du se till App Service-identitet kan komma åt certifikatet.

I globala Azure App Service-tjänstens huvudnamn har ID för **abfa0a7c-a6b6-4736-8310-5855508787cd**. Om du vill ha åtkomst till Key Vault till App Service-tjänstens huvudnamn, använder du:

```azurepowershell-interactive
Set-AzureRmKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

I Azure Government, App Service-tjänstens huvudnamn har ID för **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Använd detta ID i föregående exempel.

Välj i Key Vault, **certifikat** och **generera/importera** att ladda upp.

![Importera certifikat](media/web-sites-rm-template-guidance/import-certificate.png)

I mallen, ange namnet på certifikatet för den `keyVaultSecretName`.

Läs en exempelmall [distribuerar ett Web App-certifikat från Key Vault-hemlighet och använder den för att skapa SSL-bindning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Nästa steg

* En självstudiekurs om hur du distribuerar web apps med en mall finns i [etablera och distribuera mikrotjänster förutsägbart i Azure](app-service-deploy-complex-application-predictably.md).