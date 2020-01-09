---
title: Azure Service Fabric – konfigurera autentiseringsuppgifter för container-lagringsplatsen
description: Konfigurera autentiseringsuppgifter för lagrings plats för att ladda ned bilder från container Registry
author: arya
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: arya
ms.openlocfilehash: 25fe3c69b19d397137d1e1802e941e0433a1b160
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351662"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Konfigurera autentiseringsuppgifter för databasen för ditt program för att ladda ned behållar avbildningar

Konfigurera autentisering av containerregister genom att lägga till `RepositoryCredentials` i `ContainerHostPolicies` filen ApplicationManifest.xml. Lägg till kontot och lösenordet för containerregistret myregistry.azurecr.io, så att tjänsten kan ladda ned containeravbildningen från centrallagret.

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

Vi rekommenderar att du krypterar lösen ordet för databasen med hjälp av ett krypterings certifikat som distribueras till alla noder i klustret. När Service Fabric distribuerar tjänstpaketet till klustret används chiffreringscertifikatet för att avkryptera chiffertexten. Cmdleten Invoke-ServiceFabricEncryptText används för att skapa chiffertexten för lösenordet, som läggs till i filen ApplicationManifest.xml.
Se [Secret Management](service-fabric-application-secret-management.md) för mer information om certifikat och semantiskt kryptering.

## <a name="configure-cluster-wide-credentials"></a>Konfigurera autentiseringsuppgifter för hela klustret

Med Service Fabric kan du konfigurera autentiseringsuppgifter för hela klustret som kan användas som standard inloggnings uppgifter för alla program.

Den här funktionen kan aktive ras eller inaktive ras genom att attributet `UseDefaultRepositoryCredentials` läggs till i `ContainerHostPolicies` i ApplicationManifest. xml med ett `true`-eller `false` värde.

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

Service Fabric använder sedan standard-autentiseringsuppgifterna för lagring som kan anges i ClusterManifest i avsnittet `Hosting`.  Om `UseDefaultRepositoryCredentials` är `true`läser Service Fabric följande värden från ClusterManifest:

* DefaultContainerRepositoryAccountName (sträng)
* DefaultContainerRepositoryPassword (sträng)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (sträng)---som stöds från och med 6,4-körningen

Här är ett exempel på vad som kan läggas till i `Hosting` avsnittet i filen ClusterManifestTemplate. JSON. Avsnittet `Hosting` kan läggas till vid skapande av kluster eller senare i en konfigurations uppgradering. Mer information finns i [Ändra kluster inställningar för azure Service Fabric](service-fabric-cluster-fabric-settings.md) och [Hantera Azure Service Fabric program hemligheter](service-fabric-application-secret-management.md)

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

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>Dra nytta av den hanterade identiteten för den virtuella datorns skalnings uppsättning med hjälp av hanterad identitets tjänst (MSI)

Service Fabric stöder token som autentiseringsuppgifter för att ladda ned bilder för dina behållare.  Den här funktionen utnyttjar den hanterade identiteten för den underliggande skalnings uppsättningen för virtuella datorer för att autentisera till registret, vilket eliminerar behovet av att hantera användarautentiseringsuppgifter.  Mer information om MSI finns i [hanterad tjänstidentitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) .  Följande steg krävs för att använda den här funktionen:

1.  Se till att systemtilldelad hanterad identitet har Aktiver ATS för den virtuella datorn (se skärm bild nedan)

    ![Skapa identitet för skalnings uppsättning för virtuell dator](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  Därefter beviljar du behörigheter till den virtuella datorn (SS) för att hämta/läsa avbildningar från registret.  Gå till Access Control (IAM) för din ACR via Azure-bladet och ge din virtuella dator (SS) rätt behörigheter enligt nedan:

    ![Lägg till VM-huvudobjekt i ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  När ovanstående steg har slutförts ändrar du filen applicationmanifest. xml.  Hitta taggen som heter "ContainerHostPolicies" och Lägg till attributet `‘UseTokenAuthenticationCredentials=”true”`.

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
    > Flaggan `UseDefaultRepositoryCredentials` inställd på True när `UseTokenAuthenticationCredentials` är true orsakar ett fel under distributionen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [behållar registrets autentisering](/azure/container-registry/container-registry-authentication).
