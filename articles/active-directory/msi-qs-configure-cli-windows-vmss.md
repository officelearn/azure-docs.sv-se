---
title: "Konfigurera MSI på en virtuell dator i Azure-skala med Azure CLI"
description: "Steg för steg-instruktioner för hur du konfigurerar en hanterad tjänst identitet (MSI) på ett Azure Virtual Machine Scale Set, med hjälp av Azure CLI."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: c58ad9cc8bfa16b11201735bcc513e6dd692a2a9
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Konfigurera en virtuell dator skaluppsättning hanteras Service identitet (MSI) med hjälp av Azure CLI

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du aktiverar och ta bort MSI för en virtuell dator i Azure-skala med Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du vill köra skriptexempel CLI har tre alternativ:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
- Använd inbäddade Azure Cloud Shell via försök ”knappen”, finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera MSI under skapandet av en skaluppsättning för virtuell Azure-dator

Anger om du vill skapa en virtuell dator skala MSI-aktiverade:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/#az_login). Använd ett konto som är associerade med Azure-prenumeration som du vill distribuera virtuella datorns skaluppsättning:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgruppen](../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av skaluppsättning för virtuell dator och dess relaterade resurser med hjälp av [az gruppen skapa](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en virtuell dator-skala med [az vmss skapa](/cli/azure/vmss/#az_vmss_create) . I följande exempel skapas en virtuell dator skaluppsättningen namngivna *myVMSS* med en MSI som begärs av den `--assign-identity` parameter. Den `--admin-username` och `--admin-password` parametrarna anger administrativa namnet och lösenordet användarkontot för virtuell dator-inloggning. Uppdatera dessa värden som passar din miljö: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera MSI på en befintlig skaluppsättning för virtuell Azure-dator

Om du behöver aktivera MSI på en befintlig skaluppsättning för virtuell Azure-dator:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/#az_login). Använd ett konto som är kopplad till den Azure-prenumeration som innehåller virtuella datorns skaluppsättning.

   ```azurecli-interactive
   az login
   ```

2. Använd [az vmss tilldela-identitet](/cli/azure/vm/#az_vmss_assign_identity) med den `--assign-identity` parametern för att lägga till en MSI i en befintlig virtuell dator:

   ```azurecli-interactive
   az vmss assign-identity -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Ta bort MSI från en skaluppsättning för virtuell Azure-dator

Om du har en skaluppsättning för virtuell dator som inte längre behöver en MSI:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/#az_login). Använd ett konto som är kopplad till den Azure-prenumeration som innehåller virtuella datorns skaluppsättning.

   ```azurecli-interactive
   az login
   ```

2. Använd den `--identities` växel med [az vmss remove-identitet](/cli/azure/vmss/#az_vmss_remove_identity) att ta bort MSI:

   ```azurecli-interactive
   az vmss remove-identity -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade tjänstidentiteten](msi-overview.md)
- För fullständig Azure virtuella datorn skapas Quickstart finns i avsnittet: 

  - [Skapa en Skaluppsättning för virtuell dator med CLI](../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
















