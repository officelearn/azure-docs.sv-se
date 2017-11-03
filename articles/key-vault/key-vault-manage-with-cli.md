---
title: "Hantera Azure Key Vault med hjälp av CLI | Microsoft Docs"
description: "Använd den här självstudiekursen för att automatisera vanliga uppgifter i Nyckelvalvet med hjälp av CLI"
services: key-vault
documentationcenter: 
author: BrucePerlerMS
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: bruceper
ms.openlocfilehash: c2565a742ce4f6ab5f7639a54c4a475f00cbc260
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-key-vault-using-cli"></a>Hantera Nyckelvalv med hjälp av CLI

Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduktion

Den här självstudiekursen hjälper dig att komma igång med Azure Key Vault för att skapa en säker behållare (ett valv) i Azure för lagring och hantering av krypteringsnycklar och hemligheter i Azure. Den vägleder dig genom processen för att skapa ett valv som innehåller en nyckel eller ett lösenord som du sedan kan använda med ett Azure-program med Azure-kommandoradsgränssnittet för flera plattformar. Den sedan visar hur ett program kan sedan använda denna nyckel eller lösenord.

**Uppskattad tidsåtgång:** 20 minuter

> [!NOTE]
> Den här självstudiekursen innehåller inte instruktioner om hur du skriver det Azure-programmet att något av steg innehåller, som visar hur du tillåter ett program att använda en nyckel eller hemlighet i nyckelvalvet.
> 
> För närvarande kan du inte konfigurera Azure Key Vault på Azure-portalen. Använd i stället instruktionerna plattformsoberoende kommandoradsgränssnittet. Azure PowerShell anvisningar Se [självstudierna motsvarande](key-vault-get-started.md).
> 
> 

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

Du kan också läsa följande kurser att bekanta dig med Azure Resource Manager i Azure plattformsoberoende-kommandoradsgränssnittet:

