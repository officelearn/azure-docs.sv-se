---
title: Rotations guide för resurser med två uppsättningar autentiseringsuppgifter
description: I den här självstudien får du lära dig hur du automatiserar rotationen av en hemlighet för resurser som använder två uppsättningar autentiseringsuppgifter för autentisering.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: a061cf493fba99c518448acd9c4bf4bd5949eb98
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94831822"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>Automatisera rotationen av en hemlighet för resurser med två uppsättningar autentiseringsuppgifter för autentisering

Det bästa sättet att autentisera till Azure-tjänster är genom att använda en [hanterad identitet](../general/authentication.md), men det finns vissa scenarier där det inte är ett alternativ. I dessa fall används åtkomst nycklar eller lösen ord. Åtkomst nycklar och lösen ord bör roteras ofta.

Den här självstudien visar hur du automatiserar den periodiska rotationen av hemligheter för databaser och tjänster som använder två uppsättningar autentiseringsuppgifter för autentisering. Mer specifikt roterar Azure Storage konto nycklar som lagras i Azure Key Vault som hemligheter med hjälp av en funktion som utlöses av Azure Event Grid meddelande. :

> [!NOTE]
> Lagrings konto nycklar kan hanteras automatiskt i Key Vault genom att tillhandahålla signatur-token för delad åtkomst till lagrings kontot. Det finns tjänster som kräver anslutnings strängen för lagrings kontot med åtkomst nyckeln och för det scenariot rekommenderas den här lösningen

![Diagram över rotations lösning](../media/secrets/rotation-dual/rotation-diagram.png)

I ovanstående lösning lagrar Azure Key Vault lagrings konto enskilda åtkomst nycklar som versioner av samma hemliga växel mellan primär och sekundär nyckel i senare versioner. Eftersom en åtkomst nyckel lagras i den senaste versionen av hemligheten, kommer den alternativa nyckeln att återskapas och läggas till Key Vault som ny och den senaste versionen av hemligheten. Den här lösningen ger program hela rotations cykeln för att uppdatera till den nyaste återskapade nyckeln. 

1. 30 dagar före utgångs datumet för en hemlighet, Key Vault publicerar händelsen "nära förfallo datum" för att Event Grid.
1. Event Grid kontrollerar händelse prenumerationerna och använder HTTP POST för att anropa funktions-app-slutpunkten som prenumereras på händelsen.
1. Function-appen identifierar en alternativ nyckel (förutom senaste) och anropar lagrings kontot för att återskapa det
1. Function-appen lägger till ny återskapad nyckel till Azure Key Vault som ny version av hemligheten.

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Key Vault
* Två Azure Storage konton

Nedan kan du använda distributions länken, om du inte har befintliga nyckel valv och lagrings konton:

