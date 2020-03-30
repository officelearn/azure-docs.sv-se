---
title: Ansluta säkert till ett Azure Service Fabric-kluster
description: Beskriver hur du autentiserar klientåtkomst till ett Service Fabric-kluster och hur du skyddar kommunikationen mellan klienter och ett kluster.
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: a1f4abbabe428a09492efefca4a8da9801b9f68d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258582"
---
# <a name="connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster

När en klient ansluter till en Service Fabric-klusternod kan klienten autentiseras och säker kommunikation upprättas med certifikatsäkerhet eller Azure Active Directory (AAD). Den här autentiseringen säkerställer att endast behöriga användare kan komma åt klustret och distribuerade program och utföra hanteringsuppgifter.  Certifikat- eller AAD-säkerhet måste ha aktiverats tidigare i klustret när klustret skapades.  Mer information om klustersäkerhetsscenarier finns i [Klustersäkerhet](service-fabric-cluster-security.md). Om du ansluter till ett kluster som är skyddat med certifikat [konfigurerar du klientcertifikatet](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) på datorn som ansluter till klustret. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Ansluta till ett säkert kluster med Azure Service Fabric CLI (sfctl)

Det finns några olika sätt att ansluta till ett säkert kluster med Hjälp av Service Fabric CLI (sfctl). När du använder ett klientcertifikat för autentisering måste certifikatinformationen matcha ett certifikat som distribuerats till klusternoderna. Om certifikatet har certifikatutfärdare måste du dessutom ange betrodda certifikatutfärdare.

Du kan ansluta till `sfctl cluster select` ett kluster med kommandot.

Klientcertifikat kan anges på två olika sätt, antingen som ett cert- och nyckelpar eller som en enda PFX-fil. För lösenordsskyddade PEM-filer uppmanas du automatiskt att ange lösenordet. Om du har fått klientcertifikatet som en PFX-fil konverterar du först PFX-filen till en PEM-fil med följande kommando. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Om PFX-filen inte är lösenordsskyddad använder du -passin-pass: för den sista parametern.

Om du vill ange klientcertifikatet som en pem-fil anger du filsökvägen i `--pem` argumentet. Ett exempel:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Lösenordsskyddade pem-filer kommer att fråga efter lösenord innan du kör något kommando.

Om du vill ange ett `--cert` certifikat `--key` använder tangentparet argumenten och för att ange filsökvägarna till varje fil.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Ibland misslyckas certifikat som används för att skydda test- eller utvecklingskluster certifikatvalidering. Om du vill kringgå `--no-verify` certifikatverifieringen anger du alternativet. Ett exempel:

> [!WARNING]
> Använd inte `no-verify` alternativet när du ansluter till servicetygskluster för produktionsservice.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Dessutom kan du ange sökvägar till kataloger för betrodda CERTIFIKATCERTIFIKAT eller enskilda certifikat. Om du vill ange `--ca` dessa sökvägar använder du argumentet. Ett exempel:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

När du har anslutit bör du kunna [köra andra sfctl-kommandon](service-fabric-cli.md) för att interagera med klustret.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Ansluta till ett kluster med PowerShell
Innan du utför åtgärder i ett kluster via PowerShell upprättar du först en anslutning till klustret. Klusteranslutningen används för alla efterföljande kommandon i den angivna PowerShell-sessionen.

### <a name="connect-to-an-unsecure-cluster"></a>Ansluta till ett osäkert kluster

Om du vill ansluta till ett osäkert kluster anger du klusterslutpunktsadressen till kommandot **Connect-ServiceFabricCluster:**

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ansluta till ett säkert kluster med Azure Active Directory

Om du vill ansluta till ett säkert kluster som använder Azure Active Directory för att auktorisera åtkomst till klusteradministratören anger du tumavtrycket för klustercertifikatet och använder flaggan *AzureActiveDirectory.*  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till ett säkert kluster med hjälp av ett klientcertifikat
Kör följande PowerShell-kommando för att ansluta till ett säkert kluster som använder klientcertifikat för att auktorisera administratörsåtkomst. 

#### <a name="connect-using-certificate-common-name"></a>Ansluta med vanligt certifikatnamn
Ange det gemensamma namnet på klustercertifikatet och det gemensamma namnet på klientcertifikatet som har beviljats behörigheter för klusterhantering. Certifikatinformationen måste matcha ett certifikat på klusternoderna.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* är det vanliga namnet på det servercertifikat som är installerat på klusternoderna. *FindValue* är det vanliga namnet på administratörsklientcertifikatet. När parametrarna är ifyllda ser kommandot ut som följande exempel:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>Ansluta med tumavtryck för certifikat
Ange tumavtrycket för klustercertifikatet och tumavtrycket för klientcertifikatet som har beviljats behörigheter för klusterhantering. Certifikatinformationen måste matcha ett certifikat på klusternoderna.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* är tumavtrycket för servercertifikatet som är installerat på klusternoderna. *FindValue* är tumavtrycket för administratörsklientcertifikatet.  När parametrarna är ifyllda ser kommandot ut som följande exempel:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Ansluta till ett säkert kluster med Windows Active Directory
Om det fristående klustret distribueras med AD-säkerhet ansluter du till klustret genom att lägga till växeln "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Ansluta till ett kluster med Api:erna för FabricClient
Service Fabric SDK tillhandahåller [klassen FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) för klusterhantering. Om du vill använda FabricClient-API:erna får du paketet Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Ansluta till ett osäkert kluster

