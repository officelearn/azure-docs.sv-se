---
title: "Använda Azure Key Vault från ett webbprogram | Microsoft Docs"
description: "Använd den här kursen för att lära dig hur du använder Azure Key Vault från ett webbprogram."
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 09/15/2017
ms.author: adhurwit
ms.openlocfilehash: 107be940b4c105056c63f793fb0111b03469bf66
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="use-azure-key-vault-from-a-web-application"></a>Använda Azure Key Vault från webbprogram

## <a name="introduction"></a>Introduktion

Använd den här kursen för att lära dig hur du använder Azure Key Vault från ett webbprogram i Azure. Den vägleder dig genom processen att komma åt en hemlighet från ett Azure Key Vault så att den kan användas i ditt webbprogram.

**Uppskattad tidsåtgång:** 15 minuter

Översiktlig information om Azure Key Vault finns i [Vad är Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här självstudiekursen behöver du följande:

* En URI för en hemlighet i en Azure Key Vault
* En klient-ID och en Klienthemlighet för ett webbprogram som har registrerats med Azure Active Directory som har åtkomst till ditt Nyckelvalv
* Ett webbprogram. Vi ska visa stegen för ett ASP.NET MVC-program som distribueras i Azure som en Webbapp.

>[!IMPORTANT]
>* Det här exemplet är beroende av ett äldre sätt att manuellt etablering AAD identiteter. För närvarande finns en ny funktion i förhandsversionen kallas [hanteras Service identitet (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview), som automatiskt kan etablera AAD identiteter. Mer information finns i följande exempel på [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) för mer information.

> [!NOTE]
>* Det är viktigt att du har slutfört stegen i [Kom igång med Azure Key Vault](key-vault-get-started.md) för den här självstudiekursen så att du har en hemlighet och klient-ID och Klienthemlighet URI för ett webbprogram.


Webbprogram som kommer åt Nyckelvalvet är det som är registrerad i Azure Active Directory och har fått tillgång till ditt Nyckelvalv. Om detta inte är fallet kan du gå tillbaka till registrera ett program i komma igång-kursen och upprepa stegen.

Den här kursen är avsedd för webbutvecklare som förstår grunderna för att skapa webbprogram på Azure. Läs mer om Azure Web Apps [översikt över Web Apps](../app-service/app-service-web-overview.md).

## <a id="packages"></a>Lägg till NuGet-paket

Det finns två paket som ditt webbprogram måste ha installerats.

* Active Directory Authentication Library - innehåller metoder för att interagera med Azure Active Directory och hantera användaridentitet
* Azure Key Vault Library - innehåller metoder för att interagera med Azure Key Vault

Båda dessa paket kan installeras med hjälp av kommandot Install-Package Package Manager-konsolen.

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Ändra Web.Config

Det finns tre inställningar för program som behöver läggas till i filen web.config på följande sätt.

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

Om du inte kommer att vara värd för programmet som en Azure Web App, bör du lägga till faktiska värden för ClientId, Klienthemligheten och hemlighet URI till web.config. Annars lämnar du värdena dummy eftersom vi kommer att lägga till de faktiska värdena i Azure portal för en extra nivå av säkerhet.

## <a id="gettoken"></a>Lägg till metoden för att hämta ett åtkomsttoken

För att kunna använda Key Vault API behöver du en åtkomst-token. Key Vault klienten hanterar anrop till Key Vault-API men du måste ange den med en funktion som hämtar den åtkomst-token.  

Följande är koden för att hämta en token från Azure Active Directory. Den här koden kan gå var som helst i programmet. Jag vill lägga till en klass med verktyg för webbplatsuppgradering eller EncryptionHelper.  

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
```

> [!NOTE]
>* För närvarande är den nya funktionen Managed Service Identity (MSI) det enklaste sättet att autentisera. Mer information finns via följande länk till exemplet med [Key Vault med MSI i ett program i .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) och den relaterade [självstudien för MSI med App Service och Functions](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). 
>* Med hjälp av klient-ID och Klienthemlighet är ett annat sätt att autentisera en Azure AD-program. Och använda den i ditt webbprogram för en uppdelning av uppgifter och mer kontroll över din nyckelhantering. Men den förlitar sig på att placera Klienthemligheten i konfigurationsinställningarna, som kan vara riskabelt som som ger den hemlighet som du vill skydda i inställningarna för några. Se följande information om hur du använder en klient-ID och certifikat i stället för klient-ID och Klienthemlighet för att autentisera Azure AD-program.

## <a id="appstart"></a>Hämta hemligheten på programmet starta

Nu måste vi kod för att anropa API för Key Vault och hämta hemligheten. Följande kod kan placeras var som helst så länge den anropas innan du behöver använda den. Jag har placerat den här koden i programmet Starta händelsen i Global.asax så att den körs en gång vid start och tillgängliggör hemligheten för programmet.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Lägg till App-inställningar i Azure-portalen (valfritt)

Om du har en Azure Web App, kan du nu lägga till de faktiska värdena för AppSettings i Azure-portalen. Genom att göra de faktiska värdena är inte i web.config men skyddas via portalen där du har separata åtkomstfunktioner för kontrollen. Dessa värden ersätts med de värden som du angav i filen web.config. Kontrollera att namnen är samma.

![Programinställningar som visas i Azure-portalen][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Autentisera med ett certifikat i stället för en Klienthemlighet

Ett annat sätt att autentisera en Azure AD-program är att använda ett klient-ID och ett certifikat i stället för en klient-ID och Klienthemlighet. Nedan följer stegen för att använda ett certifikat i en Azure Web App:

1. Hämta eller skapa ett certifikat
2. Associera certifikatet med ett Azure AD-program
3. Lägg till kod i ditt webbprogram till att använda certifikat
4. Lägga till ett certifikat till ditt webbprogram

### <a name="get-or-create-a-certificate"></a>Hämta eller skapa ett certifikat

För våra ändamål gör vi ett testcertifikat. Här är några av kommandon som du kan använda i en utvecklare kommandotolk för att skapa ett certifikat. Ändra katalogen till önskad cert-filer som har skapats.  Kan också använda aktuellt datum plus 1 år för start- och slutdatum för certifikatet.

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2017 -e 07/31/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

Anteckna slutdatumet och lösenordet för PFX (i det här exemplet: 07/31/2017 och test123). Du behöver dem nedan.

Mer information om hur du skapar ett testcertifikat finns [så här: skapa dina egna testa certifikat](https://msdn.microsoft.com/library/ff699202.aspx)

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Associera certifikatet med ett Azure AD-program

Nu när du har ett certifikat måste du koppla den med Azure AD-program. Azure portal stöder för närvarande kan inte arbetsflöden. Detta kan utföras via PowerShell. Kör följande kommandon för att partner certifikatet med Azure AD-program:

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

När du har kört dessa kommandon, kan du se program i Azure AD. När du söker kan du kontrollera att du väljer ”mitt företag äger” i stället för ”program företaget använder” i dialogrutan Sök.

Läs mer om Azure AD-program och ServicePrincipal objekt i [program och tjänstens huvudnamn objekt](../active-directory/active-directory-application-objects.md).

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Lägg till kod i ditt webbprogram till att använda certifikat

Nu ska vi lägga till kod till ditt webbprogram för att komma åt cert och användas för autentisering.

Först är koden för att få åtkomst till certifikatet.

```cs
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

Observera att StoreLocation CurrentUser i stället för LocalMachine. Och som vi tillhandahåller false till metoden hitta eftersom vi använder ett test-certifikat.

Nästa är kod som använder CertificateHelper och skapar en ClientAssertionCertificate som krävs för autentisering.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Här är en ny kod att hämta åtkomsttoken. Det här ersätter metoden GetToken i föregående exempel. Jag har gett den ett annat namn i informationssyfte.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

Jag har placera alla den här koden i mitt Web App-projekt verktyg för webbplatsuppgradering klass för enkel användning.

Den senaste ändringen av koden är i metoden Application_Start. Vi måste först anropa metoden GetCert() för att läsa in ClientAssertionCertificate. Och vi ändra Återanropsmetoden som vi tillhandahåller när du skapar en ny KeyVaultClient. Observera att detta ersätter den kod som vi hade i föregående exempel.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Lägga till ett certifikat till ditt webbprogram via Azure portal

Lägga till ett certifikat till ditt webbprogram är en enkel process. Gå till Azure portal först och navigera till ditt webbprogram. På inställningsbladet för ditt webbprogram, klickar du på posten för ”anpassade domäner och SSL”. I bladet som öppnas som du kommer att kunna ladda upp certifikatet som du skapade i föregående exempel KVWebApp.pfx, se till att du kommer ihåg lösenordet för pfx.

![Lägga till ett certifikat till en Webbapp i Azure-portalen][2]

Det sista som du behöver göra är att lägga till en programinställning ditt webbprogram som har namnet webbplatsen\_BELASTNINGEN\_certifikat och värdet *. Se till att alla certifikat har lästs in. Om du vill läsa in de certifikat som du har överfört kan du ange en kommaavgränsad lista över sina tumavtryck.

Läs mer om att lägga till ett certifikat till en Webbapp i [med hjälp av certifikat i Azure webbplatser program](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>Lägg till ett certifikat på Key Vault som en hemlighet

I stället för att ladda upp certifikatet till tjänsten Web App direkt, kan du lagra den på Key Vault som en hemlighet och distribuera den därifrån. Detta är en tvåstegsprocess som beskrivs i följande blogginlägg [distribuera Azure-certifikat för webbprogram via Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>Nästa steg

Programmering referenser finns [Azure Key Vault C# klienten API-referens för](https://msdn.microsoft.com/library/azure/dn903628.aspx).

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
