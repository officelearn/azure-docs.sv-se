---
title: Azure Service Fabric – distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet | Microsoft Docs
description: Den här artikeln visar hur du tilldelar en systemtilldelad hanterad identitet till ett Azure Service Fabric-program
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: cf971d71c2566d91bc5a2490d47521725c62b17d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973413"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Distribuera Service Fabric program med systemtilldelad hanterad identitet (förhands granskning)

För att få åtkomst till funktionen för hanterad identitet för Azure Service Fabric-program måste du först aktivera hanterad identitetsprovider i klustret. Den här tjänsten ansvarar för autentiseringen av Service Fabric program med hjälp av deras hanterade identiteter och för att få åtkomst till token för deras räkning. När tjänsten är aktive rad kan du se den i Service Fabric Explorer under **system** avsnittet i det vänstra fönstret, som körs under namnet **Fabric:/system/ManagedIdentityTokenService** bredvid andra system tjänster.

> [!NOTE] 
> Distribution av Service Fabric program med hanterade identiteter stöds från och med API-versionen `"2019-06-01-preview"`. Du kan också använda samma API-version för program typ, version av program typ och tjänst resurser. Den lägsta Service Fabric runtime som stöds är 6,5 CU2. I additoin bör Bygg-/paket miljön också ha SF .NET SDK på CU2 eller högre

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

### <a name="application-template"></a>Programmall

Om du vill aktivera program med en systemtilldelad hanterad identitet lägger du till egenskapen **identitet** i program resursen med typen **systemAssigned** som visas i exemplet nedan:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Den här egenskapen deklarerar (till Azure Resource Manager och de hanterade identitets-och Service Fabric resurs leverantörerna, som denna resurs måste ha en implicit (`system assigned`) hanterad identitet.

### <a name="application-and-service-package"></a>Program-och tjänst paket

1. Uppdatera applikations manifestet för att lägga till ett **ManagedIdentity** -element i avsnittet **säkerhets objekt** , som innehåller en enda post som visas nedan:

    **ApplicationManifest. XML**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Detta mappar den identitet som är tilldelad till programmet som en resurs till ett eget namn, för ytterligare tilldelning till de tjänster som omfattar programmet. 

2. I avsnittet **service manifest import** som motsvarar den tjänst som tilldelas den hanterade identiteten lägger du till ett **IdentityBindingPolicy** -element enligt nedan:

    **ApplicationManifest. XML**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Det här elementet tilldelar identiteten för programmet till tjänsten. utan den här tilldelningen kommer tjänsten inte att kunna komma åt appens identitet. I kodfragmentet ovan mappas `SystemAssigned`-identiteten (som är ett reserverat nyckelord) till tjänstens definition under det egna namnet `WebAdmin`.

3. Uppdatera tjänst manifestet för att lägga till ett **ManagedIdentity** -element i avsnittet **Resources** med namnet som matchar värdet för inställningen `ServiceIdentityRef` från `IdentityBindingPolicy`-definitionen i applikations manifestet:

    **ServiceManifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Detta är den likvärdiga mappningen av en identitet till en tjänst enligt beskrivningen ovan, men ur tjänst definitionens perspektiv. Identiteten hänvisas till här med ett eget namn (`WebAdmin`), enligt vad som har deklarerats i applikations manifestet.

## <a name="next-steps"></a>Nästa steg
* Granska [stöd för hanterad identitet](./concepts-managed-identity.md) i Azure Service Fabric
* [Distribuera en ny](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric-kluster med stöd för hanterad identitet 
* [Aktivera hanterad identitet](./configure-existing-cluster-enable-managed-identity-token-service.md) i ett befintligt Azure Service Fabric-kluster
* Utnyttja ett Service Fabric programmets [hanterade identitet från käll koden](./how-to-managed-identity-service-fabric-app-code.md)
* [Distribuera ett Azure Service Fabric-program med en användardefinierad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Bevilja ett Azure Service Fabric program åtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)
