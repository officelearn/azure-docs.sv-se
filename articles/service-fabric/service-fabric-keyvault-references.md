---
title: Azure Service Fabric - Använda KeyVault-referenser för service fabric-program
description: I den här artikeln beskrivs hur du använder keyvaultreference-stöd för tjänstuppgörelser för programhemligheter.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545491"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>KeyVaultReference-stöd för Service Fabric-program (förhandsversion)

En vanlig utmaning när du skapar molnprogram är hur du på ett säkert sätt lagrar hemligheter som krävs av ditt program. Du kanske till exempel vill lagra autentiseringsuppgifterna för behållardatabasen i keyvault och referera till dem i programmanifestet. Service Fabric KeyVaultReference använder Service Fabric Managed Identity och gör det enkelt att referera till nyckeldatahemligheter. Resten av den här artikeln beskriver hur du använder Service Fabric KeyVaultReference och innehåller några typiska användning.

## <a name="prerequisites"></a>Krav

- Hanterad identitet för ansökan (MIT)
    
    Support för Service Fabric KeyVaultReference använder programmets hanterade identitet och därför bör program som planerar att använda KeyVaultReferences använda hanterad identitet. Följ det här [dokumentet](concepts-managed-identity.md) om du vill aktivera hanterad identitet för ditt program.

- Central Secrets Store (CSS).

    Central Secrets Store (CSS) är Service Fabric krypterade lokala hemligheter cache. CSS är en lokal hemlig lagringscache som behåller känsliga data, till exempel ett lösenord, token och nycklar, krypterade i minnet. KeyVaultReference, när den har hämtats, cachelagras i CSS.

    Lägg till nedanstående i `fabricSettings` klusterkonfigurationen under för att aktivera alla nödvändiga funktioner för KeyVaultReference-stöd.

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
    > Vi rekommenderar att du använder ett separat krypteringscertifikat för CSS. Du kan lägga till den under avsnittet "CentralSecretService".
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
För att ändringarna ska börja gälla måste du också ändra uppgraderingsprincipen för att ange en kraftfull omstart av Service Fabric-körningen på varje nod när uppgraderingen fortskrider genom klustret. Den här omstarten säkerställer att den nyligen aktiverade systemtjänsten startas och körs på varje nod. I kodavsnittet nedan är forceRestart den väsentliga inställningen. använda dina befintliga värden för resten av inställningarna.
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
- Bevilja programmets behörighet för hanterad identitetsåtkomst till keyvault

    Referera till det här [dokumentet](how-to-grant-access-other-resources.md) för att se hur du beviljar hanterad identitetsåtkomst till keyvault. Observera också att om du använder systemtilldelade hanterade identitet, skapas den hanterade identiteten först efter programdistribution.

## <a name="keyvault-secret-as-application-parameter"></a>Keyvault hemlighet som applikationsparameter
Låt oss säga att programmet måste läsa backend databas lösenord lagras i keyvault, Service Fabric KeyVaultReference stöd gör det enkelt. Nedanstående `DBPassword` exempel lyder hemlighet från keyvault med hjälp av Stöd för Service Fabric KeyVaultReference.

- Lägga till ett avsnitt i settings.xml

    Definiera `DBPassword` parameter `KeyVaultReference` med typ och värde`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Referera till det nya avsnittet i ApplicationManifest.xml i`<ConfigPackagePolicies>`

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

    Service Fabric på tjänsten instansiering kommer att lösa KeyVaultReference Parameter med hjälp av programmets hanterade identitet. Varje parameter som `<Section  Name=dbsecrets>` anges under kommer att vara en fil under mappen som pekar på av EnvironmentVariable SecretPath. Nedan C # kod utdrag visa hur man läser DBPassword i ditt program.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > För behållarscenariot kan du använda MountPoint för att styra var ska `secrets` monteras.

## <a name="keyvault-secret-as-environment-variable"></a>Keyvault hemlighet som miljö variabel

Service Fabric-miljövariabler stöder nu KeyVaultReference-typ, nedan visar exemplet hur du binder en miljövariabel till en hemlighet som lagras i KeyVault.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Keyvault hemlighet som container databas lösenord
KeyVaultReference är en typ som stöds för container RepositoryCredentials, nedan visas hur du använder en keyvault-referens som lösenord för behållardatabasen.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
- Hanterad identitet måste aktiveras för KeyVaultReference-stöd, programaktiveringen misslyckas om KeyVaultReference används utan att aktivera hanterad identitet.

- Om du använder systemtilldelade identitet skapas den först när programmet har distribuerats och detta skapar ett cirkulärt beroende. När ditt program har distribuerats kan du bevilja den systemtilldelade identitetsbehörigheten till keyvault. Du hittar den systemtilldelade identiteten efter namn {cluster}/{application name}/{servicename}

- Keyvault måste finnas i samma prenumeration som ditt serviceinfrastrukturkluster. 

## <a name="next-steps"></a>Nästa steg

* [Azure KeyVault-dokumentation](https://docs.microsoft.com/azure/key-vault/)
