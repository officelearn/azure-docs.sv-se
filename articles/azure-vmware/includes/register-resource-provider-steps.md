---
title: Registrera resurs leverantören för Azure VMware-lösningen
description: Steg för att registrera resurs leverantören för Azure VMware-lösningen.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 380cb513ec389293db757e667ed4681778e29348
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254657"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Om du vill använda Azure VMware-lösningen måste du först registrera resurs leverantören med din prenumeration.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>Alternativt kan du använda det grafiska användar gränssnittet för att registrera **Microsoft. AVS** -Resurshanterarens resurs leverantör.  Mer information finns i artikeln [Registrera resurs leverantörer och typer](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) .  
