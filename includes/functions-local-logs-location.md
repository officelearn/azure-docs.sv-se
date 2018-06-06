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
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "33814636"
---
När funktioner värden körs lokalt, skriver loggar till följande sökväg:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

I Windows, `<DefaultTempDirectory>` är det första värdet som hittades av TMP TEMP USERPROFILE miljövariabler eller Windows-katalogen.
I MacOS eller Linux `<DefaultTempDirectory>` TMPDIR miljövariabel.

> [!NOTE]
> När värden funktioner startar över den befintliga filstrukturen i katalogen.