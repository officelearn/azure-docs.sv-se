---
title: Specifika RDP-felmeddelanden för virtuella Azure-datorer | Microsoft Docs
description: Förstå felmeddelanden som kan visas vid försök att använda anslutning till fjärrskrivbord till en Windows-dator i Azure
keywords: 'Fjärrskrivbordsfel, anslutning till fjärrskrivbord fel kan inte ansluta till virtuell dator, felsökning: fjärrskrivbord'
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f4d733e29d2ba8213e1832f2c604b726283ab3e1
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417403"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Felsöka specifika RDP-felmeddelanden till en Windows-dator i Azure
Du får ett specifikt felmeddelande när du använder anslutning till fjärrskrivbord till en Windows-dator (VM) i Azure. Den här artikeln beskrivs några av de vanligaste felmeddelandena som uppstod, tillsammans med felsökning för att lösa dem. Om du har problem med att ansluta till den virtuella datorn med RDP men gör inte stöter på ett specifikt felmeddelande, finns i den [felsökningsguide för Remote Desktop](troubleshoot-rdp-connection.md).

Information om felmeddelanden, finns i följande:

* [Fjärrsessionen kopplades från eftersom det finns inga fjärranslutna servrar för fjärrskrivbordslicenser tillgängliga för att tillhandahålla en licens](#rdplicense).
* [Fjärrskrivbordet kan inte hitta datorn ”name”](#rdpname).
* [Ett autentiseringsfel inträffade. Det går inte att kontakta den lokala säkerhetskontrollen](#rdpauth).
* [Windows-säkerhetsfel: dina autentiseringsuppgifter inte fungerade](#wincred).
* [Den här datorn kan inte ansluta till fjärrdatorn](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Fjärrsessionen kopplades från eftersom det finns inga fjärranslutna servrar för fjärrskrivbordslicenser tillgängliga för att tillhandahålla en licens.
Orsak: 120 dagar licensiering respitperiod för Remote Desktop-serverrollen har upphört att gälla och du måste installera licenser.

Som en lösning kan spara en lokal kopia av RDP-filen från portalen och kör det här kommandot i en PowerShell-kommandotolk för att ansluta. Det här steget inaktiverar licensiering för bara anslutningen:

        mstsc <File name>.RDP /admin

Om du inte verkligen behöver fler än två samtidiga anslutningar till fjärrskrivbord till den virtuella datorn, kan du använda Serverhanteraren för att ta bort rollen för Remote Desktop-Server.

Mer information finns i bloggposten [virtuella Azure-datorn misslyckas med ”ingen licens fjärrskrivbordsservrar tillgänglig”](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Fjärrskrivbord kan inte hitta datorn ”name”.
Orsak: Fjärrskrivbord-klienten på datorn kan inte matcha namnet på datorn i inställningarna för RDP-filen.

Möjliga lösningar:

* Om du är på intranätet, kan du se till att datorn har åtkomst till proxyservern och kan skicka HTTPS-trafik till den.
* Om du använder en lokalt lagrad RDP-fil kan du prova att använda det som genereras av portalen. Det här steget säkerställer att du har rätt DNS-namnet för den virtuella datorn eller Molntjänsten och slutpunktsport för den virtuella datorn. Här är ett exempel på en RDP-fil som genereras av portalen:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Adressdelen av den här RDP-filen har:

* Fullständigt kvalificerade namnet på den molntjänst som innehåller den virtuella datorn (”tailspin-azdatatier.cloudapp.net” i det här exemplet).
* Externa TCP-porten av slutpunkten för Remote Desktop-trafik (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ett autentiseringsfel inträffade. Det går inte att kontakta den lokala säkerhetskontrollen.
Orsak: Den Virtuella måldatorn inte kan hitta säkerhetskontrollen i del av dina autentiseringsuppgifter för användare.

När ditt användarnamn är i formatet *SecurityAuthority*\\*användarnamn* (exempel: corp\användare1), *SecurityAuthority* delen är antingen Virtuellt datorns datornamn (för den lokala säkerhetskontrollen) eller en Active Directory-domännamn.

Möjliga lösningar:

* Om kontot är lokala för den virtuella datorn måste du kontrollera att VM-namnet har stavats korrekt.
* Om kontot är på en Active Directory-domän måste du kontrollera stavningen av domännamnet.
* Om det är ett domänkonto för Active Directory och domännamnet är rättstavat, kontrollerar du att en domänkontrollant är tillgänglig i den domänen. Det är ett vanligt problem i Azure-nätverk som innehåller domänkontrollanter att en domänkontrollant är tillgänglig eftersom den inte har startats. Som en lösning kan använda du ett lokalt administratörskonto i stället för ett domänkonto.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows-säkerhetsfel: dina autentiseringsuppgifter inte fungerade.
Orsak: Den Virtuella måldatorn inte att verifiera ditt kontonamn och lösenord.

En Windows-baserad dator kan verifiera autentiseringsuppgifterna för ett lokalt konto eller ett domänkonto.

* Lokala konton använder den *ComputerName*\\*användarnamn* syntax (exempel: SQL1\Admin4798).
* Domänkonton, använda den *DomainName*\\*användarnamn* syntax (exempel: CONTOSO\peterodman).

Om du har befordrat den virtuella datorn till en domänkontrollant i en ny Active Directory-skog, konverteras det lokala administratörskontot som du loggade in med till ett motsvarande konto med samma lösenord i ny skog och domän. Det lokala kontot tas sedan bort.

Till exempel om du har loggat in med det lokala kontot DC1\DCAdmin och befordras sedan den virtuella datorn som en domänkontrollant i en ny skog på domänen corp.contoso.com, det lokala kontot DC1\DCAdmin tas bort och är ett nytt domänkonto (CORP\DCAdmin) skapas med samma lösenord.

Kontrollera att kontonamnet är ett namn som den virtuella datorn kan verifiera som ett giltigt konto och att lösenordet är korrekt.

Om du vill ändra lösenordet för det lokala administratörskontot kan se [återställa ett lösenord eller Fjärrskrivbordstjänsten för Windows-datorer](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Den här datorn kan inte ansluta till fjärrdatorn.
Orsak: Det konto som används för att ansluta saknar fjärrskrivbord inloggning rättigheter.

Alla Windows-datorer har en Remote Desktop användare lokal grupp, som innehåller de konton och grupper som kan logga in via en fjärranslutning. Medlemmar i gruppen lokala administratörer har även åtkomst till, även om dessa konton inte visas i den lokala gruppen för Remote Desktop-användare. I den lokala administratörsgruppen för domänanslutna datorer innehåller också Domänadministratörer för domänen.

Se till att det konto som du använder för att ansluta till har Fjärrskrivbord inloggning rättigheter. Som en lösning kan du använda en domän eller lokalt administratörskonto för att ansluta via fjärrskrivbord. Lägg till önskat konto i den lokala gruppen för Remote Desktop-användare genom att använda snapin-modulen Microsoft Management Console (**Systemverktyg > lokala användare och grupper > grupper > användare av fjärrskrivbord**).

## <a name="next-steps"></a>Nästa steg
Om ingen av dessa fel uppstod och du har ett okänt problem med att ansluta via RDP, ser de [felsökningsguide för Remote Desktop](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Felsökningssteg i åtkomst till program som körs på en virtuell dator, finns i [Felsök åtkomst till ett program som körs på en Azure VM](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Om du har problem med hjälp av SSH (Secure Shell) kan ansluta till en Linux-VM i Azure finns i [Felsök SSH-anslutningar till en Linux-VM i Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

