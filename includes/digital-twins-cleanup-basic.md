---
author: baanders
description: inkludera fil för att rensa en enkel Azure Digital-instans
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011291"
---
Om du inte längre behöver resurserna som skapas i den här självstudien kan du ta bort dem genom att följa stegen nedan.

Med hjälp av [Azure Cloud Shell](https://shell.azure.com)kan du ta bort alla Azure-resurser i en resurs grupp med kommandot [AZ Group Delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) . Det här kommandot tar bort resurs gruppen och Azure Digitals-instansen.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag.

Öppna Azure Cloud Shell och kör följande kommando för att ta bort resurs gruppen och allt den innehåller.

```azurecli-interactive
az group delete --name <your-resource-group>
```