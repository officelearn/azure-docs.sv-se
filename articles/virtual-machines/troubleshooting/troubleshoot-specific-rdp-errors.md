---
title: Specifika RDP-felmeddelanden för virtuella Azure-datorer | Microsoft-dokument
description: Förstå specifika felmeddelanden som kan visas när du försöker använda anslutning till fjärrskrivbord till en virtuell Windows-dator i Azure
keywords: Fel på fjärrskrivbord,anslutningsfel för fjärrskrivbord, kan inte ansluta till virtuell dator, felsökning av fjärrskrivbord
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 851c5eb4ebfee4e4a4836a07b51578dd2b0c68cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266876"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Felsöka specifika RDP-felmeddelanden till en virtuell Windows-dator i Azure
Ett specifikt felmeddelande visas när du använder anslutning till fjärrskrivbord till en virtuell Dator (VM) i Azure. I den här artikeln beskrivs några av de vanligaste felmeddelandena som påträffades, tillsammans med felsökningssteg för att lösa dem. Om du har problem med att ansluta till den virtuella datorn med RDP men inte stöter på något specifikt felmeddelande läser du [felsökningsguiden för Fjärrskrivbord](troubleshoot-rdp-connection.md).

Information om specifika felmeddelanden finns i följande avsnitt:

* [Fjärrsessionen kopplades från eftersom det inte finns några licensservrar för fjärrskrivbord tillgängliga för att tillhandahålla en licens](#rdplicense).
* [Fjärrskrivbord kan inte hitta datorns "namn".](#rdpname)
* [Ett autentiseringsfel har uppstått. Det går inte att kontakta den lokala säkerhetsmyndigheten](#rdpauth).
* [Windows-säkerhetsfel: Dina autentiseringsuppgifter fungerade inte](#wincred).
* [Den här datorn kan inte ansluta till fjärrdatorn](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Fjärrsessionen kopplades från eftersom det inte finns några licensservrar för fjärrskrivbord tillgängliga för att tillhandahålla en licens.
Orsak: Respitperioden för 120 dagars licensiering för rollen Fjärrskrivbordsserver har gått ut och du måste installera licenser.

Som en lösning sparar du en lokal kopia av RDP-filen från portalen och kör det här kommandot i en PowerShell-kommandotolk för att ansluta. Det här steget inaktiverar licensiering för just den anslutningen:

        mstsc <File name>.RDP /admin

Om du egentligen inte behöver fler än två samtidiga fjärrskrivbordsanslutningar till den virtuella datorn kan du använda Serverhanteraren för att ta bort rollen Fjärrskrivbordsserver.

Mer information finns i blogginlägget [Azure VM misslyckas med "Inga licensservrar för fjärrskrivbord är tillgängliga".](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/)

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Fjärrskrivbord kan inte hitta datorns "namn".
Orsak: Klienten för fjärrskrivbord på datorn kan inte matcha namnet på datorn i inställningarna för RDP-filen.

Möjliga lösningar:

* Om du använder en organisations intranät kontrollerar du att datorn har åtkomst till proxyservern och kan skicka HTTPS-trafik till den.
* Om du använder en lokalt lagrad RDP-fil kan du prova att använda den som genereras av portalen. Det här steget säkerställer att du har rätt DNS-namn för den virtuella datorn, eller molntjänsten och slutpunktsporten för den virtuella datorn. Här är ett exempel RDP fil som genereras av portalen:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Adressdelen av den här RDP-filen har:

* Det fullständigt kvalificerade domännamnet för molntjänsten som innehåller den virtuella datorn ("tailspin-azdatatier.cloudapp.net" i det här exemplet).
* Den externa TCP-porten för slutpunkten för fjärrskrivbordstrafik (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ett autentiseringsfel har uppstått. Den lokala säkerhetsmyndigheten kan inte kontaktas.
Orsak: Måldatorn kan inte hitta säkerhetsutfärdaren i användarnamnsdelen av dina autentiseringsuppgifter.

När ditt användarnamn finns i formuläret *SecurityAuthority*\\*UserName* (exempel: CORP\User1) är *SecurityAuthority-delen* antingen den virtuella datorns namn (för den lokala säkerhetsmyndigheten) eller ett Active Directory-domännamn.

Möjliga lösningar:

* Om kontot är lokalt för den virtuella datorn kontrollerar du att vm-namnet är rättstavat.
* Om kontot finns i en Active Directory-domän kontrollerar du stavningen av domännamnet.
* Om det är ett Active Directory-domänkonto och domännamnet är rättstavat kontrollerar du att en domänkontrollant är tillgänglig i den domänen. Det är ett vanligt problem i virtuella Azure-nätverk som innehåller domänkontrollanter att en domänkontrollant inte är tillgänglig eftersom den inte har startats. Som en lösning kan du använda ett lokalt administratörskonto i stället för ett domänkonto.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows-säkerhetsfel: Dina autentiseringsuppgifter fungerade inte.
Orsak: Måldassen kan inte validera ditt kontonamn och lösenord.

En Windows-baserad dator kan validera autentiseringsuppgifterna för antingen ett lokalt konto eller ett domänkonto.

* För lokala konton använder du*syntaxen* *för användarnamn på datornamn*\\(exempel: SQL1\Admin4798).
* För domänkonton använder du*syntaxen* för användarnamn på *domainname*\\(t.ex. CONTOSO\peterodman).

Om du har befordrat den virtuella datorn till en domänkontrollant i en ny Active Directory-skog konverteras det lokala administratörskontot som du loggade in med till ett motsvarande konto med samma lösenord i den nya skogen och domänen. Det lokala kontot tas sedan bort.

Om du till exempel loggade in med det lokala kontot DC1\DCAdmin och sedan befordrade den virtuella datorn som domänkontrollant i en ny skog för corp.contoso.com-domänen tas det lokala DC1\DCAdmin-kontot bort och ett nytt domänkonto (CORP\DCAdmin) tas bort samma lösenord.

Kontrollera att kontonamnet är ett namn som den virtuella datorn kan verifiera som ett giltigt konto och att lösenordet är korrekt.

Om du behöver ändra lösenordet för det lokala administratörskontot läser du [Så här återställer du ett lösenord eller tjänsten Fjärrskrivbord för virtuella Windows-datorer](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Den här datorn kan inte ansluta till fjärrdatorn.
Orsak: Kontot som används för att ansluta har inte inloggningsrättigheter för fjärrskrivbord.

Varje Windows-dator har en lokal grupp för fjärrskrivbordsanvändare som innehåller de konton och grupper som kan logga in på den på distans. Medlemmar i den lokala administratörsgruppen har också åtkomst, även om dessa konton inte finns med i den lokala gruppen För fjärrskrivbordsanvändare. För domänanslutna datorer innehåller den lokala administratörsgruppen också domänadministratörerna för domänen.

Kontrollera att kontot du använder för att ansluta till har inloggningsrättigheter för fjärrskrivbord. Som en lösning använder du ett domänkonto eller ett lokalt administratörskonto för att ansluta via Fjärrskrivbord. Om du vill lägga till önskat konto i den lokala gruppen för fjärrskrivbordsanvändare använder du snapin-modulen Microsoft Management Console (**Systemverktyg > lokala användare och grupper > grupper > användare av fjärrskrivbord**).

## <a name="next-steps"></a>Nästa steg
Om inget av dessa fel uppstod och du har ett okänt problem med att ansluta med RDP läser du [felsökningsguiden för Fjärrskrivbord](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Felsökningssteg för åtkomst till program som körs på en virtuell dator finns i [Felsöka åtkomst till ett program som körs på en Virtuell Azure.](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Om du har problem med att använda Secure Shell (SSH) för att ansluta till en Virtuell Linux-dator i Azure läser [du Felsöka SSH-anslutningar till en Virtuell Linux-dator i Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

