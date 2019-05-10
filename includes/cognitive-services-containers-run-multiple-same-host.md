---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 19726330561abfad08aec3c4908c855616c6ee29
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520947"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Flera behållare som körs på samma värd

Om du planerar att köra flera behållare med portar, se till att köra varje behållare med en annan exponerade port. Till exempel den första behållaren på port 5000 och andra behållare på port 5001.

Du kan ha den här behållaren och en annan Cognitive Service-behållare som körs på värden tillsammans eller du kan ha flera behållare på samma Cognitive Service behållaren som körs.
