---
title: Azure-kvot fel | Microsoft Docs
description: Beskriver hur du löser resource kvot fel.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 2cf9f914aeaee25cda8fd426454cb39a1afa0b4f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260234"
---
# <a name="resolve-errors-for-resource-quotas"></a>Åtgärda fel för resurskvoter

Den här artikeln beskriver kvot fel som kan uppstå när du distribuerar resurser.

## <a name="symptom"></a>Symtom

Om du distribuerar en mall som skapar resurser som överstiger din Azure-kvoter kan få du ett distributionsfel som ser ut som:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Eller så kan det uppstå:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Orsak

Kvoterna gäller per resursgrupp, prenumerationer, konton och andra områden. Din prenumeration kan till exempel vara konfigurerad för att begränsa antalet kärnor för en region. Om du försöker distribuera en virtuell dator med fler kärnor än det tillåtna får ett felmeddelande om kvoten har överskridits.
För fullständig kvotinformation finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="azure-cli"></a>Azure CLI

Azure CLI, använder de `az vm list-usage` kommando för att hitta kvoter för virtuella datorer.

```azurecli
az vm list-usage --location "South Central US"
```

Som returnerar:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

Du använder PowerShell använder den **Get-AzureRmVMUsage** kommando för att hitta kvoter för virtuella datorer.

```powershell
Get-AzureRmVMUsage -Location "South Central US"
```

Som returnerar:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Lösning

Om du vill begära en kvot, gå till portalen och skicka in ett supportärende. Begär en ökning av din kvot för den region där du vill distribuera i support-problemet.

> [!NOTE]
> Kom ihåg att för resursgrupper, kvoten är för en enskild region, inte för hela prenumerationen. Om du behöver distribuera 30 kärnor i västra USA, måste du be för 30 Resource Manager-kärnor i västra USA. Om du behöver distribuera 30 kärnor i någon av regionerna som du har åtkomst, bör du be om 30 Resource Manager-kärnor i alla regioner.
>
>

1. Välj **Prenumerationer**.

   ![Prenumerationer](./media/resource-manager-quota-errors/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

   ![Välj en prenumeration](./media/resource-manager-quota-errors/select-subscription.png)

3. Välj **användning + kvoter**

   ![Välj användning och kvoter](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. I det övre högra hörnet väljer **öka**.

   ![Begär ökning](./media/resource-manager-quota-errors/request-increase.png)

5. Fyll i formulären för den typ av kvot som du vill utöka.

   ![Fyll i formuläret](./media/resource-manager-quota-errors/forms.png)