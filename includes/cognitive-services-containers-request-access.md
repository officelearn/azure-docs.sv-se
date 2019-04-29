---
author: diberry
ms.author: v-junlch
ms.service: cognitive-services
ms.topic: include
origin.date: 01/24/2019
ms.date: 02/21/2019
ms.openlocfilehash: 11a336bbcf75c6c4de61f1bb681ab6ee7aa05650
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815665"
---
Du måste först slutför och skicka den [Cognitive Services Vision behållare formulär](https://aka.ms/VisionContainersPreview) att begära åtkomst till behållaren. Formuläret begär information om dig, ditt företag och Användarscenario som du använder behållaren. När skickat, Azure Cognitive Services-teamet har granskat formuläret för att säkerställa att du uppfyller villkoren för åtkomst till det privata behållarregistret.

> [!IMPORTANT]
> Du måste använda en e-postadress som är associerad med ett Microsoft-konto (MSA) eller Azure Active Directory (Azure AD)-konto i formuläret.

Om din begäran har godkänts får du ett e-postmeddelande med instruktioner om hur du hämtar dina autentiseringsuppgifter och komma åt det privata behållarregistret.

## <a name="log-in-to-the-private-container-registry"></a>Logga in på privat behållarregister

Det finns flera sätt att autentisera med det privata behållarregistret för Cognitive Services-behållare, men den rekommenderade metoden från kommandoraden är med hjälp av den [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Använd den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommandot, som visas i följande exempel för att logga in på `containerpreview.azurecr.io`, privat behållarregister för Cognitive Services-behållare. Ersätt *\<användarnamn\>* med användarnamnet och *\<lösenord\>* med det angivna inloggningsuppgifterna lösenordet som du fick från Azure Cognitive Services-teamet.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Om du har skyddat dina autentiseringsuppgifter i en textfil kan du sammanfoga innehållet i texten fil, använda den `cat` för att den `docker login` kommandot som visas i följande exempel. Ersätt *\<{passwordFile\>* med sökvägen och namnet på den textfil som innehåller lösenordet och *\<användarnamn\>* med användarnamnet tillhandahålla i dina autentiseringsuppgifter.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```


<!-- ms.date: 02/21/2019 -->