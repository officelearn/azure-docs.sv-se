---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704163"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Använda Docker CLI för att autentisera privat behållarregister

Du kan autentisera med det privata behållarregistret för Cognitive Services-behållare i ett antal olika sätt, men den rekommenderade metoden från kommandoraden är att använda den [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Använd den [ `docker login` kommandot](https://docs.docker.com/engine/reference/commandline/login/), vilket visas i följande exempel för att logga in på `containerpreview.azurecr.io`, privat behållarregister för Cognitive Services-behållare. Ersätt *\<användarnamn\>* med användarnamnet och *\<lösenord\>* med lösenordet som angavs i autentiseringsuppgifterna som du har fått från den Azure Cognitive Services-teamet.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Om du har skyddat dina autentiseringsuppgifter i en textfil kan du sammanfoga innehållet i den textfilen med hjälp av den `cat` för att den `docker login` kommandot, som visas i följande exempel. Ersätt *\<{passwordFile\>* med sökvägen och namnet på textfilen som innehåller lösenordet och *\<användarnamn\>* med användarnamnet som tillhandahålls i dina autentiseringsuppgifter.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
