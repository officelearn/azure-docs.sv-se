---
title: Så här tilldelar du en hanterad identitet åtkomst till en Azure-resurs med hjälp av Azure CLI
description: Steg för steg åtkomst instruktioner för att tilldela en hanterad identitet på en enda resurs, till en annan resurs, med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: priyamo
ms.openlocfilehash: 64fa9e16ab1566b6a3ddccaba41b2edd9e69ea9a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55184945"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Tilldela en hanterad identitet åtkomst till en resurs med hjälp av Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet, kan du ge hanterad identitet-åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Det här exemplet visar hur du ge en Azure virtuell dator eller virtual machine scale Sets hanterad identitet åtkomst till ett Azure storage-konto med Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra CLI-exempelskript, finns det tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd inbäddad Azure Cloud Shell via ”Prova” knappen, finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) om du föredrar att använda den lokala CLI-konsolen. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använd RBAC för att tilldela en hanterad identitet åtkomst till en annan resurs

När du har aktiverat hanterad identitet på en Azure-resurs, till exempel en [Azure-dator](qs-configure-cli-windows-vm.md) eller [Azure virtual machine scale Sets](qs-configure-cli-windows-vmss.md): 

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerade med Azure-prenumerationen som du vill distribuera skalningsuppsättningen för virtuell dator eller virtuell dator:

   ```azurecli-interactive
   az login
   ```

2. I det här exemplet ger vi en virtuell Azure-datoråtkomst till ett lagringskonto. Först använder vi [az resurslistan](/cli/azure/resource/#az-resource-list) att hämta tjänstens huvudnamn för den virtuella datorn med namnet myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   För en Azure VM-skalningsuppsättning kommandot är samma utom här så kan du tjänstens huvudnamn i virtuella datorns skalningsuppsättning med namnet ”DevTestVMSS”:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. När du har ID för tjänstens huvudnamn använder [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) att ge den virtuella datorn eller VM-skalningsuppsättningar kan du ange ”läsare” åtkomst till ett lagringskonto med namnet ”myStorageAcct”:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Nästa steg

- [Hanterade identiteter för översikt över Azure-resurser](overview.md)
- För att aktivera hanterad identitet på virtuella Azure-datorer, se [konfigurera hanterade identiteter för Azure-resurser på en Azure virtuell dator med Azure CLI](qs-configure-cli-windows-vm.md).
- För att aktivera hanterad identitet på en Azure VM-skalningsuppsättning, se [konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med Azure CLI](qs-configure-cli-windows-vmss.md).