[![Bild som visar en knapp med etiketten "distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Under **resurs grupp** väljer du **Skapa ny**. Namnge gruppen **akvrotation** och klicka på **OK**.
1. Välj **Granska + skapa**.
1. Välj **Skapa**

    ![Skapa en resursgrupp](../media/secrets/rotation-dual/dual-rotation-1.png)

Nu har du ett nyckel valv och två lagrings konton. Du kan kontrol lera den här installationen i Azure CLI genom att köra följande kommando:

```azurecli
az resource list -o table -g akvrotation
```

Resultatet ser ut ungefär så här:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>Skapa och distribuera lagrings kontots nyckel rotations funktion

Skapa sedan en Function-app med en Systemhanterad identitet, förutom de andra nödvändiga komponenterna, och distribuera nyckel rotations funktioner för lagrings konto

Funktionen appens rotations funktioner kräver dessa komponenter och konfigurationen:
- En Azure App Service plan
- Ett lagrings konto som krävs för hantering av funktions programs utlösare
- En åtkomst princip för åtkomst till hemligheter i Key Vault
- Tilldela tjänst rollen lagrings konto nyckel ansvarig för att få åtkomst till lagrings kontots åtkomst nycklar
- Nyckel rotations funktioner för lagrings konto med händelse utlösare och http-utlösare (rotation på begäran)
- EventGrid händelse prenumeration för **SecretNearExpiry** -händelse

1. Välj distributions länk för Azure-mallar: 

   [![Bild som visar en knapp med etiketten "distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. I listan **resurs grupp** väljer du **akvrotation**.
1. I **lagrings kontots RG** anger du namnet på den resurs grupp där ditt lagrings konto finns. Behåll standardvärdet **[resourceGroup (). name]** om ditt lagrings konto redan finns i samma resurs grupp där du distribuerar nyckel rotations funktionen.
1. I **lagrings konto namn** anger du namnet på lagrings kontot med åtkomst nycklar som ska roteras.
1. I **Key Vault RG** anger du namnet på den resurs grupp där nyckel valvet finns. Behåll standardvärdet **[resourceGroup (). name]** om det redan finns ett nyckel valv i samma resurs grupp där du distribuerar nyckel rotations funktionen.
1. I **Key Vault namn** anger du namnet på nyckel valvet.
1. I **Funktionsapp namn** anger du namnet på Function-appen.
1. I **hemligt namn** anger du ett hemligt namn där åtkomst nycklar ska lagras.
1. I **lagrings platsen-URL** anger du funktions koden GitHub Location ( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** ).
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

   ![Granska och skapa det första lagrings kontot](../media/secrets/rotation-dual/dual-rotation-2.png)

När du har slutfört föregående steg har du ett lagrings konto, en Server grupp, en Function-app, Application Insights. Du bör se skärmen nedan när distributionen är klar: ![ distributionen är klar](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Om det uppstår problem kan du klicka på **distribuera** om för att slutföra distributionen av återstående komponenter.


Du hittar koden för mallar för distribution och rotations funktioner på [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-storage-account-access-key-to-key-vault"></a>Lägg till lagrings kontots åtkomst nyckel i Key Vault

Börja med att ange åtkomst principen för att bevilja behörighet att *Hantera hemligheter* till användare:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Nu kan du skapa en ny hemlighet med lagrings kontots åtkomst nyckel som värde. Du behöver också lagrings kontots resurs-ID, den hemliga giltighets perioden och nyckel-ID: t som ska läggas till i hemligheten, så rotations funktionen kan återskapa nyckeln i lagrings kontot.

Hämta resurs-ID för lagrings konto. Det går att hitta värdet under `id` egenskap
```azurecli
az storage account show -n akvrotationstorage
```

Visa en lista över åtkomst nycklar för lagrings kontot för att hämta nyckel värden

```azurecli
az storage account keys list -n akvrotationstorage 
```

Fyll i hämtade värden för **key1Value** och **storageAccountResourceId**

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Genom att skapa en hemlighet med ett kort utgångs datum publiceras en `SecretNearExpiry` händelse inom flera minuter, vilket i sin tur utlöser funktionen för att rotera hemligheten.

Du kan kontrol lera att åtkomst nycklar återskapas genom att hämta och jämföra lagrings konto nycklar och Key Vault hemlighet.

Du kan visa hemlig information med kommandot nedan:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Observera att `CredentialId` har uppdaterats till alternerande `keyName` och `value` återskapade ![ utdata av AZ-valvets hemliga show för det första lagrings kontot](../media/secrets/rotation-dual/dual-rotation-4.png)

Hämta åtkomst nycklar för att verifiera värde
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Utdata från listan med AZ lagrings konto nycklar för det första lagrings kontot](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>Lägg till ytterligare lagrings konton för rotation

Samma Function-app kan återanvändas för att rotera flera lagrings konton. 

Att lägga till ytterligare lagrings konto nycklar för rotering till befintlig funktion kräver:
- Tilldela tjänst rollen lagrings konto nyckel ansvarig för att få åtkomst till lagrings kontots åtkomst nycklar
- EventGrid händelse prenumeration för **SecretNearExpiry** -händelse

1. Välj distributions länk för Azure-mallar: 

   [![Bild som visar en knapp med etiketten "distribuera till Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. I listan **resurs grupp** väljer du **akvrotation**.
1. I **lagrings kontots namn** skriver du namnet på lagrings kontot med åtkomst nycklar som ska roteras
1. I **Key Vault namn** skriver du namnet på nyckel valvet
1. I **Funktionsapp namn** skriver du namnet på appens funktion
1. I **hemligt namn** skriver du ett hemligt namn där åtkomst nycklar ska lagras
1. Välj **Granska + skapa**.
1. Välj **Skapa**

   ![Granska och skapa det andra lagrings kontot](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Lägg till en annan lagrings konto åtkomst nyckel i Key Vault

Hämta resurs-ID för lagrings konto. Det går att hitta värdet under `id` egenskap
```azurecli
az storage account show -n akvrotationstorage2
```

Visa en lista över åtkomst nycklar för lagrings kontot för att hämta key2-värde

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Fyll i hämtade värden för **key2Value** och **storageAccountResourceId**

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Visa hemlig information med kommandot nedan:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Observera att `CredentialId` har uppdaterats till alternerande `keyName` och `value` återskapade ![ utdata av AZ-valvets hemliga show för det andra lagrings kontot](../media/secrets/rotation-dual/dual-rotation-8.png)

Hämta åtkomst nycklar för att verifiera värde
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Utdata från listan med AZ lagrings konto nycklar för det andra lagrings kontot](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>Tillgängliga Key Vaults rotations funktioner för dubbla autentiseringsuppgifter

- [Lagringskonto](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis Cache](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>Läs mer
- Översikt: [övervaka Key Vault med Azure Event Grid](../general/event-grid-overview.md)
- Gör så här: [skapa din första funktion i Azure Portal](../../azure-functions/functions-create-first-azure-function.md)
- Gör så här: [ta emot e-post när hemliga nyckel valv ändringar](../general/event-grid-logicapps.md)
- [Azure Event Grid händelse schema för Azure Key Vault](../../event-grid/event-schema-key-vault.md)
