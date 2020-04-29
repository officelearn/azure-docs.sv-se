---
title: Azure Service Fabric – använda referenser för Service Fabric-program för nyckel valv
description: Den här artikeln förklarar hur du använder Service Fabric KeyVaultReference-stöd för program hemligheter.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76545491"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>KeyVaultReference-stöd för Service Fabric program (för hands version)

En vanlig utmaning när du bygger moln program är hur du säkert lagrar hemligheter som krävs av ditt program. Du kanske till exempel vill lagra autentiseringsuppgifterna för behållar databasen i nyckel valvet och referera till den i program manifestet. Service Fabric KeyVaultReference använder Service Fabric hanterad identitet och gör det enkelt att referera till nyckel valv hemligheter. Resten av den här artikeln beskriver hur du använder Service Fabric KeyVaultReference och innehåller en typisk användning.

## <a name="prerequisites"></a>Krav

- Hanterad identitet för program (MIT)
    
    Service Fabric KeyVaultReference-support använder programmets hanterade identitet och därför planerar att använda KeyVaultReferences bör använda hanterad identitet. Följ det här [dokumentet](concepts-managed-identity.md) för att aktivera hanterad identitet för ditt program.

- Centrala hemligheter lagras (CSS).

    Den centrala hemligheter Store (CSS) är Service Fabrics krypterade lokala hemligheter. CSS är en lokal hemlighet som lagrar känsliga data, till exempel lösen ord, tokens och nycklar, krypterade i minnet. KeyVaultReference, som har hämtats, cachelagras i CSS.

    Lägg till nedanstående i kluster konfigurationen under `fabricSettings` för att aktivera alla nödvändiga funktioner för KeyVaultReference-stöd.

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
                }
                ]
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
För att ändringarna ska träda i kraft måste du också ändra uppgraderings principen för att ange en tvingande omstart av Service Fabric runtime på varje nod när uppgraderingen fortskrider genom klustret. Den här omstarten säkerställer att den nyligen aktiverade system tjänsten startas och körs på varje nod. I kodfragmentet nedan är forceRestart den viktigaste inställningen. Använd dina befintliga värden för resten av inställningarna.
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- Bevilja programmets åtkomst behörighet för hanterad identitet till nyckel valvet

    Referera till det här [dokumentet](how-to-grant-access-other-resources.md) för att se hur du beviljar hanterad identitets åtkomst till nyckel valv. Observera också om du använder systemtilldelad hanterad identitet skapas den hanterade identiteten endast efter program distributionen.

## <a name="keyvault-secret-as-application-parameter"></a>Nyckel valvs hemlighet som program parameter
Låt oss säga att programmet behöver läsa databas lösen ordet för Server delen som lagras i nyckel valvet. Service Fabric KeyVaultReference-support gör det enkelt. I exemplet nedan `DBPassword` läses hemligheten från ett nyckel valv med hjälp av Service Fabric KeyVaultReference-stöd.

- Lägg till ett avsnitt i Settings. XML

    Definiera `DBPassword` parameter med typ `KeyVaultReference` och värde`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Referera till det nya avsnittet i ApplicationManifest. xml i`<ConfigPackagePolicies>`

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

    Service Fabric på tjänsteinstansieringen kommer att matcha KeyVaultReference-parametern med programmets hanterade identitet. Varje parameter som anges `<Section  Name=dbsecrets>` under är en fil under mappen som EnvironmentVariable SecretPath. Under C#-kodfragmentet visas hur du läser DBPassword i ditt program.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > För container scenariot kan du använda monterings punkt för att styra var `secrets` monteringen ska ske.

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
## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
- Hanterad identitet måste aktive ras för KeyVaultReference-stöd, din program aktivering Miss fungerar om KeyVaultReference används utan att aktivera hanterad identitet.

- Om du använder en tilldelad identitet skapas den bara när programmet har distribuerats och detta skapar ett cirkulärt beroende. När ditt program har distribuerats kan du ge systemet tilldelad identitet åtkomst behörighet till nyckel valvet. Du hittar systemet tilldelad identitet efter namn {Cluster}/{Application namn}/{ServiceName}

- Nyckel valvet måste finnas i samma prenumeration som Service Fabric-klustret. 

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om Azure-valv](https://docs.microsoft.com/azure/key-vault/)
