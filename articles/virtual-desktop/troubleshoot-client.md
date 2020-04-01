---
title: Felsöka Windows Virtual Desktop - Azure
description: Så här löser du problem när du konfigurerar klientanslutningar i en windows virtual desktop-klientmiljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 595762e6e8f22dddff30f1cff8c4bb79e89624b1
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473846"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Felsöka klienten för fjärrskrivbord

I den här artikeln beskrivs vanliga problem med fjärrskrivbordsklienten och hur du åtgärdar dem.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Fjärrskrivbordsklienten för Windows 7 eller Windows 10 slutar svara eller kan inte öppnas

Från och med version 1.2.790 kan du återställa användardata från sidan Om eller med ett kommando.

Använd följande kommando för att ta bort användardata, återställa standardinställningar och avsluta prenumerationen på alla arbetsytor.

```cmd
msrdcw.exe /reset [/f]
```

Om du använder en tidigare version av fjärrskrivbordsklienten rekommenderar vi att du avinstallerar och installerar om klienten.

## <a name="web-client-wont-open"></a>Webbklienten öppnas inte

Testa först din internetanslutning genom att öppna en annan webbplats i din webbläsare. till exempel [www.bing.com](https://www.bing.com).

Använd **nslookup** för att bekräfta dns kan lösa FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Prova att ansluta till en annan klient, till exempel Fjärrskrivbordsklient för Windows 7 eller Windows 10, och kontrollera om du kan öppna webbklienten.

### <a name="opening-another-site-fails"></a>Det går inte att öppna en annan webbplats

Detta orsakas vanligtvis av problem med nätverksanslutningen eller ett nätverksutbrott. Vi rekommenderar att du kontaktar nätverkssupporten.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup kan inte matcha namnet

Detta orsakas vanligtvis av problem med nätverksanslutningen eller ett nätverksutbrott. Vi rekommenderar att du kontaktar nätverkssupporten.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Klienten kan inte ansluta, men andra klienter i nätverket kan ansluta

Om webbläsaren börjar fungera eller slutar fungera medan du använder webbklienten följer du de här anvisningarna för att felsöka den:

1. Starta om webbläsaren.
2. Rensa webbläsarcookies. Se [Så här tar du bort cookie-filer i Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Rensa webbläsarens cache. Se [rensa webbläsarens cacheminne för din webbläsare](https://binged.it/2RKyfdU).
4. Öppna webbläsaren i privat läge.

## <a name="web-client-stops-responding-or-disconnects"></a>Webbklienten slutar svara eller kopplar från

Prova att ansluta med en annan webbläsare eller klient.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Andra webbläsare och klienter också fel eller misslyckas med att öppna

Om problemen kvarstår även efter att du har bytt webbläsare kanske problemet inte finns i webbläsaren, utan i nätverket. Vi rekommenderar att du kontaktar nätverkssupporten.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webbklienten frågar efter autentiseringsuppgifter

Om webbklienten fortsätter att fråga efter autentiseringsuppgifter följer du dessa instruktioner:

1. Bekräfta att webbklientens URL är korrekt.
2. Bekräfta att de autentiseringsuppgifter du använder är för windows virtual desktop-miljön som är kopplad till webbadressen.
3. Rensa webbläsarcookies. Mer information finns i [Så här tar du bort cookie-filer i Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Rensa webbläsarens cache. Mer information finns i [Rensa webbläsarens cacheminne för din webbläsare](https://binged.it/2RKyfdU).
5. Öppna webbläsaren i privat läge.

## <a name="next-steps"></a>Nästa steg

- En översikt över felsökning av Windows Virtual Desktop och eskaleringsspåren finns i [Felsökningsöversikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du skapar en klient- och värdpool i en Windows Virtual Desktop-miljö finns i [Skapandet av klient- och värdpooler](troubleshoot-set-up-issues.md).
- Mer om du vill felsöka problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [Konfigurationen för virtuell dator för session värd](troubleshoot-vm-configuration.md).
- Mer om du vill felsöka problem när du använder PowerShell med Virtuellt Windows-skrivbord finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Information om hur du går igenom en felsökningsguide finns i [Självstudiekurs: Felsöka Resource Manager-malldistributioner](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).