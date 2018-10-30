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
ms.openlocfilehash: fa1046e8d752e133813924957b439b63fc2acfbd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988010"
---
## <a name="create-a-function"></a>Skapa en funktion

Följande kommando skapar en HTTP-utlöst funktion med namnet `MyHtpTrigger`.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

När kommandot körs visas något i stil med följande utdata:

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```