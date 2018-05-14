---
title: Hur du konfigurerar system- och tilldelade identiteter på en Azure-VMSS med Azure CLI
description: Steg för steg tilldelats instruktioner för konfiguration av system- och en identiteter på en Azure-VMSS med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 4237371073669bd46c0da8f75998157aa54ffbf1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Konfigurera en virtuell dator skaluppsättning hanteras Service identitet (MSI) med hjälp av Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du utför följande åtgärder på ett Azure Virtual Machine Scale ange (VMSS), med hjälp av Azure CLI för hanterade tjänstidentiteten:
- Aktivera och inaktivera tilldelats identiteten på ett Azure-VMSS
- Lägga till och ta bort en användare som tilldelats identiteten på ett Azure-VMSS


## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan ett system som har tilldelats och användaren som har tilldelats identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

Om du vill köra skriptexempel CLI har tre alternativ:

- Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
- Använd inbäddade Azure Cloud Shell via försök ”knappen”, finns i det övre högra hörnet av varje kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Tilldelats identitet

I det här avsnittet kan du lära dig hur du aktiverar och inaktiverar tilldelats identitet för en Azure-VMSS med Azure CLI.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera tilldelade systemidentitet under skapandet av en skaluppsättning för virtuell Azure-dator

Om du vill skapa en virtuell dator skala inställd med tilldelats identitet aktiverad:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/reference-index#az_login). Använd ett konto som är associerade med Azure-prenumeration som du vill distribuera virtuella datorns skaluppsättning:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgruppen](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av skaluppsättning för virtuell dator och dess relaterade resurser med hjälp av [az gruppen skapa](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en virtuell dator-skala med [az vmss skapa](/cli/azure/vmss/#az_vmss_create) . I följande exempel skapas en virtuell dator skaluppsättningen namngivna *myVMSS* med en tilldelad systemidentitet, som begärs av den `--assign-identity` parameter. Den `--admin-username` och `--admin-password` parametrarna anger administrativa namnet och lösenordet användarkontot för virtuell dator-inloggning. Uppdatera dessa värden som passar din miljö: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera som tilldelats Systemidentiteten på en befintlig skaluppsättning för virtuell Azure-dator

Om du behöver aktivera tilldelats identiteten på en befintlig skaluppsättning för virtuell Azure-dator:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/reference-index#az_login). Använd ett konto som är kopplad till den Azure-prenumeration som innehåller virtuella datorns skaluppsättning.

   ```azurecli-interactive
   az login
   ```

