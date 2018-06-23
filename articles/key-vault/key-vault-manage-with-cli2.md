---
title: Hantera Azure Key Vault med hjälp av CLI | Microsoft Docs
description: Använd den här artikeln för att automatisera vanliga uppgifter i Nyckelvalvet med hjälp av CLI 2.0
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: barclayn
ms.openlocfilehash: 8c2501b5e89e81709de074c0b0c93b317ecebd7b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316602"
---
# <a name="manage-key-vault-using-cli-20"></a>Hantera Nyckelvalv med hjälp av CLI 2.0

Den här artikeln beskriver hur du kommer igång med Azure Key Vault använder Azure CLI 2.0. Du kan se information på:
- Så här skapar du en förstärkt behållare (ett valv) i Azure
- Hur du lagrar och hanterar kryptografiska nycklar och hemligheter i Azure. 
- Använda Azure CLI för att skapa ett valv.
- Skapa en nyckel eller ett lösenord som du sedan kan använda med ett Azure-program. 
- Hur ett program kan använda nyckeln eller lösenordet.

Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).


> [!NOTE]
> Den här artikeln innehåller inte instruktioner om hur du skriver det Azure-programmet att något av steg innehåller, som visar hur du tillåter ett program att använda en nyckel eller hemlighet i nyckelvalvet.
>

