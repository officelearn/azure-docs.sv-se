---
title: Distribuera app med en användartilldelad hanterad identitet
description: Den här artikeln visar hur du distribuerar Service Fabric-program med en användartilldelad hanterad identitet
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: a5eeaf0d6420fa36c0a78f7553ddfd82197d8ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610343"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>Distribuera Service Fabric-program med en användartilldelad hanterad identitet (förhandsgranskning)

Om du vill distribuera ett Service Fabric-program med hanterad identitet måste programmet distribueras via Azure Resource Manager, vanligtvis med en Azure Resource Manager-mall. Mer information om hur du distribuerar Service Fabric-program via Azure Resource Manager finns i [Hantera program och tjänster som Azure Resource Manager-resurser](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Program som inte distribueras som en Azure-resurs **kan inte** ha hanterade identiteter. 
>
> Tjänst Fabric-programdistribution med hanterad `"2019-06-01-preview"`identitet stöds med API-version . Du kan också använda samma API-version för programtyp, programtypsversion och tjänstresurser.
>

## <a name="user-assigned-identity"></a>Användartilldelad identitet

Om du vill aktivera program med användartilldelad identitet lägger du först till identitetsegenskapen till programresursen med typen **userAssigned** och de refererade användartilldelade identiteterna. **identity** Lägg sedan till ett **avsnitt om hanterade enheter** i **egenskapsavsnittet** för **programresursen** som innehåller en lista med eget namn till principalId-mappning för var och en av de användartilldelade identiteterna. Mer information om Användartilldelade identiteter finns i [Skapa, lista eller ta bort en användartilldelad hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell).

### <a name="application-template"></a>Programmall

Om du vill aktivera program med **identity** användartilldelad identitet lägger du först till identitetsegenskap till programresursen med typen **userAssigned** och de refererade användartilldelade identiteterna och lägger sedan till ett **managedId-entitetsobjekt** i **egenskapsavsnittet** som innehåller en lista med eget namn till principalId-mappning för var och en av de tilldelade användaridentiteterna.

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

I exemplet ovan används resursnamnet för den tilldelade användaren som är det egna namnet på den hanterade identiteten för programmet. Följande exempel förutsätter att det faktiska egna namnet är "AdminUser".

### <a name="application-package"></a>Programpaket

1. För varje identitet `managedIdentities` som definieras i avsnittet i `<ManagedIdentity>` Azure Resource Manager-mallen lägger du till en tagg i programmanifestet under avsnittet **Huvudnamn.** Attributet `Name` måste matcha `name` egenskapen `managedIdentities` som definierats i avsnittet.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Lägg till en **IdentityBindingPolicy** för tjänsten som använder den hanterade identiteten i avsnittet **ServiceManifestImport.** Den här `AdminUser` principen mappar identiteten till ett tjänstspecifikt identitetsnamn som måste läggas till i tjänstmanifestet senare.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Uppdatera tjänstmanifestet för att lägga till en **Hanterad** identifiering `IdentityBindingPolicy` i avsnittet **Resurser** med namnet som matchar `ServiceIdentityRef` i programmanifestet:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Nästa steg

* [Så här använder du programkod för hanterad identitet i service fabric](how-to-managed-identity-service-fabric-app-code.md)
* [Så här beviljar du tjänstinfrastruktur-programåtkomst till andra Azure-resurser](how-to-grant-access-other-resources.md)
