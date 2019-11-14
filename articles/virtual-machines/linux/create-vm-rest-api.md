---
title: Skapa en virtuell Linux-dator med Azure-REST API
description: Lär dig hur du skapar en virtuell Linux-dator i Azure som använder Managed Disks och SSH-autentisering med Azure REST API.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: c1010bf4bde01920449e9252de563d79bfc61997
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036435"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Skapa en virtuell Linux-dator som använder SSH-autentisering med REST API

En virtuell Linux-dator (VM) i Azure består av olika resurser, till exempel diskar och nätverks gränssnitt, och definierar parametrar som plats, storlek och operativ system avbildning och autentiseringsinställningar.

Du kan skapa en virtuell Linux-dator via Azure Portal, Azure CLI 2,0, många Azure SDK: er, Azure Resource Manager mallar och många verktyg från tredje part, till exempel Ansible eller terraform. Alla dessa verktyg använder i slut änden REST API för att skapa den virtuella Linux-datorn.

Den här artikeln visar hur du använder REST API för att skapa en virtuell Linux-dator som kör Ubuntu 18,04-LTS med hanterade diskar och SSH-autentisering.

## <a name="before-you-start"></a>Innan du börjar

Innan du skapar och skickar in begäran behöver du:

* Prenumerationens `{subscription-id}`
  * Om du har flera prenumerationer kan du läsa [arbeta med flera prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* En `{resourceGroupName}` som du har skapat i förväg
* Ett [virtuellt nätverks gränssnitt](../../virtual-network/virtual-network-network-interface.md) i samma resurs grupp
* Ett SSH-nyckelpar (du kan [generera ett nytt](mac-create-ssh-keys.md) om du inte har något)

## <a name="request-basics"></a>Grundläggande om begäran

Använd *följande åtgärd* för att skapa eller uppdatera en virtuell dator:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Förutom parametrarna `{subscription-id}` och `{resourceGroupName}` måste du ange `{vmName}` (`api-version` är valfritt, men den här artikeln har testats med `api-version=2017-12-01`)

Följande huvuden krävs:

| Begärandehuvud   | Beskrivning |
|------------------|-----------------|
| *Content-Type:*  | Krävs. Ange till `application/json`. |
| *Auktorisering:* | Krävs. Ange till en giltig `Bearer`-[åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Allmän information om hur du arbetar med REST API begär Anden finns i [komponenter i en REST API begäran/svar](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Skapa begär ande texten

Följande vanliga definitioner används för att bygga en begär ande text:

| Namn                       | Krävs | Typ                                                                                | Beskrivning  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | sträng                                                                              | Resursplats. |
| namn                       |          | sträng                                                                              | Namn på den virtuella datorn. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Anger maskin varu inställningarna för den virtuella datorn. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Anger lagrings inställningarna för de virtuella dator diskarna. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Anger operativ system inställningarna för den virtuella datorn. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Anger nätverks gränssnitten för den virtuella datorn. |

Ett exempel på en begär ande text visas nedan. Se till att du anger namnet på den virtuella datorn i `{computerName}` och `{name}` parametrar, namnet på det nätverks gränssnitt som du har skapat under `networkInterfaces`, ditt användar namn i `adminUsername` och `path`och den *offentliga* delen av SSH-nyckelpar (finns i, till exempel `~/.ssh/id_rsa.pub`) i `keyData`. Andra parametrar som du kanske vill ändra är `location` och `vmSize`.  

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

En fullständig lista över tillgängliga definitioner i begär ande texten finns i [Virtual Machines Create eller Update Request Body definitions](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Begäran skickas

Du kan använda klienten för din preferens för att skicka denna HTTP-begäran. Du kan också använda ett [i webb läsar verktyg](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) genom att klicka på knappen **prova** .

### <a name="responses"></a>Svar

Det finns två lyckade svar för åtgärden att skapa eller uppdatera en virtuell dator:

| Namn        | Typ                                                                              | Beskrivning |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 har skapats | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Skapad     |

Ett komprimerat 201-svar som *skapats* från föregående exempel begär ande text som skapar en virtuell dator visar att ett *vmId* har tilldelats och att *provisioningState* *skapas*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Mer information om REST API svar finns i [bearbeta svars meddelandet](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure REST-API: er eller andra hanterings verktyg som Azure CLI eller Azure PowerShell finns i följande avsnitt:

- [Azure Compute Provider REST API](/rest/api/compute/)
- [Komma igång med Azure REST API](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Azure PowerShell modul](/powershell/azure/overview)
