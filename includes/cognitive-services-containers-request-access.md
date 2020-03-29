---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229257"
---
Fyll i och skicka [formuläret Cognitive Services Vision Containers Request för](https://aka.ms/VisionContainersPreview) att begära åtkomst till behållaren. Formuläret begär information om dig, ditt företag och det användarscenario som du ska använda behållaren för. När du har skickat formuläret granskar Azure Cognitive Services-teamet det för att se till att du uppfyller kriterierna för åtkomst till det privata behållarregistret.

> [!IMPORTANT]
> Du måste använda en e-postadress som är associerad med antingen ett MSA-konto (Microsoft Account) eller ett Azure Active Directory-konto (Azure AD) i formuläret .

Om din begäran godkänns får du ett e-postmeddelande med instruktioner som beskriver hur du hämtar dina autentiseringsuppgifter och får tillgång till det privata behållarregistret.

## <a name="log-in-to-the-private-container-registry"></a>Logga in i det privata behållarregistret

Det finns flera sätt att autentisera med det privata behållarregistret för Cognitive Services-behållare. Vi rekommenderar att du använder kommandoradsmetoden med hjälp av [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Använd [inloggningskommandot docker,](https://docs.docker.com/engine/reference/commandline/login/) som visas i följande `containerpreview.azurecr.io`exempel, för att logga in på , som är det privata behållarregistret för Cognitive Services-behållare. Ersätt * \<\> användarnamn* med användarnamnet och * \<lösenordet\> * med lösenordet som finns i autentiseringsuppgifterna som du fick från Azure Cognitive Services-teamet.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Om du har säkrat dina autentiseringsuppgifter i en textfil kan `docker login` du sammanfoga innehållet i textfilen till kommandot. Använd `cat` kommandot, som visas i följande exempel. Ersätt * \<passwordFile\> * med sökvägen och namnet på textfilen som innehåller lösenordet. Ersätt * \<\> användarnamn* med användarnamnet i dina autentiseringsuppgifter.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

