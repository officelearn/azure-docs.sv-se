---
title: Hantera Azure Key Vault med CLI | Microsoft Docs
description: Använd den här artikeln för att automatisera vanliga uppgifter i Key Vault med hjälp av CLI 2.0
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
ms.date: 08/28/2018
ms.author: barclayn
ms.openlocfilehash: 7d2b38a27644eed088f4a204cf989f44346e1654
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126919"
---
# <a name="manage-key-vault-using-cli-20"></a>Hantera Nyckelvalv med hjälp av CLI 2.0

Den här artikeln beskriver hur du kommer igång med att arbeta med Azure Key Vault med hjälp av Azure CLI 2.0. Du kan se information på:

- Förutsättningar
- Så här skapar du en säker behållare (ett valv) i Azure
- Att lägga till en nyckel eller hemlighet certifikatet till nyckelvalvet
- Registrerar ett program med Azure Active Directory
- Auktorisera ett program att använda en nyckel eller hemlighet
- Inställningen avancerade åtkomstprinciper för nyckelvalvet
- Arbeta med maskinvarusäkerhetsmoduler (HSM)
- Ta bort nyckelvalvet och associerade nycklar och hemligheter
- Diverse plattformsoberoende kommandoradsgränssnittet kommandon


Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Den här artikeln innehåller inte instruktioner om hur du skriver Azure-programmet att ett av stegen innehåller, som visar hur du godkänner ett program att använda en nyckel eller hemlighet i nyckelvalvet.
>

En översikt över Azure Key Vault finns i [vad är Azure Key Vault?](key-vault-whatis.md)
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure CLI-kommandon i den här artikeln, måste du ha följande objekt:

* En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Kommandoradsgränssnitt 2.0 eller senare. Om du vill installera den senaste versionen, se [installerar och konfigurerar Azure plattformsoberoende kommandoradsgränssnitt 2.0](/cli/azure/install-azure-cli).
* Ett program som ska konfigureras för att använda nyckeln eller lösenordet som du skapar i den här artikeln. Ett exempelprogram är tillgängligt från [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=45343). Anvisningar finns i den inkluderade filen Viktigt-filen.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Få hjälp med kommandoradsgränssnittet för Azure plattformsoberoende

Den här artikeln förutsätter att du är bekant med kommandoradsgränssnitt (Bash, Terminal, Kommandotolken).

--Hjälp eller -h parametern kan användas för att visa hjälp för specifika kommandon. Alternativt kan hjälpa Azure att [kommando] [alternativ]-format kan också användas för. Om du tvekar om de parametrar som krävs av ett kommando, finns i hjälpen. Exempelvis returnerar följande kommandon för alla samma information:

```azurecli
az account set --help
az account set -h
```

Du kan också läsa följande artiklar för att bekanta dig med Azure Resource Manager i kommandoradsgränssnittet för Azure plattformsoberoende:

