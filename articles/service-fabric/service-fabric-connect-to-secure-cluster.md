---
title: Ansluta säkert till ett Azure Service Fabric-kluster | Microsoft Docs
description: Beskriver hur du autentiserar klientåtkomst till ett Service Fabric-kluster och hur för säker kommunikation mellan klienter och ett kluster.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2018
ms.author: ryanwi
ms.openlocfilehash: 2ddb72f267fc46d7980007d41c5d512f50eaf47e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="connect-to-a-secure-cluster"></a>Ansluta till ett säkert kluster

När en klient ansluter till en klusternod för Service Fabric, kan klienten vara autentiserad och säker kommunikation upprättas med hjälp av certifikatet säkerhets- eller Azure Active Directory (AAD). Den här autentiseringen säkerställer att endast behöriga användare har åtkomst till klustret och distribuerade program och utföra administrativa uppgifter.  Certifikat eller AAD säkerhet måste ha tidigare aktiverats på klustret när klustret skapades.  Mer information om kluster säkerhetsscenarier finns [kluster säkerhet](service-fabric-cluster-security.md). Om du ansluter till ett kluster som skyddas med certifikat, [konfigurera klientcertifikatet](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) på den dator som ansluter till klustret. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Ansluta till en säker kluster med hjälp av Azure Service Fabric CLI (sfctl)

Det finns några olika sätt att ansluta till en säker kluster med hjälp av Service Fabric-CLI (sfctl). När du använder ett klientcertifikat för autentisering måste certifikatinformationen matcha ett certifikat som distribuerats till klusternoderna. Om ditt certifikat har certifikatutfärdare (CA), måste du dessutom ange betrodda certifikatutfärdare.

Du kan ansluta till ett kluster med hjälp av den `sfctl cluster select` kommando.

Klientcertifikat kan anges i två olika sätt, som ett certifikat och nyckelpar, eller som en enda pem-fil. För lösenordsskyddade `pem` filer, uppmanas du att automatiskt för att ange lösenordet.

Om du vill ange klientcertifikatet som en pem-fil, ange sökvägen till filen i den `--pem` argumentet. Exempel:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Lösenordsskyddad pem-filer ska fråga efter lösenord innan du kör ett kommando.

Nyckeln par används för att ange ett certifikat, de `--cert` och `--key` argument för att ange sökvägar till varje respektive fil.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Certifikat som används för att säkra test- eller dev kluster misslyckas ibland valideringen av servercertifikatet. Om du vill kringgå certifikatverifiering, ange den `--no-verify` alternativet. Exempel:

> [!WARNING]
> Använd inte den `no-verify` alternativ när du ansluter till produktion Service Fabric-kluster.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Du kan dessutom ange sökvägar till kataloger av certifikat för betrodd Certifikatutfärdare eller enskilda certifikat. Använd för att ange dessa sökvägar i `--ca` argumentet. Exempel:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

När du ansluter du ska kunna [köra andra sfctl kommandon](service-fabric-cli.md) att interagera med klustret.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Ansluta till ett kluster med hjälp av PowerShell
Innan du utför åtgärder på ett kluster med hjälp av PowerShell först upprätta en anslutning till klustret. Kluster-anslutning används för alla efterföljande kommandon i den angivna PowerShell-sessionen.

### <a name="connect-to-an-unsecure-cluster"></a>Ansluta till ett oskyddat kluster

För att ansluta till ett oskyddat kluster, ger slutpunktsadress klustret till det **Connect-ServiceFabricCluster** kommando:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ansluta till en säker kluster med hjälp av Azure Active Directory

Ange tumavtrycket klustret för att ansluta till en säker kluster som använder Azure Active Directory för att auktorisera administratörsåtkomst för klustret, och använda den *AzureActiveDirectory* flaggan.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till en säker kluster som använder ett klientcertifikat
Kör följande PowerShell-kommando för att ansluta till en säker kluster som använder klientcertifikat för att auktorisera administratörsåtkomst. 

