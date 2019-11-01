---
title: Azure Service Fabric – använda referenser för Service Fabric-program för nyckel valv | Microsoft Docs
description: Den här artikeln förklarar hur du använder Service Fabric KeyVaultReference-stöd för program hemligheter.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 09/20/2019
ms.author: atsenthi
ms.openlocfilehash: b0f1a081727721ea0325276cf9edd52c6d71fb6b
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243865"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>KeyVaultReference-stöd för Service Fabric program (för hands version)

En vanlig utmaning när du bygger moln program är hur du säkert lagrar hemligheter som krävs av ditt program. Du kanske till exempel vill lagra autentiseringsuppgifterna för behållar databasen i nyckel valvet och referera till den i program manifestet. Service Fabric KeyVaultReference använder Service Fabric hanterad identitet och gör det enkelt att referera till nyckel valv hemligheter. Resten av den här artikeln beskriver hur du använder Service Fabric KeyVaultReference och innehåller en typisk användning.

## <a name="prerequisites"></a>Krav

- Hanterad identitet för program (MIT)
    
    Service Fabric KeyVaultReference-support använder programmets hanterade identitet och därför planerar att använda KeyVaultReferences bör använda hanterad identitet. Följ det här [dokumentet](concepts-managed-identity.md) för att aktivera hanterad identitet för ditt program.

- Centrala hemligheter lagras (CSS).

    Den centrala hemligheter Store (CSS) är service Fabrics krypterade lokala hemligheter, KeyVaultReference en gång som hämtats cachelagrade i CSS.

    Lägg till nedanstående i kluster konfigurationen under `fabricSettings` för att aktivera alla nödvändiga funktioner för KeyVaultReference-stöd.

    ```json
    "fabricSettings": 
    [
        ...
    {
        "parameters":  [
            "name":  "CentralSecretService"
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
                }
                ],
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > Vi rekommenderar att du använder ett separat krypterings certifikat för CSS. Du kan lägga till det under avsnittet "CentralSecretService".

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```

- Bevilja programmets åtkomst behörighet för hanterad identitet till nyckel valvet

    Referera till det här [dokumentet](how-to-grant-access-other-resources.md) för att se hur du beviljar hanterad identitets åtkomst till nyckel valv. Observera också om du använder systemtilldelad hanterad identitet skapas den hanterade identiteten endast efter program distributionen.

## <a name="keyvault-secret-as-application-parameter"></a>Nyckel valvs hemlighet som program parameter
Låt oss säga att programmet behöver läsa databas lösen ordet för Server delen som lagras i nyckel valvet. Service Fabric KeyVaultReference-support gör det enkelt. Exemplet nedan läser `DBPassword` hemlighet från ett nyckel valv med hjälp av stöd för Service Fabric KeyVaultReference.

- Lägg till ett avsnitt i Settings. XML

    Definiera `DBPassword` parameter med typen `KeyVaultReference` och värde `<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Referera till det nya avsnittet i ApplicationManifest. xml i `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Använda KeyVaultReference i ditt program

    Service Fabric på tjänsteinstansieringen kommer att matcha KeyVaultReference-parametern med programmets hanterade identitet. Varje parameter som anges under `<Section  Name=dbsecrets>` är en fil under mappen som EnvironmentVariable SecretPath. Under C# kodfragmentet visas hur du läser DBPassword i ditt program.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > För container scenariot kan du använda monterings punkt för att styra var `secrets` ska monteras.

## <a name="keyvault-secret-as-environment-variable"></a>Nyckel valvs hemlighet som miljö variabel

Service Fabric miljövariabler stöder nu KeyVaultReference-typ, och exempel visar hur du binder en miljö variabel till en hemlighet som lagras i ett nyckel valv.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Nyckel valvs hemlighet som container databas lösen ord
KeyVaultReference är en typ som stöds för container RepositoryCredentials. nedan visas ett exempel på hur du använder en nyckel valv referens som lösen ord för container lagring.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>FAQ
- Hanterad identitet måste aktive ras för KeyVaultReference-stöd, din program aktivering Miss fungerar om KeyVaultReference används utan att aktivera hanterad identitet.

- Om du använder en tilldelad identitet skapas den bara när programmet har distribuerats och detta skapar ett cirkulärt beroende. När ditt program har distribuerats kan du ge systemet tilldelad identitet åtkomst behörighet till nyckel valvet. Du hittar systemet tilldelad identitet efter namn {Cluster}/{Application namn}/{ServiceName}

- Nyckel valvet måste finnas i samma prenumeration som Service Fabric-klustret. 

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om Azure-valv](https://docs.microsoft.com/azure/key-vault/)
