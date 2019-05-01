---
title: Självstudie – Använda en virtuell Linux-dator och ett Python-program för att lagra hemligheter i Azure Key Vault | Microsoft Docs
description: I den här självstudien lär du dig att konfigurera ett Python-program till att läsa en hemlighet från Azure Key Vault.
services: key-vault
author: mbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: a5923c3d5ea7888a3737d7976caded8eef9c1661
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702109"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Självstudier: Använda en virtuell Linux-dator och en Python-app för att lagra hemligheter i Azure Key Vault

Med Azure Key Vault kan du skydda hemligheter, till exempel API-nycklar och databasanslutningssträngar som behövs för att komma åt dina program, tjänster samt IT-resurser.

I den här självstudien konfigurerar du en Azure-webbapp till att läsa information från Azure Key Vault med hjälp av hanterade identiteter för Azure-resurser. Lär dig att:

> [!div class="checklist"]
> * Skapa ett nyckelvalv
> * Lagra en hemlighet i nyckelvalvet
> * Skapa en virtuell Linux-dator
> * Aktivera en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för den virtuella datorn
> * Bevilja de behörigheter som krävs för att konsolprogrammet ska kunna läsa data från nyckelvalvet
> * Hämta en hemlighet från nyckelvalvet

Innan du fortsätter behöver du känna till [grundbegreppen om nyckelvalv](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Git](https://git-scm.com/downloads).
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* [Azure CLI version 2.0.4 eller senare](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) eller Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Förstå Hanterad tjänstidentitet

Azure Key Vault lagrar autentiseringsuppgifter på ett säkert sätt så att de inte ingår i din kod. För att hämta dem behöver du autentisera till Azure Key Vault. För att autentisera till Key Vault behöver du dock autentiseringsuppgifter. Det är ett klassiskt bootstrap-problem. Via Azure och Azure Active Directory (Azure AD) tillhandahåller Hanterad tjänstidentitet (MSI) en bootstrap-identitet som gör det enklare att komma igång.

När du aktiverar MSI för en Azure-tjänst som Virtual Machines, App Service eller Functions skapar Azure ett tjänsthuvudnamn för instansen av tjänsten i Azure AD. Det lägger in autentiseringsuppgifterna för tjänstens huvudnamn i instansen av tjänsten.

![MSI](media/MSI.png)

Koden anropar sedan en lokal metadatatjänst som är tillgänglig på Azure-resursen för att hämta en åtkomsttoken. Koden använder den åtkomsttoken som den får från den lokala MSI-slutpunkt för att autentisera till en Azure Key Vault-tjänst.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med hjälp av kommandot `az group create` på platsen USA, västra med följande kod. Ersätt `YourResourceGroupName` med ett valfritt namn.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Du använder den här resursgruppen i hela självstudien.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

I nästa steg skapar du ett nyckelvalv i den resursgrupp som du skapade i föregående steg. Ange följande information:

* Key Vault-namn: Namnet måste vara en sträng på 3 till 24 tecken och får endast innehålla 0–9, a–z, A–Z och bindestreck (-).
* Namn på resursgrupp.
* Plats: **USA, västra**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Det kan vara bra att lagra en SQL-anslutningssträng eller annan information som både behöver skyddas och vara tillgänglig i ditt program.

Skriv följande kommandon för att skapa en hemlighet i nyckelvalvet som kallas *AppSecret*. Den här hemligheten lagrar värdet **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Skapa en virtuell Linux-dator

Skapa en virtuell dator med hjälp av kommandot `az vm create`.

Följande exempel skapar virtuell dator med namnet **myVM** och lägger till ett användarkonto med namnet **azureuser**. Parametern `--generate-ssh-keys` genererar automatiskt en SSH-nyckel och placerar den på standardnyckelplatsen (**~/.ssh**). Om du i stället vill skapa en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn och stödresurser. Utdataresultatet i följande exempel anger att den virtuella datorn har skapats:

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Skriv ned din egen `publicIpAddress` i utdataresultatet från din virtuella dator. Du använder den här adressen för att komma åt den virtuella datorn i senare steg.

## <a name="assign-an-identity-to-the-vm"></a>Tilldela en identitet till den virtuella datorn

Skapa en systemtilldelad identitet till den virtuella datorn genom att köra följande kommando:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Kommandots utdata är som följer.

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Anteckna `systemAssignedIdentity`. Du använder den i nästa steg.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Ge den virtuella datorns identitet behörighet till Key Vault

Nu kan du ge Key Vault-behörighet till den identitet som du skapade. Kör följande kommando:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Logga in på den virtuella datorn

Logga in på den virtuella datorn med hjälp av en terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Installera Python-bibliotek på den virtuella datorn

Ladda ned och installera Python-biblioteket [requests](https://pypi.org/project/requests/2.7.0/) (begäranden) för att göra HTTP GET-anrop.

## <a name="create-edit-and-run-the-sample-python-app"></a>Skapa, redigera och köra Python-exempelappen

Skapa en Python-fil som heter **Sample.py**.

Öppna Sample.py och redigera den så att den innehåller följande kod:

```python
# importing the requests library
  import requests
  
# Step 1: Fetch an access token from an MSI-enabled Azure resource      
  # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
  MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
  r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
  # This request gets an access token from Azure Active Directory by using the local MSI endpoint
  data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
  KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
  kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

Föregående kod utför en process i två steg:

   1. Hämtar en token från den lokala MSI-slutpunkten på den virtuella datorn. Slutpunkten hämtar sedan en token från Azure Active Directory.
   1. Skickar token till nyckelvalvet och hämtar din hemlighet.

Kör följande kommando. Det hemliga värdet bör visas.

```console
python Sample.py
```

I den här självstudien har du lärt dig att använda Azure Key Vault med en Python-app som körs på en virtuell Linux-dator.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när du inte längre behöver dem. Det gör du genom att markera resursgruppen för den virtuella datorn och välja **Ta bort**.

Ta bort nyckelvalvet med hjälp av kommandot `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
