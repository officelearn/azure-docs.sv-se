---
title: Azure Service Fabric – Konfigurera autentiseringsuppgifter för behållardatabaser
description: Konfigurera databasautentiseringsuppgifter för att hämta avbildningar från behållarregistret
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934981"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Konfigurera databasautentiseringsuppgifter för ditt program för att hämta behållaravbildningar

Konfigurera autentisering av `RepositoryCredentials` behållarregister genom att lägga till i `ContainerHostPolicies` avsnittet i programmanifestet. Lägg till kontot och lösenordet för behållarregistret (*myregistry.azurecr.io* i exemplet nedan), vilket gör att tjänsten kan hämta behållaravbildningen från databasen.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Vi rekommenderar att du krypterar databaslösenordet med hjälp av ett enciphermentcertifikat som distribueras till alla noder i klustret. När Service Fabric distribuerar tjänstpaketet till klustret används chiffreringscertifikatet för att avkryptera chiffertexten. Cmdleten Invoke-ServiceFabricEncryptText används för att skapa chiffertexten för lösenordet, som läggs till i filen ApplicationManifest.xml.
Se [Secret Management](service-fabric-application-secret-management.md) för mer information om certifikat och krypteringssantantika.

## <a name="configure-cluster-wide-credentials"></a>Konfigurera autentiseringsuppgifter för hela klustret

Med Service Fabric kan du konfigurera autentiseringsuppgifter för hela klustret som kan användas som standardlagringsplatsautentiseringsuppgifter av program.

Den här funktionen kan aktiveras `UseDefaultRepositoryCredentials` eller `ContainerHostPolicies` inaktiveras genom att lägga `true` `false` till attributet i ApplicationManifest.xml med ett eller ett värde.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric använder sedan standarddatabasautentiseringsuppgifterna som kan anges `Hosting` i ClusterManifest under avsnittet .  Om `UseDefaultRepositoryCredentials` `true`är , Service Fabric läser följande värden från ClusterManifest:

* DefaultContainerRepositoryAccountName (sträng)
* DefaultContainerRepositoryPassword (sträng)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (sträng)

Här är ett exempel på vad `Hosting` som kan läggas till i avsnittet i filen ClusterManifestTemplate.json. Avsnittet `Hosting` kan läggas till när klustret skapas eller senare i en konfigurationsuppgradering. Mer information finns i [Ändra klusterinställningar för Azure Service Fabric](service-fabric-cluster-fabric-settings.md) och Hantera [programhemligheter för Azure Service Fabric](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Använda token som registerautentiseringsuppgifter

Service Fabric stöder användning av token som autentiseringsuppgifter för att hämta avbildningar för dina behållare.  Den här funktionen utnyttjar den *hanterade identiteten* för den underliggande skalan för virtuella datorer som ska autentiseras till registret, vilket eliminerar behovet av att hantera användarautentiseringsuppgifter.  Mer information [finns i Hanterade identiteter för Azure-resurser.](../active-directory/managed-identities-azure-resources/overview.md)  Om du använder den här funktionen krävs följande:

1. Kontrollera att *systemtilldelade hanterade identitet* är aktiverat för den virtuella datorn.

    ![Azure-portal: Skapa identitetsalternativ för identitet för skalningsuppsättning för virtuella datorer](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Bevilja behörigheter till den virtuella datorn skala inställd på att dra / läsa av bilder från registret. Lägg till en *rolltilldelning* för din virtuella dator från bladet Åtkomstkontroll (IAM) i ditt Azure-behållarregister i Azure-portalen:

    ![Lägg till VM-huvudnamn i ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Ändra sedan ditt programmanifest. Lägg `ContainerHostPolicies` till attributet `‘UseTokenAuthenticationCredentials=”true”`i avsnittet .

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > Flaggan `UseDefaultRepositoryCredentials` som är `UseTokenAuthenticationCredentials` inställd på true while är true orsakar ett fel under distributionen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [autentisering av behållarregister](../container-registry/container-registry-authentication.md).