2. Använd [az vmss identitet tilldela](/cli/azure/vmss/identity/#az_vmss_identity_assign) kommando för att aktivera en tilldelats identitet till en befintlig virtuell dator:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera tilldelade systemidentitet från en skaluppsättning för virtuell Azure-dator

> [!NOTE]
> Inaktivera hanterade tjänstidentiteten från Skalningsuppsättning en virtuell dator stöds inte för närvarande. Under tiden kan växla du mellan att använda System tilldelade och tilldelade användaridentiteter. Sök igen efter uppdateringar.

Om du har en skaluppsättning för virtuell dator som inte längre behöver ett system som tilldelats identitet, men fortfarande måste användaren som har tilldelats identiteter, utför du följande kommando:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='UserAssigned' 
```

Ta bort MSI VM-tillägget med [az vmss identitet ta bort](/cli/azure/vmss/identity/#az_vmss_remove_identity) kommando för att ta bort systemidentitet tilldelade från en VMSS:

   ```azurecli-interactive
   az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
   ```

## <a name="user-assigned-identity"></a>Användaren som har tilldelats identitet

Lär dig hur du aktiverar och ta bort en som tilldelats användaridentitet med hjälp av Azure CLI i det här avsnittet.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Tilldela en användare som tilldelats identitet under genereringen av en Azure-VMSS

Det här avsnittet vägleder dig genom att skapa en VMSS och tilldelning av en användare som tilldelats VMSS identitet. Om du redan har en VMSS som du vill använda, hoppa över det här avsnittet och gå vidare till nästa.

1. Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda. Skapa en [resursgruppen](~/articles/azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av din användartilldelade identitet med hjälp av [az gruppen skapa](/cli/azure/group/#az_group_create). Se till att ersätta den `<RESOURCE GROUP>` och `<LOCATION>` parametervärden med egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en användare som tilldelats identitet med hjälp av [az identitet skapa](/cli/azure/identity#az-identity-create).  Den `-g` parametern anger resursgruppen där användaren som har tilldelats identitet skapas och `-n` parametern anger dess namn. Se till att ersätta den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärden med egna värden:

    > [!IMPORTANT]
    > Skapa tilldelade användaridentiteter stöder endast alfanumeriska och bindestreck (0-9 eller a-z eller A-Z eller -) tecken. Namnet bör dessutom begränsas till 24 tecken för tilldelning till VM/VMSS ska fungera korrekt. Sök igen efter uppdateringar. Mer information finns i [vanliga frågor och kända problem](known-issues.md)


    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Svaret innehåller information för den tilldelade användaridentitet skapade liknar följande. Resursen `id` värdet som tilldelas den tilldelade användaridentiteten används i följande steg.

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

3. Skapa en VMSS med [az vmss skapa](/cli/azure/vmss/#az-vmss-create). I följande exempel skapas en VMSS som är associerade med den nya användare som har tilldelats identiteten som anges av den `--assign-identity` parameter. Se till att ersätta den `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>`, och `<USER ASSIGNED IDENTITY ID>` parametervärden med egna värden. För `<USER ASSIGNED IDENTITY ID>`, använda identiteten för användare som är tilldelad resursen `id` egenskapen skapade i föregående steg: 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Tilldela en användare som tilldelats identitet till en befintlig virtuell Azure-dator

1. Skapa en användare som tilldelats identitet med hjälp av [az identitet skapa](/cli/azure/identity#az-identity-create).  Den `-g` parametern anger resursgruppen där användaren som har tilldelats identitet skapas och `-n` parametern anger dess namn. Se till att ersätta den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärden med egna värden:

    > [!IMPORTANT]
    > Skapa användartilldelade identiteter med specialtecken (dvs understreck) i namnet stöds inte för närvarande. Använd alfanumeriska tecken. Sök igen efter uppdateringar.  Mer information finns i [vanliga frågor och kända problem](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Svaret innehåller information för den tilldelade användaridentitet skapade liknar följande. Resursen `id` värdet som tilldelas den tilldelade användaridentiteten används i följande steg.

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

2. Tilldela användaridentitet tilldelad till VMSS med hjälp av [az vmss identitet tilldela](/cli/azure/vmss/identity#az_vm_assign_identity). Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med egna värden. Den `<USER ASSIGNED IDENTITY ID>` kommer att identiteten för användare som är tilldelad resursen `id` egenskapen som du skapade i föregående steg:

    ```azurecli-interactive
    az vmss identity-assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Ta bort en användare som tilldelats identitet från en Azure-VMSS

> [!NOTE]
>  Ta bort alla användartilldelade identiteter från en Virtual Machine Scale Set stöds för närvarande inte, såvida du inte har ett system som tilldelats identitet. 

Om din VMSS har flera tilldelade användaridentiteter, du kan ta bort alla utom den senaste en med hjälp av [az vmss identitet ta bort](/cli/azure/vmss/identity#az-vmss-identity-remove). Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med egna värden. Den `<MSI NAME>` är identiteten för användaren som har tilldelats egenskapen name, som finns med i identitetsavsnittet i den virtuella datorn med hjälp av `az vm show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Om din VMSS har både system som har tilldelats och användaren som har tilldelats identiteter kan du ta bort alla användaren som har tilldelats identiteter genom att använda endast de system som har tilldelats. Ange följande kommando: 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade tjänstidentiteten](overview.md)
- För fullständig Azure virtuella datorn skapas Quickstart finns i avsnittet: 

  - [Skapa en Skaluppsättning för virtuell dator med CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















