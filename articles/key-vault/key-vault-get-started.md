---
title: Komma igång med Azure Key Vault | Microsoft Docs
description: Den här självstudiekursen hjälper dig att komma igång med Azure Key Vault för att skapa en säker behållare i Azure för lagring och hantering av krypteringsnycklar och hemligheter i Azure.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: 58a283f1ce5bd2fd78c4fa2038c3998aea1598c9
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070359"
---
# <a name="get-started-with-azure-key-vault"></a>Komma igång med Azure Key Vault
Med den här artikeln får du hjälp med att komma igång med Azure Key Vault med PowerShell, och den går igenom följande aktiviteter:
- Så här skapar du en förstärkt behållare (ett valv) i Azure.
- Så här använder du KeyVault för att lagra och hantera kryptografiska nycklar och hemligheter i Azure.
- Så här kan ett program använda den här nyckeln eller lösenordet.

Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Den här artikeln innehåller inte instruktioner om hur du skriver ett Azure-program. Du kan använda [Azure Key Vault-exempelprogrammet](https://www.microsoft.com/download/details.aspx?id=45343) för de här stegen.

Anvisningar för plattformsoberoende kommandoradsgränssnitt finns i [den här självstudiekursen](key-vault-manage-with-cli2.md).

## <a name="requirements"></a>Krav
Innan du gå vidare med artikeln bekräftar du att du har:

- **En Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
- **Azure PowerShell**, **minst version 1.1.0**. Om du vill installera och sedan koppla Azure PowerShell till din Azure-prenumeration läser du [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). Om du redan har installerat Azure PowerShell och inte vet vilken version du har skriver du `(Get-Module azure -ListAvailable).Version` i Azure PowerShell-konsolen. Du kan använda den här självstudiekursen med några mindre ändringar även om du har Azure PowerShell version 0.9.1 till och med 0.9.8 installerad. Du måste till exempel använda kommandot `Switch-AzureMode AzureResourceManager`, och vissa Azure Key Vault-kommandon har ändrats. En lista med Key Vault-cmdlets för version 0.9.1 till och med 0.9.8 finns i [Cmdlets för Azure Key Vault](/powershell/module/azurerm.keyvault/#key_vault).
- **Ett program som kan konfigureras för att använda Key Vault**. Ett exempelprogram är tillgängligt från [Microsoft Download Center](http://www.microsoft.com/download/details.aspx?id=45343). Anvisningar finns i den tillhörande **Readme**-filen.

>[!NOTE]
Den här artikeln förutsätter att du har grundläggande kunskaper om PowerShell och Azure. Mer information om PowerShell finns i [Komma igång med Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Om du vill visa detaljerad hjälp om cmdlets som du ser i den här självstudiekursen använder du cmdleten **Get-Help**.

```powershell-interactive
Get-Help <cmdlet-name> -Detailed
```
    
Om du till exempel behöver hjälp med cmdleten **Connect-AzureRmAccount** skriver du:

```PowerShell
Get-Help Connect-AzureRmAccount -Detailed
```

Du kan också läsa följande artiklar för att bekanta dig med Azure Resource Manager-distributionsmodellen i Azure PowerShell:

* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)
* [Använda Azure PowerShell med Resource Manager](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Ansluta till dina prenumerationer
Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:  

```PowerShell
Connect-AzureRmAccount
```

>[!NOTE]
 Om du använder en specifik instans av Azure använder du parametern -Environment. Till exempel: 
 ```powershell
 Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
 ```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell identifierar alla prenumerationer som är associerade med det här kontot och använder den första som standard.

Om du har flera prenumerationer och vill välja en specifik prenumeration för Azure Key Vault skriver du följande för att visa prenumerationerna för ditt konto:

```powershell
Get-AzureRmSubscription
```

Ange sedan den prenumeration som du vill använda genom att skriva:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

Mer information om hur du konfigurerar Azure PowerShell finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

## <a id="resource"></a>Skapa en ny resursgrupp
När du använder Azure Resource Manager skapas alla relaterade resurser inuti en resursgrupp. Vi ska skapa en ny resursgrupp med namnet **ContosoResourceGroup** i den här självstudiekursen:

```powershell
New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East US'
```

## <a id="vault"></a>Skapa ett nyckelvalv
Skapa ett nytt nyckelvalv med cmdleten [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Denna cmdlet har tre obligatoriska parametrar: ett **resursgruppsnamn**, ett **nyckelvalvsnamn** och en **geografisk plats**.

Till exempel om du använder:
- Valvnamnet **ContosoKeyVault**.
- Resursgruppnamnet **ContosoResourceGroup**.
- Platsen **USA, östra**.

skriver du:

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```
![Utdata när kommandot för att skapa Key Vault har slutförts](./media/key-vault-get-started/output-after-creating-keyvault.png)

Utdata från denna cmdlet visar egenskaper för nyckelvalvet som du precis skapat. De två viktigaste egenskaperna är:

* **Valvnamn**: I det här exemplet är namnet **ContosoKeyVault**. Du ska använda det här namnet för andra Key Vault-cmdlets.
* **Valvets URI**: I det här exemplet är det https://contosokeyvault.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu har ditt Azure-konto behörighet att utföra åtgärder i det här nyckelvalvet. Vilket ingen annan har ännu.

> [!NOTE]
> Du kanske får felet **Prenumerationen har inte registrerats för användning av namnrymden Microsoft.KeyVault** när du försöker skapa ett nytt nyckelvalv. Om meddelandet visas kör du `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`. Efter registreringen har slutförts kan du köra kommandot New-AzureRmKeyVault igen. Mer information finns i [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider).
>
>

## <a id="add"></a>Lägga till en nyckel eller hemlighet till nyckelvalvet
Du kan behöva interagera med Key Vault och nycklar eller hemligheter på några olika sätt.

### <a name="azure-key-vault-generates-a-software-protected-key"></a>Azure Key Vault genererar en programvaruskyddad nyckel

Om du vill att Azure Key Vault ska skapa en programvaruskyddad nyckel åt dig använder du cmdleten [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) och skriver:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'
```
du kan visa nyckelns URI genom att skriva:
```powershell
$key.id
```

Du kan referera till nyckeln som du skapat eller överfört till Azure Key Vault med hjälp av dess URI. Du kan använda **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** för att hämta den aktuella versionen och **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** för att hämta den här specifika versionen.  

### <a name="importing-an-existing-pfx-file-into-azure-key-vault"></a>Importera en befintlig PFX-fil till Azure Key Vault

När det gäller befintliga nycklar som lagras i en pfx-fil som du vill överföra till Azure Key Vault är stegen olika. Till exempel:
- Om du har en befintlig programvaruskyddad nyckel i en. PFX-fil
- Pfx-filen heter softkey.pfx 
- Filen lagras på C-enheten.

Du kan skriva:

```powershell
$securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force  // This stores the password 123 in the variable $securepfxpwd
```

Skriv sedan följande för att importera nyckeln från PFX-filen, som skyddar den med programvara i Key Vault-tjänsten:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoImportedPFX' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd
```

Du kan visa nyckelns URI genom att skriva:

```powershell
$Key.id
```
Om du vill visa din nyckel skriver du: 

```powershell
Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'
```
Om du vill visa egenskaperna för PFX-filen på portalen visas något som liknar bilden nedan.

![Så här ser ett certifikat ut i portalen](./media/key-vault-get-started/imported-pfx.png)
### <a name="to-add-a-secret-to-azure-key-vault"></a>Så här lägger du till en hemlighet i Azure Key Vault

Om du vill lägga till en hemlighet till valvet, dvs. ett lösenord med namnet SQLPassword och värdet Pa$ w0rd i Azure Key Vault, börjar du med att konvertera värdet Pa$ $w0rd till en säker sträng genom att skriva:

```powershell    
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Sedan skriver du:

```powershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue
```


Nu kan du referera till det här lösenordet som du lagt till i Azure Key Vault med hjälp av dess URI. Använd **https://ContosoVault.vault.azure.net/secrets/SQLPassword** för att alltid hämta den aktuella versionen och **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** för att hämta den här specifika versionen.

Du visar hemlighetens URI genom att skriva:

```powershell
$secret.Id
```
Om du vill visa din hemlighet skriver du: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'` eller så kan du visa hemligheten på portalen.

![hemlighet](./media/key-vault-get-started/secret-value.png)

Så här visar du värdet som finns i hemligheten som oformaterad text:
```powershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "SQLPassword").SecretValueText
```
Nu är ditt nyckelvalv och din nyckel eller hemlighet redo att användas av program. Du måste ge programmen tillstånd att använda dem.  

## <a id="register"></a>Registrera ett program med Azure Active Directory
Det här steget utförs normalt av en utvecklare, på en separat dator. Det är inte specifikt för Azure Key Vault. Detaljerade anvisningar om hur du registrerar ett program med Azure Active Directory ska du läsa artikeln [Integrera program med Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) eller [använda portalen för att skapa ett Azure Active Directory-program och tjänstens huvudnamn som kan få åtkomst till resurser](../azure-resource-manager/resource-group-create-service-principal-portal.md)

> [!IMPORTANT]
> För att slutföra självstudiekursen måste ditt konto, valvet och det program som du ska registrera i det här steget finnas i samma Azure-katalog.


Program som använder ett nyckelvalv måste autentiseras med hjälp av en token från Azure Active Directory. Programmets ägare måste då först registrera programmet i sin Azure Active Directory. I slutet av registreringen hämtar programmets ägare följande värden:

- Ett **program-ID** 
- En **autentiseringsnyckel** (kallas även delad hemlighet). 

Programmet måste presentera båda dessa värden för Azure Active Directory för att få en token. Hur programmet är konfigurerat för att göra detta beror på programmet. I [Key Vault-exempelprogrammet](https://www.microsoft.com/download/details.aspx?id=45343) anger programmets ägare dessa värden i filen app.config.


Så här registrerar du programmet i Azure Active Directory:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster klickar du på **Appregistreringar**. Om du inte ser appregistreringar kan du klicka på **fler tjänster** och hitta det där.  
>[!NOTE]
Du måste välja samma katalog som innehåller Azure-prenumerationen som du skapade nyckelvalvet med. 
3. Klicka på **Ny programregistrering**.
4. På bladet **Skapa** anger du ett namn för appen och väljer sedan **WEBBAPP OCH/ELLER WEBB-API** (standardinställningen) och anger **INLOGGNINGS-URL** för din webbapp. Om du inte har den här informationen just nu kan du hitta på den för det här steget (till exempel kan du ange http://test1.contoso.com). Det spelar ingen roll om dessa platser finns eller inte. 

    ![Ny programregistrering](./media/key-vault-get-started/new-application-registration.png)
    >[!WARNING]
    Kontrollera att du valde **WEBBAPP OCH/ELLER WEBB-API**. Om du inte gjorde det ser du inte **nyckelalternativet** under inställningarna.

5. Klicka på knappen **Skapa**.
6. Du kan se listan över registrerade appar när appregistreringen har slutförts. Leta reda på appen du just registrerade och klicka på den.
7. Klicka på bladet **Registrerad app** och kopiera **Program-ID:t**
8. Klicka på **Alla inställningar**
9. I bladet **Inställningar** klickar du på **nycklar**
9. Ange en beskrivning i rutan **Nyckelbeskrivning** och välj en längd. Klicka sedan på **SPARA**. Sidan uppdateras och innehåller nu ett nyckelvärde. 
10. Du använder **Program-ID:T** och **nyckelinformationen** i nästa steg för att ställa in behörigheter för ditt valv.

## <a id="authorize"></a>Godkänna att programmet använder nyckeln eller hemligheten
Det finns två sätt att ge programmet åtkomst till nyckeln eller hemligheten i valvet.

### <a name="using-powershell"></a>Använda PowerShell
Använd cmdleten [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) för att använda PowerShell.

Om ditt valvnamn till exempel är **ContosoKeyVault** och programmet som du vill auktorisera har klient-ID:t 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed och du vill tillåta att programmet dekrypterar och loggar in med nycklar i valvet, kör du följande:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Om du vill att samma program ska kunna läsa hemligheter i valvet kör du följande:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get
```
### <a name="using-the-azure-portal"></a>Använda Azure Portal
För att ändra programmets behörighet att använda nycklar och hemligheter:
1. Välj **Åtkomstprinciper** från resursbladet Key Vault
2. Klicka på [+ Lägg till ny] upptill på bladet
3. Klicka på **Välj huvudkonto** för att välja det program du skapade tidigare
4. Välj ”Dekryptera” och ”Signera” i den nedrullningsbara listan **Nyckelbehörigheter** för att ge programmet behörighet att dekryptera och signera med nycklar i ditt valv
5. Välj ”Hämta” i den nedrullningsbara listan **Hemliga behörigheter** för att ge programmet behörighet att läsa hemligheter i valvet

## <a id="HSM"></a>Arbeta med en maskinvarusäkerhetsmodul (HSM)
Om du vill öka säkerheten ytterligare kan du importera och generera nycklar i maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM-gränsen. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Om detta krav inte är nödvändigt för dig hoppar du över det här avsnittet och går vidare till [Ta bort nyckelvalvet och associerade nycklar och hemligheter](#delete).

För att skapa dessa HSM-skyddade nycklar måste du använda [premiumnivån av Azure Key Vault för att stödja HSM-skyddade nycklar](https://azure.microsoft.com/pricing/free-trial/). Observera även att den här funktionen inte är tillgänglig för Azure i Kina.

När du skapar nyckelvalvet lägger du till parametern **-SKU**:

```powershell
New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US' -SKU 'Premium'
```


Du kan lägga till programvaruskyddade nycklar (som du såg tidigare) och HSM-skyddade nycklar till det här nyckelvalvet. Om du vill skapa en HSM-skyddad nyckel anger du parametern **-Destination** till 'HSM':

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'
```

Du kan använda följande kommando för att importera en nyckel från en PFX-fil på datorn. Det här kommandot importerar nyckeln till HSM-moduler i Key Vault-tjänsten:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'
```

Nästa kommando importerar ett BYOK-paket (Bring Your Own Key). Med det är scenariot kan du generera nyckeln i din lokala HSM och överföra den till HSM-moduler i Key Vault-tjänsten utan att nyckeln lämnar HSM-gränsen:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'
```

Mer detaljerad information om hur du skapar det här BYOK-paketet finns i [Generera och överföra HSM-skyddade nycklar för Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Ta bort nyckelvalvet och associerade nycklar och hemligheter
Om du inte längre behöver nyckelvalvet och nyckeln eller hemligheten som det innehåller kan du ta bort nyckelvalvet med cmdleten [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault):

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

Eller så kan du ta bort en hel Azure-resursgrupp, som innehåller nyckelvalvet och andra resurser som du har lagt till i gruppen:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'
```

## <a id="other"></a>Andra Azure PowerShell-cmdletar
Andra kommandon som kan vara användbara för att hantera Azure Key Vault:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Det här kommandot hämtar en tabellvy över alla nycklar och valda egenskaper.
- `$Keys[0]`: Det här kommandot visar en fullständig lista över egenskaper för den angivna nyckeln
- `Get-AzureKeyVaultSecret`: Det här kommandot visar en tabellvy över alla hemliga namn och valda egenskaper.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Exempel på hur du tar bort en särskild nyckel.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Exempel på hur du tar bort en särskild hemlighet.

## <a name="next-steps"></a>Nästa steg

- Översiktlig information om Azure Key Vault finns i [Vad är Azure Key Vault?](key-vault-whatis.md)
- Om du vill se hur nyckelvalvet används läser du avsnittet om [Azure Key Vault-loggning](key-vault-logging.md).
- En uppföljningskurs där Azure Key Vault används i en webbapp finns i [Använda Azure Key Vault från en webbapp](key-vault-use-from-web-application.md).
- Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](key-vault-developers-guide.md).
- En lista över de senaste Azure PowerShell-cmdlets för Azure Key Vault finns i [Cmdlets för Azure Key Vault](/powershell/module/azurerm.keyvault/#key_vault).
