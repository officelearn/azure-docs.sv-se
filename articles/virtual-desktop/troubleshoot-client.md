---
title: Felsöka fjärr skrivbords klienten Windows Virtual Desktop – Azure
description: Så här löser du problem när du konfigurerar klient anslutningar i en Windows-klient för virtuella skriv bord.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 595762e6e8f22dddff30f1cff8c4bb79e89624b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80473846"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Felsöka fjärr skrivbords klienten

I den här artikeln beskrivs vanliga problem med fjärr skrivbords klienten och hur du kan åtgärda dem.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Fjärr skrivbords klienten för Windows 7 eller Windows 10 slutar svara eller kan inte öppnas

Från och med version 1.2.790 kan du återställa användar data från om-sidan eller använda ett kommando.

Använd följande kommando för att ta bort användar data, återställa standardinställningar och avbryta prenumerationen på alla arbets ytor.

```cmd
msrdcw.exe /reset [/f]
```

Om du använder en tidigare version av fjärr skrivbords klienten rekommenderar vi att du avinstallerar och installerar om klienten.

## <a name="web-client-wont-open"></a>Webb klienten öppnas inte

Testa först din Internet anslutning genom att öppna en annan webbplats i webbläsaren. till exempel [www.Bing.com](https://www.bing.com).

Använd **nslookup** för att bekräfta att DNS kan matcha FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Försök att ansluta till en annan klient, t. ex. fjärr skrivbords klienten för Windows 7 eller Windows 10, och kontrol lera om du kan öppna webb klienten.

### <a name="opening-another-site-fails"></a>Det går inte att öppna en annan webbplats

Detta beror vanligt vis på problem med nätverks anslutningen eller ett nätverks avbrott. Vi rekommenderar att du kontaktar nätverks supporten.

### <a name="nslookup-cannot-resolve-the-name"></a>Det går inte att matcha namnet i nslookup

Detta beror vanligt vis på problem med nätverks anslutningen eller ett nätverks avbrott. Vi rekommenderar att du kontaktar nätverks supporten.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Klienten kan inte ansluta men andra klienter i nätverket kan ansluta

Om webbläsaren startar eller slutar fungera när du använder webb klienten följer du dessa anvisningar för att felsöka den:

1. Starta om webbläsaren.
2. Rensa webbläsarens cookies. Se [hur du tar bort cookie-filer i Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Rensa webbläsarens cache. Se [Rensa webbläsarens cacheminne för webbläsaren](https://binged.it/2RKyfdU).
4. Öppna webbläsare i privat läge.

## <a name="web-client-stops-responding-or-disconnects"></a>Webb klienten slutar svara eller kopplas från

Försök att ansluta med en annan webbläsare eller klient.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Andra webbläsare och klienter fungerar inte heller eller kan inte öppnas

Om problemen fortsätter även efter att du har bytt webbläsare, kanske problemet inte är med din webbläsare, men med nätverket. Vi rekommenderar att du kontaktar nätverks supporten.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webb klienten kommer att uppmanas att ange autentiseringsuppgifter

Följ dessa instruktioner om webb klienten ska uppmanas att ange autentiseringsuppgifter:

1. Bekräfta att webb adressen till webb klienten är korrekt.
2. Bekräfta att autentiseringsuppgifterna som du använder är för den virtuella Windows-miljön som är kopplad till URL: en.
3. Rensa webbläsarens cookies. Mer information finns i [så här tar du bort cookie-filer i Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Rensa webbläsarens cache. Mer information finns i [Rensa webbläsarens cacheminne för webbläsaren](https://binged.it/2RKyfdU).
5. Öppna webbläsaren i privat läge.

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du skapar en klient och en adresspool i en Windows Virtual Desktop-miljö finns i [skapa innehavare och skapa värdar för pooler](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).