---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 26789a12053fa6275b09836e706c391e181c8efd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187122"
---
## <a name="create-a-function"></a>Skapa en funktion

Följande kommando skapar en HTTP-utlöst funktion med namnet `MyHttpTrigger`.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

När kommandot körs visas något i stil med följande utdata:

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
