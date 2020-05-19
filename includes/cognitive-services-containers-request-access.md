---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.openlocfilehash: 88d83676de1e7fa18c4c1dcbf347da8d685ba2fa
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593347"
---
Fyll i och skicka [fråge formuläret för Cognitive Services containers](https://aka.ms/cognitivegate) för att begära åtkomst till behållaren. Formuläret efterfrågar information om dig, ditt företag och användar scenariot som du använder behållaren för. När du har skickat formuläret granskar Azure Cognitive Services-teamet det för att se till att du uppfyller villkoren för åtkomst till det privata behållar registret.

> [!IMPORTANT]
> Du måste använda en e-postadress som är kopplad till antingen ett Microsoft-konto (MSA) eller ett Azure Active Directory-konto (Azure AD) i formuläret. Mer information om godkännande villkoren finns i [Cognitive Services-hantera process](../articles/cognitive-services/cognitive-services-gating-process.md).

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

