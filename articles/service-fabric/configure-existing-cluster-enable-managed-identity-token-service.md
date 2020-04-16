---
title: Konfigurera stöd för hanterad identitet i ett befintligt Service Fabric-kluster
description: Så här aktiverar du stöd för hanterade identiteter i ett befintligt Azure Service Fabric-kluster
ms.topic: article
ms.date: 03/11/2019
ms.custom: sfrev
ms.openlocfilehash: 73c890e960f26b8e0e3fa924d9ff6b7a4cd4a4dc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415679"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Konfigurera stöd för hanterad identitet i ett befintligt Service Fabric-kluster

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

För att ändringarna ska börja gälla måste du också ändra uppgraderingsprincipen för att ange en kraftfull omstart av Service Fabric-körningen på varje nod när uppgraderingen fortskrider genom klustret. Den här omstarten säkerställer att den nyligen aktiverade systemtjänsten startas och körs på varje nod. I kodavsnittet nedan `forceRestart` är den viktigaste inställningen för att aktivera omstart. För de återstående parametrarna använder du värden som beskrivs nedan eller använder befintliga anpassade värden som redan har angetts för klusterresursen. Anpassade inställningar för fabric upgrade policy ("upgradeDescription") kan visas från Azure Portal genom att välja alternativet Fabric Upgrades på Service Fabric-resursen eller resources.azure.com. Standardalternativ för uppgraderingsprincipen (upgradeDescription) kan inte visas från powershell eller resources.azure.com. Mer information finns i [ClusterUpgradePolicy.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet)  

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
