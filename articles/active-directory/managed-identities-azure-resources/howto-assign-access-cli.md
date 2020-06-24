---
title: Tilldela en hanterad identitets åtkomst till en resurs med Azure CLI – Azure AD
description: Stegvisa instruktioner för att tilldela en hanterad identitet på en resurs, åtkomst till en annan resurs med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 782c148677c0a4ce0997fb98b3caa784aec80ef8
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84693999"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Tilldela en hanterad identitets åtkomst till en resurs med hjälp av Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet kan du ge den hanterade identiteten åtkomst till en annan resurs, precis som alla säkerhets objekt. I det här exemplet får du se hur du ger en hanterad identitets åtkomst för en virtuell Azure-dator eller skalnings uppsättning för virtuella datorer till ett Azure Storage-konto med Azure CLI.

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Det finns tre alternativ för att köra CLI-skript exempel:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure Portal (se nästa avsnitt).
    - Använd den inbäddade Azure Cloud Shell via knappen "prova", som finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) om du föredrar att använda en lokal CLI-konsol. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använd RBAC för att tilldela en hanterad identitets åtkomst till en annan resurs

När du har aktiverat hanterad identitet på en Azure-resurs, till exempel en [virtuell Azure-dator](qs-configure-cli-windows-vm.md) eller en [virtuell Azure-dators skalnings uppsättning](qs-configure-cli-windows-vmss.md): 

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med den Azure-prenumeration under vilken du vill distribuera den virtuella datorn eller skalnings uppsättningen för den virtuella datorn:

   ```azurecli-interactive
   az login
   ```

2. I det här exemplet ger vi en virtuell Azure-dator åtkomst till ett lagrings konto. Först använder vi [AZ Resource List](/cli/azure/resource/#az-resource-list) för att hämta tjänstens huvud namn för den virtuella datorn med namnet myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   För en skalnings uppsättning för virtuella Azure-datorer är kommandot detsamma, men du får tjänstens huvud namn för den virtuella datorns skalnings uppsättning med namnet "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. När du har tjänstens huvud namn-ID använder du [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) för att ge den virtuella datorn eller skalnings uppsättningen för den virtuella datorn åtkomst till ett lagrings konto med namnet "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Information om hur du aktiverar hanterad identitet på en virtuell Azure-dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure CLI](qs-configure-cli-windows-vm.md).
- Om du vill aktivera hanterad identitet på en skalnings uppsättning för virtuella Azure-datorer, se [Konfigurera hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer med Azure CLI](qs-configure-cli-windows-vmss.md).
