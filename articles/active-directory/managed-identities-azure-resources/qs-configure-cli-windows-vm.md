---
title: Konfigurera hanterade identiteter på Azure VM med Azure CLI - Azure AD
description: Steg för steg instruktioner för att konfigurera system och användartilldelade hanterade identiteter på en Azure VM med Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f2efaceefc53b3c0b5dfd899baf9fd30fdf9a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244152"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurera hanterade identiteter för Azure-resurser på en Virtuell Azure-dator med Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln, med hjälp av Azure CLI, lär du dig hur du utför följande hanterade identiteter för Azure-resurser på en Virtuell Azure:In this article, using the Azure CLI, you learn how to perform the following managed identities for Azure resources operations on an Azure VM:

- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en Virtuell Azure-dator
- Lägga till och ta bort en användartilldelad hanterad identitet på en Virtuell Azure-dator

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra CLI-skriptexemplen har du tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd det inbäddade Azure Cloud Shell via knappen "Prova", som finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) om du föredrar att använda en lokal CLI-konsol. 
      
      > [!NOTE]
      > Kommandona har uppdaterats för att återspegla den senaste versionen av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den systemtilldelade hanterade identiteten på en Azure VM med Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivera systemtilldelade hanterade identiteter när en Azure-virtuell dator skapas

Om du vill skapa en virtuell Azure-dator med den systemtilldelade hanterade identiteten aktiverad behöver ditt konto rolltilldelningen för deltagare i [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med den Azure-prenumeration som du vill distribuera den virtuella datorn i:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgrupp](../../azure-resource-manager/management/overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az group create](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en virtuell dator med [az vm create](/cli/azure/vm/#az-vm-create). I följande exempel skapas en virtuell dator med namnet *myVM* med `--assign-identity` en systemtilldelad hanterad identitet, enligt parametern. Parametrarna `--admin-username` och `--admin-password` anger namnet och lösenordet för administratörer för inloggning på den virtuella datorn. Uppdatera dessa värden baserat på din miljö: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Aktivera systemtilldelade hanterade identitet på en befintlig Azure-virtuell dator

För att aktivera systemtilldelade hanterade identitet på en virtuell dator behöver ditt konto rolltilldelningen för deltagare för [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med Azure-prenumerationen som innehåller den virtuella datorn.

   ```azurecli-interactive
   az login
   ```

2. Använd [az vm-identitet som tilldelats](/cli/azure/vm/identity/) med `identity assign` kommandot aktivera den systemtilldelade identiteten till en befintlig virtuell dator:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Inaktivera systemtilldelad identitet från en virtuell Azure-dator

Om du vill inaktivera den systemtilldelade hanterade identiteten på en virtuell dator behöver kontot rolltilldelningen [för virtuella](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) datorer.  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

Om du har en virtuell dator som inte längre behöver den systemtilldelade identiteten, men som fortfarande behöver användartilldelade identiteter, använder du följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Om du har en virtuell dator som inte längre behöver systemtilldelad identitet och den inte har några användartilldelade identiteter använder du följande kommando:

> [!NOTE]
> Värdet `none` är skiftlägeskänsligt. Det måste vara gemener. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort en användartilldelad hanterad identitet från en Azure VM med Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Tilldela en användartilldelad hanterad identitet när en Virtuell Azure skapas

Om du vill tilldela en användartilldelning till en virtuell dator när den skapas behöver kontot [rolltilldelningarna för deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitetsoperatör.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda. Skapa en [resursgrupp](~/articles/azure-resource-manager/management/overview.md#terminology) för inneslutning och distribution av din användartilldelade hanterade identitet med hjälp av [az-gruppge](/cli/azure/group/#az-group-create). Ersätt parametervärdena `<RESOURCE GROUP>` och `<LOCATION>` med dina egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create).  Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Svaret innehåller information om den användartilldelade hanterade identitet som skapats, ungefär som följande. Resurs-ID-värdet som tilldelats den användartilldelade hanterade identiteten används i följande steg.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Skapa en virtuell dator med [az vm create](/cli/azure/vm/#az-vm-create). I följande exempel skapas en virtuell dator som är associerad `--assign-identity` med den nya användartilldelade identiteten, enligt parametern. Ersätt parametervärdena `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Tilldela en användartilldelad hanterad identitet till en befintlig Virtuell Azure-dator

Om du vill tilldela en användartilldelning till en virtuell dator behöver ditt konto [rolltilldelningarna för deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitetsoperatör.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Skapa en användartilldelad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create).  Parametern `-g` anger den resursgrupp där den användartilldelade identiteten skapas och parametern `-n` anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

    > [!IMPORTANT]
    > Det stöds för närvarande inte att skapa användartilldelade hanterade identiteter med specialtecken (dvs. understreck) i namnet. Använd alfanumeriska tecken. Kom tillbaka om för att få uppdateringar.  Mer information finns i [vanliga frågor och ärenden](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Svaret innehåller information om den användartilldelade hanterade identitet som skapats, ungefär som följande. 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Tilldela den användartilldelade identiteten till den virtuella datorn med az [vm-identitetstilldelar](/cli/azure/vm). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY NAME>` användartilldelade hanterade identitetens `name` resursegenskap, som skapats i föregående steg:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användartilldelad hanterad identitet från en virtuell Azure-dator

Om du vill ta bort en användartilldelning av identitet till en virtuell dator behöver ditt konto rolltilldelningen för deltagare för [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 

Om detta är den enda användartilldelade hanterade identiteten `UserAssigned` som tilldelats den virtuella datorn, tas bort från identitetstypsvärdet.  Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY>` som tilldelas användaren är `name` egenskapen för den tilldelade identiteten, som `az vm identity show`finns i identitetsavsnittet på den virtuella datorn med:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Om den virtuella datorn inte har en systemtilldelad hanterad identitet och du vill ta bort alla användartilldelade identiteter från den använder du följande kommando:

> [!NOTE]
> Värdet `none` är skiftlägeskänsligt. Det måste vara gemener.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Om den virtuella datorn har både systemtilldelade och användartilldelade identiteter kan du ta bort alla användartilldelade identiteter genom att växla till att endast använda systemtilldelade. Ange följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Nästa steg
- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Fullständiga snabbstarter för Azure VM finns i: 
  - [Skapa en virtuell Windows-dator med CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Skapa en virtuell Linux-dator med CLI](../../virtual-machines/linux/quick-create-cli.md) 

















