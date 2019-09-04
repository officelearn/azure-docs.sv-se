---
title: Snabb start – ange och hämta en hemlighet från Azure Key Vault med hjälp av en Node-webbapp | Microsoft Docs
description: I den här snabb starten ställer du in och hämtar en hemlighet från Azure Key Vault med hjälp av en Node-webbapp
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: quickstart
ms.date: 09/03/2010
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 4bea46e62f90a41b566781457a39718849ee0e15
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259232"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med hjälp av en Node-webbapp 

Den här snabb starten visar hur du lagrar en hemlighet i Azure Key Vault och hur du hämtar den med hjälp av en webbapp. Key Vault hjälper till att skydda informationen. Om du vill se det hemliga värdet måste du köra den här snabb starten på Azure. I snabbstarten används Node.js och hanterade identiteter för Azure-resurser. Lär dig att:

* Skapa ett nyckelvalv.
* Lagra en hemlighet i nyckelvalvet.
* Hämta en hemlighet från nyckelvalvet.
* Skapa ett Azure-webbprogram.
* Aktivera en [hanterad identitet](../active-directory/managed-service-identity/overview.md) för webbappen.
* Bevilja de behörigheter som krävs för att webbprogrammet ska kunna läsa data från nyckelvalvet.

Innan du fortsätter bör du kontrol lera att du är bekant med de [grundläggande begreppen för Key Vault](key-vault-whatis.md#basic-concepts).

> [!NOTE]
> Key Vault är en central lagringsplats för programmeringsbaserad lagring av hemligheter. Det här kräver att program och användare först autentiseras mot Key Vault, dvs. presenterar en hemlighet. Den här första hemligheten måste roteras med jämna mellanrum för att uppfylla de rekommenderade säkerhetsmetoderna. 
>
> Med [hanterade tjänstidentiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) får program som körs i Azure en identitet som hanteras automatiskt av Azure. Det här löser *problemet med den första hemligheten* så att användare och program kan följa bästa praxis utan att behöva bekymra sig om roteringen av den första hemligheten.

## <a name="prerequisites"></a>Förutsättningar

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -2.0.4 eller senare. Den här snabb starten kräver att du kör Azure CLI lokalt. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa [Installera Azure CLI 2.0](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Välj ett resursgruppnamn och fyll i platshållaren.
I följande exempel skapas en resurs grupp på platsen USA, östra.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Den resursgrupp du nyss skapade används i hela den här artikeln.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Därefter skapar du ett nyckel valv med hjälp av resurs gruppen som du skapade i föregående steg. Även om den här artikeln använder "ContosoKeyVault" som namn måste du använda ett unikt namn. Ange följande information:

* Nyckel valvs namn.
* Namn på resursgrupp. Namnet måste vara en sträng på 3 till 24 tecken och får endast innehålla 0–9, a–z, A–Z och ett bindestreck (-).
* Plats: **USA, östra**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Du kan lagra en SQL-anslutningssträng eller annan information som du behöver skydda men göra tillgänglig för ditt program. I den här självstudiekursen kallas lösenordet **AppSecret** och lagrar värdet för **MySecret**.

Skriv följande kommandon för att skapa en hemlighet i nyckelvalvet som kallas **AppSecret**. Den här hemligheten lagrar värdet **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Det här kommandot visar den hemliga informationen, inklusive URI:n. När du har slutfört de här stegen bör du ha en URI till en hemlighet i nyckelvalvet. Anteckna den här informationen. Du behöver den senare.

## <a name="clone-the-repo"></a>Klona lagringsplatsen

Klona lagringsplatsen för att skapa en lokal kopia där du kan redigera källan. Kör följande kommando:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Installera beroenden

Kör följande kommandon för att installera beroenden:

```
cd key-vault-node-quickstart
npm install
```

I det här projektet används två Node [-moduler: MS-rest – Azure](https://www.npmjs.com/package/ms-rest-azure) och [Azure-nyckel valv](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>Publicera webbappen i Azure

Skapa en [Azure App Service](https://azure.microsoft.com/services/app-service/) -plan. Du kan lagra flera webbappar i den här planen.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Skapa sedan en webbapp. I följande exempel ersätter `<app_name>` du med ett globalt unikt app-namn (giltiga tecken är a-z, 0-9 och-). Körningen är inställd på NODE|6.9. Om du vill se alla körningar som `az webapp list-runtimes`stöds kör du.

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
Bläddra till din nyligen skapade webbapp och se att den fungerar. Ersätt `<app_name>` med ett unikt appnamn.

    ```
    http://<app name>.azurewebsites.net
    ```
Föregående kommando skapar också en git-aktiverad app som gör att du kan distribuera till Azure från din lokala git-lagringsplats. Den lokala git-lagrings platsen har kon figurer ATS med `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`den här URL: en:.

När du har slutfört föregående kommando kan du lägga till en Azure-fjärrdatabas till din lokala git-lagringsplats. Ersätt `<url>` med URL: en för git-lagrings platsen.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Aktivera en hanterad identitet för webbappen

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. [Hanterade identiteter för Azure-resurser (översikt)](../active-directory/managed-identities-azure-resources/overview.md) löser detta problem på ett enklare sätt genom att ge Azure-tjänsterna en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden.

Kör kommandot assign-identity för att skapa identiteten för det här programmet:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Det här kommandot fungerar på samma sätt som när du går till portalen och väljer **På** för **Identitet/Systemtilldelad** i egenskaperna för webbprogrammet.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Tilldela behörigheter till ditt program att läsa hemligheter från Key Vault

Anteckna utdata från föregående kommando. De bör ha formatet:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Kör sedan följande kommando med namnet på ditt nyckel valv och värdet för **principalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Distribuera Node-appen till Azure och hämta det hemliga värdet

Kör följande kommando för att distribuera appen till Azure:

```
git push azure master
```

När du bläddrar till `https://<app_name>.azurewebsites.net`kan du se det hemliga värdet. Se till att du ersatte namnet `<YourKeyVaultName>` med ditt valv namn.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Key Vault och sparade en hemlighet. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](key-vault-overview.md)
- Se [Azure Key Vault Developer ' s guide](key-vault-developers-guide.md)
- Lär dig mer om [nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- Granska [Azure Key Vault bästa praxis](key-vault-best-practices.md)