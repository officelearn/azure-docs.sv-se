---
title: Så här konfigurerar du system- och användartilldelade identiteter på en Azure-dator med hjälp av REST
description: Steg för steg tilldelas instruktioner för att konfigurera en system- och en identiteter på en Azure virtuell dator använder CURL för att göra REST API-anrop.
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
ms.openlocfilehash: f4831f257f706838817f60a9299551ca8feec3e6
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885553"
---
# <a name="configure-managed-identity-on-an-azure-vm-using-rest-api-calls"></a>Konfigurera hanterade identiteter på en Azure-dator med hjälp av REST API-anrop

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad identitet ger Azure-tjänster med en automatiskt hanterade systemidentitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du utför följande åtgärder för hanterade identiteter på en Azure-dator med CURL för att göra anrop till Azure Resource Manager REST-slutpunkten:

- Aktivera och inaktivera systemtilldelad identitet på en Azure VM
- Lägga till och ta bort en Användartilldelad identitet på en Azure VM

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande rolltilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:
    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en virtuell dator och aktivera och ta bort system och/eller användare tilldelas hanterad identitet från en Azure-dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad identitet från och till en virtuell dator.
- Om du använder Windows kan du installera den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) eller Använd den [Azure Cloud Shell](../../cloud-shell/overview.md) i Azure-portalen.
- [Installera den lokala konsolen i Azure CLI](/cli/azure/install-azure-cli), om du använder den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) eller en [Linux-distributionsoperativsystem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Om du använder Azure CLI lokalt, logga in på Azure med hjälp av `az login` med ett konto som är associerad med Azure-prenumeration som du vill hantera system eller användare användartilldelade identiteter.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Systemtilldelad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelad identitet på en Azure virtuell dator som använder CURL för att göra anrop till Azure Resource Manager REST-slutpunkten.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Aktivera systemtilldelad identitet under skapandet av en Azure-dator

För att skapa en Azure-dator med systemtilldelad identitet aktiverat, måste du skapa en virtuell dator och hämta en åtkomsttoken för att använda CURL för att anropa Resource Manager-slutpunkten med systemtilldelad identitet TYPVÄRDE.

1. Skapa en [resursgrupp](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az group create](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Skapa en [nätverksgränssnittet](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för din virtuella dator:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en virtuell dator med CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en virtuell dator med namnet *myVM* med en systemtilldelade identiteter, vilket identifieras i begärandetexten med värdet `"identity":{"type":"SystemAssigned"}`. Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.
 
    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Aktivera systemtilldelad identitet på en befintlig Azure VM

Om du vill aktivera systemtilldelad identitet på en befintlig virtuell dator, måste du hämta en åtkomsttoken och sedan använder vi CURL för att anropa Resource Manager REST-slutpunkt för att uppdatera identitetstypen.

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Använd följande CURL-kommando för att anropa Azure Resource Manager REST-slutpunkt för att aktivera systemtilldelade identiteter på din virtuella dator som identifieras i begärandetexten av värdet `{"identity":{"type":"SystemAssigned"}` för en virtuell dator med namnet *myVM*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.
   
   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort alla befintliga användare som tilldelats hanterade identiteter som är kopplade till den virtuella datorn, måste du lista användartilldelade identiteter med hjälp av den här CURL-kommando: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Om du har inga användartilldelade identiteter som tilldelas den virtuella datorn vilket identifieras i den `identity` värde i svaret, hoppa till steg 3 som visar hur du behåller användartilldelade identiteter samtidigt systemtilldelade identiteter på den virtuella datorn.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Om du vill aktivera systemtilldelad identitet på en virtuell dator med befintliga användartilldelade identiteter, du måste lägga till `SystemAssigned` till den `type` värde.  
   
   Exempel: om den virtuella datorn har användartilldelade identiteter `ID1` och `ID2` tilldelade till den och du vill lägga till systemtilldelade identiteter i den virtuella datorn, Använd följande CURL-anrop. Ersätt `<ACCESS TOKEN>` och `<SUBSCRIPTION ID>` med värden som är lämpligt för din miljö.

   API-versionen `2018-06-01` lagrar användartilldelade identiteter i den `userAssignedIdentities` värde i formatet ordlista inte den `identityIds` värde i en matrisformat som används i API-versionen `2017-12-01` och tidigare versioner.
   
   **API-VERSIONEN 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-VERSION 2017-12-01 och tidigare**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Inaktivera systemtilldelad identitet från en Azure virtuell dator

Om du vill inaktivera en systemtilldelade identiteter på en befintlig virtuell dator, måste du hämta en åtkomsttoken och sedan använder vi CURL för att anropa Resource Manager REST-slutpunkt för att uppdatera identitetstypen till `None`.

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Uppdatera den virtuella datorn med CURL för att anropa Azure Resource Manager REST-slutpunkten för att inaktivera systemtilldelad identitet.  I följande exempel inaktiverar systemtilldelade identiteter som identifieras i begärandetexten av värdet `{"identity":{"type":"None"}}` från en virtuell dator med namnet *myVM*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.

   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort alla befintliga användare som tilldelats hanterade identiteter som är kopplade till den virtuella datorn, måste du lista användartilldelade identiteter med hjälp av den här CURL-kommando: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Om du har inga användartilldelade identiteter som tilldelas den virtuella datorn vilket identifieras i den `identity` värde i svaret, hoppa till steg 3 som visar hur du behåller användartilldelade identiteter vid inaktivering av systemtilldelade identiteter på den virtuella datorn.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Ta bort systemtilldelad identitet från en virtuell dator med användartilldelade identiteter genom att ta bort `SystemAssigned` från den `{"identity":{"type:" "}}` värdet medan den `UserAssigned` värde och `userAssignedIdentities` ordlista värden om du använder **API versionen 2018-06-01**. Om du använder **API-versionen 2017-12-01** eller tidigare, Behåll den `identityIds` matris.

## <a name="user-assigned-identity"></a>Användartilldelad identitet

Du lär dig hur du lägger till och ta bort Användartilldelad identitet på en Azure virtuell dator som använder CURL för att göra anrop till Azure Resource Manager REST-slutpunkten i det här avsnittet.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Tilldela Användartilldelad identitet när du skapar en Azure-dator

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Skapa en [nätverksgränssnittet](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för din virtuella dator:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en Användartilldelad identitet med hjälp av anvisningarna som finns här: [skapa en användare som tilldelats hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Skapa en virtuell dator med CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en virtuell dator med namnet *myVM* i resursgruppen *myResourceGroup* med en Användartilldelad identitet `ID1`, vilket identifieras i begärandetexten med värdet `"identity":{"type":"UserAssigned"}`. Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.
 
   
   **API-VERSIONEN 2018-06-01**
    
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 

   **API-VERSION 2017-12-01 och tidigare**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Tilldela Användartilldelad identitet till en befintlig Azure VM

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Skapa en Användartilldelad identitet med hjälp av instruktionerna här [skapa en användare som tilldelats hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3.  För att säkerställa att du inte tar bort befintlig användare eller system tilldelade hanterade identiteter som är kopplade till den virtuella datorn, måste du lista över identitetstyper som tilldelats till den virtuella datorn med hjälp av följande CURL-kommando. Om du har hanterade identiteter som tilldelats virtuella datorns skalningsuppsättning, de visas under i den `identity` värde.

    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Om du har alla användare eller systemtilldelade identiteter som tilldelas den virtuella datorn vilket identifieras i den `identity` värde i svaret, gå vidare till steg 5 som visar hur du behåller Genoms systemtilldelad identitet när du lägger till en Användartilldelad identitet på den virtuella datorn.

4. Om du inte har någon användartilldelade identiteter som tilldelats den virtuella datorn, kan du använda följande CURL-kommando för att anropa Azure Resource Manager REST-slutpunkt för att tilldela den första Användartilldelad identitet till den virtuella datorn.

   I följande exempel tilldelar en Användartilldelad identitet `ID1` till en virtuell dator med namnet *myVM* i resursgruppen *myResourceGroup*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.

   **API-VERSIONEN 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-VERSION 2017-12-01 och tidigare**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Om du har en befintlig användare tilldelas eller system Användartilldelad identitet som har tilldelats den virtuella datorn:
   
   **API-VERSIONEN 2018-06-01**

   Lägg till den Användartilldelad identitet som den `userAssignedIdentities` ordlista värde.
    
   Om du har system t.ex identitets- och användaridentitet tilldelade `ID1` för närvarande tilldelad till den virtuella datorn och vill lägga till användaridentiteten `ID2` till den:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-VERSION 2017-12-01 och tidigare**

   Behåll användartilldelade identiteter som du vill behålla i den `identityIds` matrisen värdet när du lägger till ny Användartilldelad identitet.

   Om du har system t.ex identitets- och användaridentitet tilldelade `ID1` för närvarande tilldelad till den virtuella datorn och vill lägga till användaridentiteten `ID2` till den: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Ta bort Användartilldelad identitet från en Azure virtuell dator

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med ett system som tilldelats hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. För att säkerställa att du inte tar bort alla befintliga användare som tilldelats hanterade identiteter som du vill behålla tilldelade till den virtuella datorn eller ta bort systemtilldelad identitet, behöver du visa en lista över hanterade identiteter med hjälp av följande CURL-kommando: 
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   Om du har hanterade identiteter som tilldelas den virtuella datorn, de listas i svaret på den `identity` värde.

   Exempel: Om du har användartilldelade identiteter `ID1` och `ID2` tilldelas till den virtuella datorn och du vill behålla `ID1` tilldelas och behålla systemtilldelade identiteter:
   
   **API-VERSIONEN 2018-06-01**

   Lägg till `null` till Användartilldelad identitet som du vill ta bort:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API-VERSION 2017-12-01 och tidigare**

   Behåll endast användaren tilldelats identity(s) som du vill behålla i den `identityIds` matris:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Om den virtuella datorn har både systemtilldelad och användartilldelade identiteter kan du ta bort alla användaren användartilldelade identiteter genom att växla mellan att använda endast system som har tilldelats med hjälp av följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Om den virtuella datorn har bara användartilldelade identiteter och du vill ta bort alla, använder du följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
## <a name="next-steps"></a>Nästa steg

Information om hur du skapa, visa eller ta bort användare som tilldelats med hjälp av REST finns i:

- [Skapa, visa eller ta bort en Användartilldelad identitet med hjälp av REST API-anrop](how-to-manage-ua-identity-rest.md)
