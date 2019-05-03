---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 4d2cfb8a39defec9d0d429bc80bfa6abf4f62164
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65031800"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Flera behållare som körs på samma värd

Om du planerar att köra flera behållare med portar, se till att köra varje behållare med en annan exponerade port. Till exempel den första behållaren på port 5000 och andra behållare på port 5001.

Du kan ha den här behållaren och en annan Cognitive Service-behållare som körs på värden tillsammans eller du kan ha flera behållare på samma Cognitive Service behållaren som körs. 


