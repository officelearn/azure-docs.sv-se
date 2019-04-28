---
title: Uppdatera resurser i Azure hanterade program | Microsoft Docs
description: Beskriver hur du arbetar på resurser i den hanterade resursgruppen för en Azure-hanterat program.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 21f4e0aa339eb0c746f9b9b06f8aaada6c4d4b71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043466"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Arbeta med resurser i den hanterade resursgruppen för Azure-hanterat program

Den här artikeln beskriver hur du uppdaterar resurser som distribueras som en del av ett hanterat program. Som utgivare av ett hanterat program har du åtkomst till resurser i den hanterade resursgruppen. Om du vill uppdatera dessa resurser måste du hitta den hanterade resursgruppen som är associerade med ett hanterat program och åtkomst till resursen i den resursgruppen.

Den här artikeln förutsätter att du har distribuerat det hanterade programmet i den [hanteras webbprogrammet (IaaS) med Azure-hanteringstjänster](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) exempelprojektet. Att det hanterade programmet innehåller en **Standard_D1_v2** virtuell dator. Om du inte har distribuerat det hanterade programmet kan du fortfarande använda den här artikeln för att bekanta dig med stegen för att uppdatera en hanterad resursgrupp.

Följande bild visar det distribuerade hanterade programmet.

![Distribuerade hanterade programmet](./media/update-managed-resources/deployed.png)

I den här artikeln använder du Azure CLI för att:

* Identifiera det hanterade programmet
* Identifiera den hanterade resursgruppen
* Identifiera resurserna som virtuell dator i den hanterade resursgruppen
* Ändra storleken på virtuella datorn (antingen till en mindre storlek om inte används eller ett större stöd för högre belastning)
* Tilldela en princip till den hanterade resursgruppen som anger tillåtna platser

## <a name="get-managed-application-and-managed-resource-group"></a>Hämta hanterade program och hanterad resursgrupp

Hämta hanterade program i en resursgrupp med:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Hämta ID för den hanterade resursgruppen med:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Ändra storlek på virtuella datorer i en hanterad resursgrupp

Om du vill se de virtuella datorerna i den hanterade resursgruppen anger du namnet på den hanterade resursgruppen.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

För att uppdatera storleken på de virtuella datorerna, använder du:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

När åtgärden har slutförts kan du verifiera att programmet körs på Standard D2 v2.

![Hanterat program med hjälp av Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Tillämpa principen på hanterad resursgrupp

Hämta den hanterade resursgruppen och tilldelning av en princip på detta omfång. Principen **e56962a6-4747-49cd-b67b-bf8b01975c4c** är en inbyggd princip för att ange tillåtna platser.

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

Om du vill se de tillåtna platserna, använder du:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

Principtilldelningen visas i portalen.

![Visa principtilldelning](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* -Exempelprojekt, se [exempelprojekten för Azure hanterade program](sample-projects.md).
