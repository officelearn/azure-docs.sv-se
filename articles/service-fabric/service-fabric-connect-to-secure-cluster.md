---
title: Anslut säkert till ett Azure Service Fabric-kluster | Microsoft Docs
description: Beskriver hur du autentiserar klientåtkomst till Service Fabric-kluster och hur för säker kommunikation mellan klienter och ett kluster.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2019
ms.author: aljo
ms.openlocfilehash: 42c8fa15c6b1e7c98ae47180bec5cc61236a7c44
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666536"
---
# <a name="connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster

När en klient ansluter till en Service Fabric-klusternod, kan klienten vara autentiserade och säker kommunikation upprättas med hjälp av certifikatet säkerhets- eller Azure Active Directory (AAD). Den här autentiseringen säkerställer att endast behöriga användare har åtkomst till klustret och distribuerade program och utföra administrativa uppgifter.  Certifikat eller AAD säkerhet måste har tidigare aktiverats på klustret när klustret skapades.  Mer information om säkerhetsscenarier för kluster finns i [kluster security](service-fabric-cluster-security.md). Om du ansluter till ett kluster som skyddas med certifikat, [konfigurera klientcertifikatet](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) på den dator som ansluter till klustret. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Ansluta till ett säkert kluster med hjälp av Azure Service Fabric CLI (sfctl)

Det finns några olika sätt att ansluta till ett säkert kluster med Service Fabric CLI (sfctl). När du använder ett klientcertifikat för autentisering måste certifikatinformationen matcha ett certifikat som distribuerats till klusternoderna. Om ditt certifikat har certifikatutfärdare (CA), måste du dessutom ange betrodda certifikatutfärdare.

Du kan ansluta till ett kluster som använder den `sfctl cluster select` kommando.

Klientcertifikat kan anges i två olika sätt, antingen som ett certifikat och nyckelpar, eller som en PFX-fil. För lösenordsskyddade PEM-filer, uppmanas du att automatiskt att ange lösenordet. Om du har köpt klientcertifikatet som en PFX-fil måste du först konvertera PFX-filen till en PEM-fil med följande kommando. 

```bash
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Om din .pfx-filen inte är lösenordsskyddad, använda - passin pass: för den sista parametern.

Om du vill ange klientcertifikatet som en pem-fil, ange sökvägen till filen i den `--pem` argumentet. Exempel:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Lösenordsskyddade pem-filer efterfrågar lösenord innan du kör ett kommando.

Nyckeln par används för att ange ett certifikat, den `--cert` och `--key` argument för att ange sökvägarna till varje respektive fil.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Certifikat som används för att skydda test- eller dev kluster misslyckas ibland certifikatsverifiering. Om du vill kringgå certifikatverifiering, ange den `--no-verify` alternativet. Exempel:

> [!WARNING]
> Använd inte den `no-verify` alternativet när du ansluter till produktion som Service Fabric-kluster.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Du kan dessutom ange sökvägar till kataloger för betrodda CA-certifikat eller enskilda certifikat. Om du vill ange sökvägarna, använda den `--ca` argumentet. Exempel:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

När du ansluter kan du ska kunna [köra andra sfctl kommandon](service-fabric-cli.md) att interagera med klustret.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Ansluta till ett kluster med hjälp av PowerShell
Innan du utför åtgärder på ett kluster via PowerShell måste du först upprätta en anslutning till klustret. Klusteranslutningen används för alla efterföljande kommandon i den angivna PowerShell-sessionen.

### <a name="connect-to-an-unsecure-cluster"></a>Ansluta till ett oskyddat kluster

För att ansluta till ett oskyddat kluster, anger du adressen till kluster-slutpunkten till den **Connect-ServiceFabricCluster** kommando:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ansluta till ett säkert kluster med Azure Active Directory

Ange kluster certifikatets tumavtryck för att ansluta till ett säkert kluster som använder Azure Active Directory för att auktorisera administratörsåtkomst för klustret, och använda den *AzureActiveDirectory* flaggan.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till ett säkert kluster som använder ett klientcertifikat
Kör följande PowerShell-kommando för att ansluta till ett säkert kluster som använder klientcertifikat för att auktorisera administratörsåtkomst. 

#### <a name="connect-using-certificate-common-name"></a>Ansluta med certifikatets unika namn
Ange kluster certifikatets unika namn och namnet på det klientcertifikat som har beviljats behörigheter för klusterhantering. Certifikatinformationen måste matcha ett certifikat på noderna i klustret.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* är det egna namnet för certifikatet installeras på noderna i klustret. *FindValue* är namnet på klientcertifikat för administratör. När parametrarna är ifyllt kommandot ser ut som i följande exempel:
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
Ange kluster certifikatets tumavtryck och tumavtrycket för det klientcertifikat som har beviljats behörigheter för klusterhantering. Certifikatinformationen måste matcha ett certifikat på noderna i klustret.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* är tumavtrycket för certifikatet installeras på noderna i klustret. *FindValue* är tumavtrycket för klientcertifikat för administratör.  När parametrarna är ifyllt kommandot ser ut som i följande exempel:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Ansluta till ett säkert kluster med Windows Active Directory
Om ditt fristående kluster distribueras med hjälp av AD-säkerhetsgrupper, kan du ansluta till klustret genom att lägga till växeln ”WindowsCredential”.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Ansluta till ett kluster med FabricClient APIs
Service Fabric SDK innehåller de [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) klass för klusterhantering. Hämta Microsoft.ServiceFabric NuGet-paketet om du vill använda FabricClient APIs.

### <a name="connect-to-an-unsecure-cluster"></a>Ansluta till ett oskyddat kluster

Skapa en FabricClient-instans för att ansluta till ett oskyddat fjärrkluster, och ange klusteradressen:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

För kod som körs från inom ett kluster, till exempel i en tillförlitlig tjänst, skapa en FabricClient *utan* att ange klusteradressen. FabricClient som ansluter till lokala management-gatewayen på den nod som koden körs på, och undvika en extra nätverk hopp.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till ett säkert kluster som använder ett klientcertifikat

Noder i klustret måste ha giltiga certifikat vars nätverksnamn eller DNS-namnet i SAN-nätverk som visas i den [RemoteCommonNames egenskapen](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) nastavit [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Efter den här processen gör det möjligt för ömsesidig autentisering mellan klienten och noderna i klustret.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Ansluta till ett säkert kluster interaktivt med Azure Active Directory

I följande exempel används Azure Active Directory för identitets- och klientcertifikat för serveridentitet.

En dialogruta visas automatiskt för interaktiv inloggning vid anslutning till klustret.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Ansluta till ett säkert kluster som icke-interaktivt med Azure Active Directory

I följande exempel är beroende av Microsoft.IdentityModel.Clients.ActiveDirectory, Version: 2.19.208020213.

Läs mer på AAD tokenförvärv [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Ansluta till ett säkert kluster utan tidigare metadata kunskaper med Azure Active Directory

I följande exempel använder icke-interaktiv tokenförvärv, men samma metod som kan användas för att skapa en anpassad interaktiva tokenförvärv-upplevelse. Azure Active Directory-metadata som behövs för tokenförvärv läses från klusterkonfigurationen.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Ansluta till ett säkert kluster med Service Fabric Explorer
Nå [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) för ett kluster, pekar du webbläsaren för att:

`http://<your-cluster-endpoint>:19080/Explorer`

