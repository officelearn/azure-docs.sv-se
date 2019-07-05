---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448639"
---
En korrekt SSL-certifikat säkerställer att du skickar krypterad information till rätt server. Förutom kryptering, certifikatet kan även användas för autentisering. Du kan överföra en egen betrott SSL-certifikat via PowerShell-gränssnittet på enheten.

1. [Ansluta till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd den `Set-HcsCertificate` cmdlet för att ladda upp certifikatet. När du uppmanas, anger du följande parametrar:

   - `CertificateFilePath` – Sökvägen till resursen certifikatfilen i *.pfx* format.
   - `CertificatePassword` – Ett lösenord som används för att skydda certifikatet.
   - `Credentials` -Användarnamnet för att få åtkomst till resursen som innehåller certifikatet. Ange lösenordet för nätverksresursen när du tillfrågas.

     I följande exempel visas användningen av denna cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

