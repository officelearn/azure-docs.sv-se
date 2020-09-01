---
title: Bevilja behörighet till program för att få åtkomst till ett Azure Key Vault med hjälp av Azure RBAC | Microsoft Docs
description: Lär dig hur du ger åtkomst till nycklar, hemligheter och certifikat med hjälp av rollbaserad åtkomst kontroll i Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 8f1e95c1244d327478862c3919481394d974ea42
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270346"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control-preview"></a>Ge åtkomst till Key Vault nycklar, certifikat och hemligheter med en rollbaserad åtkomst kontroll i Azure (för hands version)

Rollbaserad åtkomst kontroll i Azure (Azure RBAC) är ett auktoriserings system som bygger på [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) och ger detaljerad åtkomst hantering av Azure-resurser.

Med Azure RBAC kan användare hantera nyckel-, hemligheter-och certifikat behörigheter. Det ger en plats för att hantera alla behörigheter för alla nyckel valv. 

Azure RBAC-modellen ger möjlighet att ange behörigheter för olika omfattnings nivåer: hanterings grupp, prenumeration, resurs grupp eller enskilda resurser.  Azure RBAC för Key Vault ger också möjlighet att ha separata behörigheter för enskilda nycklar, hemligheter och certifikat

Mer information finns i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Metod tips för enskilda nycklar, hemligheter och certifikat

Vår rekommendation är att använda ett valv per program per miljö (utveckling, för produktion och produktion).

Behörigheter för enskilda nycklar, hemligheter och certifikat ska endast användas för specifika scenarier:

-   Program med flera lager som behöver separera åtkomst kontroll mellan lager

-   Delade nyckel valv med vanliga hemligheter, när program behöver åtkomst till under uppsättningar av hemligheter i det nyckel valvet

Mer information om rikt linjer för Azure Key Vault hantering finns i:

