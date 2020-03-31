---
title: Tilldela en hanterad identitetsåtkomst till en resurs med Azure CLI - Azure AD
description: Steg för steg instruktioner för att tilldela en hanterad identitet på en resurs, tillgång till en annan resurs, med hjälp av Azure CLI.
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
ms.openlocfilehash: b241ac223fd1eb9df2b0a914726d8f37df5f4d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547364"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Tilldela en hanterad identitetsåtkomst till en resurs med Hjälp av Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet kan du ge den hanterade identitetsåtkomsten till en annan resurs, precis som alla säkerhetsobjekt. Det här exemplet visar hur du ger en Azure virtuell dator eller virtuell dator skalningsuppsättningens hanterade identitetsåtkomst till ett Azure-lagringskonto med Azure CLI.

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra CLI-skriptexemplen har du tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd det inbäddade Azure Cloud Shell via knappen "Prova", som finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) om du föredrar att använda en lokal CLI-konsol. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använda RBAC för att tilldela en hanterad identitetsåtkomst till en annan resurs

När du har aktiverat hanterad identitet på en Azure-resurs, till exempel en [virtuell Azure-dator](qs-configure-cli-windows-vm.md) eller [Azure-skalningsuppsättning för virtuella datorer:](qs-configure-cli-windows-vmss.md) 

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med Azure-prenumerationen där du vill distribuera skaluppsättningen för virtuella datorer eller virtuella datorer:

   ```azurecli-interactive
   az login
   ```

2. I det här exemplet ger vi en virtuell Azure-dator åtkomst till ett lagringskonto. Först använder vi [az resurslista](/cli/azure/resource/#az-resource-list) för att få tjänstens huvudnamn för den virtuella datorn som heter myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   För en Azure-skalningsuppsättning för virtuella datorer är kommandot detsamma förutom här, du får tjänstens huvudnamn för den virtuella datorns skaluppsättning med namnet "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. När du har tjänstens huvud-ID använder du [az-rolltilldelningsskap](/cli/azure/role/assignment#az-role-assignment-create) för att ge den virtuella datorn eller skalan för virtuell dator som "Reader" åtkomst till ett lagringskonto som heter "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Information om hur du aktiverar hanterad identitet på en virtuell Azure-dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en Azure VM med Azure CLI](qs-configure-cli-windows-vm.md).
- Information om hur du aktiverar hanterad identitet på en Azure-skalningsuppsättning för virtuella datorer finns i [Konfigurera hanterade identiteter för Azure-resurser på en skaladator för virtuella datorer med Azure CLI](qs-configure-cli-windows-vmss.md).
