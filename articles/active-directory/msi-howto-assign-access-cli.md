---
title: "Tilldela en MSI-åtkomst till en Azure-resurs, med hjälp av Azure CLI"
description: "Steg för steg instruktioner för att tilldela en MSI på en resurs som har åtkomst till en annan resurs, med hjälp av Azure CLI."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: bryanla
ms.openlocfilehash: 7d817a90277a1320ccc028822032916c98cae4b4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Tilldela en hanterad tjänst identitet (MSI) åtkomst till en resurs med hjälp av Azure CLI

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en MSI, kan du ge MSI-åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Det här exemplet visar hur du får en Azure-dator MSI åtkomst till ett Azure storage-konto med hjälp av Azure CLI.

## <a name="prerequisites"></a>Krav

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du vill köra skriptexempel CLI har tre alternativ:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
- Använd inbäddade Azure Cloud Shell via försök ”knappen”, finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Använda RBAC tilldela MSI-åtkomst till en annan resurs

När du har aktiverat MSI på en Azure-resurs [, till exempel en Azure VM](msi-qs-configure-cli-windows-vm.md): 

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/#login). Använd ett konto som är associerade med Azure-prenumeration som du vill distribuera den virtuella datorn:

   ```azurecli-interactive
   az login
   ```

2. I det här exemplet ger vi en virtuell dator i Azure-åtkomst till ett lagringskonto. Första vi använder [az resurslistan](/cli/azure/resource/#list) att hämta tjänstens huvudnamn för den virtuella datorn med namnet ”myVM” som skapades när vi har aktiverat MSI på den virtuella datorn:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```

3. När vi har ägar-ID för tjänsten kan vi använda [az rolltilldelning skapa](/cli/azure/role/assignment#az_role_assignment_create) om du vill ge den virtuella datorn ”Reader” åtkomst till ett lagringskonto med namnet ”myStorageAcct”:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Felsökning

Om MSI-filerna för resursen inte visas i listan över tillgängliga identiteter, kontrollerar du att MSI har aktiverats. I vårt fall vi går tillbaka till den virtuella Azure-datorn i den [Azure-portalen](https://portal.azure.com) och:

- Titta på sidan ”Configuration” och kontrollera MSI aktiverat = ”Yes”.
- Titta på sidan ”tillägg” och kontrollera MSI-tillägget har distribuerats.

Om något är fel, kan du behöva distribuera om MSI-filerna på din resurs eller felsöka distributionen misslyckades.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](msi-overview.md).
- För att aktivera MSI på en Azure VM, se [konfigurera en Azure VM hanterade tjänsten identitet (MSI) med hjälp av Azure CLI](msi-qs-configure-cli-windows-vm.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.

