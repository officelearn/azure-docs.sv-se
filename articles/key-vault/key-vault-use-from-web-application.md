---
title: 'Självstudie: Använda Azure Key Vault från en webbapp | Microsoft Docs'
description: Använd den här självstudien till att lära dig hur du använder Azure Key Vault från en webbapp.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: barclayn
ms.openlocfilehash: ff59e39e54433aa673b093e2ee1fbe8c74010e54
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171331"
---
# <a name="tutorial-use-azure-key-vault-from-a-web-application"></a>Självstudie: Använda Azure Key Vault från en webbapp

Använd den här självstudien till att lära dig hur du använder Azure Key Vault från en webbapp i Azure. Du får gå igenom processen att bedöma en hemlighet från Azure Key Vault och använda den i en webbapp. Processen utvecklas sedan och du får använda ett certifikat i stället för en klienthemlighet. Den här självstudien är avsedd för webbutvecklare med grundläggande kunskaper om att skapa webbappar i Azure.

I den här guiden får du lära dig att: 

> [!div class="checklist"]
> * Lägga till appinställningar i filen web.config
> * Lägga till en metod för att få en åtkomsttoken
> * Hämta token när appen startas
> * Autentisera med ett certifikat

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

När du ska gå igenom den här självstudien behöver du följande:

* en URI till en hemlighet i ett Azure Key Vault
* ett klient-ID och en klienthemlighet för en webbapp som är registrerad i Azure Active Directory och som har åtkomst till ditt nyckelvalv
* en webbapp. I den här självstudien visas stegen för en ASP.NET MVC-app som är distribuerad i Azure som en webbapp.

Utför stegen i [Kom igång med Azure Key Vault](key-vault-get-started.md) där du hämtar URI:n till en hemlighet, ett klient-ID, en klienthemlighet och sedan registrerar appen. Webbappen måste komma åt valvet och måste därför vara registrerad i Azure Active Directory. Den måste dessutom ha åtkomstbehörigheter till nyckelvalvet. I annat fall går du tillbaka till Registrera ett program i självstudien Kom igång och upprepar de här stegen. Mer information om att skapa webbappar i Azure finns i [Översikt över webbappar](../app-service/app-service-web-overview.md).

