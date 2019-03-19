---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 216380cf7069468d13c4e533fc90b2596aa211c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125043"
---
En korrekt SSL-certifikat säkerställer att du skickar krypterad information till rätt server. Förutom kryptering, certifikatet kan även användas för autentisering. Du kan överföra en egen betrott SSL-certifikat via PowerShell-gränssnittet på enheten.

1. [Ansluta till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd den `Set-HcsCertificate` cmdlet för att ladda upp certifikatet. När du uppmanas, anger du följande parametrar:

   - `CertificateFilePath` – Sökvägen till resursen certifikatfilen i *.pfx* format.
   - `CertificatePassword` – Ett lösenord som används för att skydda certifikatet.
   - `Credentials` -Användarnamn och lösenord för att få åtkomst till den resurs som innehåller certifikatet.

     I följande exempel visas användningen av denna cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credentials "Username/Password"
     ```

