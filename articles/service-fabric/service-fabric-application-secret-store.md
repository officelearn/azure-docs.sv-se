---
title: Service Fabric hemligheter
description: I den här artikeln beskrivs hur du använder Service Fabric hemligheter.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 16608d9eaf12fc9abc535ef316d7b5e8b74a8b37
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457519"
---
#  <a name="service-fabric-secrets-store"></a>Service Fabric hemligheter
Den här artikeln beskriver hur du skapar och använder hemligheter i Service Fabric program med hjälp av Service Fabric hemligheter (CSS). CSS är en lokal hemlighet för lagring som används för att hålla känsliga data, till exempel lösen ord, tokens och nycklar som är krypterade i minnet.

## <a name="enabling-secrets-store"></a>Aktivera hemligheter-Arkiv
 Lägg till följande i kluster konfigurationen under `fabricSettings` för att aktivera CSS. Vi rekommenderar att du använder ett certifikat som skiljer sig från kluster certifikat för CSS. Se till att krypterings certifikatet har installerats på alla noder och `NetworkService` har Läs behörighet till certifikatets privata nyckel.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-secret-resource"></a>Deklarera hemlig resurs
Du kan skapa en hemlig resurs antingen med hjälp av Resource Manager-mallen eller med hjälp av REST API.

* Använda Resource Manager-mallen
```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```
Mallen ovan skapar `supersecret` hemlig resurs, men inget värde har angetts för den hemliga resursen ännu.

* Använda REST API

Om du vill skapa en hemlig resurs `supersecret` gör en begäran till `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Du behöver ett kluster certifikat eller ett administratörs klient certifikat för att skapa en hemlighet.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>Ange hemligt värde
* Använda Resource Manager-mallen

Nedanstående Resource Manager-mall skapar och anger värden för hemliga `supersecret` med version `ver1`.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
* Använda REST API

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>Använda hemligheten i ditt program

1.  Lägg till ett avsnitt i Settings. XML-filen med innehållet nedan. Observera att värdet är av formatet {`secretname:version`}

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. Importera nu avsnittet i ApplicationManifest. XML
```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ConfigPackagePolicies CodePackageRef="Code">
        <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
        </ConfigPackagePolicies>
    </Policies>
  </ServiceManifestImport>
```

Miljövariabeln SecretPath pekar på den katalog där alla hemligheter lagras. Varje parameter som anges under avsnitt `testsecrets` kommer att lagras i en separat fil. Programmet kan nu använda hemligheten som visas nedan
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. Montera hemligheter till en behållare

Endast ändringar som krävs för att göra de hemligheter som är tillgängliga i behållaren är att ange en monterings punkt i `<ConfigPackage>`.
Här är den ändrade ApplicationManifest. XML  

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ConfigPackagePolicies CodePackageRef="Code">
        <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
        <!-- Linux Container
         <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
        -->
      </ConfigPackagePolicies>
    </Policies>
  </ServiceManifestImport>
```
Hemligheter är tillgängliga under monterings punkten i din behållare.

4. Bindnings hemlighet till en miljö variabel 

Du kan binda hemligheten till en process miljö variabel genom att ange typ = ' SecretsStoreRef '. Här är ett exempel på hur du binder `supersecret` version `ver1` till miljövariabeln `MySuperSecret` i ServiceManifest. xml.

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>Nästa steg
Läs mer om [säkerhet för program och tjänster](service-fabric-application-and-service-security.md)
