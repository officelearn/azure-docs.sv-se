---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161213"
---
En korrekt SSL-certifikat säkerställer att du skickar krypterad information till rätt server. Förutom kryptering, certifikatet kan även användas för autentisering. Du kan överföra en egen betrott SSL-certifikat via PowerShell-gränssnittet på enheten.

1. [Ansluta till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd den `Set-HcsCertificate` cmdlet för att ladda upp certifikatet. När du uppmanas, anger du följande parametrar:

   - `CertificateFilePath` – Sökvägen till resursen certifikatfilen i *.pfx* format.
   - `CertificatePassword` – Ett lösenord som används för att skydda certifikatet.
   - `Credentials` -Användarnamn och lösenord för att få åtkomst till den resurs som innehåller certifikatet.

     I följande exempel visas användningen av denna cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

