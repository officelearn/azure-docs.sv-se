---
title: Konfigurera hanterade identiteter på Azure VM med REST - Azure AD
description: Steg för steg instruktioner för att konfigurera ett system och användartilldelade hanterade identiteter på en Azure VM med CURL för att göra REST API-anrop.
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
ms.date: 06/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f975595e935a5c0254450168aa295e6e7366a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244165"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurera hanterade identiteter för Azure-resurser på en Virtuell Azure-dator med REST API-anrop

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad systemidentitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln, med HJÄLP AV CURL för att ringa anrop till AZURE Resource Manager REST-slutpunkten, lär du dig hur du utför följande hanterade identiteter för Azure-resurser på en Virtuell Azure-dator:

- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en Virtuell Azure-dator
- Lägga till och ta bort en användartilldelad hanterad identitet på en Virtuell Azure-dator

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du använder Windows installerar du [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about) eller använder [Azure Cloud Shell](../../cloud-shell/overview.md) i Azure-portalen.
- [Installera den lokala Azure CLI-konsolen](/cli/azure/install-azure-cli)om du använder [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about) eller ett [Linux-distributionsoperativsystem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Om du använder den lokala Azure CLI-konsolen loggar du in på Azure med `az login` ett konto som är kopplat till Azure-prenumerationen som du vill hantera system- eller användartilldelade hanterade identiteter.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelade hanterade identitet på en Azure VM med CURL för att ringa anrop till AZURE Resource Manager REST-slutpunkten.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivera systemtilldelade hanterade identiteter när en Azure-virtuell dator skapas

Om du vill skapa en virtuell Azure-dator med den systemtilldelade hanterade identiteten aktiverad behöver ditt konto rolltilldelningen för deltagare i [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Skapa en [resursgrupp](../../azure-resource-manager/management/overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az group create](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Skapa ett [nätverksgränssnitt](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för den virtuella datorn:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en virtuell dator med CURL för att anropa AZURE Resource Manager REST-slutpunkten. I följande exempel skapas en virtuell dator med namnet *myVM* med en systemtilldelad hanterad identitet, som identifieras i begärandetexten med värdet `"identity":{"type":"SystemAssigned"}`. Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg `<SUBSCRIPTION ID>` när du begärde en innehavaråtkomsttoken och värdet som är lämpligt för din miljö.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Begär rubriker**
   
   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 
   
   **Begäran kropp**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Aktivera systemtilldelade identitet på en befintlig Azure-virtuell dator

För att aktivera systemtilldelade hanterade identitet på en virtuell dator som ursprungligen etablerades utan den, behöver ditt konto rolltilldelningen för deltagare i [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Använd följande CURL-kommando för att anropa AZURE Resource Manager REST-slutpunkten för att aktivera systemtilldelade `{"identity":{"type":"SystemAssigned"}` hanterade identitet på den virtuella datorn som identifieras i begärandetexten med värdet för en virtuell dator med namnet *myVM*.  Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg `<SUBSCRIPTION ID>` när du begärde en innehavaråtkomsttoken och värdet som är lämpligt för din miljö.
   
   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort befintliga användartilldelade hanterade identiteter som har tilldelats den virtuella datorn `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`måste du lista de användartilldelade hanterade identiteterna med hjälp av det här CURL-kommandot: . Om du har några användartilldelade hanterade identiteter som `identity` tilldelats den virtuella datorn som identifieras i värdet i svaret, hoppa till steg 3 som visar hur du behåller användartilldelade hanterade identiteter samtidigt som systemtilldelade hanterade identiteter aktiveras på den virtuella datorn.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 
   
   **Begäran kropp**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Om du vill aktivera systemtilldelade hanterade identiteter på en virtuell dator `SystemAssigned` med `type` befintliga användartilldelade hanterade identiteter måste du lägga till värdet.  
   
   Om den virtuella datorn till exempel har `ID1` de `ID2` användartilldelade hanterade identiteterna och tilldelats den, och du vill lägga till systemtilldelade hanterade identitet till den virtuella datorn, använder du följande CURL-anrop. Ersätt `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` och med värden som är anpassade till din miljö.

   API-versionen `2018-06-01` lagrar användartilldelade hanterade `userAssignedIdentities` identiteter i värdet i `identityIds` ett ordlisteformat i `2017-12-01`motsats till värdet i ett matrisformat som används i API-versionen .
   
   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 

   **Begäran kropp**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **API VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 

   **Begäran kropp**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Inaktivera systemtilldelade hanterade identitet från en Virtuell Azure-dator

Om du vill inaktivera den systemtilldelade hanterade identiteten på en virtuell dator behöver kontot rolltilldelningen [för virtuella](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) datorer.  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Uppdatera den virtuella datorn med CURL för att anropa AZURE Resource Manager REST-slutpunkten för att inaktivera systemtilldelade hanterade identitet.  I följande exempel inaktiveras den systemtilldelade hanterade identiteten `{"identity":{"type":"None"}}` som identifieras i begärandetexten med värdet från en virtuell dator med namnet *myVM*.  Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg `<SUBSCRIPTION ID>` när du begärde en innehavaråtkomsttoken och värdet som är lämpligt för din miljö.

   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort befintliga användartilldelade hanterade identiteter som har tilldelats den virtuella datorn `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`måste du lista de användartilldelade hanterade identiteterna med hjälp av det här CURL-kommandot: . Om du har några användartilldelade hanterade identiteter som `identity` tilldelats den virtuella datorn som identifieras i värdet i svaret, hoppa till steg 3 som visar hur du behåller användartilldelade hanterade identiteter samtidigt som systemtilldelade hanterade identiteter inaktiveras på den virtuella datorn.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 

   **Begäran kropp**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Om du vill ta bort den systemtilldelade hanterade identiteten från `SystemAssigned` en `{"identity":{"type:" "}}` virtuell dator `UserAssigned` som `userAssignedIdentities` har användartilldelade hanterade identiteter tar du bort från värdet samtidigt som värdet och ordlistevärdena behålls om du använder **API-version 2018-06-01**. Om du använder **API-version 2017-12-01** `identityIds` eller tidigare behåller du matrisen.

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort användartilldelade hanterade identitet på en Azure VM med CURL för att ringa anrop till AZURE Resource Manager REST-slutpunkten.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Tilldela en användartilldelad hanterad identitet när en Virtuell Azure skapas

Om du vill tilldela en användartilldelning till en virtuell dator behöver ditt konto [rolltilldelningarna för deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitetsoperatör.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Skapa ett [nätverksgränssnitt](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för den virtuella datorn:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en användartilldelad hanterad identitet med hjälp av instruktionerna som finns här: [Skapa en användartilldelad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Skapa en virtuell dator med CURL för att anropa AZURE Resource Manager REST-slutpunkten. I följande exempel skapas en virtuell dator med namnet *myVM* i resursgruppen `ID1` *myResourceGroup* med en `"identity":{"type":"UserAssigned"}`användartilldelad hanterad identitet, som identifieras i begärandens brödtext av värdet . Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg `<SUBSCRIPTION ID>` när du begärde en innehavaråtkomsttoken och värdet som är lämpligt för din miljö.
 
   **API VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 

   **Begäran kropp**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **API VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 

   **Begäran kropp**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Tilldela en användartilldelad hanterad identitet till en befintlig Virtuell Azure-dator

Om du vill tilldela en användartilldelning till en virtuell dator behöver ditt konto [rolltilldelningarna för deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitetsoperatör.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Skapa en användartilldelad hanterad identitet med hjälp av instruktionerna som finns här, [Skapa en användartilldelad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. För att vara säkra på att du inte tar bort befintliga användar- eller systemtilldelade hanterade identiteter som har tilldelats den virtuella datorn måste du ange de identitetstyper som tilldelats den virtuella datorn med hjälp av följande CURL-kommando. Om du har hanterade identiteter som tilldelats skaluppsättningen för `identity` den virtuella datorn visas de under i värdet.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.

    Om du har några användar- eller systemtilldelade hanterade identiteter `identity` som tilldelats den virtuella datorn som identifieras i värdet i svaret, hoppa till steg 5 som visar hur du behåller den systemtilldelade hanterade identiteten samtidigt som du lägger till en användartilldelad hanterad identitet på den virtuella datorn.

4. Om du inte har några användartilldelade hanterade identiteter som tilldelats den virtuella datorn använder du följande CURL-kommando för att anropa AZURE Resource Manager REST-slutpunkten för att tilldela den första användartilldelade hanterade identiteten till den virtuella datorn.

   I följande exempel tilldelas en användartilldelad hanterad identitet `ID1` till en virtuell dator med namnet *myVM* i resursgruppen *myResourceGroup*.  Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg `<SUBSCRIPTION ID>` när du begärde en innehavaråtkomsttoken och värdet som är lämpligt för din miljö.

   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        |
 
   **Begäran kropp**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **API VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 

   **Begäran kropp**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. Om du har tilldelat en befintlig användartilldelad eller systemtilldelad hanterad identitet tilldelad till den virtuella datorn:
   
   **API VERSION 2018-06-01**

   Lägg till den användartilldelade `userAssignedIdentities` hanterade identiteten i ordlistevärdet.
    
   Om du till exempel har den systemtilldelade hanterade identiteten `ID1` och den användartilldelade hanterade identiteten som för `ID2` närvarande är tilldelad till den virtuella datorn och vill lägga till den användartilldelade hanterade identiteten i den:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 

   **Begäran kropp**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **API VERSION 2017-12-01**

   Behåll de användartilldelade hanterade identiteter som `identityIds` du vill behålla i matrisvärdet samtidigt som du lägger till den nya användartilldelade hanterade identiteten.

   Om du till exempel har den systemtilldelade hanterade identiteten `ID1` och den användartilldelade hanterade identiteten som för `ID2` närvarande är tilldelad till den virtuella datorn och vill lägga till den användartilldelade hanterade identiteten i den: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 

   **Begäran kropp**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användartilldelad hanterad identitet från en virtuell Azure-dator

Om du vill ta bort en användartilldelning av identitet till en virtuell dator behöver ditt konto rolltilldelningen för deltagare för [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

1. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. För att vara säkra på att du inte tar bort några befintliga användartilldelade hanterade identiteter som du vill behålla tilldelade till den virtuella datorn eller ta bort den systemtilldelade hanterade identiteten måste du lista de hanterade identiteterna med hjälp av följande CURL-kommando: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.
 
   Om du har hanterade identiteter som tilldelats den virtuella `identity` datorn visas de i svaret i värdet.

   Om du till exempel har användartilldelade hanterade identiteter `ID1` och `ID2` tilldelats den `ID1` virtuella datorn, och du bara vill behålla den tilldelade och behålla den systemtilldelade identiteten:
   
   **API VERSION 2018-06-01**

   Lägg `null` till den användartilldelade hanterade identitet som du vill ta bort:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 

   **Begäran kropp**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **API VERSION 2017-12-01**

   Behåll endast de användartilldelade hanterade identiteter som du `identityIds` vill behålla i matrisen:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.        | 

   **Begäran kropp**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Om den virtuella datorn har både systemtilldelade och användartilldelade hanterade identiteter kan du ta bort alla användartilldelade hanterade identiteter genom att växla till att endast använda den systemtilldelade hanterade identiteten med följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Begär rubriker**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehållstyp*     | Krävs. Ange till `application/json`.        |
|*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken. | 

**Begäran kropp**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Om den virtuella datorn bara har användartilldelade hanterade identiteter och du vill ta bort dem alla använder du följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Begär rubriker**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehållstyp*     | Krävs. Ange till `application/json`.        |
|*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken.| 

**Begäran kropp**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar, listar eller tar bort användartilldelade hanterade identiteter med REST finns i:

- [Skapa, lista eller ta bort en användartilldelade hanterade identiteter med REST API-anrop](how-to-manage-ua-identity-rest.md)