Om du vill ansluta till ett fjärrtredat kluster skapar du en FabricClient-instans och anger klusteradressen:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

För kod som körs inifrån ett kluster, till exempel i en tillförlitlig tjänst, skapar du en FabricClient *utan* att ange klusteradressen. FabricClient ansluter till den lokala hanteringsgatewayen på noden som koden körs på, vilket undviker ett extra nätverkshopp.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till ett säkert kluster med hjälp av ett klientcertifikat

Noderna i klustret måste ha giltiga certifikat vars gemensamma namn eller DNS-namn i SAN visas i [egenskapen RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) på [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Efter den här processen möjliggör ömsesidig autentisering mellan klienten och klusternoderna.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Ansluta till ett säkert kluster interaktivt med Hjälp av Azure Active Directory

I följande exempel används Azure Active Directory för klientidentitet och servercertifikat för serveridentitet.

Ett dialogfönster visas automatiskt för interaktiv inloggning vid anslutning till klustret.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Ansluta till ett säkert kluster som inte interaktivt använder Azure Active Directory

Följande exempel är beroende av Microsoft.IdentityModel.Clients.ActiveDirectory, Version: 2.19.208020213.

Mer information om AAD-tokeninsamling finns i [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Ansluta till ett säkert kluster utan föregående metadatakunskap med Hjälp av Azure Active Directory

I följande exempel används icke-interaktiv tokeninsamling, men samma metod kan användas för att skapa en anpassad interaktiv tokeninsamlingsupplevelse. Azure Active Directory-metadata som behövs för tokeninsamling läss från klusterkonfiguration.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Ansluta till ett säkert kluster med Hjälp av Service Fabric Explorer
Om du vill nå [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) för ett visst kluster pekar du webbläsaren på:

`http://<your-cluster-endpoint>:19080/Explorer`

Den fullständiga URL:en är också tillgänglig i fönstret kluster essentials i Azure-portalen.

Om du vill ansluta till ett säkert kluster i Windows eller OS X i en webbläsare kan du importera klientcertifikatet och webbläsaren frågar dig om certifikatet som ska användas för att ansluta till klustret.  På Linux-datorer måste certifikatet importeras med hjälp av avancerade webbläsarinställningar (varje webbläsare har olika mekanismer) och peka på certifikatplatsen på disken. Läs [Konfigurera ett klientcertifikat](#connectsecureclustersetupclientcert) för mer information.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ansluta till ett säkert kluster med Azure Active Directory

Om du vill ansluta till ett kluster som är skyddat med AAD pekar du webbläsaren på:

`https://<your-cluster-endpoint>:19080/Explorer`

Du uppmanas automatiskt att logga in med AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till ett säkert kluster med hjälp av ett klientcertifikat

Om du vill ansluta till ett kluster som är skyddat med certifikat pekar du webbläsaren på:

`https://<your-cluster-endpoint>:19080/Explorer`

Du uppmanas automatiskt att välja ett klientcertifikat.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Konfigurera ett klientcertifikat på fjärrdatorn

Minst två certifikat bör användas för att skydda klustret, ett för kluster- och servercertifikatet och ett annat för klientåtkomst.  Vi rekommenderar att du också använder ytterligare sekundära certifikat och klientåtkomstcertifikat.  Om du vill skydda kommunikationen mellan en klient och en klusternod med certifikatsäkerhet måste du först hämta och installera klientcertifikatet. Certifikatet kan installeras i den lokala datorns eller den aktuella användarens personliga arkiv (Min).  Du behöver också servercertifikatets tumavtryck så att klienten kan autentisera klustret.

* I Windows: Dubbelklicka på PFX-filen och följ anvisningarna för att installera certifikatet i ditt personliga arkiv `Certificates - Current User\Personal\Certificates`. Du kan också använda kommandot PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Om det är ett självsignerat certifikat måste du importera det till datorns "betrodda personer" innan du kan använda det här certifikatet för att ansluta till ett säkert kluster.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* I Mac: Dubbelklicka på PFX-filen och följ anvisningarna för att installera certifikatet i din nyckelring.

## <a name="next-steps"></a>Nästa steg

* [Uppgraderingsprocessen för Service Fabric Cluster och förväntningar från dig](service-fabric-cluster-upgrade.md)
* [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric Hälsa modell introduktion](service-fabric-health-introduction.md)
* [Programsäkerhet och runas](service-fabric-application-runas-security.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)
