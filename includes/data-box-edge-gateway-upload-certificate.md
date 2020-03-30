---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67448639"
---
Ett korrekt SSL-certifikat säkerställer att du skickar krypterad information till rätt server. Förutom kryptering möjliggör certifikatet också autentisering. Du kan ladda upp ditt eget betrodda SSL-certifikat via enhetens PowerShell-gränssnitt.

1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd `Set-HcsCertificate` cmdlet för att ladda upp certifikatet. Ange följande parametrar när du uppmanas:

   - `CertificateFilePath`- Sökvägen till resursen som innehåller certifikatfilen i *PFX-format.*
   - `CertificatePassword`- Ett lösenord som används för att skydda certifikatet.
   - `Credentials`- Användarnamn för att komma åt resursen som innehåller certifikatet. Ange lösenordet till nätverksresursen när du uppmanas att göra det.

     I följande exempel visas användningen av den här cmdleten:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

