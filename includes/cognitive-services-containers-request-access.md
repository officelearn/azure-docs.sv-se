---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.openlocfilehash: 66bd78c94e6c54d26959778cc059730c13d02629
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698553"
---
Formuläret efterfrågar information om dig, ditt företag och användar scenariot som du använder behållaren för. När du har skickat formuläret granskar Azure Cognitive Services-teamet det för att se till att du uppfyller villkoren för åtkomst till det privata behållar registret.

> [!IMPORTANT]
> Du måste använda en e-postadress som är kopplad till antingen ett Microsoft-konto (MSA) eller ett Azure Active Directory-konto (Azure AD) i formuläret.

Om din begäran godkänns får du ett e-postmeddelande med instruktioner som beskriver hur du hämtar dina autentiseringsuppgifter och får åtkomst till det privata behållar registret.

## <a name="log-in-to-the-private-container-registry"></a>Logga in i registret för privata behållare

Det finns flera sätt att autentisera med det privata behållar registret för Cognitive Services behållare. Vi rekommenderar att du använder kommando rads metoden med hjälp av [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Använd kommandot [Docker login](https://docs.docker.com/engine/reference/commandline/login/) , som visas i följande exempel, för att logga in på `containerpreview.azurecr.io` , som är det privata behållar registret för Cognitive Services behållare. Ersätt * \< username \> * med användar namnet och * \< lösen ordet \> * med lösen ordet som angavs i de autentiseringsuppgifter som du fick från Azure Cognitive Services-teamet.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Om du har skyddat dina autentiseringsuppgifter i en textfil kan du sammanfoga innehållet i den text filen till `docker login` kommandot. Använd `cat` kommandot, som du ser i följande exempel. Ersätt * \< passwordFile \> * med sökvägen till och namnet på text filen som innehåller lösen ordet. Ersätt * \< username \> * med det användar namn som anges i dina autentiseringsuppgifter.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

