---
title: Använda dynamisk DNS för att registrera värdnamn i Azure | Microsoft-dokument
description: Lär dig hur du konfigurerar dynamisk DNS för att registrera värdnamn på dina egna DNS-servrar.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: c2ef842fd62ef060f06536d66387c3facd0627b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60640386"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Använd dynamisk DNS för att registrera värdnamn i DNS-servern

[Azure tillhandahåller namnmatchning](virtual-networks-name-resolution-for-vms-and-role-instances.md) för virtuella datorer (VM) och rollinstanser. När dina namnmatchningsbehov överskrider funktionerna i Azures standard-DNS kan du tillhandahålla dina egna DNS-servrar. Genom att använda dina egna DNS-servrar kan du skräddarsy din DNS-lösning så att den passar dina egna specifika behov. Du kan till exempel behöva komma åt lokala resurser via Active Directory-domänkontrollanten.

När dina anpassade DNS-servrar är värd för Azure-virtuella datorer kan du vidarebefordra värdnamnsfrågor för samma virtuella nätverk till Azure för att matcha värdnamn. Om du inte vill använda det här alternativet kan du registrera värdnamnen för virtuella datorer i DNS-servern med dynamisk DNS (DDNS). Azure har inte autentiseringsuppgifter för att direkt skapa poster i dina DNS-servrar, så alternativa arrangemang behövs ofta. Några vanliga scenarier, med alternativ följer:

## <a name="windows-clients"></a>Windows-klienter
Windows-klienter som inte är anslutna till domän försöker identifiera DDNS-uppdateringar när de startas eller när deras IP-adress ändras. DNS-namnet är värdnamnet plus det primära DNS-suffixet. Azure lämnar det primära DNS-suffixet tomt, men du kan ställa in suffixet i den virtuella datorn, via [användargränssnittet](https://technet.microsoft.com/library/cc794784.aspx) eller [PowerShell](/powershell/module/dnsclient/set-dnsclient).

Domänanslutna Windows-klienter registrerar sina IP-adresser med domänkontrollanten med hjälp av säkra DDNS. Domänkopplingsprocessen anger det primära DNS-suffixet på klienten och skapar och underhåller förtroenderelationen.

## <a name="linux-clients"></a>Linux-klienter
Linux-klienter registrerar sig i allmänhet inte med DNS-servern vid start, de antar att DHCP-servern gör det. Azures DHCP-servrar har inte behörighet att registrera poster på DNS-servern. Du kan använda `nsupdate`ett verktyg som heter , som ingår i Bind-paketet, för att skicka DDNS-uppdateringar. Eftersom DDNS-protokollet är standardiserat `nsupdate` kan du använda även när du inte använder Bind på DNS-servern.

Du kan använda de krokar som tillhandahålls av DHCP-klienten för att skapa och underhålla värdnamnsposten på DNS-servern. Under DHCP-cykeln kör klienten skripten i */etc/dhcp/dhclient-exit-hooks.d/*. Du kan använda krokarna för att `nsupdate`registrera den nya IP-adressen med . Ett exempel:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

Du kan också `nsupdate` använda kommandot för att utföra säkra DDNS-uppdateringar. När du till exempel använder en Bind DNS-server [genereras](http://linux.yyz.us/nsupdate/)ett offentligt-privat nyckelpar . DNS-servern är [konfigurerad](http://linux.yyz.us/dns/ddns-server.html) med den offentliga delen av nyckeln, så att den kan verifiera signaturen på begäran. Om du vill ange `nsupdate`nyckelparet `-k` till använder du alternativet för att DDNS-uppdateringsbegäran ska signeras.

När du använder en Windows DNS-server kan du `-g` använda `nsupdate`Kerberos-autentisering med parametern i `nsupdate`, men den är inte tillgänglig i Windows-versionen av . Om du vill `kinit` använda Kerberos använder du för att läsa in autentiseringsuppgifterna. Du kan till exempel läsa in autentiseringsuppgifter `nsupdate -g` från en [keytab-fil](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)) och sedan hämta autentiseringsuppgifterna från cacheminnet.

Om det behövs kan du lägga till ett DNS-söksuffix i dina virtuella datorer. DNS-suffixet anges i filen */etc/resolv.conf.* De flesta Linux-distributioner hanterar automatiskt innehållet i den här filen, så oftast kan du inte redigera den. Du kan dock åsidosätta suffixet med `supersede` hjälp av DHCP-klientens kommando. Om du vill åsidosätta suffixet lägger du till följande rad i filen */etc/dhcp/dhclient.conf:*

```
supersede domain-name <required-dns-suffix>;
```
