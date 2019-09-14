---
title: Azure Service Fabric – bevilja en Service Fabric program åtkomst till andra Azure-resurser | Microsoft Docs
description: I den här artikeln förklaras hur du beviljar åtkomst till hanterade identiteter Service Fabric program till andra Azure-resurser som stöder Azure Active Directory-baserad autentisering.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 08/08/2019
ms.author: atsenthi
ms.openlocfilehash: f2621abcb2bac55ff123a11efa0ae9a082a1acbd
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968267"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Bevilja en Service Fabric programmets hanterade identitets åtkomst till Azure-resurser (förhands granskning)

Innan programmet kan använda den hanterade identiteten för att komma åt andra resurser måste behörigheter beviljas till den identiteten på den skyddade Azure-resursen som används. Att bevilja behörigheter är vanligt vis en hanterings åtgärd på "kontroll planet" i Azure-tjänsten som äger den skyddade resursen som dirigerats via Azure Resource Manager, vilket tvingar fram en tillgänglig rollbaserad åtkomst kontroll.

De exakta stegen varierar beroende på vilken typ av Azure-resurs som används, samt vilket språk/vilken klient som ska användas för att bevilja behörigheter. Resten av artikeln förutsätter en tilldelad identitet som tilldelats programmet och innehåller flera typiska exempel för din bekvämlighet, men det är inte på något sätt en fullständig hänvisning till det här ämnet. Läs dokumentationen för respektive Azure-tjänster för uppdaterade instruktioner om hur du beviljar behörigheter.  

## <a name="granting-access-to-azure-storage"></a>Bevilja åtkomst till Azure Storage
Du kan använda det Service Fabric programmets hanterade identitet (användar tilldelning i det här fallet) för att hämta data från en Azure Storage-blob. Ge identiteten behörighet som krävs i Azure Portal med följande steg:

1. Navigera till lagrings kontot
2. Klicka på länken åtkomst kontroll (IAM) i den vänstra panelen.
3. valfritt Kontrol lera befintlig åtkomst: Välj system-eller användardefinierad hanterad identitet i find-kontrollen; Välj lämplig identitet i resultat listan
4. Klicka på + Lägg till roll tilldelning ovanpå sidan för att lägga till en ny roll tilldelning för programmets identitet.
Under roll i list rutan väljer du Storage BLOB data Reader.
5. I nästa listruta under tilldela åtkomst till väljer `User assigned managed identity`du.
6. Se till att rätt prenumeration visas i list rutan prenumeration och ange sedan resurs grupp för alla resurs grupper.
7. Under Välj väljer du den UAI som motsvarar Service Fabric programmet och klickar sedan på Spara.

Stöd för systemtilldelade Service Fabric hanterade identiteter omfattar inte integrering i Azure Portal; om programmet använder en tilldelad identitet måste du hitta först klient-ID: t för programmets identitet och sedan upprepa stegen ovan, men välja `Azure AD user, group, or service principal` alternativet i kontrollen Sök.

## <a name="granting-access-to-azure-key-vault"></a>Bevilja åtkomst till Azure Key Vault
På samma sätt som med lagring kan du använda den hanterade identiteten för ett Service Fabric program för att få åtkomst till ett Azure Key Vault. Stegen för att bevilja åtkomst i Azure Portal liknar de som anges ovan och upprepas inte här. Se bilden nedan om du vill ha skillnader.

![Key Vault åtkomst princip](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

I följande exempel visas hur du beviljar åtkomst till ett valv via en mall distribution. Lägg till kodfragmentet nedan som en annan post `resources` under mallens element.

```json
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```

Mer information finns i [valv-uppdatera åtkomst princip](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användardefinierad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)