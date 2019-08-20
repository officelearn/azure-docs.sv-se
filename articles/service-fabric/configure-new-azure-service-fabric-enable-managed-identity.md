---
title: Azure Service Fabric – distribuera ett nytt Azure Service Fabric-kluster med stöd för hanterad identitet | Microsoft Docs
description: Den här artikeln visar hur du skapar ett nytt Service Fabric kluster med hanterad identitet aktive rad
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 80fb06c96f727d2b78faf4f100d59341aeaeff42
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624965"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support-preview"></a>Skapa ett nytt Azure Service Fabric-kluster med stöd för hanterad identitet (förhands granskning)

För att få åtkomst till funktionen för hanterad identitet för Azure Service Fabric-program måste du först aktivera hanterad identitetsprovider i klustret. Den här tjänsten ansvarar för autentiseringen av Service Fabric program med hjälp av deras hanterade identiteter och för att få åtkomst till token för deras räkning. När tjänsten är aktive rad kan du se den i Service Fabric Explorer under **system** avsnittet i det vänstra fönstret, som körs under namnet **Fabric:/system/ManagedIdentityTokenService** bredvid andra system tjänster.

> [!NOTE]
> Service Fabric runtime-version 6.5.658.9590 eller högre krävs för att aktivera den **hanterade Identity token-tjänsten**.  

## <a name="enable-the-managed-identity-token-service"></a>Aktivera hanterad identitets-token 
Om du vill aktivera tjänsten för hanterad identitets-token när klustret skapas kan du använda följande kod avsnitt i en Azure Resource Manager mall:

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

Om distributionen Miss lyckas med det här meddelandet innebär det att klustret inte är på den nödvändiga Service Fabric versionen (den lägsta körning som stöds är 6,5 CU2):



```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användardefinierad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Utnyttja den hanterade identiteten för ett Service Fabric program från service code](./how-to-managed-identity-service-fabric-app-code.md)
* [Bevilja ett Azure Service Fabric program åtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)

## <a name="related-articles"></a>Relaterade artiklar
* Granska [stöd för hanterad identitet](./concepts-managed-identity.md) i Azure Service Fabric

* [Aktivera stöd för hanterad identitet i ett befintligt Azure Service Fabric-kluster](./configure-existing-cluster-enable-managed-identity-token-service.md)
