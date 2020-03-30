---
title: Använda en mall för att distribuera virtuella Azure Spot-datorer (förhandsversion)
description: Lär dig hur du använder en mall för att distribuera spot-datorer för att spara kostnader.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 0e635fe7ce9b442a9cc8f0fdf614feef5a3a756a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082803"
---
# <a name="deploy-spot-vms-using-a-resource-manager-template"></a>Distribuera spot-datorer med hjälp av en Resource Manager-mall

Med hjälp av [Spot virtuella datorer](spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till en betydande kostnadsbesparingar. När som helst när Azure behöver tillbaka kapaciteten kommer Azure-infrastrukturen att ta bort spot-datorer. Därför är spot-virtuella datorer bra för arbetsbelastningar som kan hantera avbrott som batchbearbetningsjobb, utvecklings-/testmiljöer, stora beräkningsarbetsbelastningar med mera.

Priserna för spot-virtuella datorer varierar, baserat på region och SKU. Mer information finns i VM-priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Du har möjlighet att ställa in ett maxpris som du är villig att betala, per timme, för den virtuella datorn. Maxpriset för en spot-VM kan ställas in i US-dollar (USD), med upp till 5 decimaler. Värdet `0.98765`skulle till exempel vara ett maxpris på 0,98765 USD per timme. Om du ställer in `-1`maxpriset så kommer den virtuella datorn inte att vräkas baserat på priset. Priset för den virtuella datorn blir det aktuella priset för Spot eller priset för en vanlig virtuell dator, som någonsin är mindre, så länge det finns kapacitet och kvot tillgänglig. Mer information om hur du anger maxpriset finns i [Spot-virtuella datorer - Prissättning](spot-vms.md#pricing).

> [!IMPORTANT]
> Spot-instanser är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="use-a-template"></a>Använda en mall

Använd`"apiVersion": "2019-03-01"` eller senare för spotmalldistributioner. Lägg `priority`till `evictionPolicy` `billingProfile` , och egenskaperna i mallen:

```json
"priority": "Spot",
"evictionPolicy": "Deallocate",
"billingProfile": {
    "maxPrice": -1
}
```

Här är en exempelmall med de tillagda egenskaperna för en spot-vm. Ersätt resursnamnen med `<password>` dina egna och med ett lösenord för det lokala administratörskontot på den virtuella datorn.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2019-03-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "vnetId": "/subscriptions/ec9fcd04-e188-48b9-abfc-abcd515f1836/resourceGroups/spotVM/providers/Microsoft.Network/virtualNetworks/spotVM",
        "subnetName": "default",
        "networkInterfaceName": "spotVMNIC",
        "publicIpAddressName": "spotVM-ip",
        "publicIpAddressType": "Dynamic",
        "publicIpAddressSku": "Basic",
        "virtualMachineName": "spotVM",
        "osDiskType": "Premium_LRS",
        "virtualMachineSize": "Standard_D2s_v3",
        "adminUsername": "azureuser",
        "adminPassword": "<password>",
        "diagnosticsStorageAccountName": "diagstoragespot2019",
        "diagnosticsStorageAccountId": "Microsoft.Storage/storageAccounts/diagstoragespot2019",
        "diagnosticsStorageAccountType": "Standard_LRS",
        "diagnosticsStorageAccountKind": "Storage",
        "subnetRef": "[concat(variables('vnetId'), '/subnets/', variables('subnetName'))]"
    },
    "resources": [
        {
            "name": "spotVM",
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId(resourceGroup().name, 'Microsoft.Network/publicIpAddresses', variables('publicIpAddressName'))]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "name": "[variables('publicIpAddressName')]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2019-02-01",
            "location": "eastus",
            "properties": {
                "publicIpAllocationMethod": "[variables('publicIpAddressType')]"
            },
            "sku": {
                "name": "[variables('publicIpAddressSku')]"
            }
        },
        {
            "name": "[variables('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', variables('networkInterfaceName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[variables('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[variables('virtualMachineName')]",
                    "adminUsername": "[variables('adminUsername')]",
                    "adminPassword": "[variables('adminPassword')]"
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('https://', variables('diagnosticsStorageAccountName'), '.blob.core.windows.net/')]"
                    }
                },
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            }
        },
        {
            "name": "[variables('diagnosticsStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "location": "eastus",
            "properties": {},
            "kind": "[variables('diagnosticsStorageAccountKind')]",
            "sku": {
                "name": "[variables('diagnosticsStorageAccountType')]"
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[variables('adminUsername')]"
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

Du kan också skapa en spot-vm med [Azure PowerShell](../windows/spot-powershell.md) eller [Azure CLI](spot-cli.md).

Om du stöter på ett fel läser du [Felkoder](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).