En fullständig URL är också tillgängligt i klustret essentials-rutan i Azure-portalen.

Du kan importera klientcertifikatet för att ansluta till ett säkert kluster på Windows- eller OS X med en webbläsare och webbläsaren uppmanar dig för certifikatet som ska användas för att ansluta till klustret.  På Linux-datorer måste måste certifikatet importeras med hjälp av avancerade webbläsarinställningar (varje webbläsare har olika sätt) och gå till platsen för certifikat på disken. Läs [ställa in ett klientcertifikat](#connectsecureclustersetupclientcert) för mer information.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ansluta till ett säkert kluster med Azure Active Directory

Om du vill ansluta till ett kluster som skyddas med AAD, pekar du webbläsaren för att:

`https://<your-cluster-endpoint>:19080/Explorer`

Du uppmanas automatiskt att logga in med AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till ett säkert kluster som använder ett klientcertifikat

Om du vill ansluta till ett kluster som skyddas med certifikat, pekar du webbläsaren för att:

`https://<your-cluster-endpoint>:19080/Explorer`

Du uppmanas automatiskt att välja ett klientcertifikat.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Konfigurera ett klientcertifikat på fjärrdatorn

Minst två certifikat ska användas för att skydda klustret, en för klustret och server och en annan för klientåtkomst.  Vi rekommenderar att du även använda ytterligare sekundära certifikat och klientcertifikat för åtkomst.  För att skydda kommunikationen mellan en klient och en klusternod med Certifikatsäkerhet, måste du först hämta och installera klientcertifikatet. Du kan installera certifikatet i det personliga (min) arkivet för den lokala datorn eller den aktuella användaren.  Du måste också tumavtrycket för certifikatet så att klienten kan autentisera i klustret.

* I Windows: Dubbelklicka på PFX-filen och följ anvisningarna för att installera certifikatet i ditt personliga arkiv, `Certificates - Current User\Personal\Certificates`. Du kan också använda PowerShell-kommando:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Om det är ett självsignerat certifikat, måste du importera det till din dator ”betrodda personer” store innan du kan använda det här certifikatet för att ansluta till ett säkert kluster.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* På Mac: Dubbelklicka på PFX-filen och följ anvisningarna för att installera certifikatet i din nyckelring.

## <a name="next-steps"></a>Nästa steg

* [Uppgraderingsprocessen för Service Fabric-kluster och förväntningar från dig](service-fabric-cluster-upgrade.md)
* [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric Health modellen introduktion](service-fabric-health-introduction.md)
* [Programsäkerhet och RunAs](service-fabric-application-runas-security.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)
