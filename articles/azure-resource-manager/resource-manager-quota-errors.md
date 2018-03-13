---
title: Azure kvoten fel | Microsoft Docs
description: "Beskriver hur du löser resurs qouta fel."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: d2cc760bf516e8ee96629886120a1bb092932a82
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="resolve-errors-for-resource-quotas"></a>Åtgärda fel för resurskvoter

Den här artikeln beskriver kvoten fel som kan uppstå när du distribuerar resurser.

## <a name="symptom"></a>Symptom

Om du distribuerar en mall som skapar resurser som överskrider Azure-kvoter får du ett distribueringsfel som ser ut som:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Eller så kan du se:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Orsak

Kvoter tillämpas per resursgrupp, prenumerationer, konton och andra scope. Din prenumeration kan exempelvis konfigureras för att begränsa antalet kärnor för en region. Om du försöker att distribuera en virtuell dator med flera kärnor än den tillåtna visas ett felmeddelande om kvoten har överskridits.
För fullständig kvotinformation finns i [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="azure-cli"></a>Azure CLI

Azure CLI, använder den `az vm list-usage` kommando för att hitta kvoter för virtuell dator.

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

PowerShell, Använd den **Get-AzureRmVMUsage** kommando för att hitta kvoter för virtuell dator.

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

Gå till portalen för att begära en ökad kvot och filen ett supportproblem. I support-problemet du begära en ökning av din kvot för den region som du vill distribuera.

> [!NOTE]
> Kom ihåg att för resursgrupper, kvoten för varje enskild region, inte för hela prenumerationen. Om du behöver distribuera 30 kärnor i USA, västra måste du be om 30 Resource Manager kärnor i USA, västra. Om du behöver distribuera 30 kärnor i någon av regionerna som du har åtkomst bör du be om 30 Resource Manager kärnor i alla regioner.
>
>

1. Välj **prenumerationer**.

   ![Prenumerationer](./media/resource-manager-quota-errors/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

   ![Välj en prenumeration](./media/resource-manager-quota-errors/select-subscription.png)

3. Välj **användning + kvoter**

   ![Välj användnings- och kvoter](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. I det övre högra hörnet väljer **begära**.

   ![Begära](./media/resource-manager-quota-errors/request-increase.png)

5. Fyll i formulär för typ av du behöva öka kvoten.

   ![Fyll i formuläret](./media/resource-manager-quota-errors/forms.png)