---
title: "Hantera Azure Key Vault med hjälp av CLI | Microsoft Docs"
description: "Använd den här självstudiekursen för att automatisera vanliga uppgifter i Nyckelvalvet med hjälp av CLI"
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: barclayn
ms.openlocfilehash: 94ea95e7f40c8d47dd18422a9c0795655dae365b
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="manage-key-vault-using-cli"></a>Hantera Nyckelvalv med hjälp av CLI

Använd den här självstudiekursen för att hjälpa dig komma igång med Azure Key Vault för att skapa en förstärkt behållare (ett valv) i Azure. Azure Key vault används för att lagra och hantera kryptografiska nycklar och hemligheter. Den här artikeln vägleder dig genom processen för att skapa ett valv med Azure-kommandoradsgränssnittet för flera plattformar. Du kommer sedan att interagera med valvet för att utföra olika gemensamma åtgärder. 

Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).

**Uppskattad tidsåtgång:** 20 minuter

> [!NOTE]
> Den här självstudiekursen innehåller inte instruktioner om hur du skriver det Azure-programmet som innehåller något av steg. Det här exempelprogrammet används för att visa hur ett program kan ha behörighet att använda en nyckel eller hemlighet i nyckelvalvet.
> Den här artikeln fokuserar på hur du konfigurerar Azure Key Vault använder plattformsoberoende kommandoradsgränssnittet. Azure PowerShell instruktioner finns i [självstudierna motsvarande](key-vault-get-started.md).

