---
title: "Hur du konfigurerar MSI på en virtuell Azure-dator med hjälp av Azure CLI"
description: "Steg för steg-instruktioner för hur du konfigurerar en hanterad tjänst identitet (MSI) på en Azure VM, med hjälp av Azure CLI."
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
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: 36218c50a7d43cf266459f5cf001350a3ecc84cf
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>Konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure CLI

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du aktiverar och ta bort MSI för en Azure-dator med hjälp av Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du vill köra skriptexempel CLI har tre alternativ:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
- Använd inbäddade Azure Cloud Shell via försök ”knappen”, finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Aktivera MSI under skapandet av en Azure VM

Skapa en MSI-aktiverad virtuell dator:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/#az_login). Använd ett konto som är associerade med Azure-prenumeration som du vill distribuera den virtuella datorn:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgruppen](../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az gruppen skapa](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en virtuell dator med hjälp av [az vm skapa](/cli/azure/vm/#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVM* med en MSI som begärs av den `--assign-identity` parameter. Den `--admin-username` och `--admin-password` parametrarna anger administrativa namnet och lösenordet användarkontot för virtuell dator-inloggning. Uppdatera dessa värden som passar din miljö: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Aktivera MSI på en befintlig virtuell Azure-dator

Om du behöver aktivera MSI på en befintlig virtuell dator:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/#az_login). Använd ett konto som är kopplad till den Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”:

   ```azurecli-interactive
   az login
   ```

2. Använd [az tilldela-identitet](/cli/azure/vm/#az_vm_assign_identity) med den `--assign-identity` parametern för att lägga till en MSI i en befintlig virtuell dator:

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Ta bort MSI från en virtuell dator i Azure

Om du har en virtuell dator som inte längre behöver en MSI:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/#az_login). Använd ett konto som är kopplad till den Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”:

   ```azurecli-interactive
   az login
   ```

2. Använd den `-n ManagedIdentityExtensionForWindows` eller `-n ManagedIdentityExtensionForLinux` växla (beroende på vilken typ av VM) med [az virtuella tillägget Ta bort](https://docs.microsoft.com/cli/azure/vm/#assign-identity) att ta bort MSI:

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>Relaterat innehåll

- [Översikt över hanterade tjänstidentiteten](msi-overview.md)
- För fullständig Azure VM skapa Snabbstart, se: 

  - [Skapa en Windows-dator med CLI](../virtual-machines/windows/quick-create-cli.md)  
  - [Skapa en virtuell Linux-dator med CLI](../virtual-machines/linux/quick-create-cli.md) 

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.
