* [Installera Azure CLI](/cli/azure/install-azure-cli)
* [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Så här skapar du en säker behållare (ett valv) i Azure

Valv skyddas behållare som backas upp av HSM: er. Med valv så minskar risken för att säkerhetsinformation förloras av misstag eftersom lagringen av hemligheter centraliseras. Key Vaults styr och loggar dessutom åtkomsten till informationen du lagrar. Azure Key Vault kan hantera förfrågningar om och förnyande av TLS-certifikat (Transport Layer Security), och du har tillgång till alla de funktioner som krävs för en robust livscykelhantering av certifikat. I nästa steg skapar du ett valv.

### <a name="connect-to-your-subscriptions"></a>Ansluta till dina prenumerationer

För att logga in interaktivt, använder du följande kommando:

```azurecli
az login
```
Du kan skicka in ditt användarnamn och lösenord för att logga in med ett organisationskonto.

```azurecli
az login -u username@domain.com -p password
```

Om du har mer än en prenumeration och behöver ange där du kan använda, skriver du följande för att visa prenumerationerna för ditt konto:

```azurecli
az account list
```

Ange en prenumeration med parametern prenumeration.

```azurecli
az account set --subscription <subscription name or ID>
```

Mer information om hur du konfigurerar Azure plattformsoberoende kommandoradsgränssnitt finns i [installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Skapa en ny resursgrupp

När du använder Azure Resource Manager skapas alla relaterade resurser inuti en resursgrupp. Du kan skapa ett nyckelvalv i en befintlig resursgrupp. Om du vill använda en ny resursgrupp, kan du skapa en ny.

```azurecli
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Den första parametern är resursgruppens namn och den andra parametern är platsen. Om du vill hämta en lista över alla möjliga skriver platser du:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registrera resursprovidern Key Vault

 Du kanske får felet ”prenumerationen har inte registrerats för användning av namnområdet 'Microsoft.KeyVault'” när du försöker skapa ett nytt nyckelvalv. Om meddelandet visas, kontrollerar du att Key Vault-resursprovider är registrerad för din prenumeration. Det här är en engångsåtgärd för varje prenumeration.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Använd den `az keyvault create` kommando för att skapa ett nyckelvalv. Det här skriptet har tre obligatoriska parametrar: en resursgruppens namn, ett namn för nyckelvalvet och den geografiska platsen.

Skapa ett nytt valv med namnet **ContosoKeyVault**, i resursgruppen **ContosoResourceGroup**, som förvaras i den **Östasien** plats, typ: 

```azurecli
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Kommandots utdata visar egenskaper för nyckelvalvet som du har skapat. De två viktigaste egenskaperna är:

* **namn på**: I det här exemplet heter ContosoKeyVault. Du använder det här namnet i andra Key Vault-kommandon.
* **vaultUri**: I det här exemplet är URI: N https://contosokeyvault.vault.azure.net. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu har ditt Azure-konto behörighet att utföra åtgärder i det här nyckelvalvet. Från och med ännu, har ingen annan behörighet.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Att lägga till en nyckel eller hemlighet certifikatet till nyckelvalvet

Om du vill att Azure Key Vault ska skapa en programvaruskyddad nyckel åt dig använder den `az key create` kommando.

```azurecli
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```

Om du har en befintlig nyckel i en PEM-fil kan överföra du den till Azure Key Vault. Du kan välja att skydda den med programvara eller HSM. Använd följande för att importera nyckeln från filen .pem och skydda den med programvara:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'Pa$$w0rd' --protection software
```

Du kan nu referera till den nyckel som du har skapat eller överfört till Azure Key Vault med hjälp av dess URI. Använd **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** att alltid hämta den aktuella versionen. Använd https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] att hämta den här specifika versionen. Till exempel **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Lägga till en hemlighet till valvet, vilket är ett lösenord med namnet SQLPassword och som har värdet av Pa$ $w0rd till Azure Key Vaults. 

```azurecli
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Referera till det här lösenordet med hjälp av dess URI. Använd **https://ContosoVault.vault.azure.net/secrets/SQLPassword** att alltid hämta den aktuella versionen och https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] att hämta den här specifika versionen. Till exempel **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importera ett certifikat till valvet med hjälp av en PEM- eller .pfx.

```azurecli
az keyvault certificate import --vault-name 'ContosoKeyVault' --file 'c:\cert\cert.pfx' --name 'ContosoCert' --password 'Pa$$w0rd'
```

Nu ska vi visa nyckeln, hemlighet eller certifikat som du skapade:

* Om du vill visa dina nycklar, skriver du: 

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

* Om du vill visa dina hemligheter, skriver du: 

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

* Om du vill visa certifikat, skriver du: 

```azurecli
az keyvault certificate list --vault-name 'ContosoKeyVault'
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registrerar ett program med Azure Active Directory

Det här steget utförs normalt av en utvecklare, på en separat dator. Det är inte specifikt för Azure Key Vault men ingår här, för feltolerans. För att slutföra appregistreringen måste måste ditt konto, valvet och programmet vara i samma Azure-katalogen.

Program som använder ett nyckelvalv måste autentiseras med hjälp av en token från Azure Active Directory.  Programmets ägare måste registrera den i Azure Active Directory först. I slutet av registreringen hämtar programmets ägare följande värden:

- En **program-ID** (även kallat klient-ID för AAD eller appID)
- En **autentiseringsnyckel** (kallas även delad hemlighet). 

Programmet måste presentera båda dessa värden för Azure Active Directory för att få en token. Hur ett program är konfigurerad för att få en token beror på programmet. I [Key Vault-exempelprogrammet](https://www.microsoft.com/download/details.aspx?id=45343) anger programmets ägare dessa värden i filen app.config.

Detaljerade anvisningar om hur du registrerar ett program med Azure Active Directory bör du läsa igenom artiklarna med rubriken [integrera program med Azure Active Directory](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md), [Använd portalen för att skapa Azure Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](../azure-resource-manager/resource-group-create-service-principal-portal.md), och [skapa Azure-tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).

Du registrerar ett program i Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password 'Pa$$w0rd' --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Auktorisera ett program att använda en nyckel eller hemlighet

Om du vill ge programmet åtkomst till nyckeln eller hemligheten i valvet använder den `az keyvault set-policy` kommando.

Till exempel om ditt valvnamn är ContosoKeyVault programmet har en appID för 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed och du vill godkänna att programmet dekrypterar och loggar in med nycklar i ditt valv, Använd följande kommando:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Om du vill ge samma programmet att läsa hemligheter i valvet, skriver du följande kommando:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> Inställningen avancerade åtkomstprinciper för nyckelvalvet

Använd [az keyvault update](/cli/azure/keyvault#az-keyvault-update) att aktivera avancerade principer för key vault. 

 Aktivera Key Vault för distribution: tillåter att virtuella datorer att hämta certifikat som lagras som hemligheter från valvet.
 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-deployment 'true'
 ``` 

Aktivera Key Vault för diskkryptering: krävs vid användning av valvet för Azure Disk encryption.

 ```azurecli
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-disk-encryption 'true'
 ```  

Aktivera Key Vault för malldistribution: tillåter Resource Manager för att hämta hemligheter från valvet.
 ```azurecli 
 az keyvault update --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --enabled-for-template-deployment 'true'
 ```

## <a name="working-with-hardware-security-modules-hsms"></a>Arbeta med maskinvarusäkerhetsmoduler (HSM)

För extra trygghet kan du importera eller generera nycklar från maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM: ens gränser. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Om detta krav inte är nödvändigt för dig hoppar du över det här avsnittet och går vidare till [Ta bort nyckelvalvet och associerade nycklar och hemligheter](#delete-the-key-vault-and-associated-keys-and-secrets).

Du måste ha en valvbeskrivning som stöder HSM-skyddade nycklar för att skapa dessa HSM-skyddade nycklar.

När du skapar nyckelvalvet lägger du till parametern ”sku”:

```azurecli
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

Du kan lägga till programvaruskyddade nycklar (som du såg tidigare) och HSM-skyddade nycklar till det här valvet. Ange målparametern till 'HSM' om du vill skapa en HSM-skyddad nyckel:

```azurecli
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

Du kan använda följande kommando för att importera en nyckel från en PEM-fil på din dator. Det här kommandot importerar nyckeln till HSM-moduler i Key Vault-tjänsten:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```

Nästa kommando importerar ett BYOK-paket (Bring Your Own Key). På så sätt kan du generera nyckeln i din lokala HSM och överföra den till HSM-moduler i Key Vault-tjänsten utan att nyckeln lämnar HSM-gränsen:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```

Mer detaljerade instruktioner om hur du skapar den här BYOK-paket finns i [hur du använder HSM-Protected nycklar med Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Ta bort nyckelvalvet och associerade nycklar och hemligheter

Om du inte längre behöver nyckelvalvet och dess nycklar eller hemligheter kan du ta bort nyckelvalvet med hjälp av den `az keyvault delete` kommando:

```azurecli
az keyvault delete --name 'ContosoKeyVault'
```

Eller så kan du ta bort en hel Azure-resursgrupp, som innehåller nyckelvalvet och andra resurser som du har lagt till i gruppen:

```azurecli
az group delete --name 'ContosoResourceGroup'
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Diverse plattformsoberoende kommandoradsgränssnittet kommandon

Andra kommandon som kan vara användbara för att hantera Azure Key Vault.

Det här kommandot visar en tabellvy över alla nycklar och valda egenskaper:

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

Det här kommandot visar en fullständig lista över egenskaper för den angivna nyckeln:

```azurecli
az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Det här kommandot visar en tabellvy över alla hemliga namn och valda egenskaper:

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

Här är ett exempel på hur du tar bort en viss nyckel:

```azurecli
az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Här är ett exempel på hur du tar bort en särskild hemlighet:

```azurecli
az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'
```

## <a name="next-steps"></a>Nästa steg

- Fullständiga Azure CLI-referensen för key vault-kommandon, se [Key Vault CLI-referensen](/cli/azure/keyvault).

- Programmeringsreferenser finns i [utvecklarhandboken för Azure Key Vault](key-vault-developers-guide.md)

- Information om Azure Key Vault och HSM: er finns i [hur du använder HSM-Protected nycklar med Azure Key Vault](key-vault-hsm-protected-keys.md).
