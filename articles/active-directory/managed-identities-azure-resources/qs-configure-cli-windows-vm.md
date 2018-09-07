---
title: Hur du konfigurerar system- och användartilldelade hanterade identiteter på en Azure-dator med Azure CLI
description: Steg för steg-instruktioner för att konfigurera system- och användartilldelade hanterade identiteter på en Azure-dator med Azure CLI.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: eb30fc16145bb8fedba4760bbab4ef0ab0800bc9
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028503"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurera hanterade identiteter för Azure-resurser på en Azure-dator med Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln med hjälp av Azure CLI du lära dig hur du utför följande hanterade identiteter för Azure-resurser på en Azure virtuell dator:

- Aktivera och inaktivera systemtilldelade hanterad identitet på en Azure VM
- Lägga till och ta bort en Användartilldelad hanterad identitet på en Azure VM

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande rolltilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:
    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en virtuell dator och aktivera och ta bort system och/eller användartilldelade hanterad identitet från en Azure-dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad hanterad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad hanterad identitet från och till en virtuell dator.
- Om du vill köra CLI-exempelskript, finns det tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd inbäddad Azure Cloud Shell via ”Prova” knappen, finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) om du föredrar att använda den lokala CLI-konsolen.
      
      > [!NOTE]
      > Kommandon har uppdaterats för att återspegla den senaste versionen av den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).     
        

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelade hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelade hanterad identitet på en Azure virtuell dator med Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivera systemtilldelade hanterad identitet under skapandet av en Azure-dator

Skapa en Azure-dator med systemtilldelade hanterade identiteten aktiverat:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med den Azure-prenumeration som du vill distribuera den virtuella datorn i:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgrupp](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az group create](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en virtuell dator med [az vm create](/cli/azure/vm/#az-vm-create). I följande exempel skapas en virtuell dator med namnet *myVM* med en automatiskt genererad hanterad identitet, enligt en förfrågan från den `--assign-identity` parametern. Parametrarna `--admin-username` och `--admin-password` anger namnet och lösenordet för administratörer för inloggning på den virtuella datorn. Uppdatera dessa värden baserat på din miljö: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Aktivera systemtilldelade hanterad identitet på en befintlig Azure VM

Om du vill aktivera hanterade systemtilldelade identiteter på en befintlig virtuell dator:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn.

   ```azurecli-interactive
   az login
   ```

2. Använd [az identitet för virtuell dator tilldelar](/cli/azure/vm/identity/#az-vm-identity-assign) med den `identity assign` kommandot aktivera systemtilldelad identitet till en befintlig virtuell dator:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Inaktivera systemtilldelade identiteter från en Azure virtuell dator

Om du har en virtuell dator som inte längre behöver den systemtilldelade identiteten, men fortfarande ha användartilldelade identiteter, använder du följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Om du har en virtuell dator som inte längre behöver systemtilldelade identiteter och har inga användartilldelade identiteter, använder du följande kommando:

> [!NOTE]
> Värdet `none` är skiftlägeskänsligt. Det måste vara gemener. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```

Ta bort det hanterade identitet för VM-tillägg för Azure-resurser, användaren `-n ManagedIdentityExtensionForWindows` eller `-n ManagedIdentityExtensionForLinux` växla (beroende på vilken typ av virtuell dator) med [az vm-tillägget delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-managed-identity"></a>Användartilldelade hanterad identitet

I det här avsnittet får lära du dig att lägga till och ta bort en hanterad Användartilldelad identitet från en Azure-dator med Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Tilldela en hanterad Användartilldelad identitet när du skapar en Azure-dator

Det här avsnittet vägleder dig genom skapandet av en virtuell dator med tilldelningen av en hanterad Användartilldelad identitet. Om du redan har en virtuell dator som du vill använda kan du hoppa över det här avsnittet och gå vidare till nästa.

1. Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda. Skapa en [resursgrupp](~/articles/azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av din användartilldelade hanterad identitet, med hjälp av [az gruppen skapa](/cli/azure/group/#az-group-create). Ersätt parametervärdena `<RESOURCE GROUP>` och `<LOCATION>` med dina egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en Användartilldelad hanterad identitet med hjälp av [az identitet skapa](/cli/azure/identity#az-identity-create).  Den `-g` parametern anger resursgruppen där den hanterade Användartilldelad identitet skapas, och `-n` parametern anger dess namn.    
    
   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Svaret innehåller information om det användartilldelade hanterad identitet skapas, liknar följande. Resurs-id-värdet som tilldelats till den hanterade Användartilldelad identitet används i följande steg.

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

3. Skapa en virtuell dator med [az vm create](/cli/azure/vm/#az-vm-create). I följande exempel skapas en virtuell dator som är associerade med det nya Användartilldelad identitet, enligt den `--assign-identity` parametern. Ersätt parametervärdena `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Tilldela en hanterad Användartilldelad identitet till en befintlig Azure VM

1. Skapa en användartilldelad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create).  Den `-g` parametern anger resursgruppen där Användartilldelad identitet skapas, och `-n` parametern anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

    > [!IMPORTANT]
    > Skapa användartilldelade hanterade identiteter med specialtecken (t.ex. understreck) i namnet stöds inte för närvarande. Använd alfanumeriska tecken. Kom tillbaka om för att få uppdateringar.  Mer information finns i [vanliga frågor och kända problem](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Svaret innehåller information om det användartilldelade hanterad identitet skapas, liknar följande. 

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

2. Tilldela Användartilldelad identitet till den virtuella datorn med [az vm identitet tilldela](/cli/azure/vm#az-vm-identity-assign). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY NAME>` är användartilldelade hanterade identitetens resurs `name` egenskapen, som du skapade i föregående steg:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en hanterad Användartilldelad identitet från en Azure-dator

Ta bort en hanterad Användartilldelad identitet från en virtuell dator använder [az identitet för virtuell dator ta bort](/cli/azure/vm#az-vm-identity-remove). Om det här är den enda användartilldelade hanterade identiteten som tilldelats den virtuella datorn, `UserAssigned` tas bort från TYPVÄRDE identitet.  Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY>` som identitet för användartilldelade `name` egenskapen, som finns i identitetsavsnittet i den virtuella datorn med `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Om den virtuella datorn inte har en automatiskt genererad hanterad identitet och du vill ta bort alla användartilldelade identiteter från den, använder du följande kommando:

> [!NOTE]
> Värdet `none` är skiftlägeskänsligt. Det måste vara gemener.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Om den virtuella datorn har både systemtilldelade och användartilldelade identiteter kan du ta bort alla användartilldelade identiteter genom att växla mellan att bara använda systemtilldelade. Ange följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Nästa steg
- [Hanterade identiteter för översikt över Azure-resurser](overview.md)
- Fullständig Azure skapandet virtuella datorn Snabbstarter, finns här: 
  - [Skapa en Windows-dator med CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Skapa en Linux-dator med CLI](../../virtual-machines/linux/quick-create-cli.md) 

















