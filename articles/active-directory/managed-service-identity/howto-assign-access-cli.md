---
title: Tilldela en MSI-åtkomst till en Azure-resurs, med hjälp av Azure CLI
description: Steg för steg instruktioner för att tilldela en MSI på en resurs som har åtkomst till en annan resurs, med hjälp av Azure CLI.
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
ms.openlocfilehash: 947e0140c7943954be5eb285bb7ec514b74e9022
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Tilldela en hanterad tjänst identitet (MSI) åtkomst till en resurs med hjälp av Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en MSI, kan du ge MSI-åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Det här exemplet visar hur du ge en virtuell Azure-dator eller virtuell dator scale set MSI åtkomst till ett Azure storage-konto med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du vill köra skriptexempel CLI har tre alternativ:

- Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
- Använd inbäddade Azure Cloud Shell via försök ”knappen”, finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Använda RBAC tilldela MSI-åtkomst till en annan resurs

När du har aktiverat MSI på en Azure-resurs, till exempel en [virtuella Azure-datorn](qs-configure-cli-windows-vm.md) eller [skaluppsättning för virtuell dator i Azure](qs-configure-cli-windows-vmss.md): 

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/reference-index#az_login). Använd ett konto som är associerade med Azure-prenumeration som du vill distribuera skaluppsättning för virtuell dator eller virtuell dator:

   ```azurecli-interactive
   az login
   ```

2. I det här exemplet ger vi en virtuell dator i Azure-åtkomst till ett lagringskonto. Första vi använder [az resurslistan](/cli/azure/resource/#az_resource_list) att hämta tjänstens huvudnamn för den virtuella datorn med namnet ”myVM”:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   För en virtuell Azure-dator i skaluppsättning för kommandot är samma utom här får du tjänstens huvudnamn för skaluppsättning för virtuell dator med namnet ”DevTestVMSS”:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. När du har ägar-ID för tjänsten använder [az rolltilldelning skapa](/cli/azure/role/assignment#az_role_assignment_create) så att den virtuella datorn eller virtuella datorn anger du ”Reader” åtkomst till ett lagringskonto som kallas ”myStorageAcct”:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Felsökning

Om MSI-filerna för resursen inte visas i listan över tillgängliga identiteter, kontrollerar du att MSI har aktiverats. I vårt fall vi går tillbaka till den virtuella Azure-datorn eller skala för virtuell dator i den [Azure-portalen](https://portal.azure.com) och:

- Titta på sidan ”Configuration” och kontrollera MSI aktiverat = ”Yes”.
- Titta på sidan ”tillägg” och kontrollera MSI-tillägget som distribuerats (**tillägg** sidan är inte tillgänglig för en skaluppsättning för virtuell Azure-dator).

Om något är fel, kan du behöva distribuera om MSI-filerna på din resurs eller felsöka distributionen misslyckades.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](overview.md).
- För att aktivera MSI på en virtuell Azure-dator, se [konfigurera en Azure VM hanterade tjänsten identitet (MSI) med hjälp av Azure CLI](qs-configure-cli-windows-vm.md).
- Om du vill aktivera MSI i en skaluppsättning för virtuell dator i Azure finns [konfigurera ett Azure Virtual Machine skala Ange hanterade tjänsten identitet (MSI) med Azure-portalen](qs-configure-portal-windows-vmss.md)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.

