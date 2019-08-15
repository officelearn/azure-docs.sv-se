---
title: Azure Service Fabric – distribuera program med en användardefinierad hanterad identitet | Microsoft Docs
description: Den här artikeln visar hur du distribuerar Service Fabric program med en användardefinierad hanterad identitet
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: b99dbe3fd03b8854d7c1f54d17d5ced1f2534132
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963881"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Distribuera Service Fabric program med en användardefinierad hanterad identitet

Om du vill distribuera ett Service Fabric program med hanterad identitet måste programmet distribueras via Azure Resource Manager, vanligt vis med en Azure Resource Manager mall. Mer information om hur du distribuerar Service Fabric program via Azure Resource Manager finns i [hantera program och tjänster som Azure Resource Manager resurser](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Program som inte distribueras som en Azure-resurs **kan inte** ha hanterade identiteter. 
>
> Service Fabric program distribution med hanterad identitet stöds med API- `"2019-06-01-preview"`versionen. Du kan också använda samma API-version för program typ, version av program typ och tjänst resurser.
>

## <a name="user-assigned-identity"></a>Användare tilldelad identitet

Om du vill aktivera program med en användardefinierad identitet måste du först lägga till egenskapen **identitet** i program resursen med typen **userAssigned** och de referenser som tilldelats av användaren. Lägg sedan till ett **managedIdentities** -avsnitt i avsnittet **Egenskaper** för **program** resursen som innehåller en lista med ett eget namn för principalId-mappningen för var och en av de användare som tilldelats identiteter.

### <a name="application-template"></a>Programmall

Om du vill aktivera program med en tilldelad identitet måste du först lägga till en **identitets** egenskap till program resursen med typen **userAssigned** och den refererade användaren tilldelade identiteter och sedan lägga till ett **managedIdentities** -objekt inuti  **avsnittet Egenskaper** som innehåller en lista med ett eget namn för principalId-mappning för varje användare som tilldelats identiteter.

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

I exemplet ovan används resurs namnet för den användare som tilldelats identiteten som det egna namnet på den hanterade identiteten för programmet. Följande exempel förutsätter att det faktiska egna namnet är "AdminUser".

### <a name="application-package"></a>Programpaket

1. Lägg till en `<ManagedIdentity>` tagg i program `managedIdentities` manifestet under avsnittet **huvud namn** för varje identitet som definieras i avsnittet i Azure Resource Manager mall. Attributet måste matcha den `name` egenskap som definierats i `managedIdentities` avsnittet. `Name`

    **ApplicationManifest. XML**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. I avsnittet **service manifest import** lägger du till en **IdentityBindingPolicy** för tjänsten som använder den hanterade identiteten. Den här principen mappar `AdminUser` identiteten till ett tjänstspecifikt identitets namn som måste läggas till i tjänst manifestet senare.

    **ApplicationManifest. XML**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Uppdatera tjänst manifestet för att lägga till en **ManagedIdentity** i avsnittet **resurser** med `ServiceIdentityRef` namnet som matchar i `IdentityBindingPolicy` program manifestet:

    **ServiceManifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Nästa steg

* [Använda hanterad identitet i Service Fabric program kod](how-to-managed-identity-service-fabric-app-code.md)
* [Så här beviljar du Service Fabric program åtkomst till andra Azure-resurser](how-to-grant-access-other-resources.md)