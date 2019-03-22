---
title: Hur du konfigurerar system och användartilldelade hanterade identiteter på en virtuell dator i Azure-skala för att ställa in med Azure CLI
description: Steg för steg ange instruktioner för att konfigurera system- och användartilldelade hanterade identiteter på en virtuell dator i Azure-skala, med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88bcd38890baea2d6bc0460937fe4b7882f7fd23
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226054"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du utför följande hanterade identiteter för Azure-resurser på en Azure VM-skalningsuppsättning med Azure CLI:
- Aktivera och inaktivera systemtilldelade hanterad identitet på en Azure VM-skalningsuppsättning
- Lägga till och ta bort en Användartilldelad hanterad identitet på en Azure VM-skalningsuppsättning


## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande Azure rollbaserad åtkomstkontroll tilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:

    > [!NOTE]
    > Inga ytterligare Azure AD directory rolltilldelningar krävs.

    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en skalningsuppsättning för virtuell dator och aktivera och ta bort system och/eller användartilldelade hanterad identitet från en skalningsuppsättning för virtuell dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad hanterad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad hanterad identitet från och till en VM-skalningsuppsättning.
- Om du vill köra CLI-exempelskript, finns det tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd inbäddad Azure Cloud Shell via ”Prova” knappen, finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. 
      
      > [!NOTE]
      > Kommandon har uppdaterats för att återspegla den senaste versionen av den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelade hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelade hanterad identitet för en Azure VM-skalningsuppsättning med Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterad identitet under skapandet av en Azure VM-skalningsuppsättning

Skapa en VM-skalningsuppsättning med systemtilldelade hanterade identiteten aktiverat:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerade med Azure-prenumerationen som du vill distribuera virtuella datorns skalningsuppsättning:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgrupp](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av virtual machine scale Sets och dess relaterade resurser, med hjälp av [az gruppen skapa](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en VM-skalningsuppsättning med hjälp av [az vmss skapa](/cli/azure/vmss/#az-vmss-create) . I följande exempel skapas en VM-skalningsuppsättning med namnet *myVMSS* med en automatiskt genererad hanterad identitet, enligt en förfrågan från den `--assign-identity` parametern. Parametrarna `--admin-username` och `--admin-password` anger namnet och lösenordet för administratörer för inloggning på den virtuella datorn. Uppdatera dessa värden baserat på din miljö: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterad identitet på en befintlig Azure VM-skalningsuppsättning

Om du vill aktivera hanterade systemtilldelade identiteter på en befintlig Azure VM-skalningsuppsättning:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerad med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.

   ```azurecli-interactive
   az login
   ```

2. Använd [az vmss-identitet tilldela](/cli/azure/vmss/identity/#az-vmss-identity-assign) kommando för att aktivera en automatiskt genererad hanterad identitet till en befintlig virtuell dator:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera systemtilldelade hanterad identitet från en Azure VM-skalningsuppsättning

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver systemtilldelade hanterad identitet, men fortfarande ha användartilldelade hanterade identiteter, använder du följande kommando:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Om du har en virtuell dator som inte längre behöver systemtilldelade hanterad identitet och har inga hanterade användartilldelade identiteter, använder du följande kommando:

> [!NOTE]
> Värdet `none` är skiftlägeskänsligt. Det måste vara gemener. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

> [!NOTE]
> Om du har etablerat den hanterade identitet för VM-tillägg för Azure-resurser (för att bli inaktuell) kan du behöva ta bort den med [az vmss tillägget delete](https://docs.microsoft.com/cli/azure/vm/). Mer information finns i [migrera från VM-tillägg till Azure IMDS för autentisering](howto-migrate-vm-extension.md).

## <a name="user-assigned-managed-identity"></a>Användartilldelade hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och ta bort en Användartilldelad hanterad identitet med hjälp av Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en hanterad Användartilldelad identitet när du skapar en VM-skalningsuppsättning

Det här avsnittet vägleder dig genom skapandet av en VM-skalningsuppsättning och tilldelningen av en hanterad Användartilldelad identitet till virtuella datorns skalningsuppsättning. Om du redan har en skalningsuppsättning för virtuella datorer som du vill använda kan du hoppa över det här avsnittet och gå vidare till nästa.

1. Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda. Skapa en [resursgrupp](~/articles/azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av din användartilldelade hanterad identitet, med hjälp av [az gruppen skapa](/cli/azure/group/#az-group-create). Ersätt parametervärdena `<RESOURCE GROUP>` och `<LOCATION>` med dina egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create).  Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Svaret innehåller information om det användartilldelade hanterad identitet skapas, liknar följande. Resursen `id` värdet för den hanterade Användartilldelad identitet används i följande steg.

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

3. Skapa en VM-skalningsuppsättning med hjälp av [az vmss skapa](/cli/azure/vmss/#az-vmss-create). I följande exempel skapas en skalningsuppsättning för virtuell dator som är associerade med det nya användartilldelade hanterad identitet, enligt den `--assign-identity` parametern. Ersätt parametervärdena `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` och `<USER ASSIGNED IDENTITY>` med dina egna värden. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Tilldela en hanterad Användartilldelad identitet till en befintlig VM-skalningsuppsättning

1. Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create).  Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Svaret innehåller information om det användartilldelade hanterad identitet skapas, liknar följande.

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

2. Tilldela hanterade Användartilldelad identitet till VM-skalningsuppsättning som anges med [az vmss-identitet tilldela](/cli/azure/vmss/identity). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY>` är identiteten för användartilldelade resurs `name` egenskapen, som du skapade i föregående steg:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort en hanterad Användartilldelad identitet från en Azure VM-skalningsuppsättning

Ta bort en hanterad Användartilldelad identitet från en VM scale set användning [az vmss-identitet ta bort](/cli/azure/vmss/identity#az-vmss-identity-remove). Om det här är den enda användartilldelade hanterade identiteten som tilldelats virtuella datorns skaluppsättning `UserAssigned` tas bort från TYPVÄRDE identitet.  Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY>` blir användartilldelade hanterade identitetens `name` egenskapen, som finns i identitetsavsnittet i VM-skaluppsättning som anges med `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Om din skalningsuppsättning för virtuell dator inte har en automatiskt genererad hanterad vill identitet och du ta bort alla användartilldelade hanterade identiteter från den, använder du följande kommando:

> [!NOTE]
> Värdet `none` är skiftlägeskänsligt. Det måste vara gemener.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Om din VM-skalningsuppsättningen har båda systemtilldelade och användartilldelade hanterade identiteter kan du ta bort alla användartilldelade identiteter genom att växla mellan att använda endast systemtilldelade hanterad identitet. Ange följande kommando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Nästa steg

- [Hanterade identiteter för översikt över Azure-resurser](overview.md)
- För fullständig Azure VM-skalningsuppsättningen skapas Snabbstart, se: 

  - [Skapa en Skalningsuppsättning för virtuell dator med CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















