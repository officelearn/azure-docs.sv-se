---
title: Självstudie – använda Azure Key Vault med en virtuell Windows-dator i python | Microsoft Docs
description: I den här självstudien konfigurerar du ett ASP.NET Core-program för att läsa en hemlighet från ditt nyckel valv.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 3b965ab7dfafd6c78c801cf3692463efe366c852
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198090"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Självstudie: använda Azure Key Vault med en virtuell Windows-dator i python

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

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Förutsättningar

För Windows, Mac och Linux:
  * [Git](https://git-scm.com/downloads)
  * Den här självstudien kräver att du kör Azure CLI lokalt. Du måste ha Azure CLI-versionen 2.0.4 eller senare installerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa [Installera Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Om Hanterad tjänstidentitet

Azure Key Vault lagrar autentiseringsuppgifterna på ett säkert sätt så att de inte visas i din kod. Men måste du autentisera till Azure Key Vault för att hämta dina nycklar. För att autentisera till Key Vault behöver du autentiseringsuppgifter. Det är ett klassiskt bootstrap-problem. Hanterad tjänstidentitet (MSI) löser problemet genom att tillhandahålla en _bootstrapidentitet_ som förenklar processen.

När du aktiverar MSI för en Azure-tjänst, till exempel Azure Virtual Machines, Azure App Service eller Azure Functions, skapar Azure ett [huvud namn för tjänsten](basic-concepts.md). MSI gör detta för instansen av tjänsten i Azure Active Directory (Azure AD) och infogar autentiseringsuppgifterna för tjänstens huvud namn i den instansen. 

![MSI](media/MSI.png)

Sedan anropar din kod en lokal metadatatjänst som är tillgänglig på Azure-resursen för att få en åtkomsttoken. För att autentisera till en Azure Key Vault-tjänst använder din kod den åtkomsttoken som den hämtar från den lokala MSI-slutpunkten. 

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

Du använder den nya resurs gruppen i den här självstudien.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Ange följande information för att skapa ett nyckel valv i resurs gruppen som du skapade i föregående steg:

* Nyckel valvets namn: en sträng på 3 till 24 tecken som bara får innehålla siffror (0-9), bokstäver (a-z, A-Z) och bindestreck (-)
* Namn på resursgrupp
* Plats: **USA, västra**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
I det här läget är ditt Azure-konto det enda som har behörighet att utföra åtgärder på det nya nyckel valvet.

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Hemligheten kan vara en SQL-anslutningssträng eller annan information som du behöver för att hålla både säkra och tillgängliga för ditt program.

Om du vill skapa en hemlighet i nyckel valvet som heter **AppSecret**, anger du följande kommando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Den här hemligheten lagrar värdet **MySecret**.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Du kan skapa en virtuell dator med någon av följande metoder:

* [Azure CLI](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Tilldela en identitet till den virtuella datorn
I det här steget skapar du en systemtilldelad identitet för den virtuella datorn genom att köra följande kommando i Azure CLI:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observera den systemtilldelade identiteten som visas i följande kod. Utdata från föregående kommando skulle vara: 

```azurecli
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

Om du vill logga in på den virtuella datorn följer du anvisningarna i [Anslut och logga in på en virtuell Azure-dator som kör Windows](../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Skapa och kör en exempel-python-app

I nästa avsnitt finns en exempel fil med namnet *Sample.py*. Det använder ett [begär](https://2.python-requests.org/en/master/) ande bibliotek för att göra HTTP GET-anrop.

## <a name="edit-samplepy"></a>Redigera Sample.py

När du har skapat *Sample.py*öppnar du filen och kopierar koden i det här avsnittet. 

Koden visar en två stegs process:
1. Hämta en token från den lokala MSI-slutpunkten på den virtuella datorn.  
  Om du gör det hämtas även en token från Azure AD.
1. Skicka token till nyckel valvet och hämta sedan din hemlighet. 

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

Du kan visa det hemliga värdet genom att köra följande kod: 

```
python Sample.py
```

Följande kod visar hur du utför åtgärder med Azure Key Vault på en virtuell Windows-dator. 

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort den virtuella datorn och nyckel valvet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
