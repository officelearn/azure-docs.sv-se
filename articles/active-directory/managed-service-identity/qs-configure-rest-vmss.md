---
title: Så här konfigurerar du system- och användartilldelade identiteter på en Azure-VMSS med hjälp av REST
description: Steg för steg tilldelas instruktioner för att konfigurera en system- och en identiteter på en Azure-VMSS använder CURL för att göra REST API-anrop.
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
ms.date: 06/25/2018
ms.author: daveba
ms.openlocfilehash: 1f8c6d92cdce21d82ad51950ff3cba391153b76c
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187010"
---
# <a name="configure-managed-identity-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurera hanterade identiteter på en Virtual Machine Scale Sets med hjälp av REST API-anrop

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad identitet ger Azure-tjänster med en automatiskt hanterade systemidentitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du utför följande åtgärder för hanterade identiteter på en virtuell dator i skalningsuppsättningen använder CURL för att göra anrop till Azure Resource Manager REST-slutpunkten:

- Aktivera och inaktivera den systemtilldelade identiteten på en Azure VM-skalningsuppsättning
- Lägga till och ta bort en Användartilldelad identitet på en Azure VM-skalningsuppsättning

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande rolltilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:
    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en skalningsuppsättning för virtuell dator och aktivera och ta bort system som tilldelats hanterad identitet från en skalningsuppsättning för virtuell dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad identitet från och till en VM-skalningsuppsättning.
