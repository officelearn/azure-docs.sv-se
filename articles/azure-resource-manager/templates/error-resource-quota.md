---
title: Kvotfel
description: Beskriver hur du löser resurskvotfel när du distribuerar resurser med Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 410b086b39d63d03491d390364f4aec6300fc7c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273792"
---
# <a name="resolve-errors-for-resource-quotas"></a>Åtgärda fel för resurskvoter

I den här artikeln beskrivs kvotfel som kan uppstå när du distribuerar resurser.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptom

Om du distribuerar en mall som skapar resurser som överskrider dina Azure-kvoter får du ett distributionsfel som ser ut som:

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

Kvoter tillämpas per resursgrupp, prenumerationer, konton och andra omfång. Din prenumeration kan till exempel konfigureras så att antalet kärnor för en region begränsas. Om du försöker distribuera en virtuell dator med fler kärnor än det tillåtna antalet visas ett felmeddelande om att kvoten har överskridits.
Fullständig kvotinformation finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Felsökning

### <a name="azure-cli"></a>Azure CLI

För Azure CLI `az vm list-usage` använder du kommandot för att hitta kvoter för virtuella datorer.

```azurecli
az vm list-usage --location "South Central US"
```

Som returnerar:

```output
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

För PowerShell använder du kommandot **Get-AzVMUsage** för att hitta kvoter för virtuella datorer.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Som returnerar:

```output
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Lösning

Om du vill begära en kvotökning går du till portalen och lämnar in ett supportproblem. I supportfrågan begär du en ökning av kvoten för den region som du vill distribuera.

> [!NOTE]
> Kom ihåg att för resursgrupper är kvoten för varje enskild region, inte för hela prenumerationen. Om du behöver distribuera 30 kärnor i västra USA måste du be om 30 Resource Manager-kärnor i västra USA. Om du behöver distribuera 30 kärnor i någon av de regioner som du har åtkomst till bör du be om 30 Resource Manager-kärnor i alla regioner.
>
>

1. Välj **Prenumerationer**.

   ![Prenumerationer](./media/error-resource-quota/subscriptions.png)

2. Välj den prenumeration som behöver en ökad kvot.

   ![Välj en prenumeration](./media/error-resource-quota/select-subscription.png)

3. Välj **Användning + kvoter**

   ![Välj användning och kvoter](./media/error-resource-quota/select-usage-quotas.png)

4. I det övre högra hörnet väljer du **Begär ökning**.

   ![Ökning av begäran](./media/error-resource-quota/request-increase.png)

5. Fyll i formulären för den typ av kvot som du vill utöka.

   ![Fyll i formulär](./media/error-resource-quota/forms.png)