Översiktlig information om Azure Key Vault finns i [Vad är Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här självstudiekursen behöver du följande:

* En prenumeration på Microsoft Azure. Om du inte har någon kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Kommandoradsgränssnittet version 0.9.1 eller senare. Om du vill installera den senaste versionen och ansluter till din Azure-prenumeration, se [installera och konfigurera Azure-plattformsoberoende kommandoradsgränssnittet](../cli-install-nodejs.md).
* Ett program som ska konfigureras att använda nyckeln eller lösenordet som du skapar i den här självstudiekursen. Ett exempelprogram är tillgängligt från [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=45343). Anvisningar finns i den tillhörande Readme-filen.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Få hjälp med Azure plattformsoberoende-kommandoradsgränssnittet

Den här kursen förutsätter att du är bekant med kommandoradsgränssnittet (Bash, Terminal, Kommandotolken)

--Hjälp eller -h parametern kan användas för att visa hjälpen för specifika kommandon. Alternativt kan hjälpa azure [kommando] [alternativ]-format kan också användas för att returnera samma information. Till exempel returnera alla följande kommandon samma information:

    azure account set --help

    azure account set -h

    azure help account set

När osäkra om de parametrar som krävs av ett kommando, finns i hjälpen med--hjälp, -h eller azure hjälp [kommando].

Du kan också läsa följande kurser att bekanta dig med Azure Resource Manager distributionen modellen i Azure plattformsoberoende-kommandoradsgränssnittet:

* [Installera och konfigurera Azure plattformsoberoende kommandoradsgränssnittet](../cli-install-nodejs.md)
* [Med hjälp av Azure plattformsoberoende kommandoradsgränssnittet med Azure Resource Manager](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>Ansluta till dina prenumerationer

Logga in till Azure med hjälp av följande kommando:

```azurecli-interactive
azure login -u username -p password
```

eller om du vill logga in genom att skriva interaktivt

```azurecli-interactive
azure login
```

Om du har flera prenumerationer och vill välja en specifik prenumeration för Azure Key Vault skriver du följande för att visa prenumerationerna för ditt konto:

```azurecli-interactive
azure account list
```

Ange sedan den prenumeration som du vill använda genom att skriva:

```azurecli-interactive
azure account set <subscription name>
```

Mer information om hur du konfigurerar Azure plattformsoberoende kommandoradsgränssnittet finns [installera och konfigurera Azure plattformsoberoende-kommandoradsgränssnittet](../cli-install-nodejs.md).

## <a name="switch-to-using-azure-resource-manager"></a>Växla till med Azure Resource Manager
Key Vault kräver Azure Resource Manager, så Skriv följande för att växla till läget Azure Resource Manager:

```azurecli-interactive
azure config mode arm
```

## <a name="create-a-new-resource-group"></a>Skapa en ny resursgrupp
När du använder Azure Resource Manager, skapas alla relaterade resurser i en resursgrupp. Vi skapar en ny resursgrupp 'ContosoResourceGroup' för den här självstudiekursen.

```azurecli-interactive
azure group create 'ContosoResourceGroup' 'East Asia'
```

Den första parametern är resursgruppens namn och den andra parametern är platsen. Använd kommandot för plats `azure location list` att identifiera hur du anger en alternativ plats till den i det här exemplet. Om du behöver mer information skriver du:`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registerresursleverantören Key Vault
Kontrollera att Nyckelvalvet resursprovidern har registrerats i din prenumeration:

```azurecli-interactive
azure provider register Microsoft.KeyVault
```

Detta behöver bara göras en gång per prenumeration.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Använd den `azure keyvault create` kommando för att skapa en nyckelvalvet. Det här skriptet har tre obligatoriska parametrar: en resursgruppens namn, ett nyckelvalv namn och den geografiska platsen.

Exempel:
- Om du använder valvnamnet för **ContosoKeyVault**
- Resursgruppens namn av **ContosoResourceGroup**
- Platsen för **Östasien** typ:

```azurecli-interactive
azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

Kommandots utdata visar egenskaper för nyckelvalvet som du nyss skapade. De två viktigaste egenskaperna är:

* **Namnet**: I exemplet är ContosoKeyVault. Du ska använda det här namnet för andra Key Vault-cmdlets.
* **vaultUri**: I exemplet är https://contosokeyvault.vault.azure.net. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu har ditt Azure-konto behörighet att utföra åtgärder i det här nyckelvalvet. Vilket ingen annan har ännu.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Lägga till en nyckel eller hemlighet till nyckelvalvet

Om du vill Azure Key Vault för att skapa en programvaruskyddad nyckel som du använder den `azure key create` kommando och skriver du följande:

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software
````

Men om du har en befintlig nyckel i en PEM-filen sparas som en lokal fil i en fil med namnet softkey.pem som du vill överföra till Azure Key Vault, skriver du följande för att importera nyckeln från den. PEM-filen som skyddar nyckeln av programvara i Key Vault-tjänsten:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software
```

Nu kan du referera den nyckel som du har skapat eller överföras till Azure Key Vault med hjälp av dess URI. Använd **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** alltid hämta den aktuella versionen och använda **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** att hämta den här specifika versionen.

Om du vill lägga till en hemlighet i valvet, vilket är ett lösenord som heter SQLPassword och som har värdet för Pa$ $w0rd till Azure Key Vault, skriver du följande:

```azurecli-interactive
azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'
```

Nu kan du referera till det här lösenordet som du lagt till i Azure Key Vault med hjälp av dess URI. Använd **https://ContosoVault.vault.azure.net/secrets/SQLPassword** om du alltid vill hämta den senaste versionen och använd **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** om du vill hämta den här specifika versionen.

Nu ska vi visa nyckel eller hemlighet som du just skapat:

* Om du vill visa din nyckel skriver du: `azure keyvault key list --vault-name 'ContosoKeyVault'`
* Om du vill visa din hemlighet skriver du: `azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Registrera ett program med Azure Active Directory

Det här steget utförs normalt av en utvecklare, på en separat dator. Det är inte specifik för Azure Key Vault men ingår här, för fullständighetens skull.

> [!IMPORTANT]
> För att slutföra kursen, ditt konto, valvet och det program som du registrerar i det här steget måste vara i samma Azure Active directory.

Program som använder ett nyckelvalv måste autentiseras med hjälp av en token från Azure Active Directory. Programmets ägare måste då först registrera programmet i sin Azure Active Directory. I slutet av registreringen hämtar programmets ägare följande värden:

- En **program-ID** 
- En **autentiseringsnyckel** (även kallat den delade hemligheten). 

Programmet måste presentera båda dessa värden för Azure Active Directory för att få en token. Hur programmet är konfigurerat för att göra detta beror på programmet. För den [Key Vault exempelprogrammet](https://www.microsoft.com/download/details.aspx?id=45343), program-ägare anger värdena i filen app.config.

Detaljerade anvisningar om hur du registrerar ett program med Azure Active Directory bör du granska i artikeln [integrera program med Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) eller [använda portalen för att skapa Azure Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](../azure-resource-manager/resource-group-create-service-principal-portal.md) registrera programmet i Azure Active Directory:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på vänster, **App registreringar**. Om du inte ser app registreringar du klickar på **fler tjänster** och finns där.  
    >[!NOTE]
    Du måste välja samma katalog som innehåller Azure-prenumeration som du skapade nyckelvalvet. 
3. Klicka på **nya appregistrering**.
4. På den **skapa** bladet ange ett namn för programmet och välj sedan **WEB APPLICATION och/eller webb-API** (standard) och ange den **SIGN-ON-URL** för webbplatsen programmet. Om du inte har den här informationen just nu, kan du göra det för det här steget (till exempel att du kan ange http://test1.contoso.com). Det spelar ingen roll om dessa platser finns eller inte. 

   ![Ny appregistrering](./media/key-vault-manage-with-cli/new-application-registration.png)

5. Klicka på knappen **Skapa**.
6. Du kan se listan över registrerade appar när appen registreringen har slutförts. Hitta den app som du just registrerade och klicka på den.
7. Klicka på den **registrerad app** bladet kopiera den **program-ID**
8. Klicka på **alla inställningar**
9. På den **inställningar** bladet Klicka på **nycklar**
10. Ange en beskrivning i den **nyckeln beskrivning** och markera en varaktighet och sedan på **spara**. Sidan uppdateras och innehåller nu ett nyckelvärde. 
11. Du kommer att använda den **program-ID** och **nyckeln** informationen i nästa steg för att ange behörigheter för ditt valv.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Godkänna att programmet använder nyckeln eller hemligheten
För att ge programmet åtkomst nyckel eller hemlighet i valvet, använder du:

```azurecli-interactive
azure keyvault set-policy
```

Till exempel om du valvet heter ContosoKeyVault och program som du vill verifiera har ett klient-ID för 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed och du vill tillåta program att dekryptera och logga in med nycklar i ditt valv, kör följande:

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '[\"decrypt\",\"sign\"]'
```

> [!NOTE]
> Om du kör på Windows kommandotolk, ska du ersätta enkla citattecken med dubbla citattecken och också undanta interna dubbla citattecken. Till exempel ”: [\"dekryptera\",\"logga\"]”.
> 

Om du vill att samma program ska kunna läsa hemligheter i valvet kör du följande:

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '[\"get\"]'
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Om du vill använda en maskinvarusäkerhetsmodul (HSM)
Om du vill öka säkerheten ytterligare kan du importera och generera nycklar i maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM-gränsen. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Om detta krav inte är nödvändigt för dig hoppar du över det här avsnittet och går vidare till [Ta bort nyckelvalvet och associerade nycklar och hemligheter](#delete-the-key-vault-and-associated-keys-and-secrets).

Du måste ha en prenumeration för valvet som har stöd för HSM-skyddade nycklar för att skapa dessa HSM-skyddade nycklar.

När du skapar keyvault lägger du till parametern ”sku”:

```azurecli-interactive
azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

Du kan lägga till programvaruskyddade nycklar (som du såg tidigare) och HSM-skyddade nycklar till det här valvet. Ange parametern Destination till 'HSM' om du vill skapa en HSM-skyddad nyckel:

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'
```

Du kan använda följande kommando för att importera en nyckel från en PEM-filen på datorn. Det här kommandot importerar nyckeln till HSM-moduler i Key Vault-tjänsten:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'
```

Nästa kommando importerar ett BYOK-paket (Bring Your Own Key). På så sätt kan du generera nyckeln i din lokala HSM och överföra den till HSM-moduler i Key Vault-tjänsten utan att nyckeln lämnar HSM-gränsen:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'
```

Mer detaljerad information om hur du skapar den här BYOK-paket finns [använda HSM-Protected nycklar med Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Ta bort nyckelvalvet och associerade nycklar och hemligheter
Om du behöver inte längre nyckelvalvet och nyckel eller hemlighet som den innehåller, kan du ta bort nyckelvalvet med hjälp av azure keyvault delete-kommandot:

```azurecli-interactive
azure keyvault delete --vault-name 'ContosoKeyVault'
```

Eller så kan du ta bort en hel Azure-resursgrupp, som innehåller nyckelvalvet och andra resurser som du har lagt till i gruppen:

```azurecli-interactive
azure group delete --name 'ContosoResourceGroup'
```


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Andra Azure plattformsoberoende kommandoradsgränssnittet kommandon
Andra kommandon som du kan användbart för att hantera Azure Key Vault.

Det här kommandot visas en tabell visning av alla nycklar och valda egenskaper:

```azurecli-interactive
azure keyvault key list --vault-name 'ContosoKeyVault'
```

Detta kommando visar en fullständig lista över egenskaper för den angivna nyckeln:

```azurecli-interactive
azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

Det här kommandot visas en tabell visning av alla hemliga namn och egenskaper för valda:

```azurecli-interactive
azure keyvault secret list --vault-name 'ContosoKeyVault'
```

Här är ett exempel på hur du tar bort en viss nyckel:

```azurecli-interactive
azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

Här är ett exempel på hur du tar bort en specifik hemlighet:

```azurecli-interactive
azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'
```


## <a name="next-steps"></a>Nästa steg
- Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](key-vault-developers-guide.md).
- Översiktlig information om Azure Key Vault finns i [Vad är Azure Key Vault?](key-vault-whatis.md)
- Hur du arbetar med Azure Key Vault med hjälp av Powershell [Azure Key Vault Kom igång](key-vault-get-started.md).


