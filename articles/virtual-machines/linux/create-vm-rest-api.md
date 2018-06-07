---
title: Skapa en virtuell Linux-dator med Azure REST API | Microsoft Docs
description: Lär dig hur du skapar en virtuell Linux-dator i Azure som använder hanterade diskar och SSH-autentisering med Azure REST API.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: iainfou
ms.openlocfilehash: e3f41bea26e9a5ff45b31ae9d9a2e5955317ad7a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825382"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Skapa en virtuell Linux-dator som använder SSH-autentisering med REST API

En virtuell dator (VM) i Azure definieras av olika parametrar som plats, maskinvara storlek, operativsystemavbildningen och autentiseringsuppgifterna för inloggning. Den här artikeln visar hur du skapar en virtuell Linux-dator som använder SSH-autentisering med hjälp av REST API.

Använd följande för att skapa eller uppdatera en virtuell dator, *PLACERA* igen:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>Skapa en förfrågan

Att skapa den *PLACERA* begäran i `{subscription-id}` parametern är obligatorisk. Om du har flera prenumerationer, se [arbeta med flera prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Du definierar en `{resourceGroupName}` och `{vmName}` för dina resurser tillsammans med den `api-version` parameter. Den här artikeln använder `api-version=2017-12-01`.

Följande huvuden krävs:

| Huvudet i begäran   | Beskrivning |
|------------------|-----------------|
| *Content-Type:*  | Krävs. Ange till `application/json`. |
| *Auktorisering:* | Krävs. Ange att en giltig `Bearer` [åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Mer information om hur du skapar begäran finns [komponenter i en REST API-begäran och svar](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Skapa begärandetexten

Följande vanliga definitioner används för att skapa en brödtext i begäran:

| Namn                       | Krävs | Typ                                                                                | Beskrivning  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | sträng                                                                              | Resursplats. |
| namn                       |          | sträng                                                                              | Namnet för den virtuella datorn. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Anger inställningar för maskinvara för den virtuella datorn. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Anger lagringsinställningar för för virtuella diskar. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Anger inställningar för operativsystemet för den virtuella datorn. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Anger nätverksgränssnitt för den virtuella datorn. |

En fullständig lista över tillgängliga definitionerna i frågans brödtext finns [virtuella datorer skapa eller uppdatera begäran brödtext defintions](/rest/api/compute/virtualmachines/createorupdate#definitions).

### <a name="example-request-body"></a>Exempel begärandetexten

Följande exempel begäran definierar en Ubuntu 18.04 LTS avbildning som använder premiumdiskar hanteras. Används för autentisering med SSH offentlig nyckel och den virtuella datorn använder en befintlig virtuella nätverksgränssnittskortet (NIC) som du har [tidigare skapade](../../virtual-network/virtual-network-network-interface.md). Ange din offentliga SSH-nyckeln i den *osProfile.linuxConfiguration.ssh.publicKeys.keyData* fältet. Om det behövs kan du [Generera en SSH-nyckelpar](mac-create-ssh-keys.md).

```json
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  },
  "name": "myVM"
}
```

## <a name="responses"></a>Svar

Det finns två lyckade svar för åtgärden att skapa eller uppdatera en virtuell dator:

| Namn        | Typ                                                                              | Beskrivning |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Ok          |
| 201 skapas | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Skapad     |

Mer information om REST API-svar finns [bearbeta svarsmeddelandet](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Exempelsvar

En komprimerad *201 Skapad* svar från föregående exempel begärandetexten som skapar en virtuell dator visas en *vmId* har tilldelats och *provisioningState* är *Skapar*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure REST API: er och andra hanteringsverktyg, till exempel Azure CLI 2.0 eller Azure PowerShell finns i följande:

- [Azure Compute-providern REST API](/rest/api/compute/)
- [Kom igång med Azure REST API](/rest/api/azure/)
- [Azure CLI 2.0](/cli/azure/)
- [Azure PowerShell-modulen](/powershell/azure/overview)
