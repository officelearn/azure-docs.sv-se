---
title: Azure Active Directory-certifikatbaserad autentisering med Azure Cosmos DB
description: Lär dig mer om att konfigurera en Azure AD-identitet för certifikatbaserad autentisering för åtkomstnycklar från Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: cc39cc09259c1ae681e1fee070777575e2788323
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827835"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Certifikatbaserad autentisering för en Azure AD-identitet till åtkomstnycklar från ett Azure Cosmos DB-konto

Certifikatbaserad autentisering kan ditt klientprogram ska kunna autentiseras med hjälp av Azure Active Directory (Azure AD) med ett klientcertifikat. Du kan utföra certifikatbaserad autentisering på en dator där du behöver en identitet, till exempel en lokal dator eller virtuell dator i Azure. Programmet läsa nycklar för Azure Cosmos DB utan att behöva nycklarna direkt i programmet. Den här artikeln beskriver hur du skapar ett Azure AD-exempelprogram, konfigurera den för certifikatbaserad autentisering, logga in på Azure med hjälp av den nya identiteten för programmet och hämtar sedan nycklarna från ditt Azure Cosmos-konto. Den här artikeln använder Azure PowerShell för att ange identiteterna samt en C# exempelapp som autentiserar och ger åtkomst till nycklar från ditt Azure Cosmos-konto.  

## <a name="prerequisites"></a>Förutsättningar

* Installera den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

