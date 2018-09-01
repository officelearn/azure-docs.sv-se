---
title: Hur du konfigurerar system- och användartilldelade hanterade identiteter på en Azure-VMSS med hjälp av REST
description: Steg för steg instruktioner för att konfigurera ett system och användartilldelade hanterade identiteter på en Azure-VMSS med CURL för att göra REST API-anrop.
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
ms.openlocfilehash: 8159b6aa5b13ee9507fdc22e7c54bcf36870e242
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43339442"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med hjälp av REST API-anrop

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterade systemidentitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln använder CURL för att göra anrop till Azure Resource Manager REST-slutpunkt du lära dig hur du utför följande hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning:

- Aktivera och inaktivera systemtilldelade hanterad identitet på en Azure VM-skalningsuppsättning
- Lägga till och ta bort en Användartilldelad hanterad identitet på en Azure VM-skalningsuppsättning

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande rolltilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:
    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) skapa VM scale Sets och aktivera och ta bort system och/eller användartilldelade hanterad identitet från en skalningsuppsättning för virtuell dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad hanterad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad identitet från och till en VM-skalningsuppsättning.
- Om du använder Windows kan du installera den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) eller Använd den [Azure Cloud Shell](../../cloud-shell/overview.md) i Azure-portalen.
- [Installera den lokala konsolen i Azure CLI](/cli/azure/install-azure-cli), om du använder den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) eller en [Linux-distributionsoperativsystem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Om du använder Azure CLI lokalt, logga in på Azure med hjälp av `az login` med ett konto som är associerad med Azure-prenumeration du vill hantera system eller användartilldelade hanterade identiteter.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelade hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelade hanterad identitet på en VM-skalningsuppsättning med CURL för att göra anrop till Azure Resource Manager REST-slutpunkten.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterad identitet när du skapar en VM-skalningsuppsättning

För att skapa en VM-skalningsuppsättning med systemtilldelade hanterad identitet aktiverat, måste du skapa en VM-skalningsuppsättning och hämta en åtkomsttoken för att använda CURL för att anropa Resource Manager-slutpunkten med TYPVÄRDE systemtilldelade hanterad identitet.

1. Skapa en [resursgrupp](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av virtual machine scale Sets och dess relaterade resurser, med hjälp av [az gruppen skapa](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Skapa en [nätverksgränssnittet](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för VM-skalningsuppsättningar ställer du in:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en VM-skalningsuppsättning med CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en VM-skalningsuppsättning med namnet *myVMSS* i den *myResourceGroup* med en automatiskt genererad hanterad identitet, vilket identifieras i begärandetexten med värdet `"identity":{"type":"SystemAssigned"}`. Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-managed-identity-on-a-existing-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterad identitet på en befintlig skalningsuppsättning för virtuella datorer

Om du vill aktivera systemtilldelade hanterad identitet på en befintlig VM-skalningsuppsättning, måste du hämta en åtkomsttoken och sedan använder vi CURL för att anropa Resource Manager REST-slutpunkt för att uppdatera identitetstypen.

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Använd följande CURL-kommando för att anropa Azure Resource Manager REST-slutpunkt för att aktivera systemtilldelade hanterad identitet på VM-skalningsuppsättning som identifieras i begärandetexten av värdet `{"identity":{"type":"SystemAssigned"}` för en VM-skalningsuppsättning med namnet *myVMSS*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.
   
   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort alla befintliga användartilldelade hanterade identiteter som är kopplade till virtuella datorns skalningsuppsättning, måste du lista de hanterade användartilldelade identiteterna med hjälp av den här CURL-kommando: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Om du har några användartilldelade hanterade identiteter som tilldelats VM-skalningsuppsättningen som identifierades i den `identity` värde i svaret, hoppa till steg 3 som visar hur du behåller användartilldelade hanterade identiteter samtidigt systemtilldelade hanterad identitet på din skalningsuppsättning för virtuell dator.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Om du vill aktivera systemtilldelade hanterad identitet på en VM-skalningsuppsättning med befintliga användartilldelade hanterade identiteter, du måste lägga till `SystemAssigned` till den `type` värde.  
   
   Till exempel om din VM-skalningsuppsättningen har den Användartilldelad hanterade identiteter `ID1` och `ID2` tilldelade till den och du vill lägga till systemtilldelade hanterad identitet till virtuella datorns skalningsuppsättning, använder du följande CURL-anrop. Ersätt `<ACCESS TOKEN>` och `<SUBSCRIPTION ID>` med värden som är lämpligt för din miljö.

   API-versionen `2018-06-01` lagrar användartilldelade hanterade identiteter i den `userAssignedIdentities` värde i formatet ordlista inte den `identityIds` värde i en matrisformat som används i API-versionen `2017-12-01` och tidigare versioner.
   
   **API-VERSIONEN 2018-06-01**

   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```
   
   **API-VERSION 2017-12-01 och tidigare**

   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Inaktivera systemtilldelade hanterad identitet från en VM-skalningsuppsättning

Om du vill inaktivera en systemtilldelade identiteter på en befintlig VM-skalningsuppsättning, måste du hämta en åtkomsttoken och sedan använder vi CURL för att anropa Resource Manager REST-slutpunkt för att uppdatera identitetstypen till `None`.

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Uppdatera VM-skalningsuppsättningen in med CURL för att anropa Azure Resource Manager REST-slutpunkten för att inaktivera systemtilldelade hanterad identitet.  I följande exempel inaktiverar systemtilldelade hanterad identitet som identifieras i begärandetexten av värdet `{"identity":{"type":"None"}}` från en VM-skalningsuppsättning med namnet *myVMSS*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.

   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort alla befintliga användartilldelade hanterade identiteter som är kopplade till virtuella datorns skalningsuppsättning, måste du lista de hanterade användartilldelade identiteterna med hjälp av den här CURL-kommando: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Om du har några användartilldelade hanterade identiteten som tilldelats virtuella datorns skalningsuppsättning kan du hoppa till steg 3 som visar hur behåller de hanterade användartilldelade identiteterna och tar bort hanterad identitet systemtilldelade från virtual machine scale Sets.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Ta bort systemtilldelade hanterad identitet från en skalningsuppsättning för virtuella datorer som har användartilldelade hanterade identiteter genom att ta bort `SystemAssigned` från den `{"identity":{"type:" "}}` värdet medan den `UserAssigned` värde och `userAssignedIdentities` ordlista värden om du använder **API-versionen 2018-06-01**. Om du använder **API-versionen 2017-12-01** eller tidigare, Behåll den `identityIds` matris.

## <a name="user-assigned-managed-identity"></a>Användartilldelade hanterad identitet

Du lär dig hur du lägger till och ta bort Användartilldelad hanterad identitet på en VM-skalningsuppsättning med CURL för att göra anrop till Azure Resource Manager REST-slutpunkten i det här avsnittet.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en hanterad Användartilldelad identitet när du skapar en VM-skalningsuppsättning

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Skapa en [nätverksgränssnittet](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för VM-skalningsuppsättningar ställer du in:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en Användartilldelad hanterad identitet med hjälp av anvisningarna som finns här: [skapa en hanterad Användartilldelad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Skapa en VM-skalningsuppsättning med CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en VM-skalningsuppsättning med namnet *myVMSS* i resursgruppen *myResourceGroup* med en Användartilldelad hanterad identitet `ID1`, vilket identifieras i begärandetexten med värdet `"identity":{"type":"UserAssigned"}`. Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.
 
   **API-VERSIONEN 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   **API-VERSION 2017-12-01 och tidigare**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Tilldela en hanterad Användartilldelad identitet till en befintlig Azure VM-skalningsuppsättning

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Skapa en Användartilldelad hanterad identitet med hjälp av instruktionerna här [skapa en hanterad Användartilldelad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. För att säkerställa att du inte tar bort befintlig användare eller systemtilldelade hanterade identiteter som är kopplade till virtuella datorns skalningsuppsättning, måste du den identitet som typer som tilldelats VM-skalningsuppsättningen in med hjälp av följande CURL-kommando. Om du har hanterade identiteter som tilldelats virtuella datorns skalningsuppsättning, de listas i den `identity` värde.
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. Om du inte har någon användare eller systemtilldelade hanterade identiteter som tilldelats VM-skalningsuppsättning ange, Använd följande CURL-kommando för att anropa Azure Resource Manager REST-slutpunkt för att tilldela den virtuella datorn första användartilldelade hanterad identitet skalningsuppsättning.  Om du har en användare eller systemtilldelade hanterade identity(s) tilldelats virtuella datorns skalningsuppsättning kan gå vidare till steg 5 som visar hur du lägger till flera användare tilldelade hanterade identiteter i en VM-skalningsuppsättning och ger samtidigt den systemtilldelade hanteras identitet.

   I följande exempel tilldelar en Användartilldelad hanterad identitet, `ID1` till en VM-skalningsuppsättning med namnet *myVMSS* i resursgruppen *myResourceGroup*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.

   **API-VERSIONEN 2018-06-01**

    ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   
    
   **API-VERSION 2017-12-01 och tidigare**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Om du har en befintlig Användartilldelad eller systemtilldelade hanterade identiteten som tilldelats din VM-skalningsuppsättning:
   
   **API-VERSIONEN 2018-06-01**

   Lägg till hanterad Användartilldelad identitet till den `userAssignedIdentities` ordlista värde.

   Exempel: Om du har systemtilldelade hanterad identitet och den hanterade Användartilldelad identitet `ID1` för närvarande tilldelats VM-skalningsuppsättning och vill lägga till den hanterade Användartilldelad identitet `ID2` till den:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-VERSION 2017-12-01 och tidigare**

   Behåll de användartilldelade hanterade identiteter som du vill behålla i den `identityIds` matrisen värdet när du lägger till nya användartilldelade hanterad identitet.

   Exempel: Om du har systemtilldelade identitets- och den hanterade Användartilldelad identitet `ID1` för närvarande tilldelats din skalningsuppsättning för virtuell dator och vill lägga till den hanterade Användartilldelad identitet `ID2` till den: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Ta bort en hanterad Användartilldelad identitet från en VM-skalningsuppsättning

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa din VM-skalningsuppsättning med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. För att säkerställa att du inte tar bort alla befintliga användartilldelade hanterade identiteter som du vill behålla tilldelade till virtuella datorns skalningsuppsättning eller ta bort systemtilldelade hanterad identitet, behöver du visa en lista över hanterade identiteter med hjälp av följande CURL-kommando : 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   Om du har hanterade identiteter som tilldelas den virtuella datorn, de listas i svaret på den `identity` värde. 
    
   Exempel: Om du har användartilldelade hanterade identiteter `ID1` och `ID2` tilldelas till din skalningsuppsättning för virtuell dator och du vill behålla `ID1` tilldelas och behålla systemtilldelade hanterad identitet:

   **API-VERSIONEN 2018-06-01**

   Lägg till `null` tilldelas den användaren-hanterad identitet som du vill ta bort:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-VERSION 2017-12-01 och tidigare**

   Behåll endast de Användartilldelad hanterade identity(s) som du vill behålla i den `identityIds` matris:   

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Om din VM-skalningsuppsättningen har båda systemtilldelade och användartilldelade hanterade identiteter kan du ta bort alla hanterade användartilldelade identiteter genom att växla mellan att använda endast systemtilldelade med följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Om din VM-skalningsuppsättningen har endast användartilldelade hanterade identiteter och du vill ta bort alla, använder du följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar, visa eller ta bort Användartilldelad hanterade identiteter med hjälp av REST finns i:

- [Skapa, visa eller ta bort en Användartilldelad hanterad identitet med hjälp av REST API-anrop](how-to-manage-ua-identity-rest.md)
