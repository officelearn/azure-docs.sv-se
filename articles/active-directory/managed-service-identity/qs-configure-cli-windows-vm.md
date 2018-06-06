---
title: Hur du konfigurerar system- och tilldelade identiteter på en virtuell Azure-dator med hjälp av Azure CLI
description: Steg för steg tilldelats instruktioner för konfiguration av en system- och en identiteter på en Azure VM med hjälp av Azure CLI.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 09ee4dfc403bf570631f64b0b13d1592a03eed17
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698976"
---
# <a name="configure-managed-service-identity-msi-on-an-azure-vm-using-azure-cli"></a>Konfigurera hanterade tjänsten identitet (MSI) på en virtuell Azure-dator med hjälp av Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du utför följande åtgärder för hanterade tjänstidentiteten på en Azure VM, med hjälp av Azure CLI:
- Aktivera och inaktivera tilldelats identiteten på en Azure VM
- Lägga till och ta bort en användare som tilldelats identiteten på en Azure VM

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan ett system som har tilldelats och användaren som har tilldelats identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra skriptexempel CLI har tre alternativ:

    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md) från Azure-portalen (se nästa avsnitt).
    - Använd inbäddade Azure Cloud Shell via försök ”knappen”, finns i det övre högra hörnet av varje kodblock.
    - [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda den lokala CLI-konsolen. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Tilldelats identitet

I det här avsnittet kan du lära dig hur du aktiverar och inaktiverar tilldelats identiteten på en virtuell Azure-dator med hjälp av Azure CLI.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Aktivera system för tilldelade identitet under skapandet av en Azure VM

Skapa en virtuell dator i Azure med tilldelats identitet aktiverad:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/reference-index#az_login). Använd ett konto som är associerade med Azure-prenumeration som du vill distribuera den virtuella datorn:

   ```azurecli-interactive
   az login
   ```

2. Skapa en [resursgruppen](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az gruppen skapa](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Skapa en virtuell dator med hjälp av [az vm skapa](/cli/azure/vm/#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVM* med en tilldelad systemidentitet, som begärs av den `--assign-identity` parameter. Den `--admin-username` och `--admin-password` parametrarna anger administrativa namnet och lösenordet användarkontot för virtuell dator-inloggning. Uppdatera dessa värden som passar din miljö: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Aktivera system för tilldelade identiteten på en befintlig virtuell Azure-dator

Om du behöver aktivera tilldelats identiteten på en befintlig virtuell dator:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/reference-index#az_login). Använd ett konto som är kopplad till den Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”:

   ```azurecli-interactive
   az login
   ```

2. Använd [az identitet tilldela](/cli/azure/vm/identity/#az_vm_identity_assign) med den `identity assign` kommandot aktivera Systemidentiteten som tilldelats till en befintlig virtuell dator:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Inaktivera tilldelats identitet från en Azure VM

> [!NOTE]
> Inaktivera hanterade tjänstidentiteten från en virtuell dator stöds inte för närvarande. Under tiden kan växla du mellan att använda System tilldelade och tilldelade användaridentiteter.

Om du har en virtuell dator som inte längre behöver tilldelats identitet men fortfarande måste användaren som har tilldelats identiteter, använder du följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```
Ta bort MSI VM-tillägget användaren `-n ManagedIdentityExtensionForWindows` eller `-n ManagedIdentityExtensionForLinux` växla (beroende på vilken typ av VM) med [az virtuella tillägget Ta bort](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Tilldelad användaridentitet

I det här avsnittet får lära du dig att lägga till och ta bort en användare som tilldelats identitet från en Azure-dator med hjälp av Azure CLI.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Tilldela en användare som tilldelats identitet under genereringen av en Azure VM

Det här avsnittet vägleder dig genom att skapa en virtuell dator med tilldelningen av en användare som tilldelats identitet. Om du redan har en virtuell dator som du vill använda, hoppa över det här avsnittet och gå vidare till nästa.

1. Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda. Skapa en [resursgruppen](~/articles/azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av din MSI med [az gruppen skapa](/cli/azure/group/#az_group_create). Se till att ersätta den `<RESOURCE GROUP>` och `<LOCATION>` parametervärden med egna värden. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Skapa en användare som tilldelats identitet med hjälp av [az identitet skapa](/cli/azure/identity#az_identity_create).  Den `-g` parametern anger resursgruppen där användaren som har tilldelats identitet skapas och `-n` parametern anger dess namn.    
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g myResourceGroup -n myUserAssignedIdentity
```
Svaret innehåller information för den tilldelade användaridentitet skapade liknar följande. Resurs-id-värdet som tilldelas användaren som har tilldelats identitet används i följande steg.

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

3. Skapa en virtuell dator med hjälp av [az vm skapa](/cli/azure/vm/#az_vm_create). I följande exempel skapas en virtuell dator som är associerade med den nya användare som har tilldelats identiteten som anges av den `--assign-identity` parameter. Se till att ersätta den `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>`, och `<MSI ID>` parametervärden med egna värden. För `<MSI ID>`, använda identiteten för användare som är tilldelad resursen `id` egenskapen skapade i föregående steg: 

```azurecli-interactive 
az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Tilldela en användare som tilldelats identitet till en befintlig virtuell Azure-dator

1. Skapa en användare som tilldelats identitet med hjälp av [az identitet skapa](/cli/azure/identity#az-identity-create).  Den `-g` parametern anger resursgruppen där användaren som har tilldelats identitet skapas och `-n` parametern anger dess namn. Se till att ersätta den `<RESOURCE GROUP>` och `<MSI NAME>` parametervärden med egna värden:

    > [!IMPORTANT]
    > Skapa användartilldelade identiteter med specialtecken (dvs understreck) i namnet stöds inte för närvarande. Använd alfanumeriska tecken. Sök igen efter uppdateringar.  Mer information finns i [vanliga frågor och kända problem](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Svaret innehåller information om användaren som har tilldelats MSI som skapats av följande slag. Resursen `id` värdet som tilldelas den tilldelade användaridentiteten används i följande steg.

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

2. Tilldela användaridentitet tilldelad till virtuell dator med hjälp av [az identitet tilldela](/cli/azure/vm#az-vm-identity-assign). Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med egna värden. Den `<MSI ID>` kommer att identiteten för användare som är tilldelad resursen `id` egenskapen som du skapade i föregående steg:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Ta bort en användare som tilldelats identitet från en Azure VM

> [!NOTE]
> Ta bort alla användartilldelade identiteter från en virtuell dator stöds för närvarande inte, såvida du inte har ett system som tilldelats identitet.

Om den virtuella datorn har flera tilldelade användaridentiteter, du kan ta bort alla utom den senaste en med hjälp av [az identitet ta bort](/cli/azure/vm#az-vm-identity-remove). Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med egna värden. Den `<MSI NAME>` blir identiteten användartilldelade `name` -egenskap som finns med i identitetsavsnittet i den virtuella datorn med hjälp av `az vm show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Om den virtuella datorn har både system som har tilldelats och användaren som har tilldelats identiteter kan du ta bort alla användaren som har tilldelats identiteter genom att använda endast de system som har tilldelats. Ange följande kommando:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Relaterat innehåll
- [Översikt över hanterade tjänstidentiteten](overview.md)
- För fullständig Azure VM skapa Snabbstart, se: 
  - [Skapa en Windows-dator med CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Skapa en virtuell Linux-dator med CLI](../../virtual-machines/linux/quick-create-cli.md) 

















