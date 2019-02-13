---
title: Hur du konfigurerar system- och användartilldelade hanterade identiteter på en Azure-dator med hjälp av REST
description: Steg för steg instruktioner för att konfigurera ett system och användartilldelade hanterade identiteter på en Azure-dator med CURL för att göra REST API-anrop.
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
ms.date: 06/25/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e3933f10a777a1aa10a4e04f8901e7fd1af5c48
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56195642"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurera hanterade identiteter för Azure-resurser på en Azure-dator med hjälp av REST API-anrop

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterade systemidentitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln använder CURL för att göra anrop till Azure Resource Manager REST-slutpunkt du lära dig hur du utför följande hanterade identiteter för Azure-resurser på en Azure virtuell dator:

- Aktivera och inaktivera systemtilldelade hanterad identitet på en Azure VM
- Lägga till och ta bort en Användartilldelad hanterad identitet på en Azure VM

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du använder Windows kan du installera den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) eller Använd den [Azure Cloud Shell](../../cloud-shell/overview.md) i Azure-portalen.
- [Installera den lokala konsolen i Azure CLI](/cli/azure/install-azure-cli), om du använder den [Windows-undersystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) eller en [Linux-distributionsoperativsystem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Om du använder Azure CLI lokalt, logga in på Azure med hjälp av `az login` med ett konto som är associerad med Azure-prenumeration du vill hantera system eller användartilldelade hanterade identiteter.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelade hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelade hanterad identitet på en Azure virtuell dator som använder CURL för att göra anrop till Azure Resource Manager REST-slutpunkten.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivera systemtilldelade hanterad identitet under skapandet av en Azure-dator

Om du vill skapa en Azure-dator med systemtilldelade hanterade identiteten aktiverat ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolltilldelning.  Inga ytterligare Azure AD directory rolltilldelningar krävs.

1. Skapa en [resursgrupp](../../azure-resource-manager/resource-group-overview.md#terminology) för inneslutning och distribution av den virtuella datorn och dess relaterade resurser med hjälp av [az group create](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Skapa en [nätverksgränssnittet](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för din virtuella dator:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en virtuell dator med CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en virtuell dator med namnet *myVM* med en automatiskt genererad hanterad identitet, vilket identifieras i begärandetexten med värdet `"identity":{"type":"SystemAssigned"}`. Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Begärandehuvuden**
   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 
   
   **Brödtext i begäran**

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

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Aktivera systemtilldelade identiteter på en befintlig Azure VM

Om du vill aktivera systemtilldelade hanterad identitet på en virtuell dator som ursprungligen etablerades utan att det behöver ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolltilldelning.  Inga ytterligare Azure AD directory rolltilldelningar krävs.

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Använd följande CURL-kommando för att anropa Azure Resource Manager REST-slutpunkt för att aktivera systemtilldelade hanterad identitet på din virtuella dator som identifieras i begärandetexten av värdet `{"identity":{"type":"SystemAssigned"}` för en virtuell dator med namnet *myVM*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.
   
   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort alla befintliga användartilldelade hanterade identiteter som är kopplade till den virtuella datorn, måste du lista de hanterade användartilldelade identiteterna med hjälp av den här CURL-kommando: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Om du har några användartilldelade hanterade identiteter som tilldelas den virtuella datorn vilket identifieras i den `identity` värde i svaret, hoppa till steg 3 som visar hur du behåller användartilldelade hanterade identiteter samtidigt systemtilldelade hanterad identitet på den virtuella datorn.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**
   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 
   
   **Brödtext i begäran**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Om du vill aktivera systemtilldelade hanterad identitet på en virtuell dator med befintliga användartilldelade hanterade identiteter, du måste lägga till `SystemAssigned` till den `type` värde.  
   
   Exempel: om den virtuella datorn har de hanterade användartilldelade identiteterna `ID1` och `ID2` tilldelade till den och du vill lägga till systemtilldelade hanterad identitet till den virtuella datorn, Använd följande CURL-anrop. Ersätt `<ACCESS TOKEN>` och `<SUBSCRIPTION ID>` med värden som är lämpligt för din miljö.

   API-versionen `2018-06-01` lagrar användartilldelade hanterade identiteter i den `userAssignedIdentities` värde i formatet ordlista inte den `identityIds` värde i en matrisformat som används i API-versionen `2017-12-01`.
   
   **API-VERSIONEN 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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

   **API-VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 
   **Brödtext i begäran**

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

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Inaktivera systemtilldelade hanterad identitet från en Azure virtuell dator

Om du vill inaktivera systemtilldelade hanterad identitet på en virtuell dator, ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolltilldelning.  Inga ytterligare Azure AD directory rolltilldelningar krävs.

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Uppdatera den virtuella datorn med CURL för att anropa Azure Resource Manager REST-slutpunkten för att inaktivera systemtilldelade hanterad identitet.  I följande exempel inaktiverar systemtilldelade hanterad identitet som identifieras i begärandetexten av värdet `{"identity":{"type":"None"}}` från en virtuell dator med namnet *myVM*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.

   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort alla befintliga användartilldelade hanterade identiteter som är kopplade till den virtuella datorn, måste du lista de hanterade användartilldelade identiteterna med hjälp av den här CURL-kommando: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Om du har några användartilldelade hanterade identiteter som tilldelas den virtuella datorn vilket identifieras i den `identity` värde i svaret, hoppa till steg 3 som visar hur du behåller användartilldelade hanterade identiteter vid inaktivering av systemtilldelade hanterad identitet på den virtuella datorn.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Ta bort systemtilldelade hanterad identitet från en virtuell dator som har användartilldelade hanterade identiteter genom att ta bort `SystemAssigned` från den `{"identity":{"type:" "}}` värdet medan den `UserAssigned` värde och `userAssignedIdentities` ordlista värden om du använder **API-versionen 2018-06-01**. Om du använder **API-versionen 2017-12-01** eller tidigare, Behåll den `identityIds` matris.

## <a name="user-assigned-managed-identity"></a>Användartilldelade hanterad identitet

Du lär dig hur du lägger till och ta bort Användartilldelad hanterad identitet på en Azure virtuell dator som använder CURL för att göra anrop till Azure Resource Manager REST-slutpunkten i det här avsnittet.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Tilldela en hanterad Användartilldelad identitet när du skapar en Azure-dator

Om du vill tilldela en Användartilldelad identitet till en virtuell dator, ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) rolltilldelningar. Inga ytterligare Azure AD directory rolltilldelningar krävs.

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Skapa en [nätverksgränssnittet](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för din virtuella dator:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en Användartilldelad hanterad identitet med hjälp av anvisningarna som finns här: [Skapa en hanterad Användartilldelad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Skapa en virtuell dator med CURL för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en virtuell dator med namnet *myVM* i resursgruppen *myResourceGroup* med en Användartilldelad hanterad identitet `ID1`, vilket identifieras i begärandetexten med värdet `"identity":{"type":"UserAssigned"}`. Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.
 
   **API-VERSIONEN 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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
  
   **API-VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Tilldela en hanterad Användartilldelad identitet till en befintlig Azure VM

Om du vill tilldela en Användartilldelad identitet till en virtuell dator, ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) rolltilldelningar. Inga ytterligare Azure AD directory rolltilldelningar krävs.

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Skapa en Användartilldelad hanterad identitet med hjälp av instruktionerna här [skapa en hanterad Användartilldelad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. För att säkerställa att du inte tar bort befintlig användare eller systemtilldelade hanterade identiteter som är kopplade till den virtuella datorn, måste du lista över identitetstyper som tilldelats till den virtuella datorn med hjälp av följande CURL-kommando. Om du har hanterade identiteter som tilldelats virtuella datorns skalningsuppsättning, de visas under i den `identity` värde.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**
   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.

    Om du har alla användare eller systemtilldelade hanterade identiteter tilldelas den virtuella datorn vilket identifieras i den `identity` värde i svaret, gå vidare till steg 5 som visar hur du behåller Genoms systemtilldelade hanterad identitet när du lägger till en hanterad Användartilldelad identitet på den virtuella datorn.

4. Om du inte har någon användartilldelade hanterade identiteter tilldelade till den virtuella datorn, kan du använda följande CURL-kommando för att anropa Azure Resource Manager REST-slutpunkt för att tilldela den första hanterade användartilldelade-identitet till den virtuella datorn.

   I följande exempel tilldelar en Användartilldelad hanterad identitet, `ID1` till en virtuell dator med namnet *myVM* i resursgruppen *myResourceGroup*.  Ersätt `<ACCESS TOKEN>` med värdet du fick i föregående steg när du har begärt en ägar-token för åtkomst och `<SUBSCRIPTION ID>` värde som passar din miljö.

   **API-VERSIONEN 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        |
 
   **Brödtext i begäran**

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

   **API-VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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

5. Om du har en befintlig tilldelats eller systemtilldelade hanterade identiteten som tilldelats den virtuella datorn:
   
   **API-VERSIONEN 2018-06-01**

   Lägg till hanterad Användartilldelad identitet till den `userAssignedIdentities` ordlista värde.
    
   Exempel: Om du har systemtilldelade hanterad identitet och den hanterade Användartilldelad identitet `ID1` för närvarande tilldelad till den virtuella datorn och vill lägga till den hanterade Användartilldelad identitet `ID2` till den:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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

   **API-VERSION 2017-12-01**

   Behåll de användartilldelade hanterade identiteter som du vill behålla i den `identityIds` matrisen värdet när du lägger till nya användartilldelade hanterad identitet.

   Exempel: Om du har systemtilldelade hanterad identitet och den hanterade Användartilldelad identitet `ID1` för närvarande tilldelad till den virtuella datorn och vill lägga till den hanterade Användartilldelad identitet `ID2` till den: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en hanterad Användartilldelad identitet från en Azure-dator

Om du vill ta bort en Användartilldelad identitet till en virtuell dator måste ditt konto måste den [virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolltilldelning.

1. Hämta en ägar-åtkomsttoken som du ska använda i nästa steg i auktoriseringshuvudet för att skapa den virtuella datorn med en automatiskt genererad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. För att säkerställa att du inte tar bort alla befintliga användartilldelade hanterade identiteter som du vill behålla tilldelade till den virtuella datorn eller ta bort systemtilldelade hanterad identitet, behöver du visa en lista över hanterade identiteter med hjälp av följande CURL-kommando: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**
   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.
 
   Om du har hanterade identiteter som tilldelas den virtuella datorn, de listas i svaret på den `identity` värde.

   Exempel: Om du har användartilldelade hanterade identiteter `ID1` och `ID2` tilldelas till den virtuella datorn och du vill behålla `ID1` tilldelas och behålla den systemtilldelade identiteten:
   
   **API-VERSIONEN 2018-06-01**

   Lägg till `null` tilldelas den användaren-hanterad identitet som du vill ta bort:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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

   **API-VERSION 2017-12-01**

   Behåll endast de Användartilldelad hanterade identity(s) som du vill behålla i den `identityIds` matris:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange `application/json`.        |
   |*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.        | 

   **Brödtext i begäran**

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

Om den virtuella datorn har båda systemtilldelade och användartilldelade hanterade identiteter kan du ta bort alla användartilldelade hanterade identiteter genom att växla mellan att använda endast systemtilldelade hanterad identitet med hjälp av följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Begärandehuvuden**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehållstyp*     | Krävs. Ange `application/json`.        |
|*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken. | 

**Brödtext i begäran**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Om den virtuella datorn har bara användartilldelade hanterade identiteter och du vill ta bort alla, använder du följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Begärandehuvuden**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehållstyp*     | Krävs. Ange `application/json`.        |
|*Auktorisering*     | Krävs. Ange att ett giltigt `Bearer` åtkomsttoken.| 

**Brödtext i begäran**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar, visa eller ta bort Användartilldelad hanterade identiteter med hjälp av REST finns i:

- [Skapa, visa eller ta bort en Användartilldelad hanterade identiteter med hjälp av REST API-anrop](how-to-manage-ua-identity-rest.md)
