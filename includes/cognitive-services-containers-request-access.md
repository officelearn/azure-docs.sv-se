---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 08e6b5d109d6647f2a291f117f4993bae7598464
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302334"
---
Du måste först slutför och skicka den [Cognitive Services Vision behållare formulär](https://aka.ms/VisionContainersPreview) att begära åtkomst till behållaren. Formuläret begär information om dig, ditt företag och Användarscenario som du använder behållaren. När skickat, Azure Cognitive Services-teamet har granskat formuläret för att säkerställa att du uppfyller villkoren för åtkomst till det privata behållarregistret.

> [!IMPORTANT]
> Du måste använda en e-postadress som är associerad med ett Microsoft-konto (MSA) eller Azure Active Directory (Azure AD)-konto i formuläret.

Om din begäran har godkänts får du ett e-postmeddelande med instruktioner om hur du hämtar dina autentiseringsuppgifter och komma åt det privata behållarregistret.

## <a name="log-in-to-the-private-container-registry"></a>Logga in på privat behållarregister

Det finns flera sätt att autentisera med det privata behållarregistret för Cognitive Services-behållare, men den rekommenderade metoden från kommandoraden är med hjälp av den [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Använd den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommandot, som visas i följande exempel för att logga in på `containerpreview.azurecr.io`, privat behållarregister för Cognitive Services-behållare. Ersätt *\<användarnamn\>* med användarnamnet och *\<lösenord\>* med det angivna inloggningsuppgifterna lösenordet som du fick från Azure Cognitive Services-teamet.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Om du har skyddat dina autentiseringsuppgifter i en textfil kan du sammanfoga innehållet i texten fil, använda den `cat` för att den `docker login` kommandot som visas i följande exempel. Ersätt *\<{passwordFile\>* med sökvägen och namnet på den textfil som innehåller lösenordet och *\<användarnamn\>* med användarnamnet tillhandahålla i dina autentiseringsuppgifter.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

