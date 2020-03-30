---
title: Certifikatbaserad autentisering med Azure Cosmos DB och Active Directory
description: Lär dig hur du konfigurerar en Azure AD-identitet för certifikatbaserad autentisering för att komma åt nycklar från Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 085280a8064e4d12ac63939ada7cdb296d47dc70
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365770"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Certifikatbaserad autentisering för en Azure AD-identitet för att komma åt nycklar från ett Azure Cosmos DB-konto

Med certifikatbaserad autentisering kan klientprogrammet autentiseras med hjälp av Azure Active Directory (Azure AD) med ett klientcertifikat. Du kan utföra certifikatbaserad autentisering på en dator där du behöver en identitet, till exempel en lokal dator eller en virtuell dator i Azure. Ditt program kan sedan läsa Azure Cosmos DB-nycklar utan att ha nycklarna direkt i programmet. I den här artikeln beskrivs hur du skapar ett exempel på Azure AD-program, konfigurerar det för certifikatbaserad autentisering, loggar in på Azure med den nya programidentiteten och hämtar sedan nycklarna från ditt Azure Cosmos-konto. I den här artikeln används Azure PowerShell för att konfigurera identiteterna och en C#-exempelapp som autentiserar och använder nycklar från ditt Azure Cosmos-konto.  

## <a name="prerequisites"></a>Krav

* Installera den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

* Om du inte har en [Azure-prenumeration](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="register-an-app-in-azure-ad"></a>Registrera en app i Azure AD

I det här steget registrerar du ett exempelwebbprogram i ditt Azure AD-konto. Det här programmet används senare för att läsa nycklarna från ditt Azure Cosmos DB-konto. Så här registrerar du ett program: 

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna fönstret **Azure Active Directory,** gå till fönstret **Appregistreringar** och välj **Ny registrering**. 

   ![Ny programregistrering i Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Fyll i **ett ansökningsformulär för registret** med följande uppgifter:  

   * **Namn** - Ange ett namn för ditt program, kan det vara vilket namn som helst som "sampleApp".
   * **Kontotyper som stöds** – Välj **konton i den här organisationskatalogen (standardkatalog)** om du vill att resurser i den aktuella katalogen ska kunna komma åt det här programmet. 
   * **Omdirigera URL** – Välj program av typen **Web** och ange en URL där ditt program finns, det kan vara vilken URL som helst. I det här exemplet kan du `https://sampleApp.com` ange en test-URL, till exempel att det är okej även om appen inte finns.

   ![Registrera ett exempelwebbprogram](./media/certificate-based-authentication/register-sample-web-app.png)

1. Välj **Registrera** när du har fyllt i formuläret.

1. När appen har registrerats antecknar du **id-programmet och** **objekt-ID: t.** 

   ![Hämta program- och objekt-ID:erna](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Installera AzureAD-modulen

I det här steget installerar du Azure AD PowerShell-modulen. Den här modulen krävs för att få ID för det program som du registrerade i föregående steg och associera ett självsignerat certifikat till det programmet. 

1. Öppna Windows PowerShell ISE med administratörsrättigheter. Om du inte redan har gjort det installerar du AZ PowerShell-modulen och ansluter till din prenumeration. Om du har flera prenumerationer kan du ange kontexten för aktuell prenumeration enligt följande kommandon:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Installera och importera [AzureAD-modulen](/powershell/module/azuread/?view=azureadps-2.0)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Logga in på din Azure AD

Logga in på din Azure AD där du har registrerat programmet. Använd kommandot Connect-AzureAD för att logga in på ditt konto, ange dina Azure-kontouppgifter i popup-fönstret. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Öppna en annan instans av Windows PowerShell ISE och kör följande kommandon för att skapa ett självsignerat certifikat och läsa nyckeln som är associerad med certifikatet:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Skapa den certifikatbaserade autentiseringstillståndet 

Kör sedan följande kommandon för att hämta programmets objekt-ID och skapa den certifikatbaserade autentiseringsformuläret. I det här exemplet anger vi att certifikatet ska upphöra att gälla efter ett år, du kan ställa in det till alla nödvändiga slutdatum.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Ovanstående kommando resulterar i utdata som liknar skärmdumpen nedan:

![Certifikatbaserad autentiseringsgenereringsutdata](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Konfigurera ditt Azure Cosmos-konto så att det nya identiteten används

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Navigera till ditt Azure Cosmos-konto genom att öppna **bladet Åtkomstkontroll (IAM).**

1. Välj **Lägg till** och lägg till **rolltilldelning**. Lägg till exempelappen som du skapade i föregående steg med **rollen Deltagare** som visas i följande skärmbild:

   ![Konfigurera Azure Cosmos-konto för att använda den nya identiteten](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Välj **Spara** när du har fyllt i formuläret

## <a name="register-your-certificate-with-azure-ad"></a>Registrera ditt certifikat med Azure AD

Du kan associera den certifikatbaserade autentiseringsversionen med klientprogrammet i Azure AD från Azure-portalen. Om du vill associera autentiseringsuppgifterna måste du ladda upp certifikatfilen med följande steg:

I Azure-appregistreringen för klientprogrammet:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna Fönstret **Azure Active Directory,** gå till fönstret **Appregistreringar** och öppna exempelappen som du skapade i föregående steg. 

1. Välj **Certifikat & hemligheter** och **ladda**sedan upp certifikat . Bläddra i certifikatfilen som du skapade i föregående steg för att ladda upp.

1. Välj **Lägg till**. När certifikatet har överförts visas tumavtrycket, startdatumet och förfallodatumvärdena.

## <a name="access-the-keys-from-powershell"></a>Öppna nycklarna från PowerShell

I det här steget loggar du in på Azure med hjälp av programmet och certifikatet som du skapade och får åtkomst till azure Cosmos-kontots nycklar. 

1. Avmarkera initialt Azure-kontots autentiseringsuppgifter som du har använt för att logga in på ditt konto. Du kan rensa autentiseringsuppgifter med hjälp av följande kommando:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Nästa verifiera att du kan logga in på Azure-portalen med hjälp av programmets autentiseringsuppgifter och komma åt Azure Cosmos DB-nycklar:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

Det föregående kommandot visar de primära och sekundära huvudnycklarna för ditt Azure Cosmos-konto. Du kan visa aktivitetsloggen för ditt Azure Cosmos-konto för att verifiera att get keys-begäran lyckades och händelsen initieras av "sampleApp"-programmet.

![Validera anropet för hämta nycklar i Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)

## <a name="access-the-keys-from-a-c-application"></a>Öppna nycklarna från ett C#-program 

Du kan också validera det här scenariot genom att komma åt nycklar från ett C#-program. Följande C#-konsolprogram, som kan komma åt Azure Cosmos DB-nycklar med hjälp av appen som är registrerad i Active Directory. Se till att uppdatera klientid, clientID, certName, resursgruppnamn, prenumerations-ID, Azure Cosmos kontonamnsinformation innan du kör koden. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

Det här skriptet matar ut de primära och sekundära huvudnycklarna enligt följande skärmbild:

![csharp-programutdata](./media/certificate-based-authentication/csharp-application-output.png)

I likhet med föregående avsnitt kan du visa aktivitetsloggen för ditt Azure Cosmos-konto för att verifiera att händelsen hämta nycklar initieras av "sampleApp"-programmet. 


## <a name="next-steps"></a>Nästa steg

* [Skydda Azure Cosmos-nycklar med Azure Key Vault](access-secrets-from-keyvault.md)

* [Säkerhetsbaslinje för Azure Cosmos DB](security-baseline.md)
