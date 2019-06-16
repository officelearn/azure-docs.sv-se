---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 02beb6bdce096c35d8948cc8aefab6cc97be907c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063945"
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
