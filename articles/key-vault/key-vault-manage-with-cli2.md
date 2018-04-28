---
title: Hantera Azure Key Vault med hjälp av CLI | Microsoft Docs
description: Använd den här självstudiekursen för att automatisera vanliga uppgifter i Nyckelvalvet med hjälp av CLI 2.0
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
ms.date: 04/19/2018
ms.author: barclayn
ms.openlocfilehash: 95e35ed1f26a861ab934570fae613dda95fcb537
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="manage-key-vault-using-cli-20"></a>Hantera Nyckelvalv med hjälp av CLI 2.0

Den här artikeln beskriver hur du kommer igång med Azure Key Vault använder Azure CLI 2.0. Du kan se information på:
- Så här skapar du en förstärkt behållare (ett valv) i Azure
- Hur du lagrar och hanterar kryptografiska nycklar och hemligheter i Azure. 
- Processen för att skapa ett valv som innehåller en nyckel eller ett lösenord som du sedan kan använda med ett Azure-program med Azure-kommandoradsgränssnittet för flera plattformar. 
- Hur ett program kan sedan använda denna nyckel eller lösenord.

Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).

**Uppskattad tidsåtgång:** 20 minuter

> [!NOTE]
> Den här självstudiekursen innehåller inte instruktioner om hur du skriver det Azure-programmet att något av steg innehåller, som visar hur du tillåter ett program att använda en nyckel eller hemlighet i nyckelvalvet.
>

