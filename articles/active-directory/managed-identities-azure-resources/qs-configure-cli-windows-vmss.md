---
title: Konfigurera hanterade identiteter på skalnings uppsättningen för virtuella datorer – Azure CLI – Azure AD
description: Stegvisa instruktioner för att konfigurera system-och användarspecifika hanterade identiteter på en skalnings uppsättning för virtuella Azure-datorer med Azure CLI.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430094"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurera hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer med Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du utför följande hanterade identiteter för Azure-resurser på en skalnings uppsättning i en virtuell Azure-dator med hjälp av Azure CLI:
- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en skalnings uppsättning för virtuella Azure-datorer
- Lägga till och ta bort en användardefinierad hanterad identitet på en virtuell Azure-dators skalnings uppsättning


## <a name="prerequisites"></a>Krav

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att utföra hanterings åtgärderna i den här artikeln måste ditt konto ha följande Azure Role-baserade åtkomst kontroll tilldelningar:

    > [!NOTE]
    > Inga ytterligare Azure AD Directory-roll tilldelningar krävs.

    - [Virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) för att skapa en skalnings uppsättning för virtuella datorer och aktivera och ta bort system-och/eller användarspecifika hanterade identiteter från en skalnings uppsättning för virtuella datorer.
    - Rollen [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) för att skapa en användardefinierad hanterad identitet.
    - [Hanterad identitet operatörs](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll för att tilldela och ta bort en användardefinierad hanterad identitet från och till en skalnings uppsättning för virtuella datorer.
- Om du vill köra CLI-exempelskript, finns det tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd inbäddad Azure Cloud Shell via ”Prova” knappen, finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda en lokal CLI-konsol. 
      
      > [!NOTE]
      > Kommandona har uppdaterats för att återspegla den senaste versionen av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den systemtilldelade hanterade identiteten för en skalnings uppsättning för en virtuell Azure-dator med hjälp av Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelad hanterad identitet när en skalnings uppsättning för en virtuell Azure-dator skapas

Så här skapar du en skalnings uppsättning för virtuella datorer med den systemtilldelade hanterade identiteten aktive rad:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med den Azure-prenumeration under vilken du vill distribuera den virtuella datorns skal uppsättning:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resurs grupp](../../azure-resource-manager/management/overview.md#terminology) för inne slutning och distribution av den virtuella datorns skalnings uppsättning och dess relaterade resurser med [AZ Group Create](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resurs grupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en skalnings uppsättning för virtuell dator med [AZ VMSS Create](/cli/azure/vmss/#az-vmss-create) . I följande exempel skapas en skalnings uppsättning för virtuella datorer med namnet *myVMSS* med en systemtilldelad hanterad identitet, enligt begäran från parametern `--assign-identity`. Parametrarna `--admin-username` och `--admin-password` anger namnet och lösenordet för administratörer för inloggning på den virtuella datorn. Uppdatera dessa värden baserat på din miljö: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelad hanterad identitet på en befintlig skalnings uppsättning för virtuella Azure-datorer

Om du behöver aktivera den systemtilldelade hanterade identiteten på en befintlig skalnings uppsättning för virtuella Azure-datorer:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorns skalnings uppsättning.

   ```azurecli-interactive
   az login
   ```

2. Använd kommandot [AZ VMSS Identity Assign](/cli/azure/vmss/identity/#az-vmss-identity-assign) för att aktivera en systemtilldelad hanterad identitet till en befintlig virtuell dator:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera systemtilldelad hanterad identitet från en skalnings uppsättning för virtuella Azure-datorer

Om du har en skalnings uppsättning för virtuella datorer som inte längre behöver den systemtilldelade hanterade identiteten, men fortfarande behöver användare tilldelade hanterade identiteter, använder du följande kommando:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Om du har en virtuell dator som inte längre behöver systemtilldelad hanterad identitet och den inte har några användare tilldelade hanterade identiteter, använder du följande kommando:

> [!NOTE]
> Värdet `none` är Skift läges känsligt. Det måste vara i gemener. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>användardefinierad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och tar bort en användardefinierad hanterad identitet med hjälp av Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en användardefinierad hanterad identitet när en skalnings uppsättning för virtuell dator skapas

Det här avsnittet beskriver hur du skapar en skalnings uppsättning för virtuella datorer och tilldelning av en användardefinierad hanterad identitet till den virtuella datorns skal uppsättning. Om du redan har en skalnings uppsättning för virtuella datorer som du vill använda hoppar du över det här avsnittet och fortsätter till nästa.

1. Du kan hoppa över det här steget om du redan har en resurs grupp som du vill använda. Skapa en [resurs grupp](~/articles/azure-resource-manager/management/overview.md#terminology) för inne slutning och distribution av din användarspecifika hanterade identitet med hjälp av [AZ Group Create](/cli/azure/group/#az-group-create). Ersätt parametervärdena `<RESOURCE GROUP>` och `<LOCATION>` med dina egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create).  Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Svaret innehåller information om den användardefinierade hanterade identiteten som skapats, ungefär så här. Resurs `id` svärdet som tilldelats den användarspecifika hanterade identiteten används i följande steg.

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

3. Skapa en skalnings uppsättning för virtuell dator med [AZ VMSS Create](/cli/azure/vmss/#az-vmss-create). I följande exempel skapas en skalnings uppsättning för virtuella datorer som är associerad med den nya användarspecifika hanterade identiteten, som anges i parametern `--assign-identity`. Ersätt parametervärdena `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` och `<USER ASSIGNED IDENTITY>` med dina egna värden. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Tilldela en användardefinierad hanterad identitet till en befintlig skalnings uppsättning för virtuella datorer

1. Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create).  Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn. Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Svaret innehåller information om den användardefinierade hanterade identiteten som skapats, ungefär så här.

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

2. Tilldela den användarspecifika hanterade identiteten till den virtuella datorns skalnings uppsättning med [AZ VMSS Identity Assign](/cli/azure/vmss/identity). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. `<USER ASSIGNED IDENTITY>` är den användarens resurs `name` egenskap som skapades i föregående steg:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort en användardefinierad hanterad identitet från en skalnings uppsättning för virtuella Azure-datorer

Ta bort en användardefinierad hanterad identitet från en virtuell dators skalnings uppsättning Använd [AZ VMSS Identity Remove](/cli/azure/vmss/identity#az-vmss-identity-remove). Om det här är den enda tilldelade hanterade identiteten som är kopplad till den virtuella datorns skal uppsättning, tas `UserAssigned` bort från identitets typens värde.  Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY>` kommer att vara `name` egenskap som tilldelats av användaren, som finns i avsnittet identitet i den virtuella datorns skalnings uppsättning med hjälp av `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Om din skalnings uppsättning för den virtuella datorn inte har en systemtilldelad hanterad identitet och du vill ta bort alla tilldelade hanterade identiteter från den, använder du följande kommando:

> [!NOTE]
> Värdet `none` är Skift läges känsligt. Det måste vara i gemener.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Om den virtuella datorns skalnings uppsättning har både systemtilldelade och användarspecifika hanterade identiteter kan du ta bort alla tilldelade identiteter genom att växla till Använd endast systemtilldelad hanterad identitet. Ange följande kommando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Nästa steg

- [Hanterade identiteter för översikt över Azure-resurser](overview.md)
- Den fullständiga snabb starten för att skapa virtuella Azure-datorer finns i: 

  - [Skapa en skalnings uppsättning för virtuell dator med CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















