---
title: Skydda en Azure Service Fabric-kluster i Windows med hjälp av certifikat | Microsoft Docs
description: Säker kommunikation inom ett Azure Service Fabric-kluster i fristående eller lokalt, samt mellan klienter och klustret.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: ee2ce03fccc3e6556f9d261687edb050c8cfa1cc
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661453"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Skydda ett fristående kluster i Windows genom att använda X.509-certifikat
Den här artikeln beskriver hur du skyddar kommunikationen mellan de olika noderna i ditt fristående Windows-kluster. Det beskriver också hur du autentiserar klienter som ansluter till det här klustret genom att använda X.509-certifikat. Autentisering säkerställer att endast behöriga användare kan få åtkomst till klustret och distribuerade program och utföra administrativa uppgifter. Certifikatsäkerhet ska aktiveras på klustret när klustret har skapats.  

Mer information om säkerhet för klustret, till exempel nod till nod-säkerhet, klient-till-nod-säkerhet och rollbaserad åtkomstkontroll finns i [Klustersäkerhetsscenarier](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Vilka certifikat behöver du?
Börja med [hämtningspaketet Service Fabric för Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) till någon av noderna i klustret. I det Hämta paketet finns en ClusterConfig.X509.MultiMachine.json-fil. Öppna filen och Läs avsnittet för säkerhet under egenskapsavsnittet:

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

Det här avsnittet beskrivs de certifikat som du behöver för att skydda ditt fristående Windows-kluster. Om du anger ett klustercertifikat, ange värdet för ClusterCredentialType till _X509_. Om du anger ett servercertifikat för utanför anslutningar, anger du ServerCredentialType till _X509_. Även om det är inte obligatoriskt, rekommenderar vi att du har båda dessa certifikat för ett kluster med ordentligt. Om du anger dessa värden till *X509*, du måste också ange motsvarande certifikat eller Service Fabric genereras ett undantag. I vissa fall kan du bara ange den _ClientCertificateThumbprints_ eller _ReverseProxyCertificate_. I dessa scenarier, behöver du inte ange _ClusterCredentialType_ eller _ServerCredentialType_ till _X509_.


> [!NOTE]
> En [tumavtryck](https://en.wikipedia.org/wiki/Public_key_fingerprint) är den primära identiteten för ett certifikat. Om du vill ta reda på tumavtryck för certifikat som du skapar kan se [hämta ett tumavtryck](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

I följande tabell visas de certifikat som du behöver på din konfiguration:

| **CertificateInformation inställningen** | **Beskrivning** |
| --- | --- |
| ClusterCertificate |Rekommenderas för en testmiljö. Detta certifikat krävs för att skydda kommunikationen mellan noderna i ett kluster. Du kan använda två olika certifikat, en primär och en sekundär för uppgradering. Ange primär certifikatets tumavtryck i avsnittet tumavtryck och som sekundär i ThumbprintSecondary variabler. |
| ClusterCertificateCommonNames |Rekommenderas för en produktionsmiljö. Detta certifikat krävs för att skydda kommunikationen mellan noderna i ett kluster. Du kan använda en eller två kluster certifikat vanliga namn. CertificateIssuerThumbprint motsvarar tumavtrycket för den här certifikatutfärdaren. Om fler än ett certifikat med samma allmänna namn används, kan du ange flera certifikatutfärdarens tumavtryck.|
| ClusterCertificateIssuerStores |Rekommenderas för en produktionsmiljö. Det här certifikatet motsvarar kluster-certifikatets utfärdare. Du kan ange utfärdaren eget namn och motsvarande Butiksnamn under det här avsnittet i stället för att ange utfärdartumavtrycket under ClusterCertificateCommonNames.  Detta gör det enkelt att förnya certifikaten för klustret. Du kan ange flera utfärdare om fler än en kluster-certifikat används. En tom IssuerCommonName vitlistor alla certifikat i motsvarande butiker anges under X509StoreNames.|
| ServerCertificate |Rekommenderas för en testmiljö. Det här certifikatet visas för klienten när den försöker ansluta till det här klustret. För enkelhetens skull kan du välja att använda samma certifikat för ClusterCertificate och ServerCertificate. Du kan använda två olika servercertifikat, en primär och en sekundär för uppgradering. Ange primär certifikatets tumavtryck i avsnittet tumavtryck och som sekundär i ThumbprintSecondary variabler. |
| ServerCertificateCommonNames |Rekommenderas för en produktionsmiljö. Det här certifikatet visas för klienten när den försöker ansluta till det här klustret. CertificateIssuerThumbprint motsvarar tumavtrycket för den här certifikatutfärdaren. Om fler än ett certifikat med samma allmänna namn används, kan du ange flera certifikatutfärdarens tumavtryck. För enkelhetens skull kan du välja att använda samma certifikat för ClusterCertificateCommonNames och ServerCertificateCommonNames. Du kan använda en eller två vanliga namn på servercertifikat. |
| ServerCertificateIssuerStores |Rekommenderas för en produktionsmiljö. Det här certifikatet motsvarar utfärdaren av servercertifikatet. Du kan ange utfärdaren eget namn och motsvarande Butiksnamn under det här avsnittet i stället för att ange utfärdartumavtrycket under ServerCertificateCommonNames.  Detta gör det enkelt att förnya certifikaten för servern. Flera certifikatutfärdare kan vara anges om fler än ett servercertifikat används. En tom IssuerCommonName vitlistor alla certifikat i motsvarande butiker anges under X509StoreNames.|
| ClientCertificateThumbprints |Installera den här uppsättningen certifikat på de autentiserade klienterna. Du kan ha ett antal olika klientcertifikat som är installerade på de datorer som du vill tillåta åtkomst till klustret. Ange varje certifikatets tumavtryck i variabeln CertificateThumbprint. Om du anger IsAdmin till *SANT*, klienten med det här certifikatet installeras på det kan göra administratören hanteringsaktiviteter på klustret. Om IsAdmin är *FALSKT*, klienten med det här certifikatet kan utföra åtgärder endast tillåts för användaråtkomst rättigheter, normalt skrivskyddade. Mer information om roller finns i [rollbaserad åtkomstkontroll (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Ange namnet på det första klientcertifikatet för CertificateCommonName. CertificateIssuerThumbprint är tumavtrycket för den här certifikatutfärdaren. Läs mer om vanliga namn och utfärdaren i [fungerar med certifikat](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Rekommenderas för en produktionsmiljö. Det här certifikatet motsvarar utfärdaren av klientcertifikatet (både och icke-delad admin-roller). Du kan ange utfärdaren eget namn och motsvarande Butiksnamn under det här avsnittet i stället för att ange utfärdartumavtrycket under ClientCertificateCommonNames.  Detta gör det enkelt att förnya certifikaten för klienten. Flera certifikatutfärdare kan vara anges om mer än en klientcertifikatet används. En tom IssuerCommonName vitlistor alla certifikat i motsvarande butiker anges under X509StoreNames.|
| ReverseProxyCertificate |Rekommenderas för en testmiljö. Det här valfria certifikatet kan vara anges om du vill skydda din [omvänd proxy](service-fabric-reverseproxy.md). Kontrollera att den reverseProxyEndpointPort har angetts i nodeTypes om du använder det här certifikatet. |
| ReverseProxyCertificateCommonNames |Rekommenderas för en produktionsmiljö. Det här valfria certifikatet kan vara anges om du vill skydda din [omvänd proxy](service-fabric-reverseproxy.md). Kontrollera att den reverseProxyEndpointPort har angetts i nodeTypes om du använder det här certifikatet. |

Här är en exempelkonfiguration kluster där klustret, server och klientcertifikat har angetts. För kluster/server/reverseProxy certifikat, kan tumavtrycket och det gemensamma namnet inte konfigureras tillsammans för samma certifikattyp.

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

## <a name="certificate-rollover"></a>Förnya certifikatet
När du använder en certifikatets unika namn istället för tumavtryck kräver inte en uppgradering av klustret configuration förnya certifikatet. Kontrollera att den nya tumavtryck listan skär med gamla listan för utfärdaren tumavtryck uppgraderingar. Du behöver först en config-uppgradering med nya certifikatutfärdarens tumavtryck och sedan installera de nya certifikaten (kluster/certifikat och certifikat) i arkivet. Behåll det gamla utfärdarcertifikatet i certifikatarkivet för minst två timmar när du har installerat det nya utfärdarcertifikatet.
Om du använder utfärdarens-butiker behöver ingen config-uppgradering som ska utföras förnya certifikatet för utfärdare. Installera det nya utfärdarcertifikatet med en senare upphör att gälla i motsvarande certifikatarkiv och ta bort det gamla utfärdarcertifikatet efter ett par timmar.

## <a name="acquire-the-x509-certificates"></a>Hämta X.509-certifikat
Om du vill skydda kommunikation inom klustret, måste du först hämta X.509-certifikat för klusternoderna. Dessutom för att begränsa anslutning till det här klustret till behöriga datorer/användare, måste du hämta och installera certifikat för klientdatorerna.

För kluster som kör produktionsarbetsbelastningar kan använda en [certifikatutfärdare (CA)](https://en.wikipedia.org/wiki/Certificate_authority)-signerade X.509-certifikat för att skydda klustret. Mer information om hur du skaffar dessa certifikat finns i [hur du skaffar ett certifikat](https://msdn.microsoft.com/library/aa702761.aspx).

För kluster som du kan använda för testning kan välja du att använda ett självsignerat certifikat.

## <a name="optional-create-a-self-signed-certificate"></a>Valfritt: Skapa ett självsignerat certifikat
Ett sätt att skapa ett självsignerat certifikat som kan skyddas på rätt sätt är att använda skriptet CertSetup.ps1 i Service Fabric SDK-mappen i C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure-katalogen. Redigera den här filen om du vill ändra standardnamnet för certifikatet. (Leta efter värdet CN = ServiceFabricDevClusterCert.) Kör det här skriptet som `.\CertSetup.ps1 -Install`.

Nu exportera certifikatet till en .pfx-fil med ett skyddat lösenord. Hämta först tumavtrycket för certifikatet. 
1. Från den **starta** menyn kör **hantera datorcertifikat**. 

2. Gå till den **lokal dator\Personligt** mapp och hitta certifikatet som du skapade. 

3. Dubbelklicka på certifikatet för att öppna, Välj den **information** fliken och rulla ned till den **tumavtryck** fält. 

4. Ta bort blanksteg och kopiera tumavtrycket värdet till följande PowerShell-kommando. 

5. Ändra den `String` värdet till lämplig säkert lösenord för att skydda den och kör sedan följande i PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Om du vill se information om ett certifikat installerat på datorn, kör du följande PowerShell-kommando:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Du kan också om du har en Azure-prenumeration, följer du stegen i [skapa Service Fabric-kluster med hjälp av Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Installera certifikat
När du har certifikat kan installera du dem på klusternoderna. Dina noder måste ha den senaste Windows PowerShell 3.x installerad. Upprepa dessa steg på varje nod för både klustret och servercertifikat och sekundära certifikat.

1. Kopiera .pfx-fil eller filer till noden.

2. Öppna ett PowerShell-fönster som administratör och ange följande kommandon. Ersätt *$pswd* med det lösenord som du använde för att skapa det här certifikatet. Ersätt *$PfxFilePath* med den fullständiga sökvägen till PFX kopieras till den här noden.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Ange nu åtkomstkontroll på det här certifikatet så att Service Fabric-processen, som körs under kontot Nätverkstjänst, kan använda den genom att köra följande skript. Ange tumavtrycket för certifikatet och **NÄTVERKSTJÄNST** för tjänstkontot. Du kan kontrollera att ACL: er på certifikatet är korrekt genom att öppna certifikatet i **starta** > **hantera datorcertifikat** och titta på **alla uppgifter**  >  **Hantera privata nycklar**.
   
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
4. Upprepa föregående steg för varje certifikat. Du kan också använda de här stegen för att installera klientcertifikaten på de datorer som du vill tillåta åtkomst till klustret.

## <a name="create-the-secure-cluster"></a>Skapa säkra klustret
När du konfigurerar säkerhets-avsnittet i filen ClusterConfig.X509.MultiMachine.json, kan du fortsätta till den [skapa klustret](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) avsnitt för att konfigurera noder och skapa fristående kluster. Kom ihåg att använda ClusterConfig.X509.MultiMachine.json filen medan du skapar klustret. Kommandot kan till exempel se ut så här:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

När du har säkert fristående Windows-kluster som kör och har ställt in de autentiserade klienterna att ansluta till den, följer du stegen i avsnittet [ansluta till ett kluster med hjälp av PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) att ansluta till den. Exempel:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Du kan sedan köra andra PowerShell-kommandon för att arbeta med det här klustret. Du kan till exempel köra [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) att visa en lista över noderna i det här säkra klustret.


Ta bort klustret genom att ansluta till noden i klustret som du laddade ned Service Fabric-paket, öppna en kommandorad och gå till paketmappen. Nu ska du köra följande kommando:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Felaktig Certifikatkonfiguration kan förhindra att klustret kommer upp under distributionen. Om du vill diagnostisera lokal säkerhetsproblem, leta i visningsprogrammet för gruppen **applikationer och tjänsteloggar** > **Microsoft Service Fabric**.
> 
> 