En översikt över Azure Key Vault finns [vad är Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Förutsättningar
För att kunna slutföra den här självstudiekursen behöver du följande:

* En prenumeration på Microsoft Azure. Om du inte har någon kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Kommandoradsgränssnitt version 2.0 eller senare. Om du vill installera den senaste versionen och ansluter till din Azure-prenumeration, se [installera och konfigurera Azure plattformsoberoende kommandoradsgränssnittet 2.0](/cli/azure/install-azure-cli).
* Ett program som ska konfigureras att använda nyckeln eller lösenordet som du skapar i den här självstudiekursen. Ett exempelprogram är tillgängligt från [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=45343). Anvisningar finns i den tillhörande Readme-filen.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Få hjälp med Azure plattformsoberoende-kommandoradsgränssnittet
Den här kursen förutsätter att du är bekant med kommandoradsgränssnittet (Bash, Terminal, Kommandotolken)

--Hjälp eller -h parametern kan användas för att visa hjälpen för specifika kommandon. Alternativt kan hjälpa azure [kommando] [alternativ]-format kan också användas för att returnera samma information. Till exempel returnera alla följande kommandon samma information:

```azurecli-interactive
az account set --help
az account set -h
```

När osäkra om de parametrar som krävs av ett kommando, finns i hjälpen med--hjälp, -h eller az help [kommando].

Du kan också läsa följande kurser att bekanta dig med Azure Resource Manager i Azure plattformsoberoende-kommandoradsgränssnittet:

* [Installera Azure CLI](/cli/azure/install-azure-cli)
* [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Ansluta till dina prenumerationer
Om du vill logga in med ett organisationskonto, använder du följande kommando:

```azurecli
az login -u username@domain.com -p password
```

eller om du vill logga in genom att skriva interaktivt

```azurecli
az login
```

Om du har flera prenumerationer och vill välja en specifik prenumeration för Azure Key Vault skriver du följande för att visa prenumerationerna för ditt konto:

```azurecli
az account list
```

Ange sedan den prenumeration som du vill använda genom att skriva:

```azurecli
az account set --subscription <subscription name or ID>
```

Mer information om hur du konfigurerar Azure plattformsoberoende kommandoradsgränssnittet finns [installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Skapa en ny resursgrupp
När du använder Azure Resource Manager, skapas alla relaterade resurser i en resursgrupp. Vi skapar en ny resursgrupp 'ContosoResourceGroup' för den här självstudiekursen.

```azurecli
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

Den första parametern är resursgruppens namn och den andra parametern är platsen. Om du vill hämta en lista över alla möjliga skriver platser du:

```azurecli
az account list-locations
``` 

Om du behöver mer information skriver du: 

```azurecli
az account list-locations -h
```

## <a name="register-the-key-vault-resource-provider"></a>Registerresursleverantören Key Vault
När du försöker skapa ett nytt nyckelvalv kan du se felet ”prenumerationen har inte registrerats för användning av namnrymden 'Microsoft.KeyVault'”. Om meddelandet visas kontrollerar du att Key Vault-resursprovidern har registrerats i din prenumeration:

```azurecli
az provider register -n Microsoft.KeyVault
```

>[!NOTE]
Detta behöver bara göras en gång per prenumeration.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Använd den `az keyvault create` kommando för att skapa en nyckelvalvet. Det här skriptet har tre obligatoriska parametrar: en resursgruppens namn, ett nyckelvalv namn och den geografiska platsen.

Exempel:

- Om du använder valvnamnet för **ContosoKeyVault**
- Resursgruppens namn av **ContosoResourceGroup**
- Platsen för **Östasien**

skriver du:

```azurecli
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Kommandots utdata visar egenskaper för nyckelvalvet som du nyss skapade. De två viktigaste egenskaperna är:

* **namnet**: I exemplet är ContosoKeyVault. Du använder det här namnet för andra Key Vault-kommandon.
* **vaultUri**: I det här är exempel https://contosokeyvault.vault.azure.net. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu har ditt Azure-konto behörighet att utföra åtgärder i det här nyckelvalvet. Vilket ingen annan har ännu.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Lägga till en nyckel eller hemlighet till nyckelvalvet

Om du vill Azure Key Vault för att skapa en programvaruskyddad nyckel som du använder den `az key create` kommando och skriver du följande:

```azurecli
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```

Men om du har en befintlig nyckel i en PEM-filen sparas som en lokal fil i en fil med namnet softkey.pem som du vill överföra till Azure Key Vault, skriver du följande för att importera nyckeln från den. PEM-filen som skyddar nyckeln av programvara i Key Vault-tjänsten:

```azurecli
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```

Nu kan du referera den nyckel som du har skapat eller överföras till Azure Key Vault med hjälp av dess URI. Använd **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** alltid hämta den aktuella versionen och använda **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** att hämta den här specifika versionen.

Om du vill lägga till en hemlighet i valvet, vilket är ett lösenord som heter SQLPassword och som har värdet för Pa$ $w0rd till Azure Key Vault, skriver du följande:

```azurecli
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Nu kan du referera till det här lösenordet som du lagt till i Azure Key Vault med hjälp av dess URI. Använd **https://ContosoVault.vault.azure.net/secrets/SQLPassword** alltid hämta den aktuella versionen och använda **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** att hämta den här specifika versionen.

Nu ska vi visa nyckel eller hemlighet som du just skapat:

* Om du vill visa din nyckel skriver du: 

```azurecli
az keyvault key list --vault-name 'ContosoKeyVault'
```

* Om du vill visa din hemlighet skriver du: 

```azurecli
az keyvault secret list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>Registrera ett program med Azure Active Directory
Det här steget utförs normalt av en utvecklare, på en separat dator. Det är inte specifik för Azure Key Vault men ingår här, för medvetenhet.

> [!IMPORTANT]
> För att slutföra självstudiekursen måste ditt konto, valvet och det program som du ska registrera i det här steget finnas i samma Azure-katalog.
>
>

Program som använder ett nyckelvalv måste autentiseras med hjälp av en token från Azure Active Directory. Programmets ägare måste då först registrera programmet i sin Azure Active Directory. I slutet av registreringen hämtar programmets ägare följande värden:

- Ett **program-ID** 
- En **autentiseringsnyckel** (kallas även delad hemlighet). 

Programmet måste presentera båda dessa värden för Azure Active Directory för att få en token. Hur programmet är konfigurerat för att göra detta beror på programmet. I [Key Vault-exempelprogrammet](https://www.microsoft.com/download/details.aspx?id=45343) anger programmets ägare dessa värden i filen app.config.

Detaljerade anvisningar om hur du registrerar ett program med Azure Active Directory bör du granska i artikeln [integrera program med Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) eller [använda portalen för att skapa Azure Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](../azure-resource-manager/resource-group-create-service-principal-portal.md) registrera programmet i Azure Active Directory:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster klickar du på **Appregistreringar**. Om du inte ser appregistreringar kan du klicka på **fler tjänster** och hitta det där.  
[!NOTE]
Du måste välja samma katalog som innehåller Azure-prenumerationen som du skapade nyckelvalvet med. 
3. Klicka på **Ny programregistrering**.
4. På bladet **Skapa** anger du ett namn för appen och väljer sedan **WEBBAPP OCH/ELLER WEBB-API** (standardinställningen) och anger **INLOGGNINGS-URL** för din webbapp. Om du inte har den här informationen just nu, kan du göra det för det här steget (till exempel kan du ange http://test1.contoso.com ). Det spelar ingen roll om dessa platser finns eller inte. 

    ![Ny programregistrering](./media/key-vault-manage-with-cli2/new-application-registration.png)
    >[!WARNING]
    Kontrollera att du valde **WEBBAPP OCH/ELLER WEBB-API**. Om du inte gjorde det ser du inte **nyckelalternativet** under inställningarna.

5. Klicka på knappen **Skapa**.
6. Du kan se listan över registrerade appar när appregistreringen har slutförts. Leta reda på appen du just registrerade och klicka på den.
7. Klicka på bladet **Registrerad app** och kopiera **Program-ID:t**
8. Klicka på **Alla inställningar**
9. I bladet **Inställningar** klickar du på **nycklar**
9. Ange en beskrivning i rutan **Nyckelbeskrivning** och välj en längd. Klicka sedan på **SPARA**. Sidan uppdateras och innehåller nu ett nyckelvärde. 
10. Du använder **Program-ID:T** och **nyckelinformationen** i nästa steg för att ställa in behörigheter för ditt valv.


## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Godkänna att programmet använder nyckeln eller hemligheten

För att ge programmet åtkomst nyckel eller hemlighet i valvet, använda den `az keyvault set-policy` kommando.

Till exempel om du valvet heter ContosoKeyVault och program som du vill verifiera har ett klient-ID för 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed och du vill tillåta program att dekryptera och logga in med nycklar i ditt valv, kör följande:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Om du vill att samma program ska kunna läsa hemligheter i valvet kör du följande:

```azurecli
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Om du vill använda en maskinvarusäkerhetsmodul (HSM)

Om du vill öka säkerheten ytterligare kan du importera och generera nycklar i maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM-gränsen. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Om detta krav inte är nödvändigt för dig hoppar du över det här avsnittet och går vidare till [Ta bort nyckelvalvet och associerade nycklar och hemligheter](#delete-the-key-vault-and-associated-keys-and-secrets).

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

Om du inte längre behöver nyckelvalvet och nyckel eller hemlighet som den innehåller kan du ta bort nyckelvalvet med hjälp av den `az keyvault delete` kommando:

```azurecli
az keyvault delete --name 'ContosoKeyVault'
```

Eller så kan du ta bort en hel Azure-resursgrupp, som innehåller nyckelvalvet och andra resurser som du har lagt till i gruppen:

```azurecli
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Andra Azure plattformsoberoende kommandoradsgränssnittet kommandon

Andra kommandon som du kan användbart för att hantera Azure Key Vault.

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
