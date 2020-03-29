---
title: Konfigurera stöd för hanterad identitet i ett befintligt Service Fabric-kluster
description: Så här aktiverar du stöd för hanterade identiteter i ett befintligt Azure Service Fabric-kluster
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934952"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Konfigurera stöd för hanterad identitet i ett befintligt Service Fabric-kluster (förhandsversion)

Om du vill använda [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i tjänstinfrastrukturprogrammen aktiverar du först *tjänsten Hanterad identitetstoken i* klustret. Den här tjänsten ansvarar för autentisering av Service Fabric-program med hjälp av deras hanterade identiteter och för att erhålla åtkomsttoken för deras räkning. När tjänsten är aktiverad kan du se den i Service Fabric Explorer under avsnittet **System** i den vänstra rutan och köras under namnet **fabric:/System/ManagedIdentityTokenService**.

> [!NOTE]
> Service Fabric runtime version 6.5.658.9590 eller högre krävs för att aktivera **tjänsten Hanterad identitetstoken**.  
>
> Du hittar Service Fabric-versionen av ett kluster från Azure-portalen genom att öppna klusterresursen och kontrollera egenskapen **Service Fabric-version** i avsnittet **Essentials.**
>
> Om klustret är i **manuellt** uppgraderingsläge måste du först uppgradera det till 6.5.658.9590 eller senare.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Aktivera *tjänst för hanterad identitetstoken* i ett befintligt kluster

Om du vill aktivera tjänsten Hanterad identitetstoken i ett befintligt kluster måste du initiera en klusteruppgradering som anger två ändringar: (1) Aktivera den hanterade identitetstokentjänsten och (2) begära en omstart av varje nod. Lägg först till följande kodavsnitt i klustrets Azure Resource Manager-mall:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

För att ändringarna ska börja gälla måste du också ändra uppgraderingsprincipen för att ange en kraftfull omstart av Service Fabric-körningen på varje nod när uppgraderingen fortskrider genom klustret. Den här omstarten säkerställer att den nyligen aktiverade systemtjänsten startas och körs på varje nod. I kodavsnittet nedan, `forceRestart` är den väsentliga inställningen; använda dina befintliga värden för resten av inställningarna.  

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

> [!NOTE]
> När uppgraderingen har slutförts, glöm inte `forceRestart` att återställa inställningen, för att minimera effekten av efterföljande uppgraderingar. 

## <a name="errors-and-troubleshooting"></a>Fel och felsökning

Om distributionen misslyckas med följande meddelande betyder det att klustret inte körs på en tillräckligt hög Service Fabric-version:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användartilldelad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Utnyttja den hanterade identiteten för ett Service Fabric-program från servicekod](./how-to-managed-identity-service-fabric-app-code.md)
* [Bevilja en Azure Service Fabric-programåtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)