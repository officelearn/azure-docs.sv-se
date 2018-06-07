---
title: 'Proceduren för att uppgradera eller flytta SKU: N för PrimaryNodeType till högre SKU: er | Microsoft Docs'
description: 'Lär dig mer om att uppgradera eller flytta SKU: N för PrimaryNodeType till högre SKU: er med hjälp av PowerShell-kommandon och CLI-kommandona.'
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 96956543a44b6d5d967e3bae3fd833b08baf3d6f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642741"
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>Uppgradera eller flytta SKU: N för primära nodtypen till högre SKU

Den här artikeln beskriver hur du uppgraderingen eller flytta SKU för primära nodtypen för service fabric-kluster till högre SKU med hjälp av Azure PowerShell

## <a name="add-a-new-virtual-machine-scale-set"></a>Lägg till en ny skaluppsättning för virtuell dator

Distribuera en ny skaluppsättning för virtuell dator och belastningsutjämnare. Service Fabric-tillägget configuration (särskilt nodtypen) för ny skaluppsättning för virtuell dator ska vara samma som den gamla skaluppsättning du försöker uppgradera. Kontrollera i Service Fabric explorer att din nya noder är tillgängliga

#### <a name="azure-powershell"></a>Azure PowerShell

I följande exempel används Azure PowerShell för att distribuera uppdaterade Resource Manager-mall *template.json* med resursgrupp med namnet *myResourceGroup*:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile template.json -TemplateParameterFile parameters.json
```

#### <a name="azure-cli"></a>Azure CLI

Följande kommando använder Azure Service Fabric CLI för att distribuera uppdaterade Resource Manager-mall *template.json* med resursgrupp med namnet *myResourceGroup*:

```CLI
az group deployment create --resource-group myResourceGroup --template-file template.json --parameters parameters.json
```

Se följande exempel för att ändra json-mall för att lägga till nya virtuella datorn scale set resursen med primära nodtypen i befintligt kluster

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
                                "properties": {
                                    "type": "IaaSDiagnostics",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                        "storageAccountEndPoint": "https://core.windows.net/"
                                    },
                                    "publisher": "Microsoft.Azure.Diagnostics",
                                    "settings": {
                                        "WadCfg": {
                                            "DiagnosticMonitorConfiguration": {
                                                "overallQuotaInMB": "50000",
                                                "EtwProviders": {
                                                    "EtwEventSourceProviderConfiguration": [
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Actors",
                                                            "scheduledTransferKeywordFilter": "1",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableActorEventTable"
                                                            }
                                                        },
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Services",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                            }
                                                        }
                                                    ],
                                                    "EtwManifestProviderConfiguration": [
                                                        {
                                                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                            "scheduledTransferLogLevelFilter": "Information",
                                                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricSystemEventTable"
                                                            }
                                                        }
                                                    ]
                                                }
                                            }
                                        },
                                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                                    },
                                    "typeHandlerVersion": "1.5"
                                }
                            }
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
                                                }
                                            }
                                        }
                                    ],
                                    "primary": true
                                }
                            }
                        ]
                    },
                    "osProfile": {
                        "adminPassword": "[parameters('adminPassword')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[parameters('sourceVaultValue')]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateStore": "[parameters('certificateStoreValue')]",
                                        "certificateUrl": "[parameters('certificateUrlValue')]"
                                    }
                                ]
                            }
                        ]
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[parameters('vmImagePublisher')]",
                            "offer": "[parameters('vmImageOffer')]",
                            "sku": "[parameters('vmImageSku')]",
                            "version": "[parameters('vmImageVersion')]"
                        },
                        "osDisk": {
                            "caching": "ReadOnly",
                            "createOption": "FromImage",
                            "managedDisk": {
                                "storageAccountType": "[parameters('storageAccountType')]"
                            }
                        }
                    }
                }
            },
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```

## <a name="remove-old-virtual-machine-scale-set"></a>Ta bort gamla virtuella datorns skaluppsättning

