---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 664cea26f910fa5b3354e2879a33de50eb13a7f3
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286299"
---
Om du behöver konfigurera en HTTP-proxy för att göra utgående begär Anden använder du följande två argument:

| Namn | Datatyp | Beskrivning |
|--|--|--|
|HTTP_PROXY|sträng|Proxyservern som ska användas, till exempel`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|sträng|Alla autentiseringsuppgifter som krävs för att autentisera mot proxyservern, till exempel username: Password.|
|`<proxy-user>`|sträng|Användaren för proxyservern.|
|`proxy-password`|sträng|Lösen ordet som är `<proxy-user>` associerat med för proxyservern.|
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
