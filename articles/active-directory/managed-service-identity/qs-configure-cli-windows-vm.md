---
title: Så här konfigurerar du system- och användartilldelade identiteter på en Azure virtuell dator med Azure CLI
description: Steg för steg tilldelas instruktioner för att konfigurera en system- och en identiteter på en Azure virtuell dator med Azure CLI.
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
ms.openlocfilehash: cb23db13d67047225102c6888e27e8f79a3e5abf
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259321"
---
# <a name="configure-managed-service-identity-on-an-azure-vm-using-azure-cli"></a>Konfigurera hanterad tjänstidentitet på en Azure-dator med Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du utför följande åtgärder för hanterad tjänstidentitet på en Azure-dator med hjälp av Azure CLI:
- Aktivera och inaktivera systemtilldelad identitet på en Azure VM
- Lägga till och ta bort en Användartilldelad identitet på en Azure VM

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande rolltilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:
    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en virtuell dator och aktivera och ta bort system och/eller användare tilldelas hanterad identitet från en Azure-dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad identitet från och till en virtuell dator.
- Om du vill köra CLI-exempelskript, finns det tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd inbäddad Azure Cloud Shell via ”Prova” knappen, finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Systemtilldelad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelad identitet på en Azure virtuell dator med Azure CLI.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Aktivera systemtilldelad identitet under skapandet av en Azure-dator

Skapa en Azure-dator med systemtilldelad identitet som aktiverat:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az_login). Använd ett konto som är associerat med den Azure-prenumeration som du vill distribuera den virtuella datorn i:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgrupp](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az group create](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en virtuell dator med [az vm create](/cli/azure/vm/#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVM* med en systemtilldelade identiteter, enligt en förfrågan från den `--assign-identity` parametern. Parametrarna `--admin-username` och `--admin-password` anger namnet och lösenordet för administratörer för inloggning på den virtuella datorn. Uppdatera dessa värden baserat på din miljö: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Aktivera systemtilldelad identitet på en befintlig Azure VM

Om du vill aktivera den systemtilldelade identiteten på en befintlig virtuell dator:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az_login). Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn.

   ```azurecli-interactive
   az login
   ```

2. Använd [az identitet för virtuell dator tilldelar](/cli/azure/vm/identity/#az_vm_identity_assign) med den `identity assign` kommandot aktivera systemtilldelad identitet till en befintlig virtuell dator:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Inaktivera systemtilldelad identitet från en Azure virtuell dator

Om du har en virtuell dator som inte längre behöver systemtilldelad identitet, men fortfarande ha användartilldelade identiteter, använder du följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Om du har en virtuell dator som inte längre behöver systemtilldelad identitet och har inga användartilldelade identiteter, använder du följande kommando:

> [!NOTE]
> Värdet `none` är skiftlägeskänsligt. Det måste vara gemener. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```

Att ta bort tillägget Managed Service Identity VM användaren `-n ManagedIdentityExtensionForWindows` eller `-n ManagedIdentityExtensionForLinux` växla (beroende på vilken typ av virtuell dator) med [az vm-tillägget delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Tilldelad användaridentitet

I det här avsnittet får lära du dig att lägga till och ta bort Användartilldelad identitet från en Azure virtuell dator med Azure CLI.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Tilldela Användartilldelad identitet när du skapar en Azure-dator

Det här avsnittet vägleder dig genom skapandet av en virtuell dator med tilldelningen av en Användartilldelad identitet. Om du redan har en virtuell dator som du vill använda kan du hoppa över det här avsnittet och gå vidare till nästa.

1. Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda. Skapa en [resursgrupp](~/articles/azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av din hanterade tjänstidentitet, med hjälp av [az gruppen skapa](/cli/azure/group/#az_group_create). Ersätt parametervärdena `<RESOURCE GROUP>` och `<LOCATION>` med dina egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en Användartilldelad identitet med hjälp av [az identitet skapa](/cli/azure/identity#az_identity_create).  Den `-g` parametern anger resursgruppen där Användartilldelad identitet skapas, och `-n` parametern anger dess namn.    
    
   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Svaret innehåller information om Användartilldelad identitet skapas, liknar följande. Resurs-id-värdet som tilldelats Användartilldelad identitet används i följande steg.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
       "location": "westcentralus",
       "name": "<MSI NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Skapa en virtuell dator med [az vm create](/cli/azure/vm/#az_vm_create). I följande exempel skapas en virtuell dator som är associerade med den nya användartilldelade-identiteten som anges av den `--assign-identity` parametern. Ersätt parametervärdena `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` och `<MSI ID>` med dina egna värden. För `<MSI ID>`, använda Användartilldelad identitet resurs `id` egenskap som skapats i föregående steg: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Tilldela Användartilldelad identitet till en befintlig Azure VM

1. Skapa en Användartilldelad identitet med hjälp av [az identitet skapa](/cli/azure/identity#az-identity-create).  Den `-g` parametern anger resursgruppen där Användartilldelad identitet skapas, och `-n` parametern anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<MSI NAME>` med dina egna värden:

    > [!IMPORTANT]
    > Skapa användartilldelade identiteter med specialtecken (t.ex. understreck) i namnet stöds inte för närvarande. Använd alfanumeriska tecken. Kom tillbaka om för att få uppdateringar.  Mer information finns i [vanliga frågor och kända problem](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Svaret innehåller information om användaren som har tilldelats hanterad identitet som har skapats, liknar följande. Resursen `id` värde som tilldelats Användartilldelad identitet som ska användas i följande steg.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Tilldela Användartilldelad identitet till den virtuella datorn med [az vm identitet tilldela](/cli/azure/vm#az-vm-identity-assign). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. Den `<MSI ID>` kommer att identiteten för användare som är tilldelad resursen `id` egenskapen, som du skapade i föregående steg:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Ta bort Användartilldelad identitet från en Azure virtuell dator

Ta bort en Användartilldelad identitet från en virtuell dator använder [az identitet för virtuell dator ta bort](/cli/azure/vm#az-vm-identity-remove). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. Den `<MSI NAME>` som det Användartilldelad identitet `name` egenskapen, som finns med i identitetsavsnittet i en virtuell dator med hjälp av `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```

Om den virtuella datorn inte har en systemtilldelad identitet och du vill ta bort alla användartilldelade identiteter från den, använder du följande kommando:

> [!NOTE]
> Värdet `none` är skiftlägeskänsligt. Det måste vara gemener.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.identityIds=null
```

Om den virtuella datorn har både systemtilldelad och användartilldelade identiteter, du kan ta bort alla användartilldelade identiteter genom att växla mellan för att använda endast system som har tilldelats. Ange följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Relaterat innehåll
- [Hanterad tjänstidentitet-översikt](overview.md)
- Fullständig Azure skapandet virtuella datorn Snabbstarter, finns här: 
  - [Skapa en Windows-dator med CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Skapa en Linux-dator med CLI](../../virtual-machines/linux/quick-create-cli.md) 

















