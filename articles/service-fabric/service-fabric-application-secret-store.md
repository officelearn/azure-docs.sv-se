---
title: Azure Service Fabric Central Secrets Store
description: I den här artikeln beskrivs hur du använder Central Secrets Store i Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589172"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Central Secrets Store i Azure Service Fabric 
I den här artikeln beskrivs hur du använder Css (Central Secrets Store) i Azure Service Fabric för att skapa hemligheter i Service Fabric-program. CSS är en lokal hemlig lagringscache som behåller känsliga data, till exempel ett lösenord, token och nycklar, krypterade i minnet.

## <a name="enable-central-secrets-store"></a>Aktivera Central Secrets Store
Lägg till följande skript i `fabricSettings` klusterkonfigurationen under för att aktivera CSS. Vi rekommenderar att du använder ett annat certifikat än ett klustercertifikat för CSS. Kontrollera att krypteringscertifikatet är installerat `NetworkService` på alla noder och att det har läsbehörighet till certifikatets privata nyckel.
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
Du kan skapa en hemlig resurs med hjälp av antingen Azure Resource Manager-mallen eller REST API.

### <a name="use-resource-manager"></a>Använda Resurshanteraren

Använd följande mall om du vill använda Resource Manager för att skapa den hemliga resursen. Mallen skapar `supersecret` en hemlig resurs, men inget värde har angetts för den hemliga resursen ännu.


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

Om du `supersecret` vill skapa en hemlig resurs med `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`hjälp av REST API gör du en PUT-begäran till . Du behöver klustercertifikatet eller administratörsklientcertifikatet för att skapa en hemlig resurs.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Ange det hemliga värdet

### <a name="use-the-resource-manager-template"></a>Använda mallen Resurshanteraren

Använd följande Resource Manager-mall för att skapa och ange det hemliga värdet. Den här mallen anger `supersecret` det hemliga värdet för den hemliga resursen som version `ver1`.
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
### <a name="examine-the-secret-value"></a>Undersök det hemliga värdet
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Använd hemligheten i ditt program

Följ dessa steg för att använda hemligheten i ditt Service Fabric-program.

1. Lägg till ett avsnitt i filen **settings.xml** med följande kodavsnitt. Observera här att värdet är`secretname:version`i formatet { }.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importera avsnittet i **ApplicationManifest.xml**.
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

   Miljövariabeln `SecretPath` pekar på katalogen där alla hemligheter lagras. Varje parameter som `testsecrets` anges under avsnittet lagras i en separat fil. Ansökan kan nu använda hemligheten enligt följande:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Montera hemligheterna till en behållare. Den enda ändring som krävs för att `specify` göra hemligheterna `<ConfigPackage>`tillgängliga inuti behållaren är till en monteringspunkt i .
Följande kodavsnitt är det ändrade **ApplicationManifest.xml**.  

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
   Hemligheter finns tillgängliga under monteringspunkten inuti behållaren.

1. Du kan binda en hemlighet till en `Type='SecretsStoreRef`processmiljövariabel genom att ange . Följande kodavsnitt är ett exempel på `supersecret` hur `ver1` du binder `MySuperSecret` versionen till miljövariabeln i **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Nästa steg
Läs mer om [program- och tjänstsäkerhet](service-fabric-application-and-service-security.md).
