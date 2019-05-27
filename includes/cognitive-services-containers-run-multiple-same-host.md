---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 327654ca06a3997855d904414fa4258491ee6c88
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124369"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Köra flera behållare på samma värd

Om du planerar att köra flera behållare med portar, se till att köra varje behållare med en annan exponerade port. Till exempel den första behållaren på port 5000 och andra behållare på port 5001.

Du kan ha den här behållaren och en annan Azure Cognitive Services-behållare som körs på värden tillsammans. Du kan också ha flera behållare på samma Cognitive Services-behållaren som körs.
