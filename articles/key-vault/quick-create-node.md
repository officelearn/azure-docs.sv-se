---
title: Snabbstart – Konfigurera och hämta en hemlighet från Azure Key Vault med hjälp av en Node-webbapp | Microsoft Docs
description: 'Snabbstart: Konfigurera och hämta en hemlighet från Azure Key Vault med hjälp av en Node-webbapp'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 08/08/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 4592b256dfda75e81a94034545cd54dbf0d71532
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42023625"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-node-web-app"></a>Snabbstart: Konfigurera och hämta en hemlighet från Azure Key Vault med hjälp av en Node-webbapp 

Den här snabbstarten beskriver hur du lagrar en hemlighet i Key Vault och hur du hämtar den med hjälp av en webbapp. För att visa det hemliga värdet skulle du behöva köra detta i Azure. I snabbstarten används Node.js och hanterade tjänstidentiteter (MSI)

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Lagra en hemlighet i Key Vault.
> * Hämta en hemlighet från Key Vault.
> * Skapa ett Azure-webbprogram.
> * [Aktivera hanterade tjänstidentiteter](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview).
> * Bevilja de behörigheter som krävs för att webbprogrammet ska kunna läsa data från Key Vault.

Försäkra dig om att du känner till [grundbegreppen](key-vault-whatis.md#basic-concepts) innan du fortsätter.

>[!NOTE]
För att förstå varför självstudien nedan är bästa praxis är det viktigt att du förstår vissa begrepp. Key Vault är en central lagringsplats för programmeringsbaserad lagring av hemligheter. Detta kräver att program och användare först autentiseras mot Key Vault, dvs. presenterar en hemlighet. Den här första hemligheten måste dessutom roteras med jämna mellanrum för att uppfylla de rekommenderade säkerhetsmetoderna. Men [hanterad tjänstidentitet (MSI)](../active-directory/managed-service-identity/overview.md) tilldelas program som körs i Azure en identitet som hanteras automatiskt av Azure. Detta löser **problemet med den första hemligheten** där användare och program kan följa bästa praxis utan att behöva bekymra sig om roteringen av den första hemligheten

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Node JS](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 eller senare
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="login-to-azure"></a>Logga in till Azure

Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Välj ett resursgruppnamn och fyll i platshållaren.
I följande exempel skapas en resursgrupp med namnet *<YourResourceGroupName>* på platsen *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Resursgruppen som du nyss skapade används i hela den här självstudiekursen.

## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault

Nu ska du skapa ett nyckelvalv i resursgruppen som du skapade i föregående steg. ”ContosoKeyVault” används som namn på nyckelvalvet i hela den här artikeln, men det är viktigt att poängtera att du måste använda ett unikt namn. Ange följande information:

* Valvnamn – **välj ett Key Vault-namn här**.
* Resursgruppsnamn – **välj ett resursgruppsnamn här**.
* Plats – **USA, östra**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Du kan lagra en SQL-anslutningssträng eller annan information som du behöver skydda men göra tillgänglig för ditt program. I den här självstudiekursen kallas lösenordet **AppSecret** och lagrar värdet för **MySecret**.

Skriv kommandona nedan för att skapa en hemlighet i Key Vault med namnet **AppSecret** som ska lagra värdet **MySecret**:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Det här kommandot visar den hemliga informationen, inklusive URI:n. När du har slutfört de här stegen bör du ha en URI till en hemlighet i Azure Key Vault. Anteckna den här informationen. Du behöver den senare.

## <a name="clone-the-repo"></a>Klona lagringsplatsen

Klona lagringsplatsen för att skapa en lokal kopia så att du kan redigera källan genom att köra följande kommando:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Installera beroenden

Här installera vi beroendena. Kör följande kommandon: cd key-vault-node-quickstart npm install

I det här projektet används 2 Node-moduler:

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>Publicera webbappen till Azure

Nedan beskrivs de få steg som krävs

- Det första steget är att skapa en [Azure App Service](https://azure.microsoft.com/services/app-service/)-plan. Du kan lagra flera webbappar i den här planen.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- Därefter skapar vi en webbapp. I följande exempel ersätter du <app_name> med ett globalt, unikt appnamn (giltiga tecken är a-z, 0-9 och -). Körningen är inställd på NODE|6.9. Kör az webapp list-runtimes om du vill visa alla körningar
    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
    När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:
    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
    Gå till den nya webbappen, så bör du se en fungerande webbapp. Ersätt <app_name> med ett unikt appnamn.

    ```
    http://<app name>.azurewebsites.net
    ```
    Kommandot ovan skapar även en Git-aktiverad app som gör att du kan distribuera till Azure från din lokala git-lagringsplats. 
    Den lokala git-lagringsplatsen konfigureras med URL:en ”https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git”

- Skapa en distributionsanvändare. När det föregående kommandot har slutförts kan du lägga till en Azure-fjärranslutning till din lokala Git-lagringsplats. Ersätt <url> med URL:en för den fjärranslutna Git-lagringsplats som du fick från Enable Git (Aktivera Git) för din app.

    ```
    git remote add azure <url>
    ```

## <a name="enable-managed-service-identity"></a>Aktivera Hanterad tjänstidentitet

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. Hanterad tjänstidentitet (MSI) löser detta problem på ett enklare sätt genom att ge Azure-tjänsterna en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden.

Kör kommandot assign-identity för att skapa identiteten för det här programmet:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Det här kommandot fungerar på samma sätt som när du går till portalen och väljer **På** för **Hanterad tjänstidentitet** i egenskaperna för webbprogrammet.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Tilldela behörigheter till ditt program att läsa hemligheter från Key Vault

Skriv ned eller kopiera utdata från kommandot ovan. De bör ha formatet:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Kör sedan det här kommandot med namnet på ditt Key Vault och värdet för PrincipalId som kopieras från ovan:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Distribuera Node-appen till Azure och hämta det hemliga värdet

Nu är allt konfigurerat. Distribuera appen till Azure genom att köra följande kommando

```
git push azure master
```

När du har gjort det och går till https://<app_name>.azurewebsites.net kan du se det hemliga värdet.
Kontrollera att du ersatte namnet <YourKeyVaultName> med namnet på ditt valv

## <a name="next-steps"></a>Nästa steg

* [Startsida för Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Dokumentation om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK för Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Azure REST API-referens](https://docs.microsoft.com/rest/api/keyvault/)
