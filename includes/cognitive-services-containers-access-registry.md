---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67704163"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Använd Docker CLI för att autentisera registret för privata behållare

Du kan autentisera med det privata behållar registret för Cognitive Services behållare på något av flera sätt, men den rekommenderade metoden från kommando raden är att använda [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Använd [ `docker login` kommandot](https://docs.docker.com/engine/reference/commandline/login/), som du ser i följande exempel, för att logga in till `containerpreview.azurecr.io` , det privata behållar registret för Cognitive Services behållare. Ersätt *\<username\>* med användar namnet och *\<password\>* med lösen ordet som anges i de autentiseringsuppgifter som du fick från Azure Cognitive Services-teamet.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Om du har skyddat dina autentiseringsuppgifter i en textfil kan du sammanfoga innehållet i den text filen med hjälp av `cat` kommandot till `docker login` kommandot, som du ser i följande exempel. Ersätt *\<passwordFile\>* med sökvägen och namnet på text filen som innehåller lösen ordet och *\<username\>* med det användar namn som anges i dina autentiseringsuppgifter.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
