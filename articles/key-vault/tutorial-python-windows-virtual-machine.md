---
title: Självstudiekurs – använda Azure Key Vault med en Windows-dator i Python | Microsoft Docs
description: I den här självstudien konfigurerar du ett ASP.NET core-program att läsa en hemlighet från ditt nyckelvalv.
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 133de5410d5e506c9528e2dba90dd4c00d8fcc2d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438218"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Självstudier: Använda Azure Key Vault med en Windows-dator i Python

Azure Key Vault hjälper dig att skydda hemligheter, till exempel API-nycklar, databasanslutningssträngar som du behöver komma åt dina program, tjänster och IT-resurser.

I den här självstudien får du lära dig hur du hämtar ett konsolprogram för att läsa information från Azure Key Vault. Om du vill göra det använder du hanterade identiteter för Azure-resurser. 

Självstudien visar hur du:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Lägg till en hemlighet i nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.
> * Skapa en virtuell dator i Azure.
> * Aktivera en hanterad identitet.
> * Tilldela behörigheter till identitet för virtuell dator.

Innan du börjar läsa [grundläggande begrepp för Key Vault](key-vault-whatis.md#basic-concepts). 

Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Förutsättningar

För Windows, Mac och Linux:
  * [Git](https://git-scm.com/downloads)
  * Den här självstudien kräver att du kör Azure CLI lokalt. Du måste ha Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa [Installera Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Om Hanterad tjänstidentitet

Azure Key Vault lagrar autentiseringsuppgifter på ett säkert sätt, så att de inte visas i din kod. Men måste du autentisera till Azure Key Vault för att hämta dina nycklar. För att autentisera till Key Vault behöver du autentiseringsuppgifter. Det är ett klassiskt bootstrap-problem. Hanterad tjänstidentitet (MSI) löser problemet genom att tillhandahålla en _bootstrapidentitet_ som förenklar processen.

När du aktiverar MSI för en Azure-tjänst, till exempel Azure Virtual Machines, Azure App Service eller Azure Functions, Azure skapar en [tjänstens huvudnamn](key-vault-whatis.md#basic-concepts). MSI gör detta för instansen av tjänsten i Azure Active Directory (Azure AD) och lägger in autentiseringsuppgifter för tjänstens huvudnamn i den instansen. 

![MSI](media/MSI.png)

Om du vill få en åtkomsttoken, anropar sedan koden en lokala metadata-tjänst som är tillgängliga på Azure-resursen. Koden använder åtkomsttoken som får den från den lokala MSI-slutpunkten för att autentisera till en Azure Key Vault-tjänsten. 

## <a name="log-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). 

Välj ett resursgruppnamn och fyll i platshållaren. I följande exempel skapas en resursgrupp i regionen USA, västra:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Du kan använda din resursgrupp du skapade i den här självstudien.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Ange följande information för att skapa ett nyckelvalv i resursgruppen som du skapade i föregående steg:

* Namn på Key vault: en sträng på 3 till 24 tecken som får endast innehålla siffror (0-9), bokstäver (a – z, A-Z) och bindestreck (-)
* Namn på resursgrupp
* Plats: **USA, västra**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Ditt Azure-konto är nu den enda som har behörighet för att utföra åtgärder på den här nya key vault.

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Hemligheten kan vara en SQL-anslutningssträng eller annan information som du behöver både är skyddade och tillgängliga för ditt program.

Om du vill skapa en hemlighet i nyckelvalvet med namnet **AppSecret**, anger du följande kommando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Den här hemligheten lagrar värdet **MySecret**.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Du kan skapa en virtuell dator genom att använda någon av följande metoder:

* [Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-an-identity-to-the-vm"></a>Tilldela en identitet till den virtuella datorn
I det här steget skapar du en automatiskt genererad identitet för den virtuella datorn genom att köra följande kommando i Azure CLI:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observera systemtilldelade identiteten som visas i följande kod. Utdata från föregående kommando skulle vara: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Tilldela behörigheter till identitet för virtuell dator
Nu kan du tilldela behörigheterna som tidigare skapade identitet till nyckelvalvet genom att köra följande kommando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Logga in på den virtuella datorn

Om du vill logga in på den virtuella datorn, följer du anvisningarna i [Connect och logga in på Azure-datorer som kör Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-a-sample-python-app"></a>Skapa och köra en Python-exempelapp

I nästa avsnitt är en exempelfil med namnet *Sample.py*. Den använder en [begäranden](http://docs.python-requests.org/en/master/) biblioteket för att göra HTTP GET-anrop.

## <a name="edit-samplepy"></a>Redigera Sample.py

När du har skapat *Sample.py*, öppna filen och kopiera sedan koden i det här avsnittet. 

Koden visar två steg:
1. Hämta en token från den lokala MSI-slutpunkten på den virtuella datorn.  
  Gör det även hämtar en token från Azure AD.
1. Skicka token till ditt nyckelvalv och hämta din hemlighet. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Du kan visa hemligt värde genom att köra följande kod: 

```
python Sample.py
```

Följande kod visar hur du utför åtgärder med Azure Key Vault på en virtuell Windows-dator. 

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort den virtuella datorn och nyckelvalvet när de inte längre behövs.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
