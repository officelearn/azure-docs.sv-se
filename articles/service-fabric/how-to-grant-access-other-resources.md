---
title: Bevilja en programåtkomst till andra Azure-resurser
description: I den här artikeln beskrivs hur du beviljar din hanterade identitetsaktiverade Service Fabric-programåtkomst till andra Azure-resurser som stöder Azure Active Directory-baserad autentisering.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614801"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Bevilja ett Service Fabric-programs hanterade identitetsåtkomst till Azure-resurser (förhandsversion)

Innan programmet kan använda sin hanterade identitet för att komma åt andra resurser måste behörigheter beviljas till den identiteten på den skyddade Azure-resursen som används. Att bevilja behörigheter är vanligtvis en hanteringsåtgärd på "kontrollplan" för Azure-tjänsten som äger den skyddade resursen som dirigeras via Azure Resource Manager, vilket kommer att tvinga fram alla tillämpliga rollbaserade åtkomstkontroller.

Den exakta sekvensen av steg beror sedan på vilken typ av Azure-resurs som används, samt vilket språk/klient som används för att bevilja behörigheter. Resten av artikeln förutsätter en användartilldelade identitet som tilldelats programmet och innehåller flera typiska exempel för din bekvämlighet, men det är inte på något sätt en uttömmande referens för det här avsnittet; Konsultera dokumentationen för respektive Azure-tjänster för uppdaterade instruktioner om hur du beviljar behörigheter.  

## <a name="granting-access-to-azure-storage"></a>Bevilja åtkomst till Azure Storage
Du kan använda Service Fabric-programmets hanterade identitet (användartilldelad i det här fallet) för att hämta data från en Azure storage-blob. Ge identiteten de behörigheter som krävs i Azure-portalen med följande steg:

1. Navigera till lagringskontot
2. Klicka på länken åtkomstkontroll (IAM) i vänstra panelen.
3. (valfritt) Kontrollera befintlig åtkomst: välj System- eller Användartilldelade hanterade identitet i kontrollen Sök. välja lämplig identitet från den efterföljande resultatlistan
4. Klicka på + Lägg till rolltilldelning överst på sidan om du vill lägga till en ny rolltilldelning för programmets identitet.
Välj Lagringsblobbdataläsare i listrutan under Roll.
5. Välj under Tilldela åtkomst till i `User assigned managed identity`nästa listruta.
6. Kontrollera sedan att rätt prenumeration är inställd i listrutan Prenumeration. Under Resursgrupper väljer du Alla resursgrupper.
7. Under Välj väljer du UAI som motsvarar Programmet Service Fabric och klickar sedan på Spara.

Stöd för systemtilldelade tjänst fabric-hanterade identiteter inkluderar inte integrering i Azure-portalen. Om ditt program använder en systemtilldelad identitet måste du först hitta klient-ID:n för programmets identitet `Azure AD user, group, or service principal` och sedan upprepa stegen ovan men välja alternativet i sökkontrollen.

## <a name="granting-access-to-azure-key-vault"></a>Bevilja åtkomst till Azure Key Vault
På samma sätt med åtkomst till lagring kan du utnyttja den hanterade identiteten för ett Service Fabric-program för att komma åt ett Azure-nyckelvalv. Stegen för att bevilja åtkomst i Azure-portalen liknar dem som anges ovan och upprepas inte här. Se bilden nedan för skillnader.

![Åtkomstprincip för Nyckelvalv](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Följande exempel illustrerar beviljandet av åtkomst till ett valv via en malldistribution. lägg till kodavsnitten nedan som en `resources` annan post under elementet i mallen. Exemplet visar åtkomstgivning för både användartilldelade respektive systemtilldelade identitetstyper - välj den tillämpliga.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
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
Och för systemtilldelade hanterade identiteter:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Mer information finns i [Arkiv - Uppdatera åtkomstprincip](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användartilldelad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)