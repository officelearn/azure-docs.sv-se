---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/01/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88c01e8e57d4a92478b8b1ca0689ff0f8e499b39
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739635"
---
När funktioner värden körs lokalt, skriver loggar till följande sökväg:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

På Windows, `<DefaultTempDirectory>` är det första värdet som hittats av TMP TEMP, USERPROFILE miljövariabler eller Windows-katalogen.
På MacOS eller Linux, `<DefaultTempDirectory>` TMPDIR miljövariabel.

> [!NOTE]
> När funktioner värden startas, skrivs den befintliga filstrukturen i katalogen.