* [Installera och konfigurera Azure plattformsoberoende kommandoradsgränssnittet](../cli-install-nodejs.md)
* [Med hjälp av Azure plattformsoberoende kommandoradsgränssnittet med Azure Resource Manager](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>Ansluta till dina prenumerationer

Om du vill logga in med ett organisationskonto, använder du följande kommando:

    azure login -u username -p password

eller om du vill logga in genom att skriva interaktivt

    azure login

> [!NOTE]
> Inloggnings-metoden fungerar bara med organisationens konto. Ett organisationskonto är en användare som hanteras av din organisation och som definierats i organisationens Azure Active Directory-klient.
> 
> 

Om du för närvarande inte har ett organisationskonto och använder ett Microsoft-konto för att logga in på Azure-prenumerationen, kan du enkelt skapa ett med hjälp av följande steg.

1. Logga in för att logga in på den [Azure-hanteringsportalen](https://manage.windowsazure.com/), och klicka på Active Directory.
2. Om det finns ingen katalog, Välj Skapa din katalog och ange den begärda informationen.
3. Välj din katalog och Lägg till en ny användare. Den nya användaren är ett organisationskonto. Under genereringen av användaren angav du med både en e-postadress för användaren och ett tillfälligt lösenord. Spara den här informationen eftersom den används i ett annat steg.
4. Välj inställningar och välj sedan administratörer från portalen. Välj Lägg till och Lägg till den nya användaren som medadministratör. På så sätt kan organisationskonto för att hantera Azure-prenumerationen.
5. Slutligen logga ut från Azure-portalen och sedan logga in igen med den nya organisationskonto. Om det är första gången loggning med det här kontot, uppmanas du att ändra lösenordet.

Mer information om hur du använder ett organisationskonto med Microsoft Azure finns [registrera dig för Microsoft Azure som en organisation](../active-directory/sign-up-organization.md).

Om du har flera prenumerationer och vill välja en specifik prenumeration för Azure Key Vault skriver du följande för att visa prenumerationerna för ditt konto:

    azure account list

Ange sedan den prenumeration som du vill använda genom att skriva:

    azure account set <subscription name>

Mer information om hur du konfigurerar Azure plattformsoberoende kommandoradsgränssnittet finns [installera och konfigurera Azure plattformsoberoende-kommandoradsgränssnittet](../cli-install-nodejs.md).

## <a name="switch-to-using-azure-resource-manager"></a>Växla till med Azure Resource Manager
Key Vault kräver Azure Resource Manager, så Skriv följande för att växla till läget Azure Resource Manager:

    azure config mode arm

## <a name="create-a-new-resource-group"></a>Skapa en ny resursgrupp
När du använder Azure Resource Manager, skapas alla relaterade resurser i en resursgrupp. Vi skapar en ny resursgrupp 'ContosoResourceGroup' för den här självstudiekursen.

    azure group create 'ContosoResourceGroup' 'East Asia'

Den första parametern är resursgruppens namn och den andra parametern är platsen. Använd kommandot för plats `azure location list` att identifiera hur du anger en alternativ plats till den i det här exemplet. Om du behöver mer information skriver du:`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registerresursleverantören Key Vault
Kontrollera att Nyckelvalvet resursprovidern har registrerats i din prenumeration:

`azure provider register Microsoft.KeyVault`

Detta behöver bara göras en gång per prenumeration.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Använd den `azure keyvault create` kommando för att skapa en nyckelvalvet. Det här skriptet har tre obligatoriska parametrar: en resursgruppens namn, ett nyckelvalv namn och den geografiska platsen.

Skriv till exempel om du använder ContosoKeyVault valvet namn, resursgruppens namn för ContosoResourceGroup och platsen för Östasien:

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

Kommandots utdata visar egenskaper för nyckelvalvet som du nyss skapade. De två viktigaste egenskaperna är:

* **Namnet**: I exemplet är ContosoKeyVault. Du ska använda det här namnet för andra Key Vault-cmdlets.
* **vaultUri**: I exemplet är https://contosokeyvault.vault.azure.net. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu har ditt Azure-konto behörighet att utföra åtgärder i det här nyckelvalvet. Vilket ingen annan har ännu.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Lägga till en nyckel eller hemlighet till nyckelvalvet

Om du vill Azure Key Vault för att skapa en programvaruskyddad nyckel som du använder den `azure key create` kommando och skriver du följande:

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

Men om du har en befintlig nyckel i en PEM-filen sparas som en lokal fil i en fil med namnet softkey.pem som du vill överföra till Azure Key Vault, skriver du följande för att importera nyckeln från den. PEM-filen som skyddar nyckeln av programvara i Key Vault-tjänsten:

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

Nu kan du referera den nyckel som du har skapat eller överföras till Azure Key Vault med hjälp av dess URI. Använd **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** alltid hämta den aktuella versionen och använda **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** att hämta den här specifika versionen.

Om du vill lägga till en hemlighet i valvet, vilket är ett lösenord som heter SQLPassword och som har värdet för Pa$ $w0rd till Azure Key Vault, skriver du följande:

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

Nu kan du referera till det här lösenordet som du lagt till i Azure Key Vault med hjälp av dess URI. Använd **https://ContosoVault.vault.azure.net/secrets/SQLPassword** om du alltid vill hämta den senaste versionen och använd **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** om du vill hämta den här specifika versionen.

Nu ska vi visa nyckel eller hemlighet som du just skapat:

* Om du vill visa din nyckel skriver du: `azure keyvault key list --vault-name 'ContosoKeyVault'`
* Om du vill visa din hemlighet skriver du: `azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Registrera ett program med Azure Active Directory

Det här steget utförs normalt av en utvecklare, på en separat dator. Det är inte specifik för Azure Key Vault men ingår här, för fullständighetens skull.

> [!IMPORTANT]
> För att slutföra självstudiekursen måste ditt konto, valvet och det program som du ska registrera i det här steget finnas i samma Azure-katalog.
> 
> 

Program som använder ett nyckelvalv måste autentiseras med hjälp av en token från Azure Active Directory. Programmets ägare måste då först registrera programmet i sin Azure Active Directory. I slutet av registreringen hämtar programmets ägare följande värden:

* Ett **program-ID** (även kallat ett klient-ID) och en **autentiseringsnyckel** (även kallat den delade nyckeln). Programmet måste lämna båda dessa värden till Azure Active Directory att hämta en token. Hur programmet är konfigurerat för att göra detta beror på programmet. I Key Vault-exempelprogrammet anger programmets ägare dessa värden i filen app.config.

Så här registrerar du programmet i Azure Active Directory:

1. Logga in på Azure Portal.
2. Klicka på **Active Directory** till vänster och välj sedan den katalog som du ska registrera programmet i. <br> <br> 

>[!NOTE] 
> Du måste välja samma katalog som innehåller Azure-prenumeration som du skapade nyckelvalvet. Om du inte vet vilken katalog det är klickar du på **Inställningar**, identifierar prenumerationen som du skapade nyckelvalvet med och noterar namnet på katalogen som visas i den sista kolumnen.

3. Klicka på **Program**. Om inga appar har lagts till din katalog kan den här sidan visar endast den **Lägg till en App** länk. Klicka på länken eller också kan du klicka på den **lägga till** i kommandofältet.
4. I guiden **Lägg till program** på sidan **Vad vill du göra?** klickar du på **Lägg till ett program som min organisation utvecklar**.
5. På den **berätta om tillämpningsprogrammet** , ange ett namn för ditt program och välja **WEB APPLICATION och/eller webb-API** (standard). Klicka på Nästa.
6. På sidan **Appegenskaper** anger du webbappens **inloggnings-URL** och **appidentitets-URI**. Om programmet inte har dessa värden kan du hitta på dem för det här steget (du kan till exempel skriva http://test1.contoso.com i båda rutorna). Det spelar ingen roll om dessa platser finns eller inte. Det viktiga är att appidentitets-URI:n för varje program är olika för varje program i katalogen. Katalogen använder den här strängen för att identifiera din app.
7. Klicka på ikonen klar för att spara ändringarna i guiden.
8. På sidan Snabbstart **konfigurera**.
9. Bläddra till **nycklarna**, välj varaktighet och klicka sedan på **Spara**. Sidan uppdateras och innehåller nu ett nyckelvärde. Du måste konfigurera ditt program med det här nyckelvärdet och värdet **klient-ID**. (Anvisningar för den här konfigurationen är programspecifika.)
10. Kopiera värdet för klient-ID:t från den här sidan, som du ska använda i nästa steg för att ange behörigheter för valvet.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Godkänna att programmet använder nyckeln eller hemligheten
För att ge programmet åtkomst nyckel eller hemlighet i valvet, använda den `azure keyvault set-policy` kommando.

Till exempel om du valvet heter ContosoKeyVault och program som du vill verifiera har ett klient-ID för 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed och du vill tillåta program att dekryptera och logga in med nycklar i ditt valv, kör följande:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '[\"decrypt\",\"sign\"]'

> [!NOTE]
> Om du kör på Windows kommandotolk, ska du ersätta enkla citattecken med dubbla citattecken och också undanta interna dubbla citattecken. Till exempel ”: [\"dekryptera\",\"logga\"]”.
> 
> 

Om du vill att samma program ska kunna läsa hemligheter i valvet kör du följande:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '[\"get\"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Om du vill använda en maskinvarusäkerhetsmodul (HSM)
Om du vill öka säkerheten ytterligare kan du importera och generera nycklar i maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM-gränsen. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Om detta krav inte är nödvändigt för dig hoppar du över det här avsnittet och går vidare till [Ta bort nyckelvalvet och associerade nycklar och hemligheter](#delete-the-key-vault-and-associated-keys-and-secrets).

Du måste ha en prenumeration för valvet som har stöd för HSM-skyddade nycklar för att skapa dessa HSM-skyddade nycklar.

När du skapar keyvault lägger du till parametern ”sku”:

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

Du kan lägga till programvaruskyddade nycklar (som du såg tidigare) och HSM-skyddade nycklar till det här valvet. Ange parametern Destination till 'HSM' om du vill skapa en HSM-skyddad nyckel:

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

Du kan använda följande kommando för att importera en nyckel från en PEM-filen på datorn. Det här kommandot importerar nyckeln till HSM-moduler i Key Vault-tjänsten:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

Nästa kommando importerar ett BYOK-paket (Bring Your Own Key). På så sätt kan du generera nyckeln i din lokala HSM och överföra den till HSM-moduler i Key Vault-tjänsten utan att nyckeln lämnar HSM-gränsen:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Mer detaljerad information om hur du skapar den här BYOK-paket finns [använda HSM-Protected nycklar med Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Ta bort nyckelvalvet och associerade nycklar och hemligheter
Om du behöver inte längre nyckelvalvet och nyckel eller hemlighet som den innehåller, kan du ta bort nyckelvalvet med hjälp av azure keyvault delete-kommandot:

    azure keyvault delete --vault-name 'ContosoKeyVault'

Eller så kan du ta bort en hel Azure-resursgrupp, som innehåller nyckelvalvet och andra resurser som du har lagt till i gruppen:

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Andra Azure plattformsoberoende kommandoradsgränssnittet kommandon
Andra kommandon som du kan användbart för att hantera Azure Key Vault.

Det här kommandot visas en tabell visning av alla nycklar och valda egenskaper:

    azure keyvault key list --vault-name 'ContosoKeyVault'

Detta kommando visar en fullständig lista över egenskaper för den angivna nyckeln:

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Det här kommandot visas en tabell visning av alla hemliga namn och egenskaper för valda:

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Här är ett exempel på hur du tar bort en viss nyckel:

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Här är ett exempel på hur du tar bort en specifik hemlighet:

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>Nästa steg
Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](key-vault-developers-guide.md).

