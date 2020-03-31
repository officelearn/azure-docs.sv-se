---
title: Skapa en virtuell Linux-dator med REST API
description: Lär dig hur du skapar en virtuell Linux-dator i Azure som använder hanterade diskar och SSH-autentisering med Azure REST API.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 1594c030839cccdd48c4b032c6ad92f746f78e26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970280"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Skapa en virtuell Linux-dator som använder SSH-autentisering med REST API

En virtuell Linux-dator (VM) i Azure består av olika resurser, till exempel diskar och nätverksgränssnitt, och definierar parametrar som plats, storlek och inställningar för operativsystemavbildning och autentisering.

Du kan skapa en Virtuell Linux-dator via Azure-portalen, Azure CLI 2.0, många Azure SDK:er, Azure Resource Manager-mallar och många verktyg från tredje part, till exempel Ansible eller Terraform. Alla dessa verktyg använder i slutändan REST API för att skapa Linux VM.

Den här artikeln visar hur du använder REST API för att skapa en Linux VM som kör Ubuntu 18.04-LTS med hanterade diskar och SSH-autentisering.

## <a name="before-you-start"></a>Innan du börjar

Innan du skapar och skickar begäran behöver du:

* För `{subscription-id}` din prenumeration
  * Om du har flera prenumerationer läser [du Arbeta med flera prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* A `{resourceGroupName}` som du har skapat i förväg
* Ett [virtuellt nätverksgränssnitt](../../virtual-network/virtual-network-network-interface.md) i samma resursgrupp
* Ett SSH-nyckelpar (du kan [generera ett nytt](mac-create-ssh-keys.md) om du inte har ett)

## <a name="request-basics"></a>Grunderna i begäran

Om du vill skapa eller uppdatera en virtuell dator använder du följande *PUT-åtgärd:*

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Förutom parametrarna `{subscription-id}` `{resourceGroupName}` och måste du ange `{vmName}` (är`api-version` valfritt, men den här `api-version=2017-12-01`artikeln testades med)

Följande huvuden krävs:

| Begärandehuvud   | Beskrivning |
|------------------|-----------------|
| *Content-Type:*  | Krävs. Ange till `application/json`. |
| *Auktorisering:* | Krävs. Ange till en giltig -`Bearer` [åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Allmän information om hur du arbetar med REST API-begäranden finns i [Komponenter i en REST API-begäran/svar](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Skapa förfrådetexten

Följande vanliga definitioner används för att skapa en begärantext:

| Namn                       | Krävs | Typ                                                                                | Beskrivning  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | sträng                                                                              | Resursplats. |
| namn                       |          | sträng                                                                              | Namn på den virtuella datorn. |
| egenskaper.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Anger maskinvaruinställningarna för den virtuella datorn. |
| egenskaper.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Anger lagringsinställningarna för diskarna för den virtuella datorn. |
| egenskaper.osProfile       |          | [OSProfile (9)999](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Anger operativsystemets inställningar för den virtuella datorn. |
| egenskaper.networkProfile  |          | [Nätverksprofil](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Anger nätverksgränssnitten för den virtuella datorn. |

Ett exempel begäran kropp är nedan. Se till att du anger `{computerName}` `{name}` vm-namnet i och parametrarna, namnet `networkInterfaces`på det `adminUsername` `path`nätverksgränssnitt som du har skapat under , ditt användarnamn `~/.ssh/id_rsa.pub`i `keyData`och , och den *offentliga* delen av SSH-nyckelparet (finns i till exempel) i . Andra parametrar som du `location` kanske `vmSize`vill ändra inkluderar och .  

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

En fullständig lista över tillgängliga definitioner i begärandetexten finns i [Virtuella datorer skapa eller uppdatera kroppsdefinitioner för begäran](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Skicka begäran

Du kan använda klienten för dina önskemål för att skicka denna HTTP-begäran. Du kan också använda ett [verktyg i webbläsaren](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) genom att klicka på knappen **Prova.**

### <a name="responses"></a>Svar

Det finns två lyckade svar för åtgärden för att skapa eller uppdatera en virtuell dator:

| Namn        | Typ                                                                              | Beskrivning |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Skapad | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Skapad     |

Ett komprimerat *201 Skapat* svar från föregående exempelbegärandhet som skapar en virtuell dator visar att en *vmId* har tilldelats och *etableringstaten* *skapar:*

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Mer information om REST API-svar finns [i Bearbeta svarsmeddelandet](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure REST-API:er eller andra hanteringsverktyg som Azure CLI eller Azure PowerShell finns i följande:

- [REST-API för Azure Compute-provider](/rest/api/compute/)
- [Komma igång med Azure REST API](/rest/api/azure/)
- [Azure CLI](/cli/azure/)
- [Azure PowerShell-modul](/powershell/azure/overview)
