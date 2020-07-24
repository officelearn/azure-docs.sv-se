---
title: Självstudie – använda Azure Key Vault med en virtuell dator i python | Microsoft Docs
description: I den här självstudien konfigurerar du ett ASP.NET Core-program för att läsa en hemlighet från ditt nyckel valv.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 453307b304c4cb1899b1de31117c944ac66fcddb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101882"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Självstudie: använda Azure Key Vault med en virtuell dator i python

Azure Key Vault hjälper dig att skydda hemligheter, t. ex. API-nycklar, databas anslutnings strängarna som du behöver för att få åtkomst till dina program, tjänster och IT-resurser.

I den här självstudien får du lära dig hur du hämtar ett konsol program för att läsa information från Azure Key Vault. Om du vill göra det använder du hanterade identiteter för Azure-resurser. 

Självstudien visar hur du:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Lägg till en hemlighet i nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.
> * Skapa en virtuell dator i Azure.
> * Aktivera en hanterad identitet.
> * Tilldela behörigheter till den virtuella datorns identitet.

Läs [Key Vault grundläggande koncept](basic-concepts.md)innan du börjar. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Förutsättningar

För Windows, Mac och Linux:
  * [Git](https://git-scm.com/downloads)
  * Den här självstudien kräver att du kör Azure CLI lokalt. Du måste ha Azure CLI-versionen 2.0.4 eller senare installerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Hemligheten kan vara en SQL-anslutningssträng eller annan information som du behöver för att hålla både säkra och tillgängliga för ditt program.

Om du vill skapa en hemlighet i nyckel valvet som heter **AppSecret**, anger du följande kommando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Den här hemligheten lagrar värdet **MySecret**.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Du kan skapa en virtuell dator med någon av följande metoder:

* [Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Tilldela en identitet till den virtuella datorn
I det här steget skapar du en systemtilldelad identitet för den virtuella datorn genom att köra följande kommando i Azure CLI:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observera den systemtilldelade identiteten som visas i följande kod. Utdata från föregående kommando skulle vara: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Tilldela behörigheter till VM-identiteten
Nu kan du tilldela de tidigare skapade identitets behörigheterna till ditt nyckel valv genom att köra följande kommando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Logga in på den virtuella datorn

Om du vill logga in på den virtuella datorn följer du anvisningarna i [Anslut och logga in på en virtuell Azure-dator som kör Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Skapa och kör en exempel-python-app

I nästa avsnitt finns en exempel fil med namnet *Sample.py*. Det använder ett [begär](https://2.python-requests.org/en/master/) ande bibliotek för att göra HTTP GET-anrop.

## <a name="edit-samplepy"></a>Redigera Sample.py

När du har skapat *Sample.py*öppnar du filen och kopierar koden i det här avsnittet. 

Koden visar en två stegs process:
1. Hämta en token från den lokala MSI-slutpunkten på den virtuella datorn.  
  Om du gör det hämtas även en token från Azure AD.
1. Skicka token till nyckel valvet och hämta sedan din hemlighet. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Du kan visa det hemliga värdet genom att köra följande kod: 

```console
python Sample.py
```

Följande kod visar hur du utför åtgärder med Azure Key Vault på en virtuell Windows-dator. 

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort den virtuella datorn och nyckel valvet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
