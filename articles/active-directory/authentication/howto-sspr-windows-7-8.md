---
title: Återställning av lösenord för självbetjäning av Azure AD, Windows 7- och 8.1
description: Hur du aktiverar Självbetjäning för återställning av lösenord med hjälp av har glömt lösenord på Windows 7- eller 8.1 inloggningsskärmen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: 33bacb5d399e41e04a6c00225e7411cef0c20308
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075320"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>Anvisningar: Aktivera lösenordsåterställning från Windows 7, 8 och 8.1

Du har aktiverat lösenordsåterställning via självbetjäning (SSPR) som administratör, men användarna att anropa supportavdelningen om du vill återställa sina lösenord eftersom de inte kan komma att ett webbläsarfönster för att komma åt den [SSPR-portalen](https://aka.ms/sspr). Du kan aktivera länken ”Återställ lösenord” på inloggningsskärmen med hjälp av självstudierna för Windows 10-datorer, [Azure AD-lösenordsåterställning från inloggningsskärmen](tutorial-sspr-windows.md), följande riktlinjer hjälper dig att aktivera Windows 7, 8 och 8.1-användare att återställa sina lösenord med SSPR på Windows-inloggningsskärmen.

Till skillnad från Windows 10-datorer, Windows 7, 8 och 8.1 datorer har inte en Azure AD som är anslutna till en domän eller återställa Active Directory-domänanslutna krav för lösenord.

![Exempel inloggningsskärmen för Windows 7 med ”har du glömt lösenordet”? länken som visas](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>Förutsättningar

* Återställning av lösenord för självbetjäning i Azure AD måste vara aktiverat.
* Korrigerade Windows 7 eller Windows 8.1 operativsystem.
* TLS 1.2 aktiverat genom att följa vägledningen hittades i [registerinställningar för Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).

> [!WARNING]
> TLS 1.2 måste vara aktiverat, inte bara inställd på automatisk förhandla.

## <a name="install"></a>Installera

1. Hämta lämplig installationsprogrammet för versionen av Windows som du vill aktivera.

   1. Programvara som är tillgänglig på Microsoft download center på [https://aka.ms/sspraddin](https://aka.ms/sspraddin)

1. Logga in på datorn där du vill installera och kör installationsprogrammet.
1. Efter installationen rekommenderas en omstart.
1. Efter omstarten på inloggningsskärmen väljer en användare och klickar på ”har du glömt lösenordet”? återställer arbetsflöde för att initiera lösenordet.
1. Slutför arbetsflödet instruktionerna på skärmen att återställa ditt lösenord.

![Exempel Windows 7 klickar du på ”har du glömt lösenordet”? lösenordsåterställning via självbetjäning flöde](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>Tyst installation

* För tyst installation använder du den kommandot ”msiexec /i SsprWindowsLogon.PROD.msi /qn”
* Använd den kommandot ”msiexec /x SsprWindowsLogon.PROD.msi /qn” för tyst avinstallation

## <a name="caveats"></a>Varningar

Du måste registrera dig för SSPR innan du kommer att kunna använda länken ”har du glömt lösenord”.

![Ytterligare säkerhetsinformation behövs för att återställa ditt lösenord](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

Använda Microsoft Authenticator-appen för meddelanden och koder för att återställa ditt lösenord fungerar inte i den här första versionen. Användarna måste ha alternativa metoder som är registrerade och som uppfyller kraven i din princip.

## <a name="troubleshooting"></a>Felsökning

Händelser loggas både på datorn och i Azure AD.

Azure AD-händelser innehåller information om IP-adressen och klienttyp där lösenordsåterställningen inträffade.

![Exempel Windows 7 inloggning skärm för återställning av lösenord i Azure AD-granskningsloggen](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

Om det krävs ytterligare loggning måste kan en registernyckel på datorn ändras om du vill aktivera utförlig loggning. Aktivera utförlig loggning i felsökningssyfte.

```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

* Aktivera utförlig loggning genom att skapa en REG_DWORD: ”EnableLogging”, och anger värdet 1.
* Om du vill inaktivera utförlig loggning, ändra REG_DWORD ”EnableLogging” till 0.

Om din Windows 7, 8 och 8.1-datorer är bakom en proxyserver eller brandvägg, ska HTTPS-trafik (443) till passwordreset.microsoftonline.com tillåtas.

## <a name="next-steps"></a>Nästa steg

[Aktivera Windows 10-användare att återställa sina lösenord på inloggningsskärmen](tutorial-sspr-windows.md)
