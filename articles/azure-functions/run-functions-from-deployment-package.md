---
title: Köra Azure Functions från ett paket
description: Kör den Azure Functions körningen av dina funktioner genom att montera en distributions paket fil som innehåller dina projektfiler för Function-appen.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: a3e11a7c4f3fd91df2fd9dd7a44f3922c4922585
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921121"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Köra Azure Functions från en paketfil

I Azure kan du köra dina funktioner direkt från en distributions paket fil i din Function-app. Det andra alternativet är att distribuera dina filer i katalogen `d:\home\site\wwwroot` i din Function-app.

I den här artikeln beskrivs fördelarna med att köra funktioner från ett paket. Den visar också hur du aktiverar den här funktionen i Function-appen.

> [!IMPORTANT]
> När du distribuerar dina funktioner till en Linux Function-app i en [Premium-plan](functions-scale.md#premium-plan)bör du alltid köra från paket filen och [publicera din app med hjälp av Azure Functions Core tools](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>Fördelar med att köra från en paket fil
  
Det finns flera fördelar med att köra från en paket fil:

+ Minskar risken för fil kopierings låsnings problem.
+ Kan distribueras till en produktions-app (med omstart).
+ Du kan vara säker på vilka filer som körs i din app.
+ Förbättrar prestandan för [Azure Resource Manager distributioner](functions-infrastructure-as-code.md).
+ Kan minska kall start tider, särskilt för JavaScript-funktioner med stora NPM paket träd.

Mer information finns i [det här meddelandet](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Aktivera funktioner för att köra från ett paket

Om du vill att din Function-app ska kunna köras från ett paket lägger du bara till en `WEBSITE_RUN_FROM_PACKAGE`-inställning i appens inställningar. Inställningen `WEBSITE_RUN_FROM_PACKAGE` kan ha något av följande värden:

| Värde  | Beskrivning  |
|---------|---------|
| **`1`**  | Rekommenderas för Function-appar som körs i Windows. Kör från en paket fil i mappen `d:\home\data\SitePackages` i din Function-app. Om du inte [distribuerar med zip Deploy](#integration-with-zip-deployment)kräver det här alternativet att mappen också har en fil med namnet `packagename.txt`. Den här filen innehåller bara namnet på paket filen i mappen utan blank steg. |
|**`<URL>`**  | Platsen för en angiven paketfil som du vill köra. När du använder Blob Storage bör du använda en privat behållare med en [signatur för delad åtkomst (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) för att göra det möjligt för functions-körningen att komma åt paketet. Du kan använda [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att ladda upp paketfiler till ditt Blob Storage-konto. När du anger en URL måste du även [Synkronisera utlösare](functions-deployment-technologies.md#trigger-syncing) när du har publicerat ett uppdaterat paket. |

> [!CAUTION]
> När du kör en Function-app i Windows ger det externa URL-alternativet sämre kall start prestanda. När du distribuerar din Function-app till Windows ska du ställa in `WEBSITE_RUN_FROM_PACKAGE` till `1` och publicera med zip-distribution.

Följande visar en Function-app som kon figurer ATS för att köras från en. zip-fil som finns i Azure Blob Storage:

![WEBSITE_RUN_FROM_ZIP app-inställning](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> För närvarande stöds endast. zip-paketfiler.

## <a name="integration-with-zip-deployment"></a>Integrering med zip-distribution

[Zip-distribution][Zip deployment for Azure Functions] är en funktion i Azure App Service som gör att du kan distribuera ditt app-projekt till `wwwroot`-katalogen. Projektet paketeras som en. zip-distributions fil. Samma API: er kan användas för att distribuera paketet till `d:\home\data\SitePackages`-mappen. Med `WEBSITE_RUN_FROM_PACKAGE`-appens inställnings värde för `1`kopierar API: erna för zip-distribution ditt paket till `d:\home\data\SitePackages`-mappen i stället för att extrahera filerna till `d:\home\site\wwwroot`. Det skapar också `packagename.txt`-filen. Efter en omstart monteras paketet till `wwwroot` som ett skrivskyddat fil system. Mer information om zip-distribution finns i [zip-distribution för Azure Functions](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>Lägga till inställningen WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="use-key-vault-references"></a>Använda Key Vault referenser

För ökad säkerhet kan du använda Key Vault referenser tillsammans med din externa URL. Detta gör att URL: en krypteras i vila och gör det möjligt att använda Key Vault för hemlig hantering och rotation. Vi rekommenderar att du använder Azure Blob Storage så att du enkelt kan rotera den tillhör ande SAS-nyckeln. Azure Blob Storage är krypterat i vila, vilket skyddar dina program data när de inte distribueras på App Service.

1. Skapa en Azure Key Vault.

    ```azurecli
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus
    ```

1. Lägg till din externa URL som en hemlighet i Key Vault.

    ```azurecli
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<insert-your-URL>"
    ```

1. Skapa inställningen `WEBSITE_RUN_FROM_PACKAGE` app och ange värdet som en Key Vault referens till den externa URL: en.

    ```azurecli
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"
    ```

Se följande artiklar för mer information.

- [Key Vault referenser för App Service](../app-service/app-service-key-vault-references.md)
- [Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Felsökning

- Kör från paket gör `wwwroot` skrivskyddad så att du får ett fel meddelande när du skriver filer till den här katalogen.
- Tar-och gzip-format stöds inte.
- Den här funktionen skapar inte lokal cache.
- För förbättrad kall start prestanda använder du det lokala zip-alternativet (`WEBSITE_RUN_FROM_PACKAGE`= 1).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