- Om du använder Windows kan du installera den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) eller Använd den [Azure Cloud Shell](../../cloud-shell/overview.md) i Azure-portalen.
- [Installera den lokala konsolen i Azure CLI](/azure/install-azure-cli), om du använder den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) eller en [Linux-distributionsoperativsystem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Om du använder Azure CLI lokalt, logga in på Azure med hjälp av `az login` med ett konto som är associerad med Azure-prenumeration som du vill hantera system eller användare användartilldelade identiteter.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Systemtilldelad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelad identitet på en VM-skalningsuppsättning med CURL för att göra anrop till Azure Resource Manager REST-slutpunkten.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Aktivera systemtilldelade identiteter under skapandet av en VM-skalningsuppsättning

Du behöver skapa en VM-skalningsuppsättning och hämta en åtkomsttoken för att använda CURL för att anropa Resource Manager-slutpunkten med systemtilldelad identitet TYPVÄRDE för att skapa en VM-skalningsuppsättning med systemtilldelad identitet som har aktiverats.

1. Skapa en [resursgrupp](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av virtual machine scale Sets och dess relaterade resurser, med hjälp av [az gruppen skapa](/cli/azure/group/#az_group_create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Skapa en [nätverksgränssnittet](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för VM-skalningsuppsättningar ställer du in:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en VM-skalningsuppsättning med CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en VM-skalningsuppsättning med namnet *myVMSS* i den *myResourceGroup* med en systemtilldelade identiteter, vilket identifieras i begärandetexten med värdet `"identity":{"type":"SystemAssigned"}`. Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade identiteter på en befintlig Azure VM-skalningsuppsättning

Om du vill aktivera systemtilldelad identitet på en befintlig VM-skalningsuppsättning, måste du hämta en åtkomsttoken och sedan använder vi CURL för att anropa Resource Manager REST-slutpunkt för att uppdatera identitetstypen.

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Använd följande CURL-kommando för att anropa Azure Resource Manager REST-slutpunkt för att aktivera systemtilldelade identiteter på VM-skalningsuppsättning som identifieras i begärandetexten av värdet `{"identity":{"type":"SystemAssigned"}` för en VM-skalningsuppsättning med namnet  *myVMSS*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.
   
   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort alla befintliga användare som tilldelats hanterade identiteter som är kopplade till virtuella datorns skalningsuppsättning, måste du lista användartilldelade identiteter med hjälp av den här CURL-kommando: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Om du har inga användartilldelade identiteter som tilldelats VM-skalningsuppsättningen som identifierades i den `identity` värde i svaret, hoppa till steg 3 som visar hur du behåller användartilldelade identiteter samtidigt systemtilldelade identiteter på din virtuella VM-skalningsuppsättningen.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Om du vill aktivera systemtilldelad identitet på en VM-skalningsuppsättning med befintliga användartilldelade identiteter, du måste lägga till `SystemAssigned` till den `type` värde.  
   
   Till exempel om virtual machine scale Sets har användartilldelade identiteter `ID1` och `ID2` tilldelade till den och du vill lägga till systemtilldelade identiteter till virtuella datorns skalningsuppsättning, använder du följande CURL-anrop. Ersätt `<ACCESS TOKEN>` och `<SUBSCRIPTION ID>` med värden som är lämpligt för din miljö.
   
   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera systemtilldelade identiteter från en Azure VM-skalningsuppsättning

Om du vill inaktivera en systemtilldelade identiteter på en befintlig VM-skalningsuppsättning, måste du hämta en åtkomsttoken och sedan använder vi CURL för att anropa Resource Manager REST-slutpunkt för att uppdatera identitetstypen till `None`.

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Uppdatera VM-skalningsuppsättningen in med CURL för att anropa Azure Resource Manager REST-slutpunkten för att inaktivera systemtilldelad identitet.  I följande exempel inaktiverar systemtilldelade identiteter som identifieras i begärandetexten av värdet `{"identity":{"type":"None"}}` från en VM-skalningsuppsättning med namnet *myVMSS*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.

   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort alla befintliga användare som tilldelats hanterade identiteter som är kopplade till virtuella datorns skalningsuppsättning, måste du lista användartilldelade identiteter med hjälp av den här CURL-kommando: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Om du har några Användartilldelad identitet som har tilldelats virtuella datorns skalningsuppsättning kan du hoppa till steg 3 som visar hur behåller användartilldelade identiteter vid borttagning av systemtilldelad identitet från virtual machine scale Sets.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Ta bort systemtilldelad identitet från en skalningsuppsättning för virtuella datorer som har användartilldelade identiteter genom att ta bort `SystemAssigned` från den `{"identity":{"type:" "}}` värdet medan den `UserAssigned` värde och `identityIds` matris den definierar vilka användartilldelade identiteter tilldelas till virtuella datorns skalningsuppsättning.

## <a name="user-assigned-identity"></a>Användartilldelad identitet

Du lär dig hur du lägger till och ta bort Användartilldelad identitet på en VM-skalningsuppsättning med CURL för att göra anrop till Azure Resource Manager REST-slutpunkten i det här avsnittet.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en Användartilldelad identitet när du skapar en VM-skalningsuppsättning

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Skapa en [nätverksgränssnittet](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för VM-skalningsuppsättningar ställer du in:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en Användartilldelad identitet med hjälp av anvisningarna som finns här: [skapa en användare som tilldelats hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Skapa en VM-skalningsuppsättning med CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en VM-skalningsuppsättning med namnet *myVMSS* i resursgruppen *myResourceGroup* med en Användartilldelad identitet `ID1`, vilket identifieras i begärandetexten av den värdet `"identity":{"type":"UserAssigned"}`. Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Tilldela en Användartilldelad identitet till en befintlig Azure VM-skalningsuppsättning

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Skapa en Användartilldelad identitet med hjälp av instruktionerna här [skapa en användare som tilldelats hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. För att säkerställa att du inte tar bort befintlig användare eller system tilldelats hanterade identiteter som är kopplade till virtuella datorns skalningsuppsättning kan du behöva den identitet som typer som tilldelats VM-skalningsuppsättningen in med hjälp av följande CURL-kommando. Om du har hanterade identiteter som tilldelats virtuella datorns skalningsuppsättning, de visas under i den `identity` värde.
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. Om du inte har några användare eller systemtilldelade identiteter som tilldelats din VM-skalningsuppsättning, kan du använda följande CURL-kommando för att anropa Azure Resource Manager REST-slutpunkt för att tilldela den första användaren som tilldelats virtuella datorns skalningsuppsättning identitet.  Om du har en användare eller system tilldelade identity(s) som tilldelats virtuella datorns skalningsuppsättning, kan du gå vidare till steg 5 som visar hur du lägger till flera användartilldelade identiteter i en VM-skalningsuppsättning och ger samtidigt systemtilldelade identiteter.

   I följande exempel tilldelar en Användartilldelad identitet `ID1` till en VM-skalningsuppsättning med namnet *myVMSS* i resursgruppen *myResourceGroup*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Om du har användare eller systemtilldelade identiteter som tilldelats din skalningsuppsättning för virtuell dator, måste du lägga till den nya Användartilldelad identitet som `identityIDs` matris samtidigt bibehåller ett användar- och systemtilldelade identiteter som för närvarande är kopplade till den virtuella VM-skalningsuppsättningen.

   Om du har system t.ex identitets- och Användartilldelad identitet `ID1` för närvarande tilldelats din skalningsuppsättning för virtuell dator och vill lägga till användaridentiteten `ID2` , använder du följande CURL-kommando. Ersätt `<ACCESS TOKEN>` med värdet du fick i steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Ta bort en Användartilldelad identitet från en VM-skalningsuppsättning

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. För att säkerställa att du inte tar bort alla befintliga användare som tilldelats hanterade identiteter som du vill behålla tilldelade till virtuella datorns skalningsuppsättning eller ta bort systemtilldelad identitet, behöver du visa en lista över hanterade identiteter med hjälp av följande CURL-kommando: 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   Om du har hanterade identiteter som tilldelas den virtuella datorn, de listas i svaret på den `identity` värde. 
    
   Exempel: Om du har användartilldelade identiteter `ID1` och `ID2` tilldelas till din skalningsuppsättning för virtuell dator och vill bara hålla `ID1` tilldelade och bibehålla systemtilldelade identiteter, skulle du använda samma CURL-kommando som tilldela en användare som tilldelats en virtuell datorskalning hanterad identitet ange behåller bara den `ID1` värde och hålla den `SystemAssigned` värde. Detta tar bort den `ID2` Användartilldelad identitet från VM-skalningsuppsättningen behåller sitt systemtilldelade identiteter.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Om din skalningsuppsättning för virtuell dator har både systemtilldelad och användartilldelade identiteter, du kan ta bort alla användartilldelade identiteter genom att växla mellan för att använda endast system som har tilldelats med hjälp av följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Om virtual machine scale Sets har bara användartilldelade identiteter och du vill ta bort alla, använder du följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skapa, visa eller ta bort användare som tilldelats med hjälp av REST finns i:

- [Skapa, visa eller ta bort en Användartilldelad identitet med hjälp av REST API-anrop](how-to-manage-ua-identity-rest.md)