#### <a name="connect-using-certificate-common-name"></a>Ansluta med certifikatets unika namn
Ange klustret certifikatets unika namn och namnet på det klientcertifikat som har beviljats behörigheter för klusterhantering. Certifikatinformation måste matcha ett certifikat på klusternoderna.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* är namnet på certifikatet installeras på klusternoderna. *FindValue* är namnet på administratören klientcertifikatet. När parametrarna är ifyllda ser kommandot ut som i följande exempel:
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

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Ansluta till en säker kluster med hjälp av Windows Active Directory
Om fristående klustret distribueras med hjälp av AD-säkerhetsgrupp, kan du ansluta till klustret genom att lägga till växeln ”WindowsCredential”.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Ansluta till ett kluster med FabricClient APIs
Service Fabric-SDK tillhandahåller den [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) klass för klusterhantering. Om du vill använda FabricClient APIs hämta Microsoft.ServiceFabric NuGet-paketet.

### <a name="connect-to-an-unsecure-cluster"></a>Ansluta till ett oskyddat kluster

Skapa en instans av FabricClient för att ansluta till en oskyddad fjärrkluster och tillhandahålla klusteradress:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

För kod som körs från inom ett kluster, till exempel i en tillförlitlig tjänst, skapa en FabricClient *utan* anger klusteradress. FabricClient ansluter till lokala management-gateway på noden koden körs på, undvika en extra nätverket hopp.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till en säker kluster som använder ett klientcertifikat

Noder i klustret måste ha giltiga certifikat vars nätverksnamn eller DNS-namnet i SAN visas i den [RemoteCommonNames egenskapen](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials#System_Fabric_X509Credentials_RemoteCommonNames) på [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Efter den här processen kan ömsesidig autentisering mellan klienten och klusternoder.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Ansluta till en säker interaktivt med Azure Active Directory

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Ansluta till en säker icke-interaktivt med Azure Active Directory

I följande exempel är beroende av Microsoft.IdentityModel.Clients.ActiveDirectory, Version: 2.19.208020213.

Mer information om AAD-token förvärv finns [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Ansluta till en säker kluster utan föregående metadata kunskaper med Azure Active Directory

I följande exempel används icke-interaktiv token förvärv, men samma metod som kan användas för att skapa en anpassad interaktiva token förvärv upplevelse. Azure Active Directory-metadata som behövs för token läses från klusterkonfigurationen.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Ansluta till en säker kluster med hjälp av Service Fabric Explorer
Att nå [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ett kluster, peka i webbläsaren:

`http://<your-cluster-endpoint>:19080/Explorer`

Fullständig URL finns också i fönstret klustret essentials i Azure-portalen.

Du kan importera klientcertifikatet för att ansluta till en säker kluster i Windows eller OS X med en webbläsare och uppmanas du för certifikatet som ska användas för att ansluta till klustret.  För Linux-datorer måste certifikatet importeras med avancerade webbläsarinställningar (varje webbläsare har olika metoder) och gå till certifikatsplatsen på disken.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ansluta till en säker kluster med hjälp av Azure Active Directory

Peka webbläsaren för att ansluta till ett kluster som skyddas med AAD:

`https://<your-cluster-endpoint>:19080/Explorer`

Du uppmanas automatiskt att logga in med AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ansluta till en säker kluster som använder ett klientcertifikat

Peka webbläsaren för att ansluta till ett kluster som skyddas av certifikat:

`https://<your-cluster-endpoint>:19080/Explorer`

Du uppmanas automatiskt att välja ett klientcertifikat.

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Ställ in ett klientcertifikat på fjärrdatorn
Minst två certifikat ska användas för att skydda klustret, en för klustret och server-certifikat och en annan för klientåtkomst.  Vi rekommenderar att du också använda ytterligare sekundära certifikat- och klientcertifikat åtkomst.  För att säkra kommunikationen mellan en klient och en klusternod som använder för Certifikatsäkerhet, måste du först hämta och installera klientcertifikatet. Du kan installera certifikatet till det personliga (min) arkivet på den lokala datorn eller den aktuella användaren.  Du måste också tumavtrycket för certifikatet så att klienten kan autentisera till klustret.

Kör följande PowerShell-cmdlet för att ställa in klientcertifikat på den dator där du får åtkomst till klustret.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Om det är ett självsignerat certifikat, måste du importera den till din dator ”betrodda personer” store innan du kan använda det här certifikatet för att ansluta till en säker kluster.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>Nästa steg

* [Uppgraderingsprocessen för Service Fabric-kluster och förväntningar från dig](service-fabric-cluster-upgrade.md)
* [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Tjänstens hälsa för Fabric modellen introduktion](service-fabric-health-introduction.md)
* [Säkerhet och RunAs](service-fabric-application-runas-security.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)
