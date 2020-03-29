---
title: Konfigurera hanterade identiteter på skalningsuppsättning för virtuella datorer - Azure CLI - Azure AD
description: Steg för steg instruktioner för att konfigurera system och användartilldelade hanterade identiteter på en Azure virtuell dator skala uppsättning, med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
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
ms.openlocfilehash: 2832a8c584c0fbe707f22501809d772c6ffb970b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430094"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurera hanterade identiteter för Azure-resurser i en skala för virtuella datorer med Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du utför följande hanterade identiteter för Azure-resurser åtgärder på en Azure virtuell dator skalningsuppsättning, med hjälp av Azure CLI:
- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en Azure-skalningsuppsättning för virtuella datorer
- Lägga till och ta bort en användartilldelad hanterad identitet på en Azure-skalningsuppsättning för virtuella datorer


## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att utföra hanteringsåtgärderna i den här artikeln behöver ditt konto följande Azure-rollbaserade åtkomstkontrolltilldelningar:

    > [!NOTE]
    > Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

    - [Deltagare i virtuella](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) datorer för att skapa en skaluppsättning för virtuella datorer och aktivera och ta bort system- och/eller användartilldelade hanterade identitet från en skaluppsättning för virtuella datorer.
    - [Hanterad identitetsbidragstagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en användartilldelade hanterad identitet.
    - [Hanterad identitetsoperatorroll](/azure/role-based-access-control/built-in-roles#managed-identity-operator) för att tilldela och ta bort en användartilldelad hanterad identitet från och till en skaluppsättning för virtuella datorer.
- Om du vill köra CLI-skriptexemplen har du tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd det inbäddade Azure Cloud Shell via knappen "Prova", som finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda en lokal CLI-konsol. 
      
      > [!NOTE]
      > Kommandona har uppdaterats för att återspegla den senaste versionen av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den systemtilldelade hanterade identiteten för en Azure-skalningsuppsättning för virtuella datorer med Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterade identiteter när en Azure-skalningsuppsättning för virtuella datorer skapas

Så här skapar du en skalningsuppsättning för virtuella datorer med den systemtilldelade hanterade identiteten aktiverad:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med Azure-prenumerationen där du vill distribuera skaluppsättningen för den virtuella datorn:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgrupp](../../azure-resource-manager/management/overview.md#terminology) för inneslutning och distribution av skalningsuppsättningen för den virtuella datorn och dess relaterade resurser med hjälp av [az-gruppge](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en skalauppsättning för virtuella datorer med [az vmss create](/cli/azure/vmss/#az-vmss-create) . I följande exempel skapas en skalauppsättning för virtuella datorer med namnet *myVMSS* med en systemtilldelad hanterad identitet, enligt parametern. `--assign-identity` Parametrarna `--admin-username` och `--admin-password` anger namnet och lösenordet för administratörer för inloggning på den virtuella datorn. Uppdatera dessa värden baserat på din miljö: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterade identitet på en befintlig Azure-skalningsuppsättning för virtuella datorer

Om du behöver aktivera den systemtilldelade hanterade identiteten på en befintlig Azure-skalauppsättning för virtuella datorer:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med Azure-prenumerationen som innehåller skaluppsättningen för den virtuella datorn.

   ```azurecli-interactive
   az login
   ```

2. Använd kommandot [az vmss identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) för att aktivera en systemtilldelad hanterad identitet till en befintlig virtuell dator:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera systemtilldelade hanterade identitet från en Azure-skalningsuppsättning för virtuella datorer

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver den systemtilldelade hanterade identiteten, men fortfarande behöver användartilldelade hanterade identiteter, använder du följande kommando:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Om du har en virtuell dator som inte längre behöver systemtilldelade hanterade identiteter och den inte har några användartilldelade hanterade identiteter använder du följande kommando:

> [!NOTE]
> Värdet `none` är skiftlägeskänsligt. Det måste vara gemener. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>användartilldelade hanterade identitet

I det här avsnittet får du lära dig hur du aktiverar och tar bort en användartilldelad hanterad identitet med Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en användartilldelad hanterad identitet när en skaluppsättning för virtuella datorer skapas

I det här avsnittet får du hjälp med att skapa en skalningsuppsättning för virtuella datorer och tilldelning av en användartilldelad hanterad identitet till skaluppsättningen för den virtuella datorn. Om du redan har en skalningsuppsättning för den virtuella datorn som du vill använda hoppar du över det här avsnittet och fortsätter till nästa.

1. Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda. Skapa en [resursgrupp](~/articles/azure-resource-manager/management/overview.md#terminology) för inneslutning och distribution av din användartilldelade hanterade identitet med hjälp av [az-gruppge](/cli/azure/group/#az-group-create). Ersätt parametervärdena `<RESOURCE GROUP>` och `<LOCATION>` med dina egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create).  Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Svaret innehåller information om den användartilldelade hanterade identitet som skapats, ungefär som följande. `id` Resursvärdet som tilldelats den användartilldelade hanterade identiteten används i följande steg.

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

3. Skapa en skalauppsättning för virtuella datorer med [az vmss create](/cli/azure/vmss/#az-vmss-create). I följande exempel skapas en skalningsuppsättning för virtuella datorer som är `--assign-identity` associerad med den nya användartilldelade hanterade identiteten, enligt parametern. Ersätt parametervärdena `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` och `<USER ASSIGNED IDENTITY>` med dina egna värden. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Tilldela en användartilldelad hanterad identitet till en befintlig skaluppsättning för virtuella datorer

1. Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create).  Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Svaret innehåller information om den användartilldelade hanterade identitet som skapats, ungefär som följande.

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

2. Tilldela den användartilldelade hanterade identiteten till din virtuella datorskalauppsättning med az [vmss-identitetstilldelar](/cli/azure/vmss/identity). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY>` användartilldelade identitetens resursegenskap, `name` som skapats i föregående steg:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort en användartilldelad hanterad identitet från en Azure-skalningsuppsättning för virtuella datorer

Om du vill ta bort en användartilldelade hanterad identitet från en skalningsuppsättning för virtuella datorer använder du [az vmss-identiteten ta bort](/cli/azure/vmss/identity#az-vmss-identity-remove). Om detta är den enda användartilldelade hanterade identiteten som `UserAssigned` tilldelats skaluppsättningen för den virtuella datorn tas den bort från identitetstypsvärdet.  Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY>` användartilldelade hanterade identitetens `name` egenskap, som finns i identitetsavsnittet i den `az vmss identity show`virtuella datorns skaluppsättning med:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Om skaluppsättningen för den virtuella datorn inte har en systemtilldelad hanterad identitet och du vill ta bort alla användartilldelade hanterade identiteter från den använder du följande kommando:

> [!NOTE]
> Värdet `none` är skiftlägeskänsligt. Det måste vara gemener.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Om skaluppsättningen för virtuella datorer har både systemtilldelade och användartilldelade hanterade identiteter kan du ta bort alla användartilldelade identiteter genom att växla till att endast använda den systemtilldelade hanterade identiteten. Ange följande kommando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- För hela Azure virtual machine scale set creation Quickstart finns i: 

  - [Skapa en skaluppsättning för virtuella datorer med CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