En översikt över Azure Key Vault finns [vad är Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda Azure CLI-kommandona i den här artikeln, måste du ha följande:

* En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Kommandoradsgränssnitt version 2.0 eller senare. Om du vill installera den senaste versionen, [installera och konfigurera Azure plattformsoberoende kommandoradsgränssnittet 2.0](/cli/azure/install-azure-cli).
* Ett program som ska konfigureras för att använda nyckeln eller lösenord som du skapar i den här artikeln. Ett exempelprogram är tillgängligt från [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=45343). Anvisningar finns i filen Viktigt inkluderas.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Få hjälp med Azure plattformsoberoende-kommandoradsgränssnittet
Den här artikeln förutsätter att du är bekant med kommandoradsgränssnittet (Bash, Terminal, Kommandotolken).

--Hjälp eller -h parametern kan användas för att visa hjälpen för specifika kommandon. Alternativt kan hjälpa Azure [kommando] [alternativ]-format kan också användas för. När osäkra om de parametrar som krävs av ett kommando, finns i hjälpen. Till exempel returnera alla följande kommandon samma information:

```azurecli-interactive
az account set --help
az account set -h
```

Du kan också läsa följande artiklar för att bekanta dig med Azure Resource Manager i Azure plattformsoberoende-kommandoradsgränssnittet:

* [Installera Azure CLI](/cli/azure/install-azure-cli)
* [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Ansluta till dina prenumerationer

För att logga in interaktivt, använder du följande kommando:

```azurecli
az login
```
Du kan skicka in ditt användarnamn och lösenord för att logga in med ett organisationskonto.

```azurecli
az login -u username@domain.com -p password
```

Skriv följande om du vill visa prenumerationer för ditt konto om du har mer än en prenumeration och du måste ange som ska användas:

```azurecli
az account list
```

Ange en prenumeration med parametern prenumeration.

```azurecli
az account set --subscription <subscription name or ID>
```

Mer information om hur du konfigurerar Azure plattformsoberoende kommandoradsgränssnittet finns [installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Skapa en ny resursgrupp
När du använder Azure Resource Manager, skapas alla relaterade resurser i en resursgrupp. Du kan skapa ett nyckelvalv i en befintlig resursgrupp. Om du vill använda en ny resursgrupp kan du skapa en ny.

```azurecli
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Den första parametern är resursgruppens namn och den andra parametern är platsen. Om du vill hämta en lista över alla möjliga skriver platser du:

```azurecli
az account list-locations
``` 

## <a name="register-the-key-vault-resource-provider"></a>Registerresursleverantören Key Vault
 Du kan se felet ”prenumerationen har inte registrerats för användning av namnrymden 'Microsoft.KeyVault'” när du försöker skapa ett nytt nyckelvalv. Om meddelandet visas, kontrollerar du att Key Vault-resursprovidern har registrerats i din prenumeration. Det här är en engångsåtgärd för varje prenumeration.

```azurecli
az provider register -n Microsoft.KeyVault
```


## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Använd den `az keyvault create` kommando för att skapa en nyckelvalvet. Det här skriptet har tre obligatoriska parametrar: en resursgruppens namn, ett nyckelvalv namn och den geografiska platsen.

Skapa ett nytt valv med namnet **ContosoKeyVault**, i resursgrupp **ContosoResourceGroup**, som finns i den **Östasien** plats, typ: 

```azurecli
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Kommandots utdata visar egenskaper för nyckelvalvet som du har skapat. De två viktigaste egenskaperna är:

* **namnet**: I det här exemplet har namnet ContosoKeyVault. Du ska använda det här namnet för andra Key Vault-kommandon.
* **vaultUri**: I det här exemplet URI är https://contosokeyvault.vault.azure.net. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu har ditt Azure-konto behörighet att utföra åtgärder i det här nyckelvalvet. Från och med ännu, har ingen annan behörighet.

## <a name="add-a-key-secret-or-certificate-to-the-key-vault"></a>Lägga till en nyckel eller hemlighet certifikatet i nyckelvalvet

Om du vill Azure Key Vault för att skapa en programvaruskyddad nyckel som du använder den `az key create` kommando.

```azurecli
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```

Om du har en befintlig nyckel i en PEM-filen kan överföra du den till Azure Key Vault. Du kan välja att skydda nyckeln med programvaran eller HSM. Använd följande för att importera nyckeln från PEM-filen och skyddar dem med programvara:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'Pa$$w0rd' --protection software
```

Nu kan du referera den nyckel som du har skapat eller överföras till Azure Key Vault med hjälp av dess URI. Använd **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** alltid tillgång till den aktuella versionen. Använd https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] att hämta den här specifika versionen. Till exempel **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Lägg till en hemlighet valvet, som är ett lösenord som heter SQLPassword och som har värdet för Pa$ $w0rd till Azure Key valv. 

```azurecli
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Referera till det här lösenordet med hjälp av dess URI. Använd **https://ContosoVault.vault.azure.net/secrets/SQLPassword** alltid tillgång till den aktuella versionen och https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] att hämta den här specifika versionen. Till exempel **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importera ett certifikat till valvet med .pem- eller PFX.

```azurecli
az keyvault certificate import --vault-name 'ContosoKeyVault' --file 'c:\cert\cert.pfx' --name 'ContosoCert' --password 'Pa$$w0rd'
```

Nu ska vi visa nyckeln, secret eller certifikatet som du skapade:

* Om du vill visa dina nycklar, skriver du: 

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

* Om du vill visa din hemliga, skriver du: 

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

* Om du vill visa certifikat, skriver du: 

```azurecli
az keyvault certificate list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>Registrera ett program med Azure Active Directory
Det här steget utförs normalt av en utvecklare, på en separat dator. Det är inte specifik för Azure Key Vault men ingår här, för medvetenhet. För att slutföra registreringen av app måste måste ditt konto, valvet och programmet vara i samma Azure-katalogen.

Program som använder ett nyckelvalv måste autentiseras med hjälp av en token från Azure Active Directory.  Ägaren av programmet måste registrera den i Azure Active Directory först. I slutet av registreringen hämtar programmets ägare följande värden:

- En **program-ID** (AAD-klient-ID eller appID)
- En **autentiseringsnyckel** (kallas även delad hemlighet). 

Programmet måste presentera båda dessa värden för Azure Active Directory för att få en token. Hur ett program är konfigurerad för att hämta en token beror på programmet. I [Key Vault-exempelprogrammet](https://www.microsoft.com/download/details.aspx?id=45343) anger programmets ägare dessa värden i filen app.config.

Detaljerade anvisningar om hur du registrerar ett program med Azure Active Directory bör du granska artiklar med titeln [integrera program med Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), [använda portalen för att skapa Azure Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](../azure-resource-manager/resource-group-create-service-principal-portal.md), och [skapa en Azure tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).

Registrera ett program i Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password 'Pa$$w0rd' --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Godkänna att programmet använder nyckeln eller hemligheten

För att ge programmet åtkomst nyckel eller hemlighet i valvet, använda den `az keyvault set-policy` kommando.

Om din valvnamnet är ContosoKeyVault programmet har ett appID för 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed och du vill tillåta program att dekryptera och logga in med nycklar i ditt valv, använder du följande kommando:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Skriv följande kommando för att ge samma program att läsa hemligheter i ditt valv:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> Ange avancerade åtkomstprinciper för nyckelvalvet 
Använd [az keyvault uppdatering](/cli/azure/keyvault#az-keyvault-update) att aktivera avancerade principer för nyckelvalvet. 

 Aktivera Key Vault för distribution: gör att virtuella datorer att hämta certifikat som lagras som hemligheter från valvet.
 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-deployment 'true'
 ``` 

Aktivera Key Vault för diskkryptering: krävs när du använder valvet för Azure Disk encryption.

 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-disk-encryption 'true'
 ```  

Aktivera Key Vault för malldistribution: tillåter Resource Manager för att hämta hemligheter från valvet.
 ```azurecli 
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-template-deployment 'true'
 ```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Om du vill använda en maskinvarusäkerhetsmodul (HSM)

För ytterligare säkerhet kan du importera eller generera nycklar från maskinvarusäkerhetsmoduler (HSM) som lämnar aldrig HSM-gräns. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Om detta krav inte är nödvändigt för dig hoppar du över det här avsnittet och går vidare till [Ta bort nyckelvalvet och associerade nycklar och hemligheter](#delete-the-key-vault-and-associated-keys-and-secrets).

Du måste ha en prenumeration för valvet som har stöd för HSM-skyddade nycklar för att skapa dessa HSM-skyddade nycklar.

När du skapar keyvault lägger du till parametern ”sku”:

```azurecli
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

Du kan lägga till programvaruskyddade nycklar (som du såg tidigare) och HSM-skyddade nycklar till det här valvet. Ange parametern Destination till 'HSM' om du vill skapa en HSM-skyddad nyckel:

```azurecli
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

Du kan använda följande kommando för att importera en nyckel från en PEM-filen på datorn. Det här kommandot importerar nyckeln till HSM-moduler i Key Vault-tjänsten:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```

Nästa kommando importerar ett BYOK-paket (Bring Your Own Key). På så sätt kan du generera nyckeln i din lokala HSM och överföra den till HSM-moduler i Key Vault-tjänsten utan att nyckeln lämnar HSM-gränsen:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```

Mer detaljerad information om hur du skapar den här BYOK-paket finns [använda HSM-Protected nycklar med Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Ta bort nyckelvalvet och associerade nycklar och hemligheter

Om du behöver inte längre nyckelvalvet och dess nycklar och hemligheter, kan du ta bort nyckelvalvet med hjälp av den `az keyvault delete` kommando:

```azurecli
az keyvault delete --name 'ContosoKeyVault'
```

Eller så kan du ta bort en hel Azure-resursgrupp, som innehåller nyckelvalvet och andra resurser som du har lagt till i gruppen:

```azurecli
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Andra Azure plattformsoberoende kommandoradsgränssnittet kommandon

Andra kommandon som kan vara användbart för att hantera Azure Key Vault.

Det här kommandot visas en tabell visning av alla nycklar och valda egenskaper:

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

Detta kommando visar en fullständig lista över egenskaper för den angivna nyckeln:

```azurecli
az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Det här kommandot visas en tabell visning av alla hemliga namn och egenskaper för valda:

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

Här är ett exempel på hur du tar bort en viss nyckel:

```azurecli
az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Här är ett exempel på hur du tar bort en specifik hemlighet:

```azurecli
az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'
```

## <a name="next-steps"></a>Nästa steg

- Fullständiga Azure CLI-referens för nyckelvalvet kommandon, se [Key Vault CLI referens](/cli/azure/keyvault).

- Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](key-vault-developers-guide.md).

- Mer information om Azure Key Vault och HSM: er finns [använda HSM-Protected nycklar med Azure Key Vault](key-vault-hsm-protected-keys.md).
