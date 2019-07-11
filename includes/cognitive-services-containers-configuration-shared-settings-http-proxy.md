---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 9911b1c92bdca6c0cdf064ea484cfb603e659467
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712529"
---
Om du vill konfigurera en HTTP-proxy för utgående förfrågningar kan använda dessa två argument:

| Namn | Datatyp | Beskrivning |
|--|--|--|
|HTTP_PROXY|sträng|proxy för att använda, till exempel `http://proxy:8888`<br>< proxy-url >|
|HTTP_PROXY_CREDS|sträng|autentiseringsuppgifter krävs för att autentisera mot proxy, till exempel användarnamn: lösenord.|
|`<proxy-user>`|sträng|Användaren för proxyservern.|
|`proxy-password`|sträng|Lösenordet som associeras med `<proxy-user>` för proxyn.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```