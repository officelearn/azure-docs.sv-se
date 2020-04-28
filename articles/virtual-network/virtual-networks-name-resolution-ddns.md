---
title: Använda dynamisk DNS för att registrera värdnamn i Azure | Microsoft Docs
description: Lär dig hur du ställer in dynamisk DNS för att registrera värdnamn på dina egna DNS-servrar.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "60640386"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Använd dynamisk DNS för att registrera värdnamn i DNS-servern

[Azure tillhandahåller namn matchning](virtual-networks-name-resolution-for-vms-and-role-instances.md) för virtuella datorer och roll instanser. När namn matchningen behöver överskrida de funktioner som tillhandahålls av Azures standard-DNS, kan du ange dina egna DNS-servrar. Med hjälp av dina egna DNS-servrar kan du skräddarsy DNS-lösningen så att den passar dina specifika behov. Du kan till exempel behöva komma åt lokala resurser via din Active Directory domänkontrollant.

När dina anpassade DNS-servrar är värdar för virtuella Azure-datorer kan du vidarebefordra värd namns frågor för samma virtuella nätverk till Azure för att matcha värdnamn. Om du inte vill använda det här alternativet kan du registrera dina VM-värdnamn på DNS-servern med hjälp av dynamisk DNS (DDNS). Azure har inte de autentiseringsuppgifter som krävs för att direkt skapa poster i dina DNS-servrar, så det krävs ofta alternativa regler. Några vanliga scenarier, med alternativ följer:

## <a name="windows-clients"></a>Windows-klienter
Icke-domänanslutna Windows-klienter försöker med oskyddade DDNS-uppdateringar när de startar eller när deras IP-adress ändras. DNS-namnet är värd namnet och det primära DNS-suffixet. Azure lämnar det primära DNS-suffixet tomt, men du kan ange suffixet i den virtuella datorn via [användar gränssnittet](https://technet.microsoft.com/library/cc794784.aspx) eller [PowerShell](/powershell/module/dnsclient/set-dnsclient).

Domänanslutna Windows-klienter registrerar sina IP-adresser med domänkontrollanten med hjälp av säker DDNS. Processen för domän anslutning anger det primära DNS-suffixet på klienten och skapar och underhåller förtroende relationen.

## <a name="linux-clients"></a>Linux-klienter
Linux-klienter registrerar vanligt vis inte sig själva med DNS-servern vid start, de antar att DHCP-servern gör det. Azures DHCP-servrar har inte de autentiseringsuppgifter som krävs för att registrera poster på din DNS-server. Du kan använda ett verktyg som `nsupdate`kallas, som ingår i bind-paketet, för att skicka DDNS-uppdateringar. Eftersom DDNS-protokollet är standardiserat kan du använda `nsupdate` även när du inte använder Bind på DNS-servern.

Du kan använda hookarna som tillhandahålls av DHCP-klienten för att skapa och underhålla hostname-posten på DNS-servern. Under DHCP-cykeln kör klienten skripten i */etc/DHCP/dhclient-Exit-hooks.d/*. Du kan använda hookarna för att registrera den nya IP-adressen `nsupdate`med hjälp av. Ett exempel:

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

Du kan också använda `nsupdate` kommandot för att utföra säkra DDNS-uppdateringar. När du använder en DNS-server för bind [skapas](http://linux.yyz.us/nsupdate/)till exempel ett offentligt privat privat nyckel par. DNS-servern [konfigureras](http://linux.yyz.us/dns/ddns-server.html) med den offentliga delen av nyckeln, så att den kan verifiera signaturen på begäran. Om du vill ange nyckel paret `nsupdate`för använder du `-k` alternativet för att DDNS ska signeras.

När du använder en Windows DNS-server kan du använda Kerberos-autentisering med `-g` parametern i `nsupdate`, men den är inte tillgänglig i Windows-versionen av. `nsupdate` Använd `kinit` för att läsa in autentiseringsuppgifterna för att använda Kerberos. Du kan till exempel läsa in autentiseringsuppgifter från en [keytab-fil](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)och sedan `nsupdate -g` hämta autentiseringsuppgifterna från cachen.

Om det behövs kan du lägga till ett DNS-söksuffix till dina virtuella datorer. DNS-suffixet anges i */etc/resolv.conf* -filen. De flesta Linux-distributioner hanterar automatiskt innehållet i den här filen, så vanligt vis kan du inte redigera den. Du kan dock åsidosätta suffixet med hjälp av DHCP-klientens `supersede` kommando. Om du vill åsidosätta suffixet lägger du till följande rad i */etc/DHCP/dhclient.conf* -filen:

```
supersede domain-name <required-dns-suffix>;
```
