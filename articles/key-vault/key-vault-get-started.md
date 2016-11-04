---
title: Komma igång med Azure Key Vault | Microsoft Docs
description: Den här självstudiekursen hjälper dig att komma igång med Azure Key Vault för att skapa en säker behållare i Azure för lagring och hantering av krypteringsnycklar och hemligheter i Azure.
services: key-vault
documentationcenter: ''
author: cabailey
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/10/2016
ms.author: cabailey

---
# Komma igång med Azure Key Vault
Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).

## Introduktion
Den här självstudiekursen hjälper dig att komma igång med Azure Key Vault för att skapa en säker behållare (ett valv) i Azure för lagring och hantering av krypteringsnycklar och hemligheter i Azure. Kursen beskriver steg för steg hur du använder Azure PowerShell för att skapa ett valv som innehåller en nyckel eller ett lösenord som du sedan kan använda med ett Azure-program. Därefter tittar vi på hur ett program kan använda den här nyckeln eller lösenordet.

**Uppskattad tidsåtgång:** 20 minuter

> [!NOTE]
> Den här kursen innehåller inte instruktioner för hur du skriver Azure-programmet som ingår i ett av stegen, nämligen hur du tillåter ett program att använda en nyckel eller hemlighet i nyckelvalvet.
> 
> I den här kursen används Azure PowerShell. Anvisningar för plattformsoberoende kommandoradsgränssnitt finns i [den här självstudiekursen](key-vault-manage-with-cli.md).
> 
> 

Översiktlig information om Azure Key Vault finns i [Vad är Azure Key Vault?](key-vault-whatis.md)

## Krav
För att kunna slutföra den här självstudiekursen behöver du följande:

