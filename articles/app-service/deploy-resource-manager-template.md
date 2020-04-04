---
title: Distribuera appar med mallar
description: Hitta vägledning om hur du skapar Azure Resource Manager-mallar för att etablera och distribuera App Service-appar.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 0a282a412823207e5f662441158000e8c6121796
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637929"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Vägledning om hur du distribuerar webbappar med hjälp av Azure Resource Manager-mallar

Den här artikeln innehåller rekommendationer för hur du skapar Azure Resource Manager-mallar för att distribuera Azure App Service-lösningar. Dessa rekommendationer kan hjälpa dig att undvika vanliga problem.

## <a name="define-dependencies"></a>Definiera beroenden

Att definiera beroenden för webbappar kräver en förståelse för hur resurserna i en webbapp interagerar. Om du anger beroenden i en felaktig ordning kan du orsaka distributionsfel eller skapa ett konkurrenstillstånd som stoppar distributionen.

> [!WARNING]
> Om du inkluderar ett MSDeploy-platstillägg i mallen måste du ange alla konfigurationsresurser som beroende av MSDeploy-resursen. Konfigurationsändringar gör att platsen startas om asynkront. Genom att göra konfigurationsresurserna beroende av MSDeploy ser du till att MSDeploy avslutas innan platsen startas om. Utan dessa beroenden kan platsen startas om under distributionsprocessen för MSDeploy. En exempelmall finns i [WordPress-mall med beroende av webbut distribuera](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Följande bild visar beroendeordningen för olika App Service-resurser:

![Beroenden för webbappar](media/web-sites-rm-template-guidance/web-dependencies.png)

Du distribuerar resurser i följande ordning:

**Nivå 1**
* App Service plan.
* Andra relaterade resurser, till exempel databaser eller lagringskonton.

**Nivå 2**
* Webbappen – beror på App Service-planen.
* Azure Application Insights-instans som är inriktad på servergruppen – beror på App Service-planen.

**Nivå 3**
* Källkontroll – beror på webbappen.
* MSDeploy-webbplatstillägg – beror på webbappen.
* Azure Application Insights-instans som riktar sig till webbappen – beror på webbappen.

**Nivå 4**
* App Service-certifikat - beror på källkontroll eller MSDeploy om någon av dem finns. Annars beror det på webbappen.
* Konfigurationsinställningar (anslutningssträngar, web.config-värden, appinställningar)--beror på källkontroll eller MSDeploy om någon av dem finns. Annars beror det på webbappen.

**Nivå 5**
* Värdnamnsbindningar – beror på certifikatet om det finns. Annars beror det på en resurs på högre nivå.
* Webbplatstillägg – beror på konfigurationsinställningar om sådana finns. Annars beror det på en resurs på högre nivå.

Vanligtvis innehåller din lösning endast några av dessa resurser och nivåer. För saknade nivåer mappas lägre resurser till nästa högre nivå.

I följande exempel visas en del av en mall. Värdet för anslutningssträngkonfigurationen beror på TILLÄGGET MSDeploy. MSDeploy-tillägget beror på webbappen och databasen. 

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

Ett färdigt exempel som använder koden ovan finns i [Mall: Skapa en enkel Umbraco Web App](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Hitta information om MSDeploy-fel

Om resource manager-mallen använder MSDeploy kan distributionsfelmeddelandena vara svåra att förstå. Prova följande steg om du vill ha mer information efter en misslyckad distribution:

1. Gå till webbplatsens [Kudu konsol](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Bläddra till mappen på D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Leta efter filerna appManagerStatus.xml och appManagerLog.xml. Den första filen loggar statusen. Den andra filen loggar information om felet. Om felet inte är klart för dig kan du inkludera det när du ber om hjälp på [forumet](https://docs.microsoft.com/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Välj ett unikt webbappnamn

Namnet på webbappen måste vara globalt unikt. Du kan använda en namngivningskonvention som sannolikt är unik, eller så kan du använda funktionen [uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) för att hjälpa till med att skapa ett unikt namn.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Distribuera webbappcertifikat från Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om mallen innehåller en [Microsoft.Web/certificates-resurs](/azure/templates/microsoft.web/certificates) för TLS/SSL-bindning och certifikatet lagras i ett Nyckelvalv måste du se till att App Service-identiteten kan komma åt certifikatet.

I globala Azure har apptjänsttjänstens huvudnamn ID för **abfa0a7c-a6b6-4736-8310-5855508787cd**. Om du vill bevilja åtkomst till Key Vault för huvudmannen för App-tjänsten använder du:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

I Azure Government har apptjänsttjänstens huvudnamn ID **för 6a02c803-dafd-4136-b4c3-5a6f318b4714**. Använd det ID:t i föregående exempel.

I key vault väljer du **Certifikat** och **Generera/Importera** för att ladda upp certifikatet.

![Importera certifikatet](media/web-sites-rm-template-guidance/import-certificate.png)

Ange namnet på certifikatet för `keyVaultSecretName`.

En exempelmall finns i [Distribuera ett Web App-certifikat från Key Vault hemlighet och använda det för att skapa SSL-bindning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Nästa steg

* En självstudiekurs om hur du distribuerar webbappar med en mall finns [i Etablera och distribuera mikrotjänster förutsägbart i Azure](deploy-complex-application-predictably.md).
* Mer information om JSON-syntax och egenskaper för resurstyper i mallar finns i [Mallreferens för Azure Resource Manager](/azure/templates/).
