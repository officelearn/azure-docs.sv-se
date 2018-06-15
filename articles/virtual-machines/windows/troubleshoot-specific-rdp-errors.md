---
title: Felmeddelanden i RDP för virtuella datorer i Azure | Microsoft Docs
description: Förstå felmeddelanden som kan visas när du försöker använda anslutning till fjärrskrivbord till en virtuell Windows-dator i Azure
keywords: 'Remote desktop-fel, fel anslutning till fjärrskrivbord kan inte ansluta till en virtuell dator, felsökning: fjärrskrivbord'
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
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 8c027db917216d273b5fb36f8920d6fa2fb4c9a9
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072158"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Felsöka specifika RDP-felmeddelanden till en virtuell Windows-dator i Azure
Du kan få ett visst felmeddelande när du använder en fjärrskrivbordsanslutning till en Windows-dator (VM) i Azure. Den här artikeln beskrivs några av de vanligaste felmeddelandena som uppstod, tillsammans med felsökning för att lösa dem. Om du har problem med anslutningen till den virtuella datorn med hjälp av RDP men vill inte påträffar ett visst felmeddelande går den [felsökningsguide för fjärrskrivbord](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Information om specifika felmeddelanden finns i:

* [Fjärrsessionen kopplades från eftersom det finns inga fjärranslutna servrar för fjärrskrivbordslicenser kan tillhandahålla en licens](#rdplicense).
* [Fjärrskrivbordet kan inte hitta datorn ”name”](#rdpname).
* [Ett autentiseringsfel har uppstått. Det går inte att kontakta den lokala säkerhetskontrollen](#rdpauth).
* [Windows-Security-fel: dina autentiseringsuppgifter fungerar inte](#wincred).
* [Den här datorn kan inte ansluta till fjärrdatorn](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Fjärrsessionen kopplades från eftersom det inte finns några fjärranslutna servrar för fjärrskrivbordslicenser kan tillhandahålla en licens.
Orsak: 120 dagar licensiering tidsperioden för serverrollen för fjärrskrivbord har upphört att gälla och du måste installera licenser.

Spara en lokal kopia av RDP-filen från portalen som en lösning och kör det här kommandot i en PowerShell-kommandotolk för att ansluta. Det här steget inaktiverar licensiering för bara anslutningen:

        mstsc <File name>.RDP /admin

Om du inte verkligen behöver fler än två samtidiga anslutningar till fjärrskrivbord till den virtuella datorn kan använda du Serverhanteraren för att ta bort serverrollen för Fjärrskrivbordstjänster.

Mer information finns i bloggposten [Azure VM misslyckas med ”ingen licens fjärrskrivbordsservrar tillgänglig”](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Fjärrskrivbordet kan inte hitta datorn ”name”.
Orsak: Fjärrskrivbordsklienten på datorn kan inte matcha namnet på datorn i inställningarna för RDP-filen.

Möjliga lösningar:

* Om du är på intranätet, kan du kontrollera att datorn har åtkomst till proxyservern och skicka HTTPS-trafik till den.
* Om du använder en lokalt lagrad RDP-fil, försök att använda det som genereras av portalen. Det här steget säkerställer du att rätt DNS-namn för den virtuella datorn, eller Molntjänsten och endpoint-porten för den virtuella datorn. Här är ett exempel på en RDP-fil som genererats av portalen:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Adressdelen av den här RDP-filen har:

* Fullständigt kvalificerade domännamnet för den molntjänst som innehåller den virtuella datorn (”tailspin-azdatatier.cloudapp.net” i det här exemplet).
* Externa TCP-porten för slutpunkten för Remote Desktop-trafik (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ett autentiseringsfel har uppstått. Att det går inte kontakta den lokala säkerhetskontrollen.
Orsak: Målet VM kan inte hitta säkerhetskontrollen i den användare del av dina autentiseringsuppgifter.

Om användarnamnet är i formatet *SecurityAuthority*\\*användarnamn* (exempel: corp\användare1), *SecurityAuthority* del är antingen VM datornamnet (för den lokala säkerhetskontrollen) eller en Active Directory-domännamn.

Möjliga lösningar:

* Om kontot är lokalt på den virtuella datorn, se till att namnet på virtuella datorn är rätt stavat.
* Om kontot är på en Active Directory-domän, kontrollera stavningen av namnet på en domän.
* Om det är ett domänkonto för Active Directory och domännamnet är rättstavat, kontrollera att en domänkontrollant i domänen. Det är ett vanligt problem i virtuella Azure-nätverk som innehåller domänkontrollanter som en domänkontrollant är inte tillgänglig eftersom den inte har startats. Som en tillfällig lösning kan du använda ett lokalt administratörskonto i stället för ett domänkonto.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows-Security-fel: dina autentiseringsuppgifter fungerar inte.
Orsak: Målet VM kan inte verifiera ditt kontonamn och lösenord.

En Windows-baserad dator kan verifiera autentiseringsuppgifterna för ett lokalt konto eller ett domänkonto.

* Lokala konton använder den *ComputerName*\\*användarnamn* syntax (exempel: SQL1\Admin4798).
* Domänkonton och använda den *DomainName*\\*användarnamn* syntax (exempel: CONTOSO\peterodman).

Om du har befordrat den virtuella datorn till en domänkontrollant i en ny Active Directory-skog, konverteras det lokala administratörskontot som du loggade in med till ett motsvarande konto med samma lösenord i ny skog och domän. Det lokala kontot så tas bort.

Till exempel om du har loggat in med det lokala kontot DC1\DCAdmin och sedan upphöja den virtuella datorn som en domänkontrollant i en ny skog för domänen corp.contoso.com, det lokala kontot DC1\DCAdmin tas bort och är en ny domänkonto (CORP\DCAdmin) Skapa med samma lösenord.

Kontrollera att kontonamnet är ett namn som den virtuella datorn kan verifiera som ett giltigt konto och att lösenordet är korrekt.

Om du behöver ändra lösenordet för det lokala administratörskontot, se [hur du återställer ett lösenord eller tjänsten Remote Desktop för Windows-datorer](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Den här datorn kan inte ansluta till fjärrdatorn.
Orsak: Det konto som används för att ansluta saknar fjärrskrivbord inloggning rättigheter.

Alla Windows-datorer har ett fjärrskrivbord lokala användargruppen som innehåller de konton och grupper som kan logga in på den via fjärranslutning. Medlemmar i gruppen lokala administratörer har också åtkomst, även om dessa konton inte visas i den lokala användargruppen för Remote Desktop. För domänanslutna datorer innehåller också Domänadministratörer för domänen i den lokala administratörsgruppen.

Se till att det konto som du använder för att ansluta till har Fjärrskrivbord inloggning rättigheter. Som en tillfällig lösning kan använda en domän eller lokalt administratörskonto för att ansluta via fjärrskrivbord. Lägg till önskat konto i den lokala användargruppen för fjärrskrivbord genom att använda snapin-modulen Microsoft Management Console (**Systemverktyg > lokala användare och grupper > grupper > användare av fjärrskrivbord**).

## <a name="next-steps"></a>Nästa steg
Om ingen av dessa fel uppstod och du har en okänd utfärda med att ansluta med RDP, se den [felsökningsguide för fjärrskrivbord](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Felsökningssteg för åtkomst till program som körs på en virtuell dator, finns i [Felsök åtkomst till ett program som körs på en Azure VM](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Om du har problem med hjälp av SSH (Secure Shell) kan ansluta till en Linux-VM i Azure finns [felsökning av SSH-anslutningar till en Linux-VM i Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

