---
title: Uppdatera hanterade resurser
description: Beskriver hur du arbetar med resurser i den hanterade resursgruppen för ett Azure-hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651220"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Arbeta med resurser i den hanterade resursgruppen för Azure-hanterade program

I den här artikeln beskrivs hur du uppdaterar resurser som distribueras som en del av ett hanterat program. Som utgivare av ett hanterat program har du åtkomst till resurserna i den hanterade resursgruppen. Om du vill uppdatera dessa resurser måste du hitta den hanterade resursgruppen som är associerad med ett hanterat program och komma åt resursen i resursgruppen.

Den här artikeln förutsätter att du har distribuerat det hanterade programmet i [IaaS (Managed Web Application) med exempelprojekt för Azure-hanteringstjänster.](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) Det hanterade programmet innehåller en **Standard_D1_v2** virtuell dator. Om du inte har distribuerat det hanterade programmet kan du fortfarande använda den här artikeln för att bekanta dig med stegen för att uppdatera en hanterad resursgrupp.

Följande bild visar det distribuerade hanterade programmet.

![Distribuerade hanterade program](./media/update-managed-resources/deployed.png)

I den här artikeln använder du Azure CLI för att:

* Identifiera det hanterade programmet
* Identifiera den hanterade resursgruppen
* Identifiera resurserna för virtuella datorer i den hanterade resursgruppen
* Ändra den virtuella datorns storlek (antingen till en mindre storlek om den inte används, eller en större för att stödja mer belastning)
* Tilldela en princip till den hanterade resursgruppen som anger tillåtna platser

## <a name="get-managed-application-and-managed-resource-group"></a>Hämta hanterade program och hanterad resursgrupp

Om du vill hämta hanterade program i en resursgrupp använder du:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Om du vill hämta ID:et för den hanterade resursgruppen använder du:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Ändra storlek på virtuella datorer i hanterad resursgrupp

Om du vill visa de virtuella datorerna i den hanterade resursgruppen anger du namnet på den hanterade resursgruppen.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Om du vill uppdatera storleken på de virtuella datorerna använder du:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

När åtgärden är klar kontrollerar du att programmet körs på Standard D2 v2.

![Hanterad applikation med Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Tillämpa princip på hanterad resursgrupp

Hämta den hanterade resursgruppen och tilldelningen av en princip i det omfånget. Policy **e56962a6-4747-49cd-b67b-bf8b01975c4c** är en inbyggd policy för att ange tillåtna platser.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Om du vill se tillåtna platser använder du:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

Principtilldelningen visas i portalen.

![Visa principtilldelning](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* Exempel på projekt finns i [Exempel på projekt för Azure-hanterade program](sample-projects.md).
