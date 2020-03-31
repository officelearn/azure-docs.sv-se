---
title: Konfigurera hanterade identiteter på Azure VMSS med REST - Azure AD
description: Steg för steg instruktioner för att konfigurera ett system och användartilldelade hanterade identiteter på en Azure VMSS med CURL för att göra REST API-anrop.
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
ms.openlocfilehash: dce9894b26d03c351a2209792cc076de91feba54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253343"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurera hanterade identiteter för Azure-resurser i en skala för virtuella datorer med REST API-anrop

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad systemidentitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln kan du använda CURL för att ringa anrop till SLUTPUNKTEN FÖR AZURE Resource Manager REST, hur du utför följande hanterade identiteter för Azure-resurser på en skalningsuppsättning för virtuella datorer:

- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en Azure-skalningsuppsättning för virtuella datorer
- Lägga till och ta bort en användartilldelad hanterad identitet på en Azure-skalningsuppsättning för virtuella datorer

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att utföra hanteringsåtgärderna i den här artikeln behöver ditt konto följande Azure-rollbaserade åtkomstkontrolltilldelningar:

    > [!NOTE]
    > Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

    - [Deltagare i virtuella](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) datorer för att skapa en skaluppsättning för virtuella datorer och aktivera och ta bort system- och/eller användartilldelade hanterade identitet från en skaluppsättning för virtuella datorer.
    - [Hanterad identitetsbidragstagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en användartilldelade hanterad identitet.
    - [Hanterad identitetsoperatorroll](/azure/role-based-access-control/built-in-roles#managed-identity-operator) för att tilldela och ta bort en användartilldelad identitet från och till en skaluppsättning för virtuella datorer.
- Om du använder Windows installerar du [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about) eller använder [Azure Cloud Shell](../../cloud-shell/overview.md) i Azure-portalen.
- [Installera den lokala Azure CLI-konsolen](/cli/azure/install-azure-cli)om du använder [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about) eller ett [Linux-distributionsoperativsystem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Om du använder den lokala Azure CLI-konsolen loggar du in på Azure med `az login` ett konto som är kopplat till Azure-prenumerationen som du vill hantera system- eller användartilldelade hanterade identiteter.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelade hanterade identitet på en virtuell datorskalauppsättning med CURL för att ringa anrop till AZURE Resource Manager REST-slutpunkten.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterade identiteter när en skaluppsättning för virtuella datorer skapas

Om du vill skapa en skalningsuppsättning för virtuella datorer med systemtilldelade hanterade identitet aktiverad måste du skapa en skalningsuppsättning för virtuella datorer och hämta en åtkomsttoken för att använda CURL för att anropa Resurshanterarens slutpunkt med det systemtilldelade värdet för hanterad identitetstyp.

1. Skapa en [resursgrupp](../../azure-resource-manager/management/overview.md#terminology) för inneslutning och distribution av skalningsuppsättningen för den virtuella datorn och dess relaterade resurser med hjälp av [az-gruppge](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Skapa ett [nätverksgränssnitt](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för din skalningsuppsättning för virtuella datorer:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator skalningsuppsättning med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en skalningsuppsättning för virtuella datorer med CURL för att anropa AZURE Resource Manager REST-slutpunkten. I följande exempel skapas en skalauppsättning för virtuella datorer med namnet *myVMSS* i *myResourceGroup* med en `"identity":{"type":"SystemAssigned"}`systemtilldelad hanterad identitet, som identifieras i begärandens brödtext med värdet . Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg `<SUBSCRIPTION ID>` när du begärde en innehavaråtkomsttoken och värdet som är lämpligt för din miljö.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken. | 

   **Begäran kropp**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterade identitet på en befintlig skaluppsättning för virtuella datorer

Om du vill aktivera den systemtilldelade hanterade identiteten i en befintlig skalningsuppsättning för virtuella datorer måste du hämta en åtkomsttoken och sedan använda CURL för att anropa RESURSHANTERARENS REST-slutpunkt för att uppdatera identitetstypen.

1. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator skalningsuppsättning med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Använd följande CURL-kommando för att anropa AZURE Resource Manager REST-slutpunkten för att aktivera systemtilldelade hanterade `{"identity":{"type":"SystemAssigned"}` identitet på din virtuella datorskala som anges i begärandetexten av värdet för en virtuell datorskalauppsättning med namnet *myVMSS*.  Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg `<SUBSCRIPTION ID>` när du begärde en innehavaråtkomsttoken och värdet som är lämpligt för din miljö.
   
   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort några befintliga användartilldelade hanterade identiteter som har tilldelats skaluppsättningen för den `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`virtuella datorn måste du lista de användartilldelade hanterade identiteterna med hjälp av det här CURL-kommandot: . Om du har tilldelats några användartilldelade hanterade identiteter tilldelade `identity` till skalan för den virtuella datorn som anges i värdet i svaret, hoppa till steg 3 som visar hur du behåller användartilldelade hanterade identiteter samtidigt som systemtilldelade hanterade identiteter aktiveras på din virtuella datorskalauppsättning.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

3. Om du vill aktivera systemtilldelade hanterade identiteter på en skalauppsättning för virtuella `SystemAssigned` datorer `type` med befintliga användartilldelade hanterade identiteter måste du lägga till värdet.  
   
   Om skaluppsättningen för den virtuella datorn till exempel `ID1` `ID2` har de användartilldelade hanterade identiteterna och tilldelats den, och du vill lägga till den systemtilldelade hanterade identiteten i skaluppsättningen för den virtuella datorn, använder du följande CURL-anrop. Ersätt `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` och med värden som är anpassade till din miljö.

   API-versionen `2018-06-01` lagrar användartilldelade hanterade `userAssignedIdentities` identiteter i värdet i `identityIds` ett ordlisteformat i `2017-12-01`motsats till värdet i ett matrisformat som används i API-versionen .
   
   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
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

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Inaktivera systemtilldelade hanterade identitet från en skaluppsättning för virtuella datorer

Om du vill inaktivera en systemtilldelad identitet på en befintlig skalningsuppsättning för virtuella datorer måste du hämta en `None`åtkomsttoken och sedan använda CURL för att anropa RESURSHANTERARENS REST-slutpunkt för att uppdatera identitetstypen till .

1. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator skalningsuppsättning med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Uppdatera skalningsuppsättningen för den virtuella datorn med CURL för att anropa REST-slutpunkten för Azure Resource Manager för att inaktivera den systemtilldelade hanterade identiteten.  I följande exempel inaktiveras den systemtilldelade hanterade identiteten `{"identity":{"type":"None"}}` som identifieras i begärandetexten av värdet från en skalauppsättning för virtuell dator med namnet *myVMSS*.  Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg `<SUBSCRIPTION ID>` när du begärde en innehavaråtkomsttoken och värdet som är lämpligt för din miljö.

   > [!IMPORTANT]
   > För att säkerställa att du inte tar bort några befintliga användartilldelade hanterade identiteter som har tilldelats skaluppsättningen för den `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`virtuella datorn måste du lista de användartilldelade hanterade identiteterna med hjälp av det här CURL-kommandot: . Om du har tilldelats någon användartilldelad hanterad identitet tilldelad skaluppsättningen för den virtuella datorn går du vidare till steg 3 som visar hur du behåller de användartilldelade hanterade identiteterna samtidigt som den systemtilldelade hanterade identiteten tas bort från din skaluppsättning för virtuella datorer.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"None"
       }
    }
   ```

   Om du vill ta bort den systemtilldelade hanterade identiteten från en `SystemAssigned` skaluppsättning för virtuella datorer som har användartilldelade hanterade identiteter tar du bort från `{"identity":{"type:" "}}` värdet samtidigt som `UserAssigned` värdet och `userAssignedIdentities` ordlistevärdena behålls om du använder **API-version 2018-06-01**. Om du använder **API-version 2017-12-01** `identityIds` eller tidigare behåller du matrisen.

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort användartilldelade hanterade identitet på en virtuell datorskalauppsättning med CURL för att ringa anrop till AZURE Resource Manager REST-slutpunkten.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en användartilldelad hanterad identitet när en skaluppsättning för virtuella datorer skapas

1. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator skalningsuppsättning med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Skapa ett [nätverksgränssnitt](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för din skalningsuppsättning för virtuella datorer:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator skalningsuppsättning med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en användartilldelad hanterad identitet med hjälp av instruktionerna som finns här: [Skapa en användartilldelad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Skapa en skalningsuppsättning för virtuella datorer med CURL för att anropa AZURE Resource Manager REST-slutpunkten. I följande exempel skapas en skalasuppsättning för virtuella datorer med namnet *myVMSS* i `ID1`resursgruppen *myResourceGroup* med en användartilldelad hanterad identitet, som identifieras i begärandens brödtext med värdet `"identity":{"type":"UserAssigned"}`. Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg `<SUBSCRIPTION ID>` när du begärde en innehavaråtkomsttoken och värdet som är lämpligt för din miljö.
 
   **API VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken. | 

   **Begäran kropp**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **API VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehållstyp*     | Krävs. Ange till `application/json`.        |
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken. |
 
   **Begäran kropp**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Tilldela en användartilldelad hanterad identitet till en befintlig Azure-skalningsuppsättning för virtuella datorer

1. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator skalningsuppsättning med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Skapa en användartilldelad hanterad identitet med hjälp av instruktionerna som finns här, [Skapa en användartilldelad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. För att vara säkra på att du inte tar bort befintliga användar- eller systemtilldelade hanterade identiteter som har tilldelats skaluppsättningen för den virtuella datorn måste du ange de identitetstyper som tilldelats den virtuella datorns skalningsuppsättning med hjälp av följande CURL-kommando. Om du har hanterade identiteter som tilldelats skaluppsättningen `identity` för den virtuella datorn visas de i värdet.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken. |   
 

4. Om du inte har några användar- eller systemtilldelade hanterade identiteter som tilldelats din skalauppsättning för virtuella datorer använder du följande CURL-kommando för att anropa AZURE Resource Manager REST-slutpunkten för att tilldela den första användartilldelade hanterade identiteten till den virtuella datorn skalningsuppsättning.  Om du har tilldelat en eller flera användartilldelade hanterade identiteter tilldelade till skaluppsättningen för den virtuella datorn går du vidare till steg 5 som visar hur du lägger till flera användartilldelade hanterade identiteter i en skalauppsättning för virtuella datorer samtidigt som den systemtilldelade hanterade Identitet.

   I följande exempel tilldelas en användartilldelad hanterad identitet till `ID1` en skalasuppsättning för virtuella datorer med namnet *myVMSS* i resursgruppen *myResourceGroup*.  Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg `<SUBSCRIPTION ID>` när du begärde en innehavaråtkomsttoken och värdet som är lämpligt för din miljö.

   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **API VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Om du har tilldelat en befintlig användartilldelad eller systemtilldelad hanterad identitet tilldelad din skalauppsättning för den virtuella datorn:
   
   **API VERSION 2018-06-01**

   Lägg till den användartilldelade `userAssignedIdentities` hanterade identiteten i ordlistevärdet.

   Om du till exempel har den systemtilldelade hanterade identiteten `ID1` och den användartilldelade hanterade identitet som för närvarande är `ID2` tilldelad din virtuella datorskala och vill lägga till den användartilldelade hanterade identiteten i den:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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

   Om du till exempel har en systemtilldelad identitet `ID1` och den användartilldelade hanterade identitet som för närvarande är tilldelad till din skaluppsättning för virtuella datorer och vill lägga till den användartilldelade hanterade identiteten `ID2` i den:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Ta bort en användartilldelad hanterad identitet från en skaluppsättning för virtuella datorer

1. Hämta en bärare åtkomsttoken, som du kommer att använda i nästa steg i auktoriseringshuvudet för att skapa din virtuella dator skalningsuppsättning med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. För att vara säkra på att du inte tar bort några befintliga användartilldelade hanterade identiteter som du vill behålla tilldelade till skalningsuppsättningen för den virtuella datorn eller ta bort den systemtilldelade hanterade identiteten måste du lista de hanterade identiteterna med hjälp av följande CURL-kommando :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Begär rubriker**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Tillstånd*     | Krävs. Ange till `Bearer` en giltig åtkomsttoken. |
   
   Om du har hanterade identiteter som tilldelats den virtuella `identity` datorn visas de i svaret i värdet. 
    
   Om du till exempel har användartilldelade hanterade identiteter `ID1` och `ID2` tilldelats din virtuella `ID1` datorskalauppsättning, och du bara vill behålla den tilldelade och behålla den systemtilldelade hanterade identiteten:

   **API VERSION 2018-06-01**

   Lägg `null` till den användartilldelade hanterade identitet som du vill ta bort:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Om skaluppsättningen för virtuella datorer har både systemtilldelade och användartilldelade hanterade identiteter kan du ta bort alla användartilldelade hanterade identiteter genom att växla till att endast använda systemtilldelade med följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
    
Om skaluppsättningen för den virtuella datorn bara har användartilldelade hanterade identiteter och du vill ta bort dem alla använder du följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar, listar eller tar bort användartilldelade hanterade identiteter med REST finns i:

- [Skapa, lista eller ta bort en användartilldelad hanterad identitet med REST API-anrop](how-to-manage-ua-identity-rest.md)