I det här exemplet måste du etablera Azure Active Directory-identiteter manuellt. Dock bör du använda [hanterad tjänstidentitet (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview) i stället. MSI:er kan automatiskt etablera Azure AD-identiteter. Mer information finns i exemplet på [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) och den relaterade självstudien [MSI med App Service och Functions](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). Du kan även titta på den Key Vault-specifika [MSI-självstudien](tutorial-web-application-keyvault.md)


## <a id="packages"></a>Lägga till NuGet-paket

Du måste installera två paket för webbappen.

* Active Directory Authentication Library – har metoder för att interagera med Azure Active Directory och hantera användaridentiteter
* Azure Key Vault Library – har metoder för att interagera med Azure Key Vault

Du kan installera båda paketen från Package Manager-konsolen med kommandot Install-Package.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory 
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Ändra web.config

Du måste lägga till tre appinställningar i filen web.config. Vi kommer att höja säkerheten genom att lägga till de faktiska värdena i Azure Portal.

```xml
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
    <!-- If you aren't hosting your app as an Azure Web App, then you should use the actual ClientId, Client Secret, and Secret URI values -->
```



## <a id="gettoken"></a>Lägga till en metod för att få en åtkomsttoken

Om du vill använda Key Vault-API:t behöver du en åtkomsttoken. Key Vault-klienten hanterar anrop till Key Vault-API:t, men du måste ange en funktion som hämtar den åtkomsttoken som behövs. I följande exempel visas kod för att hämta en åtkomsttoken från Azure Active Directory. Du kan placera koden var som helst i appen. Jag föredrar att lägga till en Utils- eller EncryptionHelper-klass.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
// Using Client ID and Client Secret is a way to authenticate an Azure AD application.
// Using it in your web application allows for a separation of duties and more control over your key management. 
// However, it does rely on putting the Client Secret in your configuration settings.
// For some people, this can be as risky as putting the secret in your configuration settings.
```

## <a id="appstart"></a>Hämta hemligheten när appen startas

Nu behöver vi kod för att anropa Key Vault-API:t och hämta hemligheten. Du kan placera följande kod var som helst så länge den anropas innan du behöver använda hemligheten. Jag har placerat koden i händelsen Application Start i Global.asax så att den körs en gång när appen startas och hemligheten blir tillgänglig i appen.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Lägga till appinställningar i Azure Portal

Du kan nu lägga till de faktiska värdena för webbappens inställningar i Azure Portal. När du gör det finns inte de faktiska värdena längre i web.config, utan de skyddas via Portal där du har andra funktioner för åtkomstkontroll. Dessa värden ersätter värdena du angav i filen web.config. Kontrollera att du använder samma namn.

![Appinställningar som visas i Azure Portal][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Autentisera med ett certifikat i stället för en klienthemlighet

Nu när du förstår hur du autentiserar en Azure AD-app med ett klient-ID och en klienthemlighet ska vi istället använda ett klient-ID och ett certifikat. Så här använder du ett certifikat i en Azure-webbapp:

1. Hämta eller skapa ett certifikat
2. Associera certifikatet med en Azure AD-app
3. Lägg till kod för användning av certifikatet i webbappen
4. Lägg till ett certifikat i din webbapp

### <a name="get-or-create-a-certificate"></a>Hämta eller skapa ett certifikat

 Vi kommer att skapa ett testcertifikat i den här självstudien. Här är ett skript för att skapa ett självsignerat certifikat. Gå till katalogen där du vill skapa certifikatfilerna.  Du kan använda dagens datum och datumet om ett år som start- och slutdatum för certifikatet.

```powershell
#Create self-signed certificate and export pfx and cer files 
$PfxFilePath = "c:\data\KVWebApp.pfx" 
$CerFilePath = "c:\data\KVWebApp.cer" 
$DNSName = "MyComputer.Contoso.com" 
$Password ="MyPassword" 
$SecStringPw = ConvertTo-SecureString -String $Password -Force -AsPlainText 
$Cert = New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation "cert:\LocalMachine\My" -NotBefore 05/15/2018 -NotAfter 05/15/2019 
Export-PfxCertificate -cert $cert -FilePath $PFXFilePath -Password $SecStringPw 
Export-Certificate -cert $cert -FilePath $CerFilePath 
```

Anteckna slutdatumet och lösenordet för .pfx-filen (i det här exemplet är det den 15 maj 2019 och MyPassword). Du behöver dem i skriptet nedan. 
### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Associera certifikatet med en Azure AD-app

Nu när du har ett certifikat kan du behöva associera det med en Azure AD-app. Du kan skapa den här associationen via PowerShell. Kör följande kommandon för att associera certifikatet med Azure AD-appen:

```powershell
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

När du har kört kommandona kan du se appen i Azure AD. När du söker bland de registrerade apparna ska du välja **Mina appar** i stället för ”Alla appar” i sökrutan. 

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Lägg till kod för användning av certifikatet i webbappen

Nu ska vi lägga till kod i webbappen så att den kan komma åt certifikatet och använda det för autentisering. 

Först har vi kod för åtkomst till certifikatet. Lägg märke till att StoreLocation är CurrentUser i stället för LocalMachine. Dessutom anger vi ”false” för metoden Find eftersom vi använder ett testcertifikat.

```cs
//Add this using statement
using System.Security.Cryptography.X509Certificates;  

public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```



Sedan kommer kod där CertificateHelper används till att skapa ett ClientAssertionCertificate som krävs för autentisering.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Här är den nya koden för att hämta åtkomsttoken. Den här koden ersätter GetToken-metoden i föregående exempel. Jag ger den ett annat namn för att underlätta. För enkelhetens skull har jag placerat all den här koden i klassen Utils i projektet för webbappen.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```



De senaste kodändringarna ligger i metoden Application_Start. Först måste vi anropa metoden GetCert() och läsa in vårt ClientAssertionCertificate. Sedan ändrar vi motanropsmetoden som vi anger när vi skapar en ny KeyVaultClient. Den här koden ersätter koden i föregående exempel.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Lägga till ett certifikat i en webbapp via Azure Portal

Att lägga till ett certifikat i din webbapp är en enkel process i två steg. Öppna först Azure Portal och navigera till webbappen. På sidan Inställningar för webbappen klickar du på posten **SSL-inställningar**. När den öppnas laddar du upp certifikatet du skapade i föregående exempel, KVWebApp.pfx. Se till att du kommer ihåg lösenordet för pfx-filen.

![Lägga till ett certifikat i en Webbapp i Azure Portal][2]

Det sista du behöver göra är att lägga till en appinställning för webbappen med namnet WEBSITE\_LOAD\_CERTIFICATES och värdet *. Det här steget gör att samtliga certifikat läses in. Om du bara vill läsa in de certifikat du har laddat upp kan du ange en kommaavgränsad lista med tumavtrycken.


## <a name="clean-up-resources"></a>Rensa resurser
När du inte längre behöver de här resurserna tar du bort apptjänsten, nyckelvalvet och Azure AD-appen som du använde i självstudien.  


## <a id="next"></a>Nästa steg
> [!div class="nextstepaction"]
>[API-referens för Azure Key Vault Management](/dotnet/api/overview/azure/keyvault/management).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 