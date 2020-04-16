---
title: Självstudiekurs - Använd Azure Key Vault med en virtuell Windows-dator i Python | Microsoft-dokument
description: I den här självstudien konfigurerar du ett ASP.NET kärnprogram för att läsa en hemlighet från nyckelvalvet.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 85dc751909f61eff40ad57868bafa7ea2019ed89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422874"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Självstudiekurs: Använd Azure Key Vault med en virtuell Windows-dator i Python

Azure Key Vault hjälper dig att skydda hemligheter som API-nycklar, databasanslutningssträngar du behöver för att komma åt dina program, tjänster och IT-resurser.

I den här självstudien får du lära dig hur du får ett konsolprogram för att läsa information från Azure Key Vault. För att göra det använder du hanterade identiteter för Azure-resurser. 

Självstudien visar hur du:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Lägg till en hemlighet i nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.
> * Skapa en virtuell dator i Azure.
> * Aktivera en hanterad identitet.
> * Tilldela behörigheter till vm-identiteten.

Innan du börjar läser du [grundläggande begrepp för Nyckelvalv](basic-concepts.md). 

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Krav

För Windows, Mac och Linux:
  * [Git](https://git-scm.com/downloads)
  * Den här självstudien kräver att du kör Azure CLI lokalt. Du måste ha Azure CLI version 2.0.4 eller senare installerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Om Hanterad tjänstidentitet

Azure Key Vault lagrar autentiseringsuppgifter på ett säkert sätt, så att de inte visas i din kod. Men måste du autentisera till Azure Key Vault för att hämta dina nycklar. För att autentisera till Key Vault behöver du autentiseringsuppgifter. Det är ett klassiskt bootstrap-problem. Hanterad tjänstidentitet (MSI) löser problemet genom att tillhandahålla en _bootstrapidentitet_ som förenklar processen.

När du aktiverar MSI för en Azure-tjänst, till exempel Virtuella Azure-datorer, Azure App Service eller Azure Functions, skapar Azure ett [tjänsthuvudnamn](basic-concepts.md). MSI gör detta för instansen av tjänsten i Azure Active Directory (Azure AD) och injicerar tjänstens huvudreferenser i den instansen. 

![MSI](../media/MSI.png)

För att få en åtkomsttoken anropar koden sedan en lokal metadatatjänst som är tillgänglig på Azure-resursen. För att autentisera till en Azure Key Vault-tjänst använder din kod åtkomsttoken som den får från den lokala MSI-slutpunkten. 

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

Du kan använda den nyskapade resursgruppen i hela den här självstudien.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Om du vill skapa ett nyckelvalv i resursgruppen som du skapade i föregående steg anger du följande information:

* Nyckelvalvsnamn: en sträng med 3 till 24 tecken som bara kan innehålla siffror (0–9), bokstäver (a-z, A-Ö) och bindestreck (-)
* Namn på resursgrupp
* Plats: **Västra USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Nu är ditt Azure-konto det enda som har behörighet att utföra åtgärder på det här nya nyckelvalvet.

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Hemligheten kan vara en SQL-anslutningssträng eller annan information som du behöver för att hålla både säker och tillgänglig för ditt program.

Om du vill skapa en hemlighet i nyckelvalvet **appsecret**anger du följande kommando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Den här hemligheten lagrar värdet **MySecret**.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Du kan skapa en virtuell dator med någon av följande metoder:

* [The Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Tilldela en identitet till den virtuella datorn
I det här steget skapar du en systemtilldelad identitet för den virtuella datorn genom att köra följande kommando i Azure CLI:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observera den systemtilldelade identitet som visas i följande kod. Resultatet av föregående kommando skulle vara: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Tilldela behörigheter till vm-identiteten
Nu kan du tilldela de tidigare skapade identitetsbehörigheterna till nyckelvalvet genom att köra följande kommando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Logga in på den virtuella datorn

Om du vill logga in på den virtuella datorn följer du instruktionerna i [Anslut och loggar in på en virtuell Azure-dator som kör Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Skapa och kör ett exempel på Python-appen

I nästa avsnitt finns en exempelfil med namnet *Sample.py*. Den använder ett [begärandebibliotek](https://2.python-requests.org/en/master/) för att ringa HTTP GET-samtal.

## <a name="edit-samplepy"></a>Redigera Sample.py

När du har *skapat Sample.py*öppnar du filen och kopierar sedan koden i det här avsnittet. 

Koden visar en tvåstegsprocess:
1. Hämta en token från den lokala MSI-slutpunkten på den virtuella datorn.  
  Om du gör det hämtas också en token från Azure AD.
1. Skicka token till ditt nyckelvalv och hämta sedan din hemlighet. 

```python
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

Du kan visa det hemliga värdet genom att köra följande kod: 

```console
python Sample.py
```

Följande kod visar hur du utför åtgärder med Azure Key Vault på en virtuell Windows-dator. 

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort den virtuella datorn och nyckelvalvet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