1. Inaktivera VM-instanser av den gamla virtuella skala med avsikt att ta bort noden. Den här åtgärden kan ta lång tid att slutföra. Du kan inaktivera alla samtidigt och de ska placeras i kö. Vänta tills alla noder har inaktiverats. 
#### <a name="azure-powershell"></a>Azure PowerShell
I följande exempel används Azure Service Fabric PowerShell för att inaktivera noden instans med namnet *NTvm1_0* från den gamla virtuella skala uppsättning med namnet *NTvm1*:
```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode
```
#### <a name="azure-cli"></a>Azure CLI
Följande kommando använder Azure Service Fabric CLI för att inaktivera noden instans med namnet *NTvm1_0* från den gamla virtuella skala uppsättning med namnet *NTvm1*:
```CLI
sfctl node disable --node-name "_NTvm1_0" --deactivation-intent RemoveNode
```
2. Ta bort den fullständiga skaluppsättning. Vänta tills skaluppsättning Etableringsstatus lyckades
#### <a name="azure-powershell"></a>Azure PowerShell
I följande exempel används Azure PowerShell för att ta bort klar skaluppsättningen namngivna *NTvm1* från resursgrupp med namnet *myResourceGroup*:
```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```
#### <a name="azure-cli"></a>Azure CLI
Följande kommando använder Azure Service Fabric CLI för att ta bort klar skaluppsättningen namngivna *NTvm1* från resursgrupp med namnet *myResourceGroup*:

```CLI
az vmss delete --name NTvm1 --resource-group myResourceGroup
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>Ta bort belastningsutjämnaren som rör gamla skaluppsättning

Ta bort belastningsutjämnaren som rör gamla skaluppsättning. Det här steget gör att en kort period driftstopp för klustret

#### <a name="azure-powershell"></a>Azure PowerShell

I följande exempel används Azure PowerShell för att ta bort belastningsutjämnaren med namnet *LB-myCluster-NTvm1* rör gamla skala från resursgrupp med namnet *myResourceGroup*:

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

Följande kommando använder Azure Service Fabric CLI för att ta bort belastningsutjämnaren med namnet *LB-myCluster-NTvm1* rör gamla skala från resursgrupp med namnet *myResourceGroup*:

```CLI
az network lb delete --name LB-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>Ta bort offentlig IP-adress som rör gamla skaluppsättning

Store DNS-inställningar för offentliga IP-adressen rör gamla skala i variabeln och ta sedan bort offentlig IP-adress

#### <a name="azure-powershell"></a>Azure PowerShell

I följande exempel används Azure PowerShell för att lagra DNS-inställningar för offentliga IP-adress med namnet *LBIP-myCluster-NTvm1* i variabeln och ta bort IP-adressen:

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

Följande kommando använder Azure Service Fabric CLI för att hämta DNS-inställningar för offentliga IP-adress med namnet *LBIP-myCluster-NTvm1* och ta bort IP-adressen:

```CLI
az network public-ip show --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
az network public-ip delete --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>Uppdatera offentlig IP-adress som rör ny skaluppsättning

Uppdatera DNS-inställningar för offentliga IP-adress som är relaterade till nya skala med DNS-inställningar för offentliga IP-adress som är relaterade till gamla skaluppsättning

#### <a name="azure-powershell"></a>Azure PowerShell
I följande exempel används Azure PowerShell för att uppdatera DNS-inställningarna för den offentliga IP-adressen med namnet *LBIP-myCluster-NTvm3* med DNS-inställningar lagras i variabler i tidigare steg:

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm3  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP
```

#### <a name="azure-cli"></a>Azure CLI

Följande kommando använder Azure Service Fabric CLI för att uppdatera DNS-inställningarna för den offentliga IP-adressen med namnet *LBIP-myCluster-NTvm3* med DNS-inställningar av gamla offentlig IP-adress som samlats in i tidigare steg:

```CLI
az network public-ip update --name LBIP-myCluster-NTvm3 --resource-group myResourceGroup --dns-name myCluster
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>Ta bort service fabric-noden kunskap från FM

Meddela Service Fabric att noder, vilket är nere har tagits bort från klustret. (Kör detta kommando för alla Virtuella instanser av den äldre skaluppsättning för virtuell dator) (Om hållbarhet gamla skaluppsättning för virtuell dator var silver eller guld, det här steget behövs kanske inte. Eftersom som görs av systemet automatiskt.)

#### <a name="azure-powershell"></a>Azure PowerShell
I följande exempel används Azure Service Fabric PowerShell för att meddela service fabric noden med namnet *NTvm1_0* har tagits bort:

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```

#### <a name="azure-cli"></a>Azure CLI

Följande kommando använder Azure Service Fabric CLI för att meddela service fabric noden med namnet *NTvm1_0* har tagits bort:

```CLI
sfctl node remove-state --node-name _NTvm1_0
```
