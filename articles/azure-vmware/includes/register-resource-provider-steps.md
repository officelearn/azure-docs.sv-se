---
title: Registrera resurs leverantören för Azure VMware-lösningen
description: Steg för att registrera resurs leverantören för Azure VMware-lösningen.
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512390"
---
Om du vill använda Azure VMware-lösningen måste du först registrera resurs leverantören med din prenumeration.

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Ytterligare sätt att registrera resurs leverantören finns i [Azure Resource providers och-typer](../../azure-resource-manager/management/resource-providers-and-types.md).