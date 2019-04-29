---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: e80feac7dbf16652cc2e2a6176ed8b2c8c48e35b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598905"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Flera behållare som körs på samma värd

Om du planerar att köra flera behållare med portar, se till att köra varje behållare med en annan port. Till exempel den första behållaren på port 5000 och andra behållare på port 5001.

Ersätt den `<container-registry>` och `<container-name>` med värdena för de behållare som du använder. De behöver inte vara samma behållare. Du kan ha Ansikts-behållaren och LUIS-behållaren som körs på värden tillsammans eller du kan ha flera Ansikts-behållare som körs. 

Kör den första behållaren på port 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Kör andra behållare på port 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Varje efterföljande behållare ska vara på en annan port. 
