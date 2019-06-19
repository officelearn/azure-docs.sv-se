---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 4cdcec850f32d7e94f33eb28e5bf7839e511f347
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187666"
---
Fylla i och skicka den [Cognitive Services Vision behållare formulär](https://aka.ms/VisionContainersPreview) att begära åtkomst till behållaren. Formuläret begär information om dig, ditt företag och Användarscenario som du använder behållaren. När du har skickat formuläret Azure Cognitive Services-teamet har granskat den och kontrollera att du uppfyller villkoren för åtkomst till det privata behållarregistret.

> [!IMPORTANT]
> Du måste använda en e-postadress som är associerad med ett Microsoft-konto (MSA) eller ett Azure Active Directory (AD Azure). i formuläret.

Om din begäran har godkänts får du ett e-postmeddelande med instruktioner som beskriver hur du hämtar dina autentiseringsuppgifter och komma åt det privata behållarregistret.

## <a name="log-in-to-the-private-container-registry"></a>Logga in på privat behållarregister

Det finns flera sätt att autentisera med det privata behållarregistret för Cognitive Services-behållare. Vi rekommenderar att du använder metoden kommandoraden med hjälp av den [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Använd den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommandot, som visas i följande exempel för att logga in på `containerpreview.azurecr.io`, vilket är privat behållarregister för Cognitive Services-behållare. Ersätt *\<användarnamn\>* med användarnamnet och *\<lösenord\>* med det angivna inloggningsuppgifterna lösenordet som du fick från Azure Cognitive Services-teamet.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Om du har skyddat dina autentiseringsuppgifter i en textfil kan du sammanfoga innehållet i textfilen till den `docker login` kommando. Använd den `cat` kommandot, som visas i följande exempel. Ersätt *\<{passwordFile\>* med sökvägen och namnet på textfilen som innehåller lösenordet. Ersätt *\<användarnamn\>* med det användarnamn som anges i dina autentiseringsuppgifter.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

