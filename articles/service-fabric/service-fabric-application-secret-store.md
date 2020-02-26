---
title: Azure Service Fabric centrala hemligheter
description: Den här artikeln beskriver hur du använder Central hemligheter i Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589172"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Centrala hemligheter i Azure Service Fabric 
Den här artikeln beskriver hur du använder den centrala hemligheter Store (CSS) i Azure Service Fabric för att skapa hemligheter i Service Fabric program. CSS är en lokal hemlighet som lagrar känsliga data, till exempel lösen ord, tokens och nycklar, krypterade i minnet.

## <a name="enable-central-secrets-store"></a>Aktivera Central hemligheter Arkiv
Lägg till följande skript i kluster konfigurationen under `fabricSettings` för att aktivera CSS. Vi rekommenderar att du använder ett annat certifikat än ett kluster certifikat för CSS. Se till att krypterings certifikatet har installerats på alla noder och att `NetworkService` har Läs behörighet till certifikatets privata nyckel.
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
## <a name="declare-a-secret-resource"></a>Deklarera en hemlig resurs
Du kan skapa en hemlig resurs genom att antingen använda Azure Resource Manager-mallen eller REST API.

### <a name="use-resource-manager"></a>Använd Resource Manager

Använd följande mall för att skapa den hemliga resursen med hjälp av Resource Manager. Mallen skapar en `supersecret` hemlig resurs, men inget värde har angetts för den hemliga resursen ännu.


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

### <a name="use-the-rest-api"></a>Använd REST API

Skapa en `supersecret` hemlig resurs med hjälp av REST API genom att göra en begäran om att `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Du behöver ett kluster certifikat eller ett administratörs klient certifikat för att skapa en hemlig resurs.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Ange det hemliga värdet

### <a name="use-the-resource-manager-template"></a>Använda Resource Manager-mallen

Använd följande Resource Manager-mall för att skapa och ange det hemliga värdet. Den här mallen anger det hemliga värdet för den `supersecret` hemliga resursen som version `ver1`.
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
### <a name="use-the-rest-api"></a>Använd REST API

Använd följande skript för att använda REST API för att ange det hemliga värdet.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Granska det hemliga värdet
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Använd hemligheten i ditt program

Följ dessa steg om du vill använda hemligheten i ditt Service Fabric-program.

1. Lägg till ett avsnitt i **Settings. XML-** filen med följande kodfragment. Observera att värdet är i formatet {`secretname:version`}.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importera avsnittet i **ApplicationManifest. XML**.
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

   Miljövariabeln `SecretPath` kommer att peka på den katalog där alla hemligheter lagras. Varje parameter som anges under avsnittet `testsecrets` lagras i en separat fil. Programmet kan nu använda hemligheten på följande sätt:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Montera hemligheterna till en behållare. Den enda ändring som krävs för att göra de hemligheter som är tillgängliga i behållaren är att `specify` en monterings punkt i `<ConfigPackage>`.
Följande kodfragment är den ändrade **ApplicationManifest. XML**.  

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

1. Du kan binda en hemlighet till en process miljö variabel genom att ange `Type='SecretsStoreRef`. Följande fragment är ett exempel på hur du binder `supersecret`-versionen `ver1` till miljövariabeln `MySuperSecret` i **ServiceManifest. XML**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Nästa steg
Läs mer om [säkerhet för program och tjänster](service-fabric-application-and-service-security.md).
