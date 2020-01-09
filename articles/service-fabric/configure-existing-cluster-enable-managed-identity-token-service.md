---
title: Azure Service Fabric – konfigurera ett befintligt Azure Service Fabric-kluster för att aktivera stöd för hanterade identiteter
description: Den här artikeln visar hur du konfigurerar ett befintligt Azure Service Fabric-kluster för att aktivera stöd för hanterade identiteter
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 13b8b38a206b0dae0877263a5cda56a134d4788d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351613"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support-preview"></a>Konfigurera ett befintligt Azure Service Fabric-kluster för att aktivera hanterad identitets support (för hands version)
För att få åtkomst till funktionen för hanterad identitet för Azure Service Fabric-program måste du först aktivera **hanterad identitetsprovider** i klustret. Den här tjänsten ansvarar för autentiseringen av Service Fabric program med hjälp av deras hanterade identiteter och för att få åtkomst till token för deras räkning. När tjänsten är aktive rad kan du se den i Service Fabric Explorer under **system** avsnittet i det vänstra fönstret, som körs under namnet **Fabric:/system/ManagedIdentityTokenService**.

> [!NOTE]
> Service Fabric runtime-version 6.5.658.9590 eller högre krävs för att aktivera den **hanterade Identity token-tjänsten**.  
> 
> Du kan hitta Service Fabric versionen av ett kluster från Azure Portal genom att öppna kluster resursen och kontrol lera egenskapen **Service Fabric version** i avsnittet **Essentials** .
> 
> Om klustret är i **manuellt** uppgraderings läge måste du först uppgradera det till 6.5.658.9590 eller senare.


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>Aktivera hanterad identitets-token i ett befintligt kluster
Om du vill aktivera tjänsten hanterad identitets-token i ett befintligt kluster måste du initiera en kluster uppgradering som anger två ändringar: aktiverar tjänsten hanterad identitets-token och begär en omstart av varje nod. Det gör du genom att lägga till följande två kodfragment i Azure Resource Manager-mallen:

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

För att ändringarna ska träda i kraft måste du också ändra uppgraderings principen för att ange en tvingande omstart av Service Fabric runtime på varje nod när uppgraderingen fortskrider genom klustret. Den här omstarten säkerställer att den nyligen aktiverade system tjänsten startas och körs på varje nod. I kodfragmentet nedan är `forceRestart` den viktigaste inställningen. Använd dina befintliga värden för resten av inställningarna.  

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
> När uppgraderingen är klar ska du inte glömma att återställa `forceRestart`-inställningen för att minimera effekten av efterföljande uppgraderingar. 

## <a name="errors-and-troubleshooting"></a>Fel och fel sökning

Om distributionen Miss lyckas med följande meddelande innebär det att klustret inte körs på en hög nog Service Fabric version:

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