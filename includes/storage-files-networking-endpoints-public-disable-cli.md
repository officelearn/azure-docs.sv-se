---
title: inkludera fil
description: inkludera fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465046"
---
Följande CLI-kommando kommer att neka all trafik till lagrings kontots offentliga slut punkt. Observera att det här kommandot har `-bypass` parametern inställd på `AzureServices` . Detta ger betrodda tjänster från första part, till exempel Azure File Sync att komma åt lagrings kontot via den offentliga slut punkten.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```