---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67704163"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Använda Docker CLI för att autentisera det privata behållarregistret

Du kan autentisera med det privata behållarregistret för Cognitive Services-behållare på något av flera sätt, men den rekommenderade metoden från kommandoraden är att använda [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Använd [ `docker login` kommandot](https://docs.docker.com/engine/reference/commandline/login/), som visas i följande exempel, för att logga in på `containerpreview.azurecr.io`, det privata behållarregistret för Cognitive Services Containers. Ersätt * \<\> användarnamn* med användarnamn och * \<lösenord\> * med det lösenord som finns i autentiseringsuppgifterna som du fick från Azure Cognitive Services-teamet.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Om du har säkrat dina autentiseringsuppgifter i en textfil kan du sammanfoga `cat` innehållet i `docker login` textfilen, med kommandot, till kommandot, som visas i följande exempel. Ersätt * \<passwordFile\> * med sökvägen och namnet på textfilen som innehåller lösenordet och * \<användarnamnet\> * med användarnamnet som finns i dina autentiseringsuppgifter.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
