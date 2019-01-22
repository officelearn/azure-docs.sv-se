---
title: Självstudie – Använda Azure Key Vault med en virtuell Azure-dator i Python | Microsoft Docs
description: I den här självstudien konfigurerar du att ett Python-program ska läsa en hemlighet från nyckelvalvet
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
ms.openlocfilehash: 8c816d17807432d75b6102190fc37d25a525d7cf
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244179"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-virtual-machine-in-python"></a>Självstudier: Använda Azure Key Vault med en virtuell Azure-dator i Python

Med Azure Key Vault kan du skydda hemligheter, till exempel API-nycklar och databasanslutningssträngar som behövs för att komma åt dina program, tjänster samt IT-resurser.

I den här självstudien följer du de steg som behövs för att få ett Azure-webbprogram att läsa information från Azure Key Vault med hjälp av hanterade identiteter för Azure-resurser. Lär dig att:

> [!div class="checklist"]
> * Skapa ett nyckelvalv.
> * Lagra en hemlighet i nyckelvalvet.
> * Skapa en virtuell dator i Azure.
> * Aktivera en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för den virtuella datorn.
> * Bevilja de behörigheter som krävs för att konsolprogrammet ska kunna läsa data från nyckelvalvet.
> * Hämta en hemlighet från nyckelvalvet.

Innan du fortsätter rekommenderar vi att du läser avsnittet om [grundbegreppen om nyckelvalv](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Nödvändiga komponenter
För alla plattformar behöver du:

* Git ([ladda ned](https://git-scm.com/downloads)).
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 och senare. Tillgänglig för Windows, Mac och Linux.

### <a name="managed-service-identity-and-how-it-works"></a>Så här fungerar hanterad tjänstidentitet
I den här självstudien användas hanterad tjänstidentitet (MSI).

Azure Key Vault lagrar autentiseringsuppgifter på ett säkert sätt så att de inte ingår i din kod. För att hämta dem behöver du autentisera till Key Vault. För att autentisera till Key Vault behöver du autentiseringsuppgifter. Det är ett klassiskt bootstrap-problem. Via Azure och Azure Active Directory (Azure AD) tillhandahåller MSI en ”bootstrap-identitet” som gör det enklare att komma igång.

När du aktiverar MSI för en Azure-tjänst som Virtual Machines, App Service eller Functions skapar Azure [tjänstens huvudnamn](key-vault-whatis.md#basic-concepts) för instansen av tjänsten i Azure AD. Azure lägger in autentiseringsuppgifterna för tjänstens huvudnamn i instansen av tjänsten. 

![MSI](media/MSI.png)

Koden anropar sedan en lokal metadatatjänst som är tillgänglig på Azure-resursen för att hämta en åtkomsttoken.
Koden använder den åtkomsttoken som den får från den lokala MSI-slutpunkt för att autentisera till en Azure Key Vault-tjänst. 

## <a name="log-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Välj ett resursgruppnamn och fyll i platshållaren.
I följande exempel skapas en resursgrupp i regionen USA, västra:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Den resursgrupp du nyss skapade används i hela den här artikeln.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Nu ska du skapa ett nyckelvalv i resursgruppen som du skapade i föregående steg. Ange följande information:

* Key Vault-namn: Namnet måste vara en sträng på 3 till 24 tecken och får endast innehålla 0–9, a–z, A–Z och bindestreck (-).
* Namn på resursgrupp.
* Plats: **USA, västra**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-secret-to-the-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

Vi lägger till en hemlighet för att illustrera hur detta fungerar. Du kan lagra en SQL-anslutningssträng eller annan information som du behöver skydda men göra tillgänglig för ditt program.

Skriv följande kommandon för att skapa en hemlighet i nyckelvalvet som kallas *AppSecret*. Den här hemligheten lagrar värdet **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#az_vm_create).

Följande exempel skapar virtuell dator med namnet *myVM* och lägger till ett användarkonto med namnet *azureuser*. Parametern `--generate-ssh-keys` genererar automatiskt en SSH-nyckel och placerar den på standardnyckelplatsen (*~/.ssh*). Om du i stället vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn och stödresurser. Utdataresultatet i följande exempel anger att den virtuella datorn har skapats:

```
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

Skriv ned ditt eget `publicIpAddress`-värde i utdataresultatet från din virtuella dator. Den här adressen används för att ansluta till den virtuella datorn i nästa steg.

## <a name="assign-an-identity-to-the-virtual-machine"></a>Tilldela en identitet till den virtuella datorn
I det här steget skapar vi en systemtilldelad identitet till den virtuella datorn. Kör följande kommando i Azure CLI:

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Kommandots utdata är som följer. Anteckna värdet för **systemAssignedIdentity**. 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-the-virtual-machine-identity-permission-to-the-key-vault"></a>Ge identiteten för virtuell dator behörighet för Key Vault
Nu kan vi ge identiteten behörighet till nyckelvalvet. Kör följande kommando:

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-virtual-machine"></a>Logga in på den virtuella datorn

Logga in på den virtuella datorn genom att följa [den här självstudien](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-the-sample-python-app"></a>Skapa och köra Python-exempelappen

Följande exempelfil heter *Sample.py*. Den använder [begäransbiblioteket](https://pypi.org/project/requests/2.7.0/) för att göra HTTP GET-anrop.

## <a name="edit-samplepy"></a>Redigera Sample.py
När du har skapat Sample.py öppnar du filen och kopierar följande kod. Koden är en tvåstegsprocess: 
1. Hämta en token från den lokala MSI-slutpunkten på den virtuella datorn. Slutpunkten hämtar sedan en token från Azure Active Directory.
2. Skicka token till nyckelvalvet och hämta din hemlighet. 

```
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

När du kör kommandot nedan bör det hemliga värdet visas: 

```
python Sample.py
```

Följande kod visar hur du utför åtgärder med Azure Key Vault på en virtuell Windows-dator. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
