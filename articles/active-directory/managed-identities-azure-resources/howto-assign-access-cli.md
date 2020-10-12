---
title: Tilldela en hanterad identitets åtkomst till en resurs med Azure CLI – Azure AD
description: Stegvisa instruktioner för att tilldela en hanterad identitet på en resurs, åtkomst till en annan resurs med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13c69dda1e300bcff95b6a017fdeb308a6bbf3a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969247"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Tilldela en hanterad identitets åtkomst till en resurs med hjälp av Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet kan du ge den hanterade identiteten åtkomst till en annan resurs, precis som alla säkerhets objekt. I det här exemplet får du se hur du ger en hanterad identitets åtkomst för en virtuell Azure-dator eller skalnings uppsättning för virtuella datorer till ett Azure Storage-konto med Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra exempel skripten har du två alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md)som du kan öppna med knappen **prova** på det övre högra hörnet av kodblock.
    - Kör skript lokalt genom att installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli)och logga sedan in på Azure med [AZ-inloggning](/cli/azure/reference-index#az-login). Använd ett konto som är kopplat till den Azure-prenumeration som du vill skapa resurser i.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använd Azure RBAC för att tilldela en hanterad identitets åtkomst till en annan resurs

När du har aktiverat hanterad identitet på en Azure-resurs, till exempel en [virtuell Azure-dator](qs-configure-cli-windows-vm.md) eller en [virtuell Azure-dators skalnings uppsättning](qs-configure-cli-windows-vmss.md): 

1. I det här exemplet ger vi en virtuell Azure-dator åtkomst till ett lagrings konto. Först använder vi [AZ Resource List](/cli/azure/resource/#az-resource-list) för att hämta tjänstens huvud namn för den virtuella datorn med namnet myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   För en skalnings uppsättning för virtuella Azure-datorer är kommandot detsamma, men du får tjänstens huvud namn för den virtuella datorns skalnings uppsättning med namnet "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. När du har tjänstens huvud namn-ID använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) för att ge den virtuella datorn eller skalnings uppsättningen för den virtuella datorn åtkomst till ett lagrings konto med namnet "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Information om hur du aktiverar hanterad identitet på en virtuell Azure-dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure CLI](qs-configure-cli-windows-vm.md).
- Om du vill aktivera hanterad identitet på en skalnings uppsättning för virtuella Azure-datorer, se [Konfigurera hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer med Azure CLI](qs-configure-cli-windows-vmss.md).
