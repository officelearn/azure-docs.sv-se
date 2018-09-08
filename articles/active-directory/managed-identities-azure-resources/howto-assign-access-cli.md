---
title: Tilldela MSI-åtkomst till en Azure-resurs, med hjälp av Azure CLI
description: Steg för steg åtkomst instruktioner för att tilldela en MSI på en enda resurs, till en annan resurs, med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: f46475dfc74212a01b70d487c04d6cd54ca4ca2f
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158168"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Tilldela en hanterad tjänstidentitet (MSI)-åtkomst till en resurs med hjälp av Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en MSI, kan du ge MSI-åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Det här exemplet visar hur du ge en Azure virtuell dator eller virtual machine scale Sets MSI åtkomst till ett Azure storage-konto med Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du vill köra CLI-exempelskript, finns det tre alternativ:

- Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
- Använd inbäddad Azure Cloud Shell via ”Prova” knappen, finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Använd RBAC för att tilldela MSI-åtkomst till en annan resurs

När du har aktiverat MSI på en Azure-resurs, till exempel en [Azure-dator](qs-configure-cli-windows-vm.md) eller [Azure virtual machine scale Sets](qs-configure-cli-windows-vmss.md): 

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerade med Azure-prenumerationen som du vill distribuera skalningsuppsättningen för virtuell dator eller virtuell dator:

   ```azurecli-interactive
   az login
   ```

2. I det här exemplet ger vi en virtuell Azure-datoråtkomst till ett lagringskonto. Först använder vi [az resurslistan](/cli/azure/resource/#az-resource-list) att hämta tjänstens huvudnamn för den virtuella datorn med namnet ”myVM”:

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

## <a name="troubleshooting"></a>Felsökning

Om MSI för resursen inte visas i listan över tillgängliga identiteter, kontrollerar du att MSI har aktiverats. I det här fallet vi gå tillbaka till Azure-dator eller skalningsuppsättning virtuell dator i [Azure-portalen](https://portal.azure.com) och:

- Titta på sidan ”Configuration” och se till att MSI aktiverat = ”Yes”.
- Titta på sidan ”tillägg” och se till att MSI-tillägget har distribuerats (**tillägg** sidan är inte tillgänglig för en Azure VM-skalningsuppsättning).

Om något är fel, kan du behöva distribuera om MSI på resursen igen eller felsöka distributionsfel.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](overview.md).
- För att aktivera MSI på virtuella Azure-datorer, se [konfigurera en Azure VM hanterad tjänstidentitet (MSI) med Azure CLI](qs-configure-cli-windows-vm.md).
- För att aktivera MSI på en Azure VM-skalningsuppsättning, se [konfigurera en Azure virtuell dator skala ange hanterad tjänstidentitet (MSI) med Azure portal](qs-configure-portal-windows-vmss.md)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.

