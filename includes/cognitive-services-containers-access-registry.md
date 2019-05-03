---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 27c6b59a458fb79b86d7064d710a01593a1745dc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028882"
---
## <a name="use-docker-cli-to-authenticate-private-container-registry"></a>Använd Docker CLI för att autentisera privat behållarregister

Det finns flera sätt att autentisera med det privata behållarregistret för Cognitive Services-behållare, men den rekommenderade metoden från kommandoraden är med hjälp av den [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Använd den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommandot, som visas i följande exempel för att logga in på `containerpreview.azurecr.io`, privat behållarregister för Cognitive Services-behållare. Ersätt *\<användarnamn\>* med användarnamnet och *\<lösenord\>* med det angivna inloggningsuppgifterna lösenordet som du fick från Azure Cognitive Services-teamet.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Om du har skyddat dina autentiseringsuppgifter i en textfil kan du sammanfoga innehållet i texten fil, använda den `cat` för att den `docker login` kommandot som visas i följande exempel. Ersätt *\<{passwordFile\>* med sökvägen och namnet på den textfil som innehåller lösenordet och *\<användarnamn\>* med användarnamnet tillhandahålla i dina autentiseringsuppgifter.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
