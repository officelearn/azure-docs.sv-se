---
title: Konfigurera stöd för hanterad identitet för ett nytt Service Fabric-kluster
description: Så här aktiverar du stöd för hanterade identiteter i ett nytt Azure Service Fabric-kluster
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 0e35d2192fdcdb294b349105f3f0158564cec86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76930468"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster-preview"></a>Konfigurera stöd för hanterad identitet för ett nytt Service Fabric-kluster (förhandsversion)

Om du vill använda [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i tjänstinfrastrukturprogrammen aktiverar du först *tjänsten Hanterad identitetstoken i* klustret. Den här tjänsten ansvarar för autentisering av Service Fabric-program med hjälp av deras hanterade identiteter och för att erhålla åtkomsttoken för deras räkning. När tjänsten är aktiverad kan du se den i Service Fabric Explorer under avsnittet **System** i den vänstra rutan och köras under namnet **fabric:/System/ManagedIdentityTokenService** bredvid andra systemtjänster.

> [!NOTE]
> Service Fabric runtime version 6.5.658.9590 eller högre krävs för att aktivera **tjänsten Hanterad identitetstoken**.  

## <a name="enable-the-managed-identity-token-service"></a>Aktivera tjänsten Hanterad identitetstoken

Om du vill aktivera tjänsten Hanterad identitetstoken vid skapande av kluster lägger du till följande kodavsnitt i klustrets Azure Resource Manager-mall:

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

## <a name="errors"></a>Fel

Om distributionen misslyckas med det här meddelandet betyder det att klustret inte finns på den service fabric-version som krävs (den minsta körningen som stöds är 6,5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Relaterade artiklar

* Granska [stöd för hanterad identitet](./concepts-managed-identity.md) i Azure Service Fabric

* [Aktivera stöd för hanterad identitet i ett befintligt Azure Service Fabric-kluster](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Nästa steg

* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användartilldelad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Utnyttja den hanterade identiteten för ett Service Fabric-program från servicekod](./how-to-managed-identity-service-fabric-app-code.md)
* [Bevilja en Azure Service Fabric-programåtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)