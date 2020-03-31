---
title: Distribuera en Service Fabric-app med systemtilldelad MI
description: Den här artikeln visar hur du tilldelar en systemtilldelad hanterad identitet till ett Azure Service Fabric-program
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: d5a14722363d642957904f9c7c699d3cf1d66c0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614833"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Distribuera Service Fabric-program med systemtilldelad hanterad identitet (förhandsversion)

För att komma åt funktionen hanterad identitet för Azure Service Fabric-program måste du först aktivera den hanterade identitetstokentjänsten i klustret. Den här tjänsten ansvarar för autentisering av Service Fabric-program med hjälp av deras hanterade identiteter och för att erhålla åtkomsttoken för deras räkning. När tjänsten är aktiverad kan du se den i Service Fabric Explorer under avsnittet **System** i den vänstra rutan och köras under namnet **fabric:/System/ManagedIdentityTokenService** bredvid andra systemtjänster.

> [!NOTE] 
> Distribution av Service Fabric-program med hanterade `"2019-06-01-preview"`identiteter stöds från och med API-version . Du kan också använda samma API-version för programtyp, programtypsversion och tjänstresurser. Minsta service fabric-körning som stöds är 6,5 CU2. I additoin bör bygg- och paketmiljön också ha SF .Net SDK på CU2 eller högre

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

### <a name="application-template"></a>Programmall

Om du vill aktivera program med en **identity** systemtilldelad hanterad identitet lägger du till identitetsegenskapen i programresursen, med typ **systemAssigned** som visas i exemplet nedan:

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
Den här egenskapen deklarerar (till Azure Resource Manager och resursleverantörerna för hanterad identitet`system assigned`och tjänstinfrastruktur, respektive att den här resursen ska ha en implicit ( ) hanterad identitet.

### <a name="application-and-service-package"></a>Program- och servicepaket

1. Uppdatera programmanifestet för att lägga till ett **ManagedIdentity-element** i avsnittet **Huvudnamn,** som innehåller en enda post enligt nedan:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Detta mappar identiteten som tilldelats programmet som en resurs till ett eget namn, för ytterligare tilldelning till de tjänster som ingår i programmet. 

2. I avsnittet **ServiceManifestImport** som motsvarar den tjänst som tilldelas den hanterade identiteten lägger du till ett **IdentityBindingPolicy-element** enligt nedan:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Det här elementet tilldelar programmets identitet till tjänsten. Utan den här tilldelningen kommer tjänsten inte att kunna komma åt programmets identitet. I kodavsnittet ovan `SystemAssigned` mappas identiteten (som är ett reserverat nyckelord) till `WebAdmin`tjänstens definition under det egna namnet .

3. Uppdatera tjänstmanifestet om du vill lägga till ett **ManagedIdentity-element** i `IdentityBindingPolicy` avsnittet **Resurser** med namnet som matchar värdet för `ServiceIdentityRef` inställningen från definitionen i programmanifestet:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Detta är motsvarande mappning av en identitet till en tjänst som beskrivs ovan, men ur tjänstdefinitionen. Identiteten refereras här av dess`WebAdmin`eget namn ( ), som deklarerats i ansökan manifestet.

## <a name="next-steps"></a>Efterföljande moment
* Granska [stöd för hanterad identitet](./concepts-managed-identity.md) i Azure Service Fabric
* [Distribuera en ny](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric-kluster med stöd för hanterad identitet 
* [Aktivera hanterad identitet](./configure-existing-cluster-enable-managed-identity-token-service.md) i ett befintligt Azure Service Fabric-kluster
* Utnyttja ett Service Fabric-programs [hanterade identitet från källkoden](./how-to-managed-identity-service-fabric-app-code.md)
* [Distribuera ett Azure Service Fabric-program med en användartilldelad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Bevilja en Azure Service Fabric-programåtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)
