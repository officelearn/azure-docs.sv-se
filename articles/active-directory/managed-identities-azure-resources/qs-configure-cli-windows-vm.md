---
title: Konfigurera system-och användarspecifika hanterade identiteter på en virtuell Azure-dator med Azure CLI
description: Steg för steg-instruktioner för att konfigurera system-och användarspecifika hanterade identiteter på en virtuell Azure-dator med Azure CLI.
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
ms.openlocfilehash: 272315346091bacb15aef02184e1cc72d64ed49d
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309806"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln använder du Azure CLI för att lära dig hur du utför följande hanterade identiteter för Azure-resurser på en virtuell Azure-dator:

- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en virtuell Azure-dator
- Lägga till och ta bort en användardefinierad hanterad identitet på en virtuell Azure-dator

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)** .
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra CLI-exempelskript, finns det tre alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd inbäddad Azure Cloud Shell via ”Prova” knappen, finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) om du föredrar att använda en lokal CLI-konsol. 
      
      > [!NOTE]
      > Kommandona har uppdaterats för att återspegla den senaste versionen av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den systemtilldelade hanterade identiteten på en virtuell Azure-dator med Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivera systemtilldelad hanterad identitet när en virtuell Azure-dator skapas

För att skapa en virtuell Azure-dator med den systemtilldelade hanterade identiteten måste ditt konto ha roll tilldelningen [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med den Azure-prenumeration som du vill distribuera den virtuella datorn i:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgrupp](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az group create](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en virtuell dator med [az vm create](/cli/azure/vm/#az-vm-create). I följande exempel skapas en virtuell dator med namnet *myVM* med en tilldelad hanterad identitet, enligt `--assign-identity` begäran från parametern. Parametrarna `--admin-username` och `--admin-password` anger namnet och lösenordet för administratörer för inloggning på den virtuella datorn. Uppdatera dessa värden baserat på din miljö: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Aktivera systemtilldelad hanterad identitet på en befintlig virtuell Azure-dator

Om du vill aktivera systemtilldelad hanterad identitet på en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med den Azure-prenumeration som innehåller den virtuella datorn.

   ```azurecli-interactive
   az login
   ```

2. Använd [AZ VM Identity tilldela](/cli/azure/vm/identity/) med `identity assign` kommandot Aktivera den systemtilldelade identiteten till en befintlig virtuell dator:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Inaktivera systemtilldelad identitet från en virtuell Azure-dator

Om du vill inaktivera systemtilldelad hanterad identitet på en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

Om du har en virtuell dator som inte längre behöver den systemtilldelade identiteten, men fortfarande behöver användare tilldelade identiteter, använder du följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Använd följande kommando om du har en virtuell dator som inte längre behöver en tilldelad identitet och som inte har några användarspecifika identiteter:

> [!NOTE]
> Värdet `none` är Skift läges känsligt. Det måste vara i gemener. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort en användardefinierad hanterad identitet från en virtuell Azure-dator med hjälp av Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Tilldela en användardefinierad hanterad identitet när en virtuell Azure-dator skapas

För att tilldela en användardefinierad identitet till en virtuell dator när den skapas, måste ditt konto ha roll tilldelningarna [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitets operatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Du kan hoppa över det här steget om du redan har en resurs grupp som du vill använda. Skapa en [resurs grupp](~/articles/azure-resource-manager/resource-group-overview.md#terminology) för inne slutning och distribution av din användarspecifika hanterade identitet med hjälp av [AZ Group Create](/cli/azure/group/#az-group-create). Ersätt parametervärdena `<RESOURCE GROUP>` och `<LOCATION>` med dina egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en användartilldelad hanterad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create).  Parametern `-g` anger resursgruppen där den användartilldelade hanterade identiteten skapas, och parametern `-n` anger dess namn.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Svaret innehåller information om den användardefinierade hanterade identiteten som skapats, ungefär så här. Resurs-ID-värdet som tilldelats den användarspecifika hanterade identiteten används i följande steg.

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

3. Skapa en virtuell dator med [az vm create](/cli/azure/vm/#az-vm-create). I följande exempel skapas en virtuell dator som är associerad med den nya användardefinierade identiteten, som anges `--assign-identity` av parametern. Ersätt parametervärdena `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Tilldela en användardefinierad hanterad identitet till en befintlig virtuell Azure-dator

För att tilldela en användardefinierad identitet till en virtuell dator måste ditt konto ha roll tilldelningarna [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitets operatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Inga ytterligare roll tilldelningar för Azure AD-katalogen krävs.

1. Skapa en användartilldelad identitet med hjälp av [az identity create](/cli/azure/identity#az-identity-create).  Parametern anger resurs gruppen där den användardefinierade identiteten skapas `-n` och parametern anger dess namn. `-g` Ersätt parametervärdena `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden:

    > [!IMPORTANT]
    > Det finns för närvarande inte stöd för att skapa användare som tilldelats hanterade identiteter med specialtecken (dvs under streck) i namnet. Använd alfanumeriska tecken. Kom tillbaka om för att få uppdateringar.  Mer information finns i [vanliga frågor och svar](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Svaret innehåller information om den användardefinierade hanterade identiteten som skapats, ungefär så här. 

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

2. Tilldela den användarspecifika identiteten till den virtuella datorn med [AZ VM Identity Assign](/cli/azure/vm). Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. `<USER ASSIGNED IDENTITY NAME>` Är resurs`name` egenskapen för den användare som tilldelats den hanterade identiteten som skapades i föregående steg:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användare som tilldelats en hanterad identitet från en virtuell Azure-dator

För att ta bort en tilldelad identitet till en virtuell dator måste ditt konto ha roll tilldelningen [virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) . 

Om det här är den enda tilldelade hanterade identiteten som är kopplad till den `UserAssigned` virtuella datorn, tas den bort från identitets typens värde.  Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. Användaren tilldelas identitetens egenskap, som finns i identitets avsnittet på den virtuella datorn med `az vm identity show`: `name` `<USER ASSIGNED IDENTITY>`

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Om den virtuella datorn inte har en systemtilldelad hanterad identitet och du vill ta bort alla användare som tilldelats identiteter från den, använder du följande kommando:

> [!NOTE]
> Värdet `none` är Skift läges känsligt. Det måste vara i gemener.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Om den virtuella datorn har både systemtilldelade och användarspecifika identiteter, kan du ta bort alla tilldelade identiteter genom att växla till Använd endast tilldelade system. Ange följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Nästa steg
- [Hanterade identiteter för översikt över Azure-resurser](overview.md)
- De fullständiga snabb starterna för att skapa virtuella Azure-datorer finns i: 
  - [Skapa en virtuell Windows-dator med CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Skapa en virtuell Linux-dator med CLI](../../virtual-machines/linux/quick-create-cli.md) 

