- [Metod tips för Azure Key Vault](best-practices.md)
- [Azure Key Vault tjänst gränser](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations-preview"></a>Inbyggda Azure-roller för Key Vault data Plans åtgärder (för hands version)

| Inbyggd roll | Beskrivning | ID |
| --- | --- | --- |
| Key Vault administratör (förhands granskning) | Utför alla data Plans åtgärder på ett nyckel valv och alla objekt, inklusive certifikat, nycklar och hemligheter. Det går inte att hantera Key Vault-resurser eller hantera roll tilldelningar. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault certifikat ansvarig (förhands granskning) | Utföra alla åtgärder för certifikaten för ett nyckel valv, förutom hantera behörigheter. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault kryptografi utanordnare (förhands granskning)| Utföra alla åtgärder för nycklarna i ett nyckel valv, förutom hantera behörigheter. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault kryptering av krypterings tjänst (för hands version) | Läs metadata för nycklar och utför figursatta/unwrap-åtgärder. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault krypto-användare (förhands granskning) | Utföra kryptografiska åtgärder med hjälp av nycklar. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault läsare (förhands granskning)| Läs metadata för nyckel valv och dess certifikat, nycklar och hemligheter. Det går inte att läsa känsliga värden, till exempel hemligt innehåll eller nyckel material. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault hemligheter (förhands granskning)| Utföra alla åtgärder för ett nyckel valvs hemligheter, förutom hantera behörigheter. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault hemligheter, användare (förhands granskning)| Läsa hemligt innehåll. Fungerar bara för nyckel valv som använder behörighets modellen "Azure-rollbaserad åtkomst kontroll". | 4633458b-17de-408a-b874-0445c86b69e6 |

Mer information om definitioner av inbyggda Azure-roller finns i [inbyggda roller i Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Använda Azure RBAC-hemlighet, nyckel och certifikat behörigheter med Key Vault

Den nya Azure RBAC-behörighets modellen för Key Vault ger ett alternativ till åtkomst princips modellen för valv. 

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Aktivera Azure RBAC-behörigheter på Key Vault

> [!IMPORTANT]
> Om du ställer in Azure RBAC-behörighets modell inaktive ras alla behörigheter för åtkomst principer. Det kan orsaka avbrott när motsvarande Azure-roller inte är tilldelade.

1.  Aktivera Azure RBAC-behörigheter för nya nyckel valv:

    ![Aktivera RBAC-behörigheter – nytt valv](../media/rbac/image-1.png)

2.  Aktivera Azure RBAC-behörigheter för befintligt nyckel valv:

    ![Aktivera RBAC-behörighet – befintligt valv](../media/rbac/image-2.png)

### <a name="assign-role"></a>Tilldela rollen

> [!Note]
> Vi rekommenderar att du använder det unika roll-ID: t i stället för roll namnet i skript. Därför fortsätter skripten att fungera om en roll får ett nytt namn. Under för hands versionen skulle varje roll ha "(för hands version)"-suffix, vilket skulle tas bort senare. I detta dokument roll namn används endast för läsbarhet.

Azure CLI-kommando för att skapa en roll tilldelning:

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

På den Azure Portal är skärmen roll tilldelningar för Azure tillgängligt för alla resurser på fliken åtkomst kontroll (IAM).

![Roll tilldelning – fliken (IAM)](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Roll tilldelning för resurs gruppens omfattning

1.  Gå till resurs gruppen Key Vault.
    ![Roll tilldelning-resurs grupp](../media/rbac/image-4.png)

2.  Klicka på åtkomst kontroll (IAM) \> Lägg till roll tilldelning \> Lägg till

3.  Skapa Key Vault läsar roll Key Vault läsare (för hands version) för den aktuella användaren

    ![Lägg till roll resurs grupp](../media/rbac/image-5.png)

Azure CLI:
```azurecli
az role assignment create --role "Key Vault Reader (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

Över roll tilldelningen ger möjlighet att lista Key Vault-objekt i Key Vault.

### <a name="key-vault-scope-role-assignment"></a>Roll tilldelning för Key Vault omfattning

1. Gå till \> fliken Key Vault åtkomst kontroll (IAM)

2. Klicka på Lägg till roll tilldelning \> Lägg till

3. Skapa rollen som administratör för nyckel hemligheter "Key Vault hemligheter (förhands granskning) för den aktuella användaren.

    ![Roll tilldelning – nyckel valv](../media/rbac/image-6.png)

 Azure CLI:

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

När du har skapat roll tilldelningen ovan kan du skapa/uppdatera/ta bort hemligheter.

4. Skapa ny hemlighet (hemligheter \> + generate/import) för att testa roll tilldelningen på hemliga nivån.

    ![Lägg till roll nyckel valv](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Roll tilldelning för hemlig omfattning

1. Öppna en av tidigare skapade hemligheter, meddelande översikt och åtkomst kontroll (IAM) (för hands version)

2. Klicka på åtkomst kontroll (IAM) (för hands version) fliken

    ![Roll tilldelning – hemlighet](../media/rbac/image-8.png)

3. Skapa rollen som administratör för nyckel hemligheter "Key Vault hemligheter (förhands granskning) för den aktuella användaren, samma som den gjorde ovan för Key Vault.

Azure CLI:

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

### <a name="test-and-verify"></a>Testa och verifiera

> [!NOTE]
> Webbläsare använder cachelagring och sid uppdatering krävs efter borttagning av roll tilldelningar.<br>
> Tillåt flera minuter för roll tilldelningar att uppdateras

1. Verifiera att lägga till ny hemlighet utan rollen "Key Vault hemligheter" på nyckel valvs nivå.

Gå till fliken nyckel valv åtkomst kontroll (IAM) och ta bort roll tilldelningen Key Vault hemligheter (förhands granskning) för den här resursen.

![Ta bort tilldelning – nyckel valv](../media/rbac/image-9.png)

Navigera till tidigare skapade hemligheter. Du kan se alla hemliga egenskaper.

![Hemlig vy med åtkomst](../media/rbac/image-10.png)

Create New Secret (hemligheter \> + generate/import) ska visa följande fel:

   ![Skapa ny hemlighet](../media/rbac/image-11.png)

2.  Verifiera hemlighet redigering utan rollen "Key Vault hemlig tjänsteman" på nivån SECRET.

-   Gå till den tidigare skapade fliken Secret Access Control (IAM) (för hands version) och ta bort roll tilldelningen Key Vault hemligheter (förhands granskning) för den här resursen.

-   Navigera till tidigare skapade hemligheter. Du kan se hemliga egenskaper.

   ![Hemlig vy utan åtkomst](../media/rbac/image-12.png)

3. Verifiera hemligheter Läs utan läsar roll på Key Vault-nivå.

-   Gå till Key Vault resurs gruppens åtkomst kontroll (IAM) och ta bort roll tilldelningen Key Vault Reader (för hands version).

-   Om du navigerar till Key Vault-fliken hemligheter visas följande fel:

   ![Fliken hemlighet-fel](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Skapa anpassade roller 

[kommandot AZ Role definition Create](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-create)

**(CLI bash-skript)</br>**
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```

Mer information om hur du skapar anpassade roller finns i:

[Anpassade roller i Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

Kontrol lera bilaga för tillgängliga åtgärder: **tillgängliga åtgärder**

## <a name="known-limits-and-performance"></a>Kända begränsningar och prestanda

-   2000 Azure-roll tilldelningar per prenumeration

-   Svars tid för roll tilldelningar: vid aktuell förväntad prestanda tar det upp till 10 minuter (600 sekunder) efter att roll tilldelningarna har ändrats för att rollen ska tillämpas

## <a name="learn-more"></a>Läs mer

- [Översikt över Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Själv studie kurs om anpassade roller](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-cli)
