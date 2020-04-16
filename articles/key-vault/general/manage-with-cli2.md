---
title: Hantera Azure Key Vault med CLI - Azure Key Vault | Microsoft-dokument
description: Använd den här artikeln för att automatisera vanliga uppgifter i Key Vault med hjälp av Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 127f0cdfc8cecf9789a68210f4b7ce1927333cc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422587"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Hantera Key Vault med Hjälp av Azure CLI 

Den här artikeln beskriver hur du kommer igång med Azure Key Vault med Hjälp av Azure CLI.  Du kan se information om:

- Så här skapar du en härdade behållare (ett valv) i Azure
- Lägga till en nyckel, hemlighet eller ett certifikat i nyckelvalvet
- Registrera ett program med Azure Active Directory
- Tillåta ett program att använda en nyckel eller hemlighet
- Ställa in avancerade åtkomstprinciper för nyckelvalv
- Arbeta med maskinvarusäkerhetsmoduler (HSM)
- Ta bort nyckelvalvet och tillhörande nycklar och hemligheter
- Diverse kommandoradsgränssnittskommandon för Azure-plattformsgränserna


Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Den här artikeln innehåller inte instruktioner om hur du skriver Azure-programmet som ett av stegen innehåller, som visar hur du auktoriserar ett program att använda en nyckel eller hemlighet i nyckelvalvet.
>

En översikt över Azure Key Vault finns i [Vad är Azure Key Vault?](overview.md)) Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill använda Azure CLI-kommandona i den här artikeln måste du ha följande objekt:

* En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Azure Command-Line Interface version 2.0 eller senare. Information om hur du installerar den senaste versionen finns [i Installera Azure CLI](/cli/azure/install-azure-cli).
* Ett program som ska konfigureras för att använda nyckeln eller lösenordet som du skapar i den här artikeln. Ett exempelprogram är tillgängligt från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45343). Instruktioner finns i den medföljande Readme-filen.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Få hjälp med Azure Cross-Platform Command-Line Interface

Den här artikeln förutsätter att du är bekant med kommandoradsgränssnittet (Bash, Terminal, Kommandotolken).

Parametern --help eller -h kan användas för att visa hjälp för specifika kommandon. Alternativt kan Azure help [command] [options]-formatet också användas. När du är osäker på vilka parametrar som behövs av ett kommando, se hjälp. Följande kommandon returnerar till exempel samma information:

```azurecli
az account set --help
az account set -h
```

Du kan också läsa följande artiklar för att bekanta dig med Azure Resource Manager i Azure Cross-Platform Command-Line Interface:

* [Installera Azure CLI](/cli/azure/install-azure-cli)
* [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Så här skapar du en härdade behållare (ett valv) i Azure

Valv är säkrade behållare som backas upp av maskinvarusäkerhetsmoduler. Med valv så minskar risken för att säkerhetsinformation förloras av misstag eftersom lagringen av hemligheter centraliseras. Key Vaults styr och loggar dessutom åtkomsten till informationen du lagrar. Azure Key Vault kan hantera förfrågningar om och förnyande av TLS-certifikat (Transport Layer Security), och du har tillgång till alla de funktioner som krävs för en robust livscykelhantering av certifikat. I nästa steg ska du skapa ett valv.

### <a name="connect-to-your-subscriptions"></a>Ansluta till dina prenumerationer

Om du vill logga in interaktivt använder du följande kommando:

```azurecli
az login
```
Om du vill logga in med ett organisationskonto kan du skicka in ditt användarnamn och lösenord.

```azurecli
az login -u username@domain.com -p password
```

Om du har mer än en prenumeration och behöver ange vilken du ska använda skriver du följande för att se prenumerationerna för ditt konto:

```azurecli
az account list
```

Ange en prenumeration med prenumerationsparametern.

```azurecli
az account set --subscription <subscription name or ID>
```

Mer information om hur du konfigurerar Azure Cross-Platform Command-Line Interface finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Skapa en ny resursgrupp

När du använder Azure Resource Manager skapas alla relaterade resurser i en resursgrupp. Du kan skapa ett nyckelvalv i en befintlig resursgrupp. Om du vill använda en ny resursgrupp kan du skapa en ny.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Den första parametern är resursgruppsnamn och den andra parametern är platsen. Så här hämtar du en lista över alla möjliga platser:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registrera resursleverantören Key Vault

 Felmeddelandet "Prenumerationen är inte registrerad för att använda namnområdet 'Microsoft.KeyVault' när du försöker skapa ett nytt nyckelvalv. Om meddelandet visas kontrollerar du att Key Vault-resursprovidern är registrerad i din prenumeration. Det här är en engångsåtgärd för varje prenumeration.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Använd `az keyvault create` kommandot för att skapa ett nyckelvalv. Det här skriptet har tre obligatoriska parametrar: ett resursgruppnamn, ett nyckelvalvsnamn och den geografiska platsen.

Om du vill skapa ett nytt valv med namnet **ContosoKeyVault**skriver du i resursgruppen **ContosoResourceGroup**, som finns på platsen i **Östasien,:** 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Utdata för det här kommandot visar egenskaperna för nyckelvalvet som du har skapat. De två viktigaste egenskaperna är:

* **namn**: I exemplet är namnet ContosoKeyVault. Du ska använda det här namnet för andra Key Vault-kommandon.
* **vaultUri**: I exemplet är https://contosokeyvault.vault.azure.netURI . Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu har ditt Azure-konto behörighet att utföra åtgärder i det här nyckelvalvet. I nuläget är ingen annan auktoriserad.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Lägga till en nyckel, hemlighet eller ett certifikat i nyckelvalvet

Om du vill att Azure Key Vault ska skapa en `az key create` programvaruskyddad nyckel åt dig använder du kommandot.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Om du har en befintlig nyckel i en PEM-fil kan du överföra den till Azure Key Vault. Du kan välja att skydda nyckeln med programvara eller HSM. Detta exempel importerar nyckeln från .pem-filen och skyddar den med programvara, med hjälp av lösenordet "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Du kan nu referera till nyckeln som du skapade eller laddade upp till Azure Key Vault med hjälp av dess URI. Används **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** för att alltid få den aktuella versionen. Använd https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] för att hämta den här specifika versionen. Till exempel **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Lägg till en hemlighet i valvet, som är ett lösenord som heter SQLPassword, och som har värdet "hVFkk965BuUv" i Azure Key Vaults. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Referera till det här lösenordet med hjälp av URI.Reference this password by using its URI. Används **https://ContosoVault.vault.azure.net/secrets/SQLPassword** för att alltid hämta den aktuella versionen och https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] för att hämta den här specifika versionen. Till exempel **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importera ett certifikat till valvet med hjälp av en .pem eller .pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Nu ska vi visa nyckeln, hemligheten eller certifikatet som du har skapat:

* Om du vill visa dina nycklar skriver du: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Om du vill visa dina hemligheter skriver du: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Om du vill visa certifikat skriver du: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registrera ett program med Azure Active Directory

Det här steget utförs normalt av en utvecklare, på en separat dator. Den är inte specifik för Azure Key Vault men ingår här, för medvetenhet. För att slutföra appregistreringen måste ditt konto, valvet och programmet finnas i samma Azure-katalog.

Program som använder ett nyckelvalv måste autentiseras med hjälp av en token från Azure Active Directory.  Programmets ägare måste registrera det i Azure Active Directory först. I slutet av registreringen hämtar programmets ägare följande värden:

- Ett **program-ID** (kallas även AAD-klient-ID eller appID)
- En **autentiseringsnyckel** (kallas även delad hemlighet). 

Programmet måste presentera båda dessa värden för Azure Active Directory för att få en token. Hur ett program är konfigurerat för att hämta en token beror på programmet. I [Key Vault-exempelprogrammet](https://www.microsoft.com/download/details.aspx?id=45343) anger programmets ägare dessa värden i filen app.config.

För detaljerade steg för att registrera ett program med Azure Active Directory bör du granska artiklarna med titeln [Integrera program med Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md), Använd portal för att skapa ett Azure Active [Directory-program och tjänsthuvudnamn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md)och Skapa ett [Azure-tjänsthuvudnamn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

Så här registrerar du ett program i Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Tillåta ett program att använda en nyckel eller hemlighet

Om du vill att programmet ska komma åt nyckeln `az keyvault set-policy` eller hemligheten i valvet använder du kommandot.

Om ditt valvnamn till exempel är ContosoKeyVault har programmet ett appID på 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed och du vill auktorisera programmet för att dekryptera och signera med nycklar i valvet, använd följande kommando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Om du vill auktorisera samma program för att läsa hemligheter i valvet skriver du följande kommando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a>Ställa in avancerade åtkomstprinciper för nyckelvalv

Använd [az keyvault-uppdatering](/cli/azure/keyvault#az-keyvault-update) för att aktivera avancerade principer för nyckelvalvet.

 Aktivera Key Vault för distribution: Tillåter virtuella datorer att hämta certifikat som lagras som hemligheter från valvet.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Aktivera Key Vault för diskkryptering: Obligatoriskt när du använder valvet för Azure Disk-kryptering.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Aktivera Key Vault för malldistribution: Gör det möjligt för Resource Manager att hämta hemligheter från valvet.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Arbeta med maskinvarusäkerhetsmoduler (HSM)

För extra säkerhet kan du importera eller generera nycklar från maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM-gränsen. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Om detta krav inte är nödvändigt för dig hoppar du över det här avsnittet och går vidare till Ta bort nyckelvalvet och associerade nycklar och hemligheter.

Om du vill skapa HSM-skyddade nycklar behöver du en valvprenumeration som stöder HSM-skyddade nycklar.

När du skapar keyvault lägger du till parametern 'sku':

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Du kan lägga till programvaruskyddade nycklar (som du såg tidigare) och HSM-skyddade nycklar till det här valvet. Om du vill skapa en HSM-skyddad nyckel ställer du in målparametern på "HSM":

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Du kan använda följande kommando för att importera en nyckel från en PEM-fil på datorn. Det här kommandot importerar nyckeln till HSM-moduler i Key Vault-tjänsten:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Nästa kommando importerar ett "bring your own key" (BYOK) paket. På så sätt kan du generera nyckeln i din lokala HSM och överföra den till HSM-moduler i Key Vault-tjänsten utan att nyckeln lämnar HSM-gränsen:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Mer detaljerade instruktioner om hur du genererar det här BYOK-paketet finns i [Så här använder du HSM-skyddade nycklar med Azure Key Vault](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Ta bort nyckelvalvet och tillhörande nycklar och hemligheter

Om du inte längre behöver nyckelvalvet och dess nycklar eller hemligheter `az keyvault delete` kan du ta bort nyckelvalvet med kommandot:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Eller så kan du ta bort en hel Azure-resursgrupp, som innehåller nyckelvalvet och andra resurser som du har lagt till i gruppen:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Diverse kommandoradsgränssnittskommandon för Azure-plattformsgränserna

Andra kommandon som du kan ha nytta av för att hantera Azure Key Vault.

I det här kommandot visas en tabellvisning av alla nycklar och markerade egenskaper:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Det här kommandot visar en fullständig lista över egenskaper för den angivna nyckeln:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

I det här kommandot visas en tabellvisning av alla hemliga namn och markerade egenskaper:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Här är ett exempel på hur du tar bort en viss nyckel:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Här är ett exempel på hur du tar bort en viss hemlighet:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Nästa steg

- Fullständig Azure CLI-referens för nyckelvalvskommandon finns i [Cli-referens för Nyckelvalv](/cli/azure/keyvault).

- Information om programmeringsreferenser finns [i utvecklarhandboken för Azure Key Vault](developers-guide.md)

- Information om Azure Key Vault och HSM finns i [Så här använder du HSM-skyddade nycklar med Azure Key Vault](../keys/hsm-protected-keys.md).