* En prenumeration på Microsoft Azure. Om du inte har en prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell, **minst version 1.1.0**. Om du vill installera och sedan koppla Azure PowerShell till din Azure-prenumeration läser du [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md). Om du redan har installerat Azure PowerShell och inte vet vilken version du har skriver du `(Get-Module azure -ListAvailable).Version` i Azure PowerShell-konsolen. Du kan använda den här självstudiekursen med några mindre ändringar även om du har Azure PowerShell version 0.9.1 till och med 0.9.8 installerad. Du måste till exempel använda kommandot `Switch-AzureMode AzureResourceManager`, och vissa Azure Key Vault-kommandon har ändrats. En lista med Key Vault-cmdlets för version 0.9.1 till och med 0.9.8 finns i [Cmdlets för Azure Key Vault](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx). 
* Ett program som ska konfigureras att använda nyckeln eller lösenordet som du skapar i den här självstudiekursen. Ett exempelprogram är tillgängligt från [Microsoft Download Center](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Anvisningar finns i den tillhörande Readme-filen.

Den här kursen är avsedd för nybörjare av Azure PowerShell, men vi förutsätter att du förstår de grundläggande begreppen, t.ex. moduler, cmdlets och sessioner. Mer information finns i [Komma igång med Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Om du vill visa detaljerad hjälp om cmdlets som du ser i den här självstudiekursen använder du cmdleten **Get-Help**.

    Get-Help <cmdlet-name> -Detailed

Om du till exempel behöver hjälp med cmdleten **Login-AzureRmAccount** skriver du:

    Get-Help Login-AzureRmAccount -Detailed

Du kan också läsa följande självstudier för att bekanta dig med Azure Resource Manager i Azure PowerShell:

* [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md)
* [Använda Azure PowerShell med Resource Manager](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Ansluta till dina prenumerationer
Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:  

    Login-AzureRmAccount 

Om du använder en specifik instans av Azure, till exempel Azure Government, använder du parametern -Environment med det här kommandot. Till exempel: `Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. Azure PowerShell identifierar alla prenumerationer som är associerade med det här kontot och använder den första som standard.

Om du har flera prenumerationer och vill välja en specifik prenumeration för Azure Key Vault skriver du följande för att visa prenumerationerna för ditt konto:

    Get-AzureRmSubscription

Ange sedan den prenumeration som du vill använda genom att skriva:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Mer information om hur du konfigurerar Azure PowerShell finns i [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md).

## <a id="resource"></a>Skapa en ny resursgrupp
När du använder Azure Resource Manager skapas alla relaterade resurser inuti en resursgrupp. Vi ska skapa en ny resursgrupp med namnet **ContosoResourceGroup** i den här självstudiekursen:

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Skapa ett nyckelvalv
Skapa ett nytt nyckelvalv med cmdleten [New-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736\(v=azure.300\).aspx). Denna cmdlet har tre obligatoriska parametrar: ett **resursgruppsnamn**, ett **nyckelvalvsnamn** och en **geografisk plats**.

Om du till exempel använder valvnamnet **ContosoKeyVault**, resursgruppsnamnet **ContosoResourceGroup** och platsen **East Asia** skriver du:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Utdata från denna cmdlet visar egenskaper för nyckelvalvet som du precis skapat. De två viktigaste egenskaperna är:

* **Valvnamn**: I det här exemplet är namnet **ContosoKeyVault**. Du ska använda det här namnet för andra Key Vault-cmdlets.
* **Valvets URI**: I exemplet är detta https://contosokeyvault.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu har ditt Azure-konto behörighet att utföra åtgärder i det här nyckelvalvet. Vilket ingen annan har ännu.

> [!NOTE]
> Om du får felet **Prenumerationen har inte registrerats för användning av namnrymden Microsoft.KeyVault** när du försöker skapa ett nytt nyckelvalv kör du `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` och kör sedan kommandot New-AzureRmKeyVault igen. Mer information finns i [Register-AzureRmResourceProvider](https://msdn.microsoft.com/library/azure/mt759831\(v=azure.300\).aspx).
> 
> 

## <a id="add"></a>Lägga till en nyckel eller hemlighet till nyckelvalvet
Om du vill att Azure Key Vault ska skapa en programvaruskyddad nyckel åt dig använder du cmdleten [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx)  och skriver följande:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

Men om du har en befintlig programvaruskyddad nyckel i en PFX-fil som sparats på enhet C:\ i en fil med namnet softkey.pfx som du vill överföra till Azure Key Vault skriver du följande för att ange variabeln **securepfxpwd** för lösenordet **123** för PFX-filen:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Skriv sedan följande för att importera nyckeln från PFX-filen, som skyddar den med programvara i Key Vault-tjänsten:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


Nu kan du referera till nyckeln som du skapat eller överfört till Azure Key Vault med hjälp av dess URI. Använd **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** om du alltid vill hämta den aktuella versionen och använd **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** om du vill hämta den här specifika versionen.  

Du kan visa nyckelns URI genom att skriva:

    $Key.key.kid

Om du vill lägga till en hemlighet till valvet, dvs. ett lösenord med namnet SQLPassword och värdet Pa$ w0rd i Azure Key Vault, börjar du med att konvertera värdet Pa$ $w0rd till en säker sträng genom att skriva följande:

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Skriv sedan följande:

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Nu kan du referera till det här lösenordet som du lagt till i Azure Key Vault med hjälp av dess URI. Använd **https://ContosoVault.vault.azure.net/secrets/SQLPassword** om du alltid vill hämta den senaste versionen och använd **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** om du vill hämta den här specifika versionen.

Du visar hemlighetens URI genom att skriva:

    $secret.Id

Nu ska vi visa nyckeln eller hemligheten som du precis skapat:

* Om du vill visa din nyckel skriver du: `Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
* Om du vill visa din hemlighet skriver du: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Nu är ditt nyckelvalv och din nyckel eller hemlighet redo att användas av program. Du måste ge programmen tillstånd att använda dem.  

## <a id="register"></a>Registrera ett program med Azure Active Directory
Det här steget utförs normalt av en utvecklare, på en separat dator. Det är inte specifikt för Azure Key Vault men tas med i kursen för att inte utelämna något.

> [!IMPORTANT]
> För att slutföra självstudiekursen måste ditt konto, valvet och det program som du ska registrera i det här steget finnas i samma Azure-katalog.
> 
> 

Program som använder ett nyckelvalv måste autentiseras med hjälp av en token från Azure Active Directory. Programmets ägare måste då först registrera programmet i sin Azure Active Directory. I slutet av registreringen hämtar programmets ägare följande värden:

* Ett **program-ID** (även kallat ett klient-ID) och en **autentiseringsnyckel** (även kallat den delade nyckeln). Programmet måste presentera båda dessa värden för Azure Active Directory för att få en token. Hur programmet är konfigurerat för att göra detta beror på programmet. I Key Vault-exempelprogrammet anger programmets ägare dessa värden i filen app.config.

Så här registrerar du programmet i Azure Active Directory:

1. Logga in på den klassiska Azure-portalen.
2. Klicka på **Active Directory** till vänster och välj sedan den katalog som du ska registrera programmet i. <br> <br> **Obs!** Du måste välja samma katalog som innehåller Azure-prenumerationen som du skapade nyckelvalvet med. Om du inte vet vilken katalog det är klickar du på **Inställningar**, identifierar prenumerationen som du skapade nyckelvalvet med och noterar namnet på katalogen som visas i den sista kolumnen.
3. Klicka på **Program**. Om inga appar har lagts till i katalogen visas bara länken **Lägg till en app** på den här sidan. Klicka på länken eller på **Lägg till** i kommandofältet.
4. I guiden **Lägg till program** på sidan **Vad vill du göra?** klickar du på **Lägg till ett program som min organisation utvecklar**.
5. På sidan **Berätta om appen** anger du ett namn för appen och väljer sedan **Webbapp och/eller webb-API** (standardinställningen). Klicka på ikonen **Nästa**.
6. På sidan **Appegenskaper** anger du webbappens **inloggnings-URL** och **appidentitets-URI**. Om programmet inte har dessa värden kan du hitta på dem för det här steget (du kan till exempel skriva http://test1.contoso.com i båda rutorna). Det spelar ingen roll om dessa platser finns eller inte. Det viktiga är att appidentitets-URI:n för varje program är olika för varje program i katalogen. Katalogen använder den här strängen för att identifiera din app.
7. Klicka på ikonen **Slutför** för att spara dina ändringar i guiden.
8. På sidan **Snabbstart** klickar du på **Konfigurera**.
9. Bläddra till **nycklarna**, välj varaktighet och klicka sedan på **Spara**. Sidan uppdateras och innehåller nu ett nyckelvärde. Du måste konfigurera ditt program med det här nyckelvärdet och värdet **klient-ID**. (Anvisningar för den här konfigurationen är programspecifika.)
10. Kopiera värdet för klient-ID:t från den här sidan, som du ska använda i nästa steg för att ange behörigheter för valvet.

## <a id="authorize"></a>Godkänna att programmet använder nyckeln eller hemligheten
För att ge programmet tillstånd att komma åt nyckeln eller hemligheten i valvet använder du cmdleten  [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625\(v=azure.300\).aspx).

Om ditt valvnamn till exempel är **ContosoKeyVault** och programmet som du vill auktorisera har klient-ID:t 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed och du vill tillåta att programmet dekrypterar och loggar in med nycklar i valvet, kör du följande:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Om du vill att samma program ska kunna läsa hemligheter i valvet kör du följande:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Om du vill använda en maskinvarusäkerhetsmodul (HSM)
Om du vill öka säkerheten ytterligare kan du importera och generera nycklar i maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM-gränsen. HSM-modulerna är FIPS 140-2 Level 2-verifierade. Om detta krav inte är nödvändigt för dig hoppar du över det här avsnittet och går vidare till [Ta bort nyckelvalvet och associerade nycklar och hemligheter](#delete).

Om du vill skapa HSM-skyddade nycklar behöver du en [valvprenumeration som stöder HSM-skyddade nycklar](https://azure.microsoft.com/pricing/free-trial/).  Observera också att den här funktionen inte är tillgänglig för Azure i Kina.

När du skapar valvet lägger du till parametern **-SKU**:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



Du kan lägga till programvaruskyddade nycklar (som du såg tidigare) och HSM-skyddade nycklar till det här valvet. Om du vill skapa en HSM-skyddad nyckel anger du parametern **-Destination** till 'HSM':

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Du kan använda följande kommando för att importera en nyckel från en PFX-fil på datorn. Det här kommandot importerar nyckeln till HSM-moduler i Key Vault-tjänsten:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


Nästa kommando importerar ett BYOK-paket (Bring Your Own Key). Med det är scenariot kan du generera nyckeln i din lokala HSM och överföra den till HSM-moduler i Key Vault-tjänsten utan att nyckeln lämnar HSM-gränsen:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Mer detaljerad information om hur du skapar det här BYOK-paketet finns i [Generera och överföra HSM-skyddade nycklar för Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Ta bort nyckelvalvet och associerade nycklar och hemligheter
Om du inte längre behöver nyckelvalvet och nyckeln eller hemligheten som det innehåller kan du ta bort nyckelvalvet med cmdleten [Remove-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485\(v=azure.300\).aspx):

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Eller så kan du ta bort en hel Azure-resursgrupp, som innehåller nyckelvalvet och andra resurser som du har lagt till i gruppen:

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Andra Azure PowerShell-cmdlets
Andra kommandon som kan vara användbara för att hantera Azure Key Vault:

* `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Det här kommandot hämtar en tabellvy över alla nycklar och valda egenskaper.
* `$Keys[0]`: Det här kommandot visar en fullständig lista över egenskaper för den angivna nyckeln.
* `Get-AzureKeyVaultSecret`: Det här kommandot visar en tabellvy över alla hemliga namn och valda egenskaper.
* `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Exempel på hur du tar bort en särskild nyckel.
* `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Exempel på hur du tar bort en särskild hemlighet.

## <a id="next"></a>Nästa steg
En uppföljningskurs där Azure Key Vault används i en webbapp finns i [Använda Azure Key Vault från en webbapp](key-vault-use-from-web-application.md).

Om du vill se hur nyckelvalvet används läser du avsnittet om [Azure Key Vault-loggning](key-vault-logging.md).

En lista över de senaste Azure PowerShell-cmdlets för Azure Key Vault finns i [Cmdlets för Azure Key Vault](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.300\).aspx). 

Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](key-vault-developers-guide.md).

<!--HONumber=Oct16_HO3-->


