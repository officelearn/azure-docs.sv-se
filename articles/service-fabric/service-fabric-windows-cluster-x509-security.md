---
title: Skydda ett kluster i Windows med hjälp av certifikat
description: Säker kommunikation inom ett Azure Service Fabric fristående eller lokalt kluster, samt mellan klienter och klustret.
author: dkkapur
ms.topic: conceptual
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 5a18f957dfb7143f403d5ac30ea184023021f12c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75613932"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Skydda ett fristående kluster i Windows med X.509-certifikat
I den här artikeln beskrivs hur du skyddar kommunikationen mellan de olika noderna i ditt fristående Windows-kluster. Den beskriver också hur du autentiserar klienter som ansluter till det här klustret med hjälp av X.509-certifikat. Autentisering säkerställer att endast behöriga användare kan komma åt klustret och de distribuerade programmen och utföra hanteringsuppgifter. Certifikatsäkerhet bör aktiveras i klustret när klustret skapas.  

Mer information om klustersäkerhet, till exempel nod-till-nodsäkerhet, klient-till-nodsäkerhet och rollbaserad åtkomstkontroll, finns i [Klustersäkerhetsscenarier](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Vilka certifikat behöver du?
Börja med [genom att hämta Service Fabric för Windows Server-paketet](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) till en av noderna i klustret. I det hämtade paketet hittar du en ClusterConfig.X509.MultiMachine.json-fil. Öppna filen och granska avsnittet för säkerhet under egenskapsavsnittet:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

I det här avsnittet beskrivs de certifikat som du behöver för att skydda ditt fristående Windows-kluster. Om du anger ett klustercertifikat anger du värdet för ClusterCredentialType till _X509_. Om du anger ett servercertifikat för externa anslutningar anger du ServerCredentialType till _X509_. Även om det inte är obligatoriskt rekommenderar vi att du har båda dessa certifikat för ett korrekt skyddat kluster. Om du anger dessa värden till *X509*måste du också ange motsvarande certifikat eller Service Fabric genererar ett undantag. I vissa fall kanske du bara vill ange _ClientCertificateThumbprints_ eller _ReverseProxyCertificate_. I dessa fall behöver du inte ange _ClusterCredentialType_ eller _ServerCredentialType_ till _X509_.


> [!NOTE]
> Ett [tumavtryck](https://en.wikipedia.org/wiki/Public_key_fingerprint) är certifikatets primära identitet. Information om hur du visar tumavtrycket för de certifikat som du skapar finns i [Hämta ett tumavtryck för ett certifikat](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

I följande tabell visas de certifikat som du behöver i klusterkonfigurationen:

| **Inställningar för CertificateInformation** | **Beskrivning** |
| --- | --- |
| Klustercertifiera |Rekommenderas för en testmiljö. Det här certifikatet krävs för att skydda kommunikationen mellan noderna i ett kluster. Du kan använda två olika certifikat, en primär och en sekundär, för uppgradering. Ange tumavtrycket för det primära certifikatet i avsnittet Tumavtryck och det sekundära i tumavtryckssekrevantumablerna. |
| ClusterCertificateCommonNames |Rekommenderas för en produktionsmiljö. Det här certifikatet krävs för att skydda kommunikationen mellan noderna i ett kluster. Du kan använda ett eller två vanliga namn i klustercertifikatet. CertificateIssuerThumbprint motsvarar tumavtrycket för utfärdaren av detta certifikat. Om mer än ett certifikat med samma gemensamma namn används kan du ange flera tumavtryck för utfärdare.|
| ClusterCertificateIssuerStores |Rekommenderas för en produktionsmiljö. Det här certifikatet motsvarar utfärdaren av klustercertifikatet. Du kan ange utfärdarens gemensamma namn och motsvarande butiksnamn under det här avsnittet i stället för att ange ut utfärdarens tumavtryck under ClusterCertificateCommonNames.  Detta gör det enkelt att rulla över klusterutfärdarecertifikat. Flera utfärdare kan anges om mer än ett klustercertifikat används. En tom IssuerCommonName vitlistar alla certifikat i motsvarande butiker som anges under X509StoreNames.|
| ServerCertificate |Rekommenderas för en testmiljö. Det här certifikatet visas för klienten när det försöker ansluta till det här klustret. För enkelhetens skull kan du välja att använda samma certifikat för ClusterCertificate och ServerCertificate. Du kan använda två olika servercertifikat, en primär och en sekundär, för uppgradering. Ange tumavtrycket för det primära certifikatet i avsnittet Tumavtryck och det sekundära i tumavtryckssekrevantumablerna. |
| ServerCertificateCommonNames |Rekommenderas för en produktionsmiljö. Det här certifikatet visas för klienten när det försöker ansluta till det här klustret. CertificateIssuerThumbprint motsvarar tumavtrycket för utfärdaren av detta certifikat. Om mer än ett certifikat med samma gemensamma namn används kan du ange flera tumavtryck för utfärdare. För enkelhetens skull kan du välja att använda samma certifikat för ClusterCertificateCommonNames och ServerCertificateCommonNames. Du kan använda ett eller två vanliga servercertifikatnamn. |
| ServerCertificateIssuerStores |Rekommenderas för en produktionsmiljö. Det här certifikatet motsvarar utfärdaren av servercertifikatet. Du kan ange utfärdarens gemensamma namn och motsvarande butiksnamn under det här avsnittet i stället för att ange utfärdartumavtrycket under ServerCertificateCommonNames.  Detta gör det enkelt att rulla över serverutfärdarecertifikat. Flera utfärdare kan anges om mer än ett servercertifikat används. En tom IssuerCommonName vitlistar alla certifikat i motsvarande butiker som anges under X509StoreNames.|
| ClientCertificateThumbprints |Installera den här uppsättningen certifikat på de autentiserade klienterna. Du kan ha ett antal olika klientcertifikat installerade på de datorer som du vill tillåta åtkomst till klustret. Ange tumavtrycket för varje certifikat i variabeln CertificateThumbprint. Om du anger IsAdmin till *true*kan klienten med det här certifikatet vara installerat på den utföra administratörshanteringsaktiviteter i klustret. Om IsAdmin är *falskt*kan klienten med det här certifikatet utföra de åtgärder som endast tillåts för användaråtkomsträttigheter, vanligtvis skrivskyddad. Mer information om roller finns i [Rollbaserad åtkomstkontroll (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Ange det gemensamma namnet på det första klientcertifikatet för CertificateCommonName. CertificateIssuerThumbprint är tumavtrycket för utfärdaren av det här certifikatet. Mer information om vanliga namn och utfärdaren finns i [Arbeta med certifikat](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Rekommenderas för en produktionsmiljö. Det här certifikatet motsvarar utfärdaren av klientcertifikatet (både administratörs- och icke-administratörsroller). Du kan ange utfärdarens gemensamma namn och motsvarande butiksnamn under det här avsnittet i stället för att ange ut utfärdarens tumavtryck under ClientCertificateCommonNames.  Detta gör det enkelt att rulla över klientutfärdarecertifikat. Flera utfärdare kan anges om mer än ett klientcertifikat används. En tom IssuerCommonName vitlistar alla certifikat i motsvarande butiker som anges under X509StoreNames.|
| OmvändProxyCertificate |Rekommenderas för en testmiljö. Det här valfria certifikatet kan anges om du vill skydda [din omvända proxy](service-fabric-reverseproxy.md). Kontrollera att reverseProxyEndpointPort anges i nodeTypes om du använder det här certifikatet. |
| ReverseProxyCertificateCommonNames |Rekommenderas för en produktionsmiljö. Det här valfria certifikatet kan anges om du vill skydda [din omvända proxy](service-fabric-reverseproxy.md). Kontrollera att reverseProxyEndpointPort anges i nodeTypes om du använder det här certifikatet. |

Här är ett exempel kluster konfiguration där klustret, servern och klientcertifikat har angetts. För kluster-/server-/reverseProxy-certifikat kan tumavtrycket och det gemensamma namnet inte konfigureras tillsammans för samma certifikattyp.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Överrullning av certifikat
När du använder ett vanligt certifikatnamn i stället för ett tumavtryck kräver certifikatöverrullning inte en uppgradering av klusterkonfigurationen. För uppgraderingar av utfärdare av tumavtryck kontrollerar du att den nya tumavtryckslistan skär med den gamla listan. Du måste först uppgradera konfigurationen med de nya svarvarnaven och sedan installera de nya certifikaten (både kluster-/servercertifikat och utfärdarcertifikat) i arkivet. Behåll det gamla utfärdarcertifikatet i certifikatarkivet i minst två timmar efter installation av det nya utfärdarcertifikatet.
Om du använder utfärdararkiv behöver ingen konfigurationsuppgradering utföras för överrullning av utfärdarcertifikat. Installera det nya utfärdarcertifikatet med ett senare utgångsdatum i motsvarande certifikatarkiv och ta bort det gamla utfärdarcertifikatet efter några timmar.

## <a name="acquire-the-x509-certificates"></a>Skaffa X.509-certifikaten
För att skydda kommunikationen i klustret måste du först hämta X.509-certifikat för klusternoderna. Om du vill begränsa anslutningen till det här klustret till auktoriserade datorer/användare måste du dessutom hämta och installera certifikat för klientdatorerna.

För kluster som kör produktionsarbetsbelastningar använder du ett [certifikatutfärdare -signerat](https://en.wikipedia.org/wiki/Certificate_authority)X.509-certifikat för att skydda klustret. Mer information om hur du skaffar dessa certifikat finns i [Så här skaffar du ett certifikat](https://msdn.microsoft.com/library/aa702761.aspx).

För kluster som du använder för teständamål kan du välja att använda ett självsignerat certifikat.

## <a name="optional-create-a-self-signed-certificate"></a>Valfritt: Skapa ett självsignerat certifikat
Ett sätt att skapa ett självsignerat certifikat som kan skyddas korrekt är att använda skriptet CertSetup.ps1 i mappen Service Fabric SDK i katalogen C:\Program Files\Microsoft SDK\Service Fabric\ClusterSetup\Secure. Redigera den här filen om du vill ändra standardnamnet på certifikatet. (Leta efter värdet CN=ServiceFabricDevClusterCert.) Kör skriptet `.\CertSetup.ps1 -Install`som .

Exportera nu certifikatet till en PFX-fil med ett skyddat lösenord. Först får du tumavtrycket av certifikatet. 
1. Kör **Hantera datorcertifikat på** **Start-menyn** . 

2. Gå till mappen **Lokal dator\Personlig** och leta reda på certifikatet som du har skapat. 

3. Dubbelklicka på certifikatet för att öppna det, välj fliken **Information** och rulla ned till **tumavtrycksfältet.** 

4. Ta bort blankstegen och kopiera tumavtrycksvärdet till följande PowerShell-kommando. 

5. Ändra `String` värdet till ett lämpligt säkert lösenord för att skydda det och kör följande i PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Om du vill se information om ett certifikat som är installerat på datorn kör du följande PowerShell-kommando:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Alternativt, om du har en Azure-prenumeration, följ stegen i [Skapa ett Service Fabric-kluster med hjälp av Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Installera certifikaten
När du har certifikat kan du installera dem på klusternoderna. Noderna måste ha den senaste Windows PowerShell 3.x installerad på dem. Upprepa dessa steg på varje nod för både kluster- och servercertifikat och eventuella sekundära certifikat.

1. Kopiera PFX-filen eller filerna till noden.

2. Öppna ett PowerShell-fönster som administratör och ange följande kommandon. Ersätt *$pswd* med det lösenord som du använde för att skapa certifikatet. Ersätt *$PfxFilePath* med den fullständiga sökvägen till PFX som kopierats till den här noden.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Ange nu åtkomstkontrollen för det här certifikatet så att Service Fabric-processen, som körs under Network Service-kontot, kan använda den genom att köra följande skript. Ange tumavtrycket för certifikatet och **NÄTTJÄNSTEN** för tjänstkontot. Du kan kontrollera att ACL:erna i certifikatet är korrekta genom att öppna certifikatet i **Starta** > **hantera datorcertifikat** och titta på **Alla uppgifter** > **hantera privata nycklar**.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Upprepa föregående steg för varje servercertifikat. Du kan också använda dessa steg för att installera klientcertifikaten på de datorer som du vill tillåta åtkomst till klustret.

## <a name="create-the-secure-cluster"></a>Skapa det säkra klustret
När du har konfigurerat säkerhetsavsnittet i filen ClusterConfig.X509.MultiMachine.json kan du gå vidare till avsnittet [Skapa kluster](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) för att konfigurera noderna och skapa det fristående klustret. Kom ihåg att använda filen ClusterConfig.X509.MultiMachine.json när du skapar klustret. Kommandot kan till exempel se ut så här:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

När du har lyckats köra det säkra fristående Windows-klustret och har konfigurerat de autentiserade klienterna för att ansluta till det följer du stegen i avsnittet [Anslut till ett kluster med PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) för att ansluta till det. Ett exempel:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Du kan sedan köra andra PowerShell-kommandon för att arbeta med det här klustret. Du kan till exempel köra [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) för att visa en lista över noder i det här säkra klustret.


Om du vill ta bort klustret ansluter du till noden i klustret där du hämtade Service Fabric-paketet, öppnar en kommandorad och går till paketmappen. Kör nu följande kommando:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Felaktig certifikatkonfiguration kan förhindra att klustret kommer upp under distributionen. Om du vill själv diagnostisera säkerhetsproblem tittar du i Gruppen **Loggbokens program och tjänster loggar** > **Microsoft-Service Fabric**.
> 
> 

