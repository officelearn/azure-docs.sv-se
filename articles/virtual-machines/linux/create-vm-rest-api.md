---
title: Skapa en Linux-dator med Azure REST-API | Microsoft Docs
description: Lär dig hur du skapar en Linux-dator i Azure som använder Managed Disks och SSH-autentisering med Azure REST API.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 3eeaee9bc6320231f10aa85227e2f43756181806
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433488"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Skapa en Linux-dator som använder SSH-autentisering med REST API

En Linux-dator (VM) i Azure består av olika resurser, till exempel diskar och nätverk-gränssnitt och definierar parametrar, till exempel plats, storlek och operativsystemet inställningar för avbildning och autentisering.

Du kan skapa en Linux-VM via Azure portal, Azure CLI 2.0, många Azure SDK: er, Azure Resource Manager-mallar och många verktyg från tredje part, till exempel Ansible eller Terraform. Alla dessa verktyg använda slutligen REST API för att skapa Linux VM.

Den här artikeln visar hur du använder REST-API för att skapa en Linux-VM som kör Ubuntu 18.04-LTS med hanterade diskar och SSH-autentisering.

## <a name="before-you-start"></a>Innan du börjar

Innan du skapar och skickar begäran behöver du:

* Den `{subscription-id}` för din prenumeration
  * Om du har flera prenumerationer, se [arbeta med flera prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions)
* En `{resourceGroupName}` du har skapat i tid
* En [virtuellt nätverksgränssnitt](../../virtual-network/virtual-network-network-interface.md) i samma resursgrupp
* En SSH-nyckelpar (du kan [Generera en ny](mac-create-ssh-keys.md) om du inte har någon)

## <a name="request-basics"></a>Grunderna i begäran

Om du vill skapa eller uppdatera en virtuell dator, använder du följande *PLACERA* igen:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Förutom den `{subscription-id}` och `{resourceGroupName}` parametrar, måste du ange den `{vmName}` (`api-version` är valfritt, men den här artikeln har testats med `api-version=2017-12-01`)

Följande huvuden krävs:

| Begärandehuvud   | Beskrivning |
|------------------|-----------------|
| *Content-Type:*  | Krävs. Ange `application/json`. |
| *Auktorisering:* | Krävs. Ange att ett giltigt `Bearer` [åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Allmän information om hur du arbetar med REST API-begäranden finns i [komponenterna i en REST API-begäran/svar](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Skapa begärandetexten

Följande vanliga definitioner används för att skapa en brödtext i begäran:

| Namn                       | Krävs | Typ                                                                                | Beskrivning  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | sträng                                                                              | Resursplats. |
| namn                       |          | sträng                                                                              | Namnet för den virtuella datorn. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Anger maskinvaruinställningarna för den virtuella datorn. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Anger lagringsinställningar för för virtuella diskar. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Anger inställningar för operativsystemet för den virtuella datorn. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Anger nätverksgränssnitt för den virtuella datorn. |

En exempel-begärandetexten är under. Kontrollera att du anger namnet på virtuell dator i den `{computerName}` och `{name}` parametrar, namnet på nätverksgränssnittet som du har skapat under `networkInterfaces`, ditt användarnamn i `adminUsername` och `path`, och *offentliga*del av din SSH-nyckelpar (finns i, till exempel `~/.ssh/id_rsa.pub`) i `keyData`. Andra parametrar som du kanske vill ändra inkluderar `location` och `vmSize`.  

```json
{
  "location": "eastus",
  "name": "{vmName}",
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
      "computerName": "{vmName}",
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
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

En fullständig lista över tillgängliga definitioner i begärandetexten, se [virtuella datorer skapa eller uppdatera begäran brödtext defintions](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Begäran skickades

Du kan använda klienten om vill skicka den här HTTP-begäran. Du kan också använda en [webbläsarbaserade verktyget](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) genom att klicka på den **prova** knappen.

### <a name="responses"></a>Svar

Det finns två lyckades för åtgärden att skapa eller uppdatera en virtuell dator:

| Namn        | Typ                                                                              | Beskrivning |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Ok          |
| 201 Skapad | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Skapad     |

Ett komprimerat *201 Skapad* svar från föregående exempel begärandetexten som skapar en virtuell dator visas en *vmId* har tilldelats och *provisioningState* är *Skapar*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Mer information om REST API-svar finns i [bearbeta svarsmeddelandet](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure REST API: er eller andra hanteringsverktyg, till exempel Azure CLI eller Azure PowerShell finns i följande:

- [Azure Compute-providern REST API](/rest/api/compute/)
- [Kom igång med Azure REST API](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Azure PowerShell-modul](/powershell/azure/overview)