* Om du inte har en [Azure-prenumeration](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing) kan du skapa ett[kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="register-an-app-in-azure-ad"></a>Registrera en app i Azure AD

I det här steget ska du registrera ett exempelwebbprogram i din Azure AD-konto. Det här programmet används senare för att läsa nycklarna från ditt Azure Cosmos DB-konto. Använd följande steg för att registrera ett program: 

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna Azure **Active Directory** rutan, gå till App-registreringar och väljer **ny registrering**. 

   ![Ny programregistrering i Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Fyll i **registrera ett program** formuläret med följande uppgifter:  

   * **Namn på** – ange ett namn för ditt program, det kan vara vilket namn som helst, till exempel ”exempelapp för”.
   * **Stöd för kontotyper** – Välj **konton i den här organisationskatalog endast (standardkatalog)** så att resurser i din aktuella katalog till det här programmet. 
   * **Omdirigerings-URL** – Välj program av typen **Web** och ange en URL där programmet lagras, det kan vara vilken URL. I det här exemplet kan du ange en test-URL som `https://sampleApp.com` det är okej även om appen inte finns.

   ![Registrera ett exempelwebbprogram](./media/certificate-based-authentication/register-sample-web-app.png)

1. Välj **registrera** när du har fyllt i formuläret.

1. När appen har registrerats kan anteckna den **Application(client) ID** och **objekt-ID**, du kommer att använda informationen i nästa steg. 

   ![Hämta program- och objekt-ID](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Installera modulen AzureAD

I det här steget installerar du Azure AD PowerShell-modulen. Den här modulen krävs för att hämta ID för programmet som du registrerade i föregående steg och associera ett självsignerat certifikat till programmet. 

1. Öppna Windows PowerShell ISE med administratörsbehörighet. Om du inte redan har gjort installera AZ PowerShell-modulen och ansluta till din prenumeration. Om du har flera prenumerationer kan ange du kontexten i den aktuella prenumerationen som visas i följande kommandon:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Installera och importera den [AzureAD](/powershell/module/azuread/?view=azureadps-2.0) modul

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Logga in på din Azure AD

Logga in på din Azure AD där du har registrerat programmet. Använd kommandot Connect-AzureAD logga in på ditt konto genom att ange dina autentiseringsuppgifter för Azure-konto i popup-fönstret. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Öppna en annan instans av Windows PowerShell ISE och kör följande kommandon för att skapa ett självsignerat certifikat och läsa nyckeln som är associerade med certifikatet:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Skapa den certifikatbaserad autentisering 

Kör sedan följande kommandon för att hämta objekt-ID för ditt program och skapa den certifikatbaserad autentisering. I det här exemplet vi ställer in certifikatet som upphör att gälla efter ett år, kan du ändra det till alla nödvändiga slutdatum.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Kommandot ovan resulterar i utdata som liknar skärmbilden nedan:

![Certifikatbaserad credential skapa utdata](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Konfigurera ditt Azure Cosmos-konto om du vill använda den nya identiteten

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Navigera till ditt Azure Cosmos-konto, öppna den **åtkomstkontroll (IAM)** bladet.

1. Välj **Lägg till** och **Lägg till rolltilldelning**. Lägg till exempelprogram som du skapade i föregående steg med **deltagare** roll som du ser i följande skärmbild:

   ![Konfigurera Azure Cosmos-konto om du vill använda den nya identiteten](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Välj **spara** när du har fyllt i formuläret


## <a name="access-the-keys-from-powershell"></a>Åtkomstnycklar från PowerShell

I det här steget ska du logga in på Azure med hjälp av programmet och det certifikat som du skapade och Azure Cosmos-kontots åtkomstnycklar. 

1. Först avmarkera autentiseringsuppgifterna för den Azure-konto som du har använt för att logga in på ditt konto. Du kan ta bort autentiseringsuppgifter med hjälp av följande kommando:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Verifiera sedan att logga in på Azure-portalen med hjälp av programmets autentiseringsuppgifter och få åtkomst till Azure Cosmos DB-nycklar:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

Föregående kommando visar master primära och sekundära nycklarna för ditt Azure Cosmos-konto. Du kan visa ditt Azure Cosmos-konto för att verifiera att begäran om att hämta nycklar lyckades och händelsen initieras av programmet ”exempelapp för” aktivitetslogg. 
 
![Verifiera anropet get nycklar i Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Nycklarna från en C# program 

Du kan också bekräfta det här scenariot genom att gå till nycklarna från en C# program. Följande C# konsolapp som har åtkomst till Azure Cosmos DB-nycklar med hjälp av appen som registrerats i Active Directory. Se till att uppdatera tenantId, clientID, certName, resursgruppens namn, prenumerations-ID, Azure Cosmos-kontot detaljer innan du kör koden. 

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
 
 
        /// <summary>
        /// Get an access token from Azure AD using client credentials.
        /// If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each
        /// </summary>
        private static async Task<AuthenticationResult> GetAccessToken(AuthenticationContext authContext, string resourceUri, ClientAssertionCertificate cert)
        {
            //
            // Get an access token from Azure AD using client credentials.
            // If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each.
            //
            AuthenticationResult result = null;
            int retryCount = 0;
            bool retry = false;
 
            do
            {
                retry = false;
                errorCode = 0;
 
                try
                {
                    result = await authContext.AcquireTokenAsync(resourceUri, cert);
                }
                catch (AdalException ex)
                {
                    if (ex.ErrorCode == "temporarily_unavailable")
                    {
                        retry = true;
                        retryCount++;
                        Thread.Sleep(3000);
                    }
 
                    Console.WriteLine(
                        String.Format("An error occurred while acquiring a token\nTime: {0}\nError: {1}\nRetry: {2}\n",
                        DateTime.Now.ToString(),
                        ex.ToString(),
                        retry.ToString()));
 
                    errorCode = -1;
                }
 
            } while ((retry == true) && (retryCount < 3));
            return result;
        }
    }
}
```

Det här skriptet returnerar primära och sekundära huvudnycklarna enligt följande skärmbild:

![utdata för csharp-program](./media/certificate-based-authentication/csharp-application-output.png)

Liknar föregående avsnitt, du kan visa ditt Azure Cosmos-konto för att verifiera att hämta nycklar begäran händelsen initieras av programmet ”exempelapp för” aktivitetslogg. 


## <a name="next-steps"></a>Nästa steg

* [Säkra Azure Cosmos-nycklar med hjälp av Azure Key Vault](access-secrets-from-keyvault.md)

* [Säkerhetsattribut för Azure Cosmos DB](cosmos-db-security-attributes.md)
