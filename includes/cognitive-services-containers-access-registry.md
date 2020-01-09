---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704163"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Använd Docker CLI för att autentisera registret för privata behållare

Du kan autentisera med det privata behållar registret för Cognitive Services behållare på något av flera sätt, men den rekommenderade metoden från kommando raden är att använda [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Använd [kommandot`docker login`](https://docs.docker.com/engine/reference/commandline/login/), som du ser i följande exempel, för att logga in på `containerpreview.azurecr.io`, det privata behållar registret för Cognitive Services behållare. Ersätt *\<användar namn\>* med användar namnet och *\<lösen ordet\>* med lösen ordet som anges i de autentiseringsuppgifter som du fick från Azure Cognitive Services-teamet.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Om du har skyddat dina autentiseringsuppgifter i en textfil kan du sammanfoga innehållet i den text filen med hjälp av kommandot `cat` till kommandot `docker login`, som du ser i följande exempel. Ersätt *\<passwordFile\>* med sökvägen och namnet på text filen som innehåller lösen ordet och *\<användar namn\>* med det användar namn som anges i dina autentiseringsuppgifter.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
