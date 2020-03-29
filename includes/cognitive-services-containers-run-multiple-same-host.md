---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704302"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Köra flera behållare på samma värd

Om du tänker köra flera behållare med exponerade portar, se till att köra varje behållare med en annan exponerad port. Kör till exempel den första behållaren på port 5000 och den andra behållaren på port 5001.

Du kan ha den här behållaren och en annan Azure Cognitive Services-behållare som körs på värdvärden tillsammans. Du kan också ha flera behållare med samma Cognitive Services-behållare som körs.
