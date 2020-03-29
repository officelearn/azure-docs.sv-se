---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68320503"
---
Om du behöver konfigurera en HTTP-proxy för att göra utgående begäranden använder du följande två argument:

| Namn | Datatyp | Beskrivning |
|--|--|--|
|HTTP_PROXY|sträng|Den proxy som ska användas, till exempel,`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|sträng|Alla autentiseringsuppgifter som behövs för att autentisera mot proxyn, till exempel användarnamn:lösenord.|
|`<proxy-user>`|sträng|Användaren för proxyn.|
|`<proxy-password>`|sträng|Lösenordet som `<proxy-user>` är associerat med för proxyn.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
