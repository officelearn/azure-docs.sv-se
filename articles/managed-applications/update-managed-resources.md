---
title: Uppdatera resurser i Azure hanterade program | Microsoft Docs
description: "Beskriver hur du arbetar på resurser i den hanterade resursgruppen för en Azure hanterade program."
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: d3c955d7be0e7e6d45751c0e685bad498e524d94
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Arbeta med resurser i den hanterade resursgruppen för Azure hanterade program

Den här artikeln beskriver hur du uppdaterar resurser som distribueras som en del av ett hanterat program. Som utgivaren av ett hanterat program har du åtkomst till resurser i den hantera resursgruppen. Om du vill uppdatera dessa resurser, måste du hitta hanterade resursgruppen som är kopplade till ett hanterat program och åtkomst till resursen i resursgruppen.

Den här artikeln förutsätter att du har distribuerat det hanterade programmet i den [hanteras webbprogrammet (IaaS) med Azure hanteringstjänster](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) exempelprojektet. Att hanterade programmet innehåller en **Standard_D1_v2** virtuella datorn. Du kan använda den här artikeln ska bli bekant med stegen för att uppdatera en hanterad resursgrupp även om du inte har distribuerat det hanterade programmet.

Följande bild visar det distribuerade hanterade programmet.

![Distribuerade program](./media/update-managed-resources/deployed.png)

I den här artikeln kan du använda Azure CLI för att:

* Identifiera hanterade program
* Identifiera hanterade resursgruppen.
* Identifiera virtuella resurser i hanterade resursgrupp
* Ändra storleken på virtuella datorn (antingen till en mindre storlek om inte, eller en större att stödja mer belastning)
* Tilldela en princip till den hantera resursgruppen som anger de tillåtna platserna

## <a name="get-managed-application-and-managed-resource-group"></a>Hämta hanterade program och hanterade resursgruppen.

Om du vill hämta hanterade program i en resursgrupp, använder du:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

För att hämta ID för den hantera resursgruppen använder du:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Ändra storlek på virtuella datorer i hanterat resursgrupp

Ange namnet på gruppen hanterad resurs för att se de virtuella datorerna i gruppen hanterad resurs.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Uppdatera storleken på de virtuella datorerna med:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

När åtgärden har slutförts kontrollerar du att programmet körs på Standard D2 v2.

![Hanterade program med hjälp av Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Använda principen för hanterade resursgruppen.

Hämta hanterade resursgrupp och tilldelning av en princip på detta scope. Principen **e56962a6-4747-49cd-b67b-bf8b01975c4c** är en inbyggd princip för att ange tillåtna platser.

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

Principtilldelningens visas på portalen.

![Visa tilldelning av principer](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* Exempelprojekt, se [exempelprojekten för Azure hanterade program](sample-projects.md).
