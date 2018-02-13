---
title: "Skydda ett Azure Service Fabric-kluster i Windows genom att använda certifikat | Microsoft Docs"
description: "Säker kommunikation inom ett Azure Service Fabric-kluster i fristående eller lokalt, samt mellan klienter och klustret."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: ee1a2eeeda95b03b185090841cf93c4183c5fce2
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Skydda ett fristående kluster på Windows med X.509-certifikat
Den här artikeln beskriver hur du skyddar kommunikationen mellan olika noder för din fristående Windows-kluster. Det beskriver också hur du autentiserar klienter som ansluter till klustret med hjälp av X.509-certifikat. Autentisering säkerställer att endast auktoriserade användare kan få åtkomst till klustret och distribuerade program och utföra administrativa uppgifter. Certifikatsäkerhet ska aktiveras på klustret när klustret skapas.  

Mer information om säkerhet i klustret, till exempel nod till nod säkerhet, klient-till-nod säkerhet och rollbaserad åtkomstkontroll finns [kluster säkerhetsscenarier](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Vilka certifikat behöver du?
Börja med, [hämta Service Fabric för Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) till någon av noderna i klustret. I det Hämta paketet finns en ClusterConfig.X509.MultiMachine.json-fil. Öppna filen och läser du avsnittet för säkerhet under egenskapsavsnittet:

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
                "CertificateIssuerThumbprint": "[Thumbprint]",
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

Det här avsnittet beskrivs de certifikat som du behöver för att skydda din fristående Windows-kluster. Om du anger ett certifikat för klustret, ange värdet för ClusterCredentialType till _X509_. Om du anger ett servercertifikat för utanför anslutningar tilldelas ServerCredentialType _X509_. Även om det är inte obligatoriskt, rekommenderar vi att du har båda certifikaten för ett kluster med ordentligt. Om du värdena *X509*, du måste också ange motsvarande certifikat eller Service Fabric genererar ett undantag. I vissa fall kanske du vill ange bara den _ClientCertificateThumbprints_ eller _ReverseProxyCertificate_. I de fall du behöver inte ange _ClusterCredentialType_ eller _ServerCredentialType_ till _X509_.


> [!NOTE]
> En [tumavtrycket](https://en.wikipedia.org/wiki/Public_key_fingerprint) den primära identiteten för ett certifikat. Om du vill ta reda på tumavtryck för certifikat som du skapar kan se [hämta en tumavtrycket för ett certifikat](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

I följande tabell visas de certifikat som du behöver på din konfiguration:

| **CertificateInformation inställning** | **Beskrivning** |
| --- | --- |
| ClusterCertificate |Vi rekommenderar för en testmiljö. Detta certifikat krävs för att skydda kommunikationen mellan noder i ett kluster. Du kan använda två olika certifikat, en primär och en sekundär för uppgradering. Ange tumavtrycket för certifikatet för primär i avsnittet tumavtrycket och som sekundär i ThumbprintSecondary variabler. |
| ClusterCertificateCommonNames |Vi rekommenderar för en produktionsmiljö. Detta certifikat krävs för att skydda kommunikationen mellan noder i ett kluster. Du kan använda en eller två certifikat vanliga klusternamn. CertificateIssuerThumbprint motsvarar tumavtrycket för den här certifikatutfärdaren. Du kan ange flera utfärdaren tumavtryck om fler än ett certifikat med samma allmänna namn används.|
| ClusterCertificateIssuerStores |Vi rekommenderar för en produktionsmiljö. Det här certifikatet motsvarar kluster-certifikatets utfärdare. Du kan ange utfärdaren eget namn och motsvarande store-namn under det här avsnittet i stället för att ange utfärdaren tumavtrycket under ClusterCertificateCommonNames.  Detta gör det enkelt att förnya certifikaten för klustret. Flera certifikatutfärdare kan anges om mer än ett kluster certifikat används. En tom IssuerCommonName whitelists alla certifikat i motsvarande lagrar anges under X509StoreNames.|
| ServerCertificate |Vi rekommenderar för en testmiljö. Det här certifikatet visas till klienten när den försöker ansluta till det här klustret. Du kan välja att använda samma certifikat för ClusterCertificate och ServerCertificate för enkelhetens skull. Du kan använda två olika servercertifikat, en primär och en sekundär för uppgradering. Ange tumavtrycket för certifikatet för primär i avsnittet tumavtrycket och som sekundär i ThumbprintSecondary variabler. |
| ServerCertificateCommonNames |Vi rekommenderar för en produktionsmiljö. Det här certifikatet visas till klienten när den försöker ansluta till det här klustret. CertificateIssuerThumbprint motsvarar tumavtrycket för den här certifikatutfärdaren. Du kan ange flera utfärdaren tumavtryck om fler än ett certifikat med samma allmänna namn används. Du kan välja att använda samma certifikat för ClusterCertificateCommonNames och ServerCertificateCommonNames för enkelhetens skull. Du kan använda en eller två vanliga namn servercertifikat. |
| ServerCertificateIssuerStores |Vi rekommenderar för en produktionsmiljö. Det här certifikatet motsvarar utfärdaren av servercertifikatet. Du kan ange utfärdaren eget namn och motsvarande store-namn under det här avsnittet i stället för att ange utfärdaren tumavtrycket under ServerCertificateCommonNames.  Detta gör det enkelt att förnya certifikaten för server. Flera certifikatutfärdare kan vara angivna om mer än ett servercertifikat används. En tom IssuerCommonName whitelists alla certifikat i motsvarande lagrar anges under X509StoreNames.|
| ClientCertificateThumbprints |Installera den här uppsättningen av certifikat på de autentiserade klienterna. Du kan ha ett antal olika klientcertifikat som är installerad på de datorer som du vill tillåta åtkomst till klustret. Ange varje certifikatets tumavtryck i variabeln CertificateThumbprint. Om du anger IsAdmin *SANT*, kan klienten med det här certifikatet installeras på den göra administratören hanteringsaktiviteter på klustret. Om IsAdmin *FALSKT*, klienten med det här certifikatet kan utföra åtgärder endast tillåtet för användaråtkomst rättigheter, normalt skrivskyddade. Mer information om roller finns [rollbaserad åtkomstkontroll (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Ange namnet på det första klientcertifikatet för CertificateCommonName. CertificateIssuerThumbprint är tumavtrycket för den här certifikatutfärdaren. Läs mer om vanliga namn och utfärdaren i [arbeta med certifikat](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Vi rekommenderar för en produktionsmiljö. Det här certifikatet motsvarar utfärdaren av klientcertifikatet (både admin och icke-administratörer roller). Du kan ange utfärdaren eget namn och motsvarande store-namn under det här avsnittet i stället för att ange utfärdaren tumavtrycket under ClientCertificateCommonNames.  Detta gör det enkelt att förnyade certifikaten för klienten. Flera certifikatutfärdare kan vara angivna om mer än ett klientcertifikat används. En tom IssuerCommonName whitelists alla certifikat i motsvarande lagrar anges under X509StoreNames.|
| ReverseProxyCertificate |Vi rekommenderar för en testmiljö. Den här valfria certifikat kan vara anges om du vill skydda din [omvänd proxy](service-fabric-reverseproxy.md). Kontrollera att reverseProxyEndpointPort har angetts i nodetypes får om du använder det här certifikatet. |
| ReverseProxyCertificateCommonNames |Vi rekommenderar för en produktionsmiljö. Den här valfria certifikat kan vara anges om du vill skydda din [omvänd proxy](service-fabric-reverseproxy.md). Kontrollera att reverseProxyEndpointPort har angetts i nodetypes får om du använder det här certifikatet. |

Här är ett exempel klusterkonfigurationen där klustret, server och klientcertifikat har angetts. ReverseProxy-kluster/server-certifikat kan tumavtrycket och det gemensamma namnet inte konfigureras tillsammans för samma certifikattyp.

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
        }
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
När du använder ett eget namn för certifikatet i stället för ett tumavtryck kräver inte konfiguration av klusteruppgradering förnya certifikatet. Kontrollera att listan tumavtrycket skär med den gamla listan för utfärdaren tumavtrycket uppgraderingar. Du behöver först en config-uppgradering med nya utfärdaren tumavtryck och sedan installera de nya certifikaten (kluster/servercertifikat och certifikat) i arkivet. Behåll det gamla utfärdarcertifikatet i certifikatarkivet för minst två timmar efter att du installerar det nya utfärdarcertifikatet.
Om du använder utfärdaren lagrar sedan måste ingen config-uppgradering utföras förnya certifikatet för utfärdare. Installera det nya utfärdarcertifikatet med en senare upphör att gälla i motsvarande certifikatarkiv och ta bort det gamla utfärdarcertifikatet efter några timmar.

## <a name="acquire-the-x509-certificates"></a>Hämta X.509-certifikat
För säker kommunikation inom klustret, behöver du först skaffa X.509-certifikat för klusternoderna. Dessutom för att begränsa anslutning till detta kluster till auktoriserade datorer och användare måste hämta och installera certifikat för klientdatorer.

För kluster som kör produktionsarbetsbelastningar, använda en [certifikat (certifikatutfärdare)](https://en.wikipedia.org/wiki/Certificate_authority)-signerad X.509-certifikat för att skydda klustret. Mer information om hur du skaffar dessa certifikat finns [hur du skaffar ett certifikat](http://msdn.microsoft.com/library/aa702761.aspx).

Du kan välja att använda ett självsignerat certifikat för kluster som du använder för testning.

## <a name="optional-create-a-self-signed-certificate"></a>Valfritt: Skapa ett självsignerat certifikat
Ett sätt att skapa ett självsignerat certifikat som kan skyddas på rätt sätt är att använda skriptet CertSetup.ps1 i Service Fabric SDK-mappen i katalogen C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Redigera den här filen om du vill ändra standardnamnet för certifikatet. (Leta efter värde CN = ServiceFabricDevClusterCert.) Kör skriptet som `.\CertSetup.ps1 -Install`.

Nu exportera certifikatet till en .pfx-fil med ett skyddat lösenord. Först hämta tumavtrycket för certifikatet. 
1. Från den **starta** menyn kör **hantera datorcertifikat**. 

2. Gå till den **lokala personliga** mappen och hitta certifikatet som du skapade. 

3. Dubbelklicka på certifikatet för att öppna den, markerar du den **information** fliken och rulla ned till den **tumavtrycket** fältet. 

4. Ta bort blanksteg och kopiera tumavtrycket värdet till följande PowerShell-kommando. 

5. Ändra den `String` värde till ett lämpligt säkert lösenord för att skydda den och kör du följande i PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Kör följande PowerShell-kommando om du vill se information om ett certifikat installerat på datorn:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Alternativt, om du har en Azure-prenumeration, följer du stegen i [skapa ett Service Fabric-kluster med hjälp av Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Installera certifikat
När du har certifikat kan installera du dem på klusternoderna. Noderna behöver senaste Windows PowerShell 3.x installerad. Upprepa dessa steg på varje nod för klustret och servercertifikat och alla sekundära certifikat.

1. Kopiera den .pfx-filen eller filerna till noden.

2. Öppna ett PowerShell-fönster som administratör och ange följande kommandon. Ersätt *$pswd* och det lösenord som du använde för att skapa det här certifikatet. Ersätt *$PfxFilePath* med den fullständiga sökvägen till .pfx kopieras till den här noden.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Ange åtkomstkontroll på det här certifikatet så att Service Fabric-processen, som körs under kontot Nätverkstjänst, kan använda genom att köra följande skript. Ange tumavtrycket för certifikatet och **NÄTVERKSTJÄNSTEN** för tjänstkontot. Du kan kontrollera att ACL: er i certifikatet är korrekt genom att öppna certifikatet i **starta** > **hantera datorcertifikat** och titta på **alla aktiviteter**  >  **Hantera privata nycklar**.
   
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
4. Upprepa föregående steg för varje servercertifikat. Du kan också använda de här stegen för att installera klientcertifikat på de datorer som du vill tillåta åtkomst till klustret.

## <a name="create-the-secure-cluster"></a>Skapa säker klustret
När du har konfigurerat Säkerhetsavsnittet i filen ClusterConfig.X509.MultiMachine.json kan du fortsätta till den [skapa klustret](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) avsnittet för att konfigurera noderna och skapa fristående kluster. Kom ihåg att använda filen ClusterConfig.X509.MultiMachine.json medan du skapar klustret. Kommandot kan se ut ungefär så här:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

När du har säker fristående Windows-kluster som körs har och har skapat autentiserade klienter att ansluta till den, följer du stegen i avsnittet [Anslut till ett kluster med hjälp av PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) att ansluta till den. Exempel:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Du kan sedan köra andra PowerShell-kommandon för att arbeta med det här klustret. Du kan till exempel köra [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode.md?view=azureservicefabricps) att visa en lista över noderna i det här säker klustret.


Ansluta till nod i klustret där du hämtade Service Fabric-paket för att ta bort klustret, öppna en kommandotolk och gå till paketmappen. Nu ska du köra följande kommando:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Felaktig konfiguration kan förhindra att klustret kommande under distributionen. För att själv diagnosticera säkerhetsproblem, titta i Loggboken grupp **program- och tjänstloggar** > **Microsoft Service Fabric**.
> 
> 

