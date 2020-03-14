---
title: Konfigurera hanterade identiteter på Azure-VMSS med REST-Azure AD
description: Steg för steg-instruktioner för att konfigurera ett system och användare som tilldelats hanterade identiteter på en Azure-VMSS med hjälp av sväng för att göra REST API-anrop.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253343"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurera hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer med hjälp av REST API-anrop

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad system identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod. 

I den här artikeln använder du en sväng för att ringa till Azure Resource Manager REST-slutpunkten. du får lära dig hur du utför följande hanterade identiteter för Azure-resurser på en skalnings uppsättning för virtuella datorer:

- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en skalnings uppsättning för virtuella Azure-datorer
- Lägga till och ta bort en användardefinierad hanterad identitet på en virtuell Azure-dators skalnings uppsättning

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att utföra hanterings åtgärderna i den här artikeln måste ditt konto ha följande Azure Role-baserade åtkomst kontroll tilldelningar:

    > [!NOTE]
    > Inga ytterligare Azure AD Directory-roll tilldelningar krävs.

    - [Virtuell dator deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) för att skapa en skalnings uppsättning för virtuella datorer och aktivera och ta bort system-och/eller användarspecifika hanterade identiteter från en skalnings uppsättning för virtuella datorer.
    - Rollen [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) för att skapa en användardefinierad hanterad identitet.
    - [Hanterad identitet operatörs](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll för att tilldela och ta bort en användardefinierad identitet från och till en skalnings uppsättning för virtuella datorer.
- Om du använder Windows installerar du Windows- [undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about) eller använder [Azure Cloud Shell](../../cloud-shell/overview.md) i Azure Portal.
- [Installera den lokala Azure CLI-konsolen](/cli/azure/install-azure-cli)om du använder [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about) eller ett [Linux-distributions operativ system](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Om du använder en lokal Azure CLI-konsol loggar du in på Azure med `az login` med ett konto som är associerat med den Azure-prenumeration som du vill hantera system eller användare som tilldelats hanterade identiteter.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar systemtilldelad hanterad identitet på en virtuell dators skalnings uppsättning med hjälp av sväng för att ringa till Azure Resource Manager REST-slutpunkten.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Aktivera systemtilldelad hanterad identitet när en skalnings uppsättning för virtuell dator skapas

Om du vill skapa en skalnings uppsättning för virtuella datorer med systemtilldelad hanterad identitet aktive rad måste du skapa en skalnings uppsättning för virtuella datorer och hämta en åtkomsttoken för att anropa Resource Manager-slutpunkten med det systemtilldelade värdet för hanterad identitets typ.

1. Skapa en [resurs grupp](../../azure-resource-manager/management/overview.md#terminology) för inne slutning och distribution av den virtuella datorns skalnings uppsättning och dess relaterade resurser med [AZ Group Create](/cli/azure/group/#az-group-create). Du kan hoppa över det här steget om du redan har en resursgrupp som du vill använda i stället:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Skapa ett [nätverks gränssnitt](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för den virtuella datorns skalnings uppsättning:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa din skalnings uppsättning för virtuella datorer med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en skalnings uppsättning för virtuella datorer med hjälp av vändning för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en skalnings uppsättning för virtuella datorer med namnet *myVMSS* i *myResourceGroup* med en systemtilldelad hanterad identitet som identifieras i begär ande texten av värdet `"identity":{"type":"SystemAssigned"}`. Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg när du begärde en åtkomst-token för Bearer och `<SUBSCRIPTION ID>` värdet efter behov för din miljö.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

   **Brödtext i begäran**

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

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Aktivera systemtilldelad hanterad identitet på en befintlig virtuell dators skalnings uppsättning

Om du vill aktivera systemtilldelad hanterad identitet på en befintlig virtuell dators skalnings uppsättning måste du skaffa en åtkomsttoken och sedan använda spiral för att anropa Resource Manager REST-slutpunkten för att uppdatera identitets typen.

1. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa din skalnings uppsättning för virtuella datorer med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Använd följande spiral kommando för att anropa Azure Resource Manager REST-slutpunkten för att aktivera systemtilldelad hanterad identitet på den virtuella datorns skalnings uppsättning som identifieras i begär ande texten med värdet `{"identity":{"type":"SystemAssigned"}` för en skalnings uppsättning för virtuell dator med namnet *myVMSS*.  Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg när du begärde en åtkomst-token för Bearer och `<SUBSCRIPTION ID>` värdet efter behov för din miljö.
   
   > [!IMPORTANT]
   > För att se till att du inte tar bort befintliga hanterade identiteter som har tilldelats till den virtuella datorns skalnings uppsättning, måste du lista de användare som tilldelats hanterade identiteter med hjälp av det här spiral kommandot: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Om du har tilldelade tilldelade hanterade identiteter som har tilldelats till den virtuella datorns skalnings uppsättning som identifieras i `identity` svärdet i svaret går du vidare till steg 3 som visar hur du behåller användarspecifika hanterade identiteter samtidigt som du aktiverar systemtilldelad hanterad identitet på den virtuella datorns skal uppsättning.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

   **Brödtext i begäran**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. Om du vill aktivera systemtilldelad hanterad identitet på en virtuell dators skalnings uppsättning med befintliga användarspecifika hanterade identiteter, måste du lägga till `SystemAssigned` till `type`-värdet.  
   
   Om den virtuella datorns skalnings uppsättning till exempel har tilldelats tilldelade hanterade identiteter `ID1` och `ID2` tilldelats, och du vill lägga till systemtilldelad hanterad identitet i den virtuella datorns skalnings uppsättning, använder du följande anrop. Ersätt `<ACCESS TOKEN>` och `<SUBSCRIPTION ID>` med värden som är lämpliga för din miljö.

   Med API-version `2018-06-01` lagras användare tilldelade hanterade identiteter i `userAssignedIdentities`-värdet i ett ord lista i stället för `identityIds` svärdet i ett mat ris format som används i API-versionen `2017-12-01`.
   
   **API-VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. |
 
   **Brödtext i begäran**

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
   
   **API-VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

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

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Inaktivera systemtilldelad hanterad identitet från en skalnings uppsättning för virtuell dator

Om du vill inaktivera en systemtilldelad identitet på en befintlig virtuell dators skalnings uppsättning måste du skaffa en åtkomsttoken och sedan använda spiral för att anropa Resource Manager REST-slutpunkten för att uppdatera identitets typen till `None`.

1. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa din skalnings uppsättning för virtuella datorer med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Uppdatera den virtuella datorns skalnings uppsättning med hjälp av vändning för att anropa Azure Resource Manager REST-slutpunkten för att inaktivera systemtilldelad hanterad identitet.  I följande exempel inaktive ras systemtilldelad hanterad identitet som identifieras i begär ande texten av värdet `{"identity":{"type":"None"}}` från en skalnings uppsättning för virtuell dator med namnet *myVMSS*.  Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg när du begärde en åtkomst-token för Bearer och `<SUBSCRIPTION ID>` värdet efter behov för din miljö.

   > [!IMPORTANT]
   > För att se till att du inte tar bort befintliga hanterade identiteter som har tilldelats till den virtuella datorns skalnings uppsättning, måste du lista de användare som tilldelats hanterade identiteter med hjälp av det här spiral kommandot: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Om du har en användardefinierad hanterad identitet som är tilldelad till den virtuella datorns skal uppsättning går du vidare till steg 3 som visar hur du behåller de tilldelade hanterade identiteterna samtidigt som den systemtilldelade hanterade identiteten tas bort från den virtuella datorns skal uppsättning.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

   **Brödtext i begäran**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   Om du vill ta bort den systemtilldelade hanterade identiteten från en skalnings uppsättning för virtuella datorer som har tilldelade hanterade identiteter tar du bort `SystemAssigned` från `{"identity":{"type:" "}}`-värdet samtidigt som du behåller `UserAssigned`-värdet och värdena för `userAssignedIdentities`-ordlistan om du använder **API version 2018-06-01**. Behåll `identityIds` matrisen om du använder **API version 2017-12-01** eller tidigare.

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort användardefinierad hanterad identitet på en virtuell dators skalnings uppsättning med hjälp av sväng för att ringa till Azure Resource Manager REST-slutpunkten.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Tilldela en användardefinierad hanterad identitet när en skalnings uppsättning för virtuell dator skapas

1. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa din skalnings uppsättning för virtuella datorer med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Skapa ett [nätverks gränssnitt](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) för den virtuella datorns skalnings uppsättning:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa din skalnings uppsättning för virtuella datorer med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Skapa en användardefinierad hanterad identitet med hjälp av anvisningarna här: [skapa en användardefinierad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Skapa en skalnings uppsättning för virtuella datorer med hjälp av vändning för att anropa Azure Resource Manager REST-slutpunkten. I följande exempel skapas en skalnings uppsättning för virtuella datorer med namnet *myVMSS* i resurs gruppen *myResourceGroup* med en användardefinierad hanterad identitets `ID1`som identifieras i begär ande texten av värdet `"identity":{"type":"UserAssigned"}`. Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg när du begärde en åtkomst-token för Bearer och `<SUBSCRIPTION ID>` värdet efter behov för din miljö.
 
   **API-VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

   **Brödtext i begäran**

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

   **API-VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. |
 
   **Brödtext i begäran**

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Tilldela en användardefinierad hanterad identitet till en befintlig skalnings uppsättning för virtuella Azure-datorer

1. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa din skalnings uppsättning för virtuella datorer med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Skapa en tilldelad hanterad identitet med hjälp av anvisningarna här, [skapa en användardefinierad hanterad identitet](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. För att se till att du inte tar bort befintliga användare eller systemtilldelade hanterade identiteter som har tilldelats till den virtuella datorns skalnings uppsättning, måste du lista de identitets typer som tilldelats den virtuella datorns skalnings uppsättning med hjälp av följande spiral-kommando. Om du har hanterade identiteter som har tilldelats den virtuella datorns skalnings uppsättning visas de i `identity` svärdet.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. |   
 

4. Om du inte har några användare eller systemtilldelade hanterade identiteter tilldelade till den virtuella datorns skalnings uppsättning, använder du följande kommando för att anropa Azure Resource Manager REST-slutpunkten för att tilldela den första användarspecifika hanterade identiteten till den virtuella datorn skalnings uppsättning.  Om du har en användare eller systemtilldelad hanterad identitet (er) som har tilldelats till den virtuella datorns skal uppsättning går du vidare till steg 5 som visar hur du lägger till flera användarspecifika hanterade identiteter i en skalnings uppsättning för virtuella datorer samtidigt som du behåller de systemtilldelade hanterade Autentiseringsidentitet.

   I följande exempel tilldelas en användardefinierad hanterad identitet `ID1` till en skalnings uppsättning för virtuell dator med namnet *myVMSS* i resurs gruppen *myResourceGroup*.  Ersätt `<ACCESS TOKEN>` med det värde som du fick i föregående steg när du begärde en åtkomst-token för Bearer och `<SUBSCRIPTION ID>` värdet efter behov för din miljö.

   **API-VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

   **Brödtext i begäran**

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
    
   **API-VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

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

5. Om du har en befintlig användardefinierad eller systemtilldelad hanterad identitet som är tilldelad till skalnings uppsättningen för den virtuella datorn:
   
   **API-VERSION 2018-06-01**

   Lägg till den användarspecifika hanterade identiteten i `userAssignedIdentities`s ord listans värde.

   Om du till exempel har systemtilldelad hanterad identitet och den tilldelade hanterade identiteten `ID1` för närvarande tilldelad till den virtuella datorns skala och vill lägga till den tilldelade hanterade identitets `ID2` till den:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

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

   Behåll de användare som tilldelats hanterade identiteter som du vill behålla i `identityIds` mat ris värde när du lägger till den nya användarspecifika hanterade identiteten.

   Om du t. ex. har tilldelats en systemtilldelad identitet och den tilldelade hanterade identiteten `ID1` för närvarande tilldelad till den virtuella datorns skalnings uppsättning och vill lägga till den användare som tilldelats den användare som tilldelats den hanterade identitets `ID2`:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

   **Brödtext i begäran**

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

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Ta bort en användardefinierad hanterad identitet från en skalnings uppsättning för virtuella datorer

1. Hämta en Bearer-åtkomsttoken som du kommer att använda i nästa steg i Authorization-huvudet för att skapa din skalnings uppsättning för virtuella datorer med en systemtilldelad hanterad identitet.

   ```azurecli-interactive
   az account get-access-token
   ```

2. För att se till att du inte tar bort befintliga hanterade identiteter som du vill behålla tilldelade till den virtuella datorns skalnings uppsättning eller ta bort den systemtilldelade hanterade identiteten, måste du lista de hanterade identiteterna med hjälp av följande spiral kommando :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. |
   
   Om du har hanterade identiteter som har tilldelats den virtuella datorn visas de i svaret i `identity`-värdet. 
    
   Om du till exempel har tilldelat hanterade identiteter `ID1` och `ID2` tilldelats till skalnings uppsättningen för den virtuella datorn, och du bara vill behålla `ID1` tilldelade och behålla den systemtilldelade hanterade identiteten:

   **API-VERSION 2018-06-01**

   Lägg till `null` i den användare-tilldelade hanterade identitet som du vill ta bort:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

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

   Behåll bara de användar tilldelade hanterade identiteter som du vill behålla i `identityIds` matris:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Begärandehuvuden**

   |Begärandehuvud  |Beskrivning  |
   |---------|---------|
   |*Innehålls typ*     | Krävs. Ange till `application/json`.        |
   |*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

   **Brödtext i begäran**

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

Om den virtuella datorns skalnings uppsättning har både systemtilldelade och användarspecifika hanterade identiteter, kan du ta bort alla tilldelade hanterade identiteter genom att växla till Använd endast system som tilldelats med hjälp av följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Begärandehuvuden**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehålls typ*     | Krävs. Ange till `application/json`.        |
|*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

**Brödtext i begäran**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Om den virtuella datorns skalnings uppsättning bara har tilldelade hanterade identiteter och du vill ta bort alla använder du följande kommando:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Begärandehuvuden**

|Begärandehuvud  |Beskrivning  |
|---------|---------|
|*Innehålls typ*     | Krävs. Ange till `application/json`.        |
|*Auktorisering*     | Krävs. Ange en giltig `Bearer` åtkomsttoken. | 

**Brödtext i begäran**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar, listar eller tar bort användare som tilldelats hanterade identiteter med hjälp av REST finns i:

- [Skapa, Visa eller ta bort en användardefinierad hanterad identitet med hjälp av REST API-anrop](how-to-manage-ua-identity-rest.md)
