---
title: Så här konfigurerar du system- och användartilldelade identiteter på en Azure-VMSS med Azure CLI
description: Steg för steg tilldelas instruktioner för konfiguration av system- och en identiteter på en Azure-VMSS med Azure CLI.
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: c90ab99908f2fd8095eca1a8d58582fb339362be
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187987"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure CLI-skalningsuppsättning

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du utför följande åtgärder på en Azure VM Scale ange (VMSS), med hjälp av Azure CLI för hanterad tjänstidentitet:
- Aktivera och inaktivera systemtilldelad identitet i en Azure VMSS
- Lägga till och ta bort en Användartilldelad identitet i en Azure VMSS


## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande rolltilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:
    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en skalningsuppsättning för virtuell dator och aktivera och ta bort systemtilldelad hanterad identitet från en skalningsuppsättning för virtuell dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad identitet från och till en VM-skalningsuppsättning.
- Om du vill köra CLI-exempelskript, finns det tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd inbäddad Azure Cloud Shell via ”Prova” knappen, finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Systemtilldelad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelad identitet för en Azure-VMSS med Azure CLI.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade identiteter under skapandet av en Azure VM-skalningsuppsättning

Skapa en VM-skalningsuppsättning med systemtilldelad identitet som aktiverat:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az_login). Använd ett konto som är associerade med Azure-prenumerationen som du vill distribuera virtuella datorns skalningsuppsättning:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgrupp](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av virtual machine scale Sets och dess relaterade resurser, med hjälp av [az gruppen skapa](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en VM-skalningsuppsättning med hjälp av [az vmss skapa](/cli/azure/vmss/#az_vmss_create) . I följande exempel skapas en VM-skalningsuppsättning med namnet *myVMSS* med en systemtilldelade identiteter, enligt en förfrågan från den `--assign-identity` parametern. Parametrarna `--admin-username` och `--admin-password` anger namnet och lösenordet för administratörer för inloggning på den virtuella datorn. Uppdatera dessa värden baserat på din miljö: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade identiteter på en befintlig Azure VM-skalningsuppsättning

Om du vill aktivera den systemtilldelade identiteten på en befintlig Azure VM-skalningsuppsättning:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az_login). Använd ett konto som är associerad med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.

   ```azurecli-interactive
   az login
   ```

2. Använd [az vmss-identitet tilldela](/cli/azure/vmss/identity/#az_vmss_identity_assign) kommando för att aktivera en systemtilldelad identitet till en befintlig virtuell dator:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera systemtilldelade identiteter från en Azure VM-skalningsuppsättning

> [!NOTE]
> Inaktiverar hanterad tjänstidentitet från en Virtual Machine Scale Sets stöds för närvarande inte. Under tiden kan växla du mellan att använda System tilldelas och tilldelade användaridentiteter. Kom tillbaka om för att få uppdateringar.

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver en systemtilldelad identitet, men fortfarande ha användartilldelade identiteter, utför du följande kommando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='UserAssigned' 
```

Ta bort MSI VM-tillägget med [az vmss-identitet ta bort](/cli/azure/vmss/identity/#az_vmss_remove_identity) till att ta bort systemtilldelade identiteter från en Skalningsuppsättningen:

   ```azurecli-interactive
   az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
   ```

## <a name="user-assigned-identity"></a>Användartilldelad identitet

I det här avsnittet får du lära dig hur du aktiverar och ta bort en Användartilldelad identitet med hjälp av Azure CLI.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Tilldela Användartilldelad identitet när du skapar en Azure VMSS

Det här avsnittet vägleder dig genom skapandet av en VMSS och tilldelningen av en användare som tilldelats VMSS identitet. Om du redan har en VMSS som du vill använda kan du hoppa över det här avsnittet och gå vidare till nästa.

1. Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda. Skapa en [resursgrupp](~/articles/azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av din Användartilldelad identitet med hjälp av [az gruppen skapa](/cli/azure/group/#az_group_create). Ersätt parametervärdena `<RESOURCE GROUP>` och `<LOCATION>` med dina egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en Användartilldelad identitet med hjälp av [az identitet skapa](/cli/azure/identity#az-identity-create).  Den `-g` parametern anger resursgruppen där Användartilldelad identitet skapas, och `-n` parametern anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Svaret innehåller information om Användartilldelad identitet skapas, liknar följande. Resursen `id` värde som tilldelats Användartilldelad identitet som ska användas i följande steg.

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

3. Skapa en VMSS med [az vmss skapa](/cli/azure/vmss/#az-vmss-create). I följande exempel skapas en VMSS som är associerade med den nya användartilldelade-identiteten som anges av den `--assign-identity` parametern. Ersätt parametervärdena `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` och `<USER ASSIGNED IDENTITY ID>` med dina egna värden. För `<USER ASSIGNED IDENTITY ID>`, använda Användartilldelad identitet resurs `id` egenskap som skapats i föregående steg: 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Tilldela Användartilldelad identitet till en befintlig Azure VM

1. Skapa en Användartilldelad identitet med hjälp av [az identitet skapa](/cli/azure/identity#az-identity-create).  Den `-g` parametern anger resursgruppen där Användartilldelad identitet skapas, och `-n` parametern anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

    > [!IMPORTANT]
    > Skapa användartilldelade identiteter med specialtecken (t.ex. understreck) i namnet stöds inte för närvarande. Använd alfanumeriska tecken. Kom tillbaka om för att få uppdateringar.  Mer information finns i [vanliga frågor och kända problem](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Svaret innehåller information om Användartilldelad identitet skapas, liknar följande. Resursen `id` värde som tilldelats Användartilldelad identitet som ska användas i följande steg.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Tilldela Användartilldelad identitet till VMSS med [az vmss-identitet tilldela](/cli/azure/vmss/identity#az_vm_assign_identity). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VMSS NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY ID>` kommer att identiteten för användare som är tilldelad resursen `id` egenskapen, som du skapade i föregående steg:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Ta bort Användartilldelad identitet från en Azure VMSS

> [!NOTE]
>  Ta bort alla användartilldelade identiteter från en Virtual Machine Scale Sets stöds för närvarande inte, om du inte har en systemtilldelad identitet. 

Om din VMSS har flera användartilldelade identiteter kan du ta bort alla utom den avslutande med [az vmss-identitet ta bort](/cli/azure/vmss/identity#az-vmss-identity-remove). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VMSS NAME>` med dina egna värden. Den `<MSI NAME>` är Användartilldelad identitet namnegenskapen som finns med i identitetsavsnittet i en virtuell dator med hjälp av `az vm show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <MSI NAME>
```
Om din VMSS har både systemtilldelad och användartilldelade identiteter, du kan ta bort alla användartilldelade identiteter genom att växla mellan för att använda endast system som har tilldelats. Ange följande kommando: 

```azurecli-interactive
az vmss update -n <VMSS NAME> -g <RESOURCE GROUP> --set identity.type='SystemAssigned' identity.identityIds=null
```

## <a name="next-steps"></a>Nästa steg

- [Hanterad tjänstidentitet-översikt](overview.md)
- För fullständig Azure VM-skalningsuppsättningen skapas Snabbstart, se: 

  - [Skapa en Skalningsuppsättning för virtuell dator med CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















