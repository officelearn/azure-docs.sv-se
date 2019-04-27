---
title: Med dynamisk DNS för att registrera värdnamn i Azure | Microsoft Docs
description: Lär dig hur du konfigurerar dynamisk DNS för att registrera värdnamn i DNS-servrarna.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640386"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Använd dynamisk DNS för att registrera värdnamn i DNS-servern

[Azure ger namnmatchning](virtual-networks-name-resolution-for-vms-and-role-instances.md) för virtuella datorer (VM) och rollinstanser. När din namnmatchning måste överskrida funktionerna i Azures standard DNS, kan du ange dina egna DNS-servrar. DNS-servrarna ger dig möjlighet att anpassa dina DNS-lösning så att den passar dina egna behov. Du kan behöva åtkomst till lokala resurser via din Active Directory-domänkontrollant.

När din anpassade DNS-servrar finns som virtuella Azure-datorer, kan du vidarebefordra värdnamn frågor för samma virtuella nätverk till Azure för att matcha värdnamn. Om du inte vill använda det här alternativet kan du registrera VM-värdnamn i DNS-servern med hjälp av dynamisk DNS (DDNS). Azure har inte autentiseringsuppgifterna som direkt skapa poster i DNS-servrarna, så att andra åtgärder krävs ofta. Det följer några vanliga scenarier med alternativ:

## <a name="windows-clients"></a>Windows-klienter
Icke-domänanslutna Windows-klienter försöker oskyddat DDNS uppdateringar när de startar eller när deras IP-adressen ändras. DNS-namnet är värdnamnet plus det primära DNS-suffixet. Azure blir det primära DNS-suffixet är tom, men du kan ange suffixet i den virtuella datorn den [användargränssnittet](https://technet.microsoft.com/library/cc794784.aspx) eller [PowerShell](/powershell/module/dnsclient/set-dnsclient).

Domänanslutna Windows-klienter för att registrera sina IP-adresser med domänkontrollanten genom att använda säker DDNS. Processen för domänanslutning anger primära DNS-suffix på klienten och skapar och underhåller förtroenderelationen.

## <a name="linux-clients"></a>Linux-klienter
Linux-klienter vanligtvis registrera inte sig själva med DNS-servern vid start, de antar DHCP-server gör. Azures DHCP-servrar har inte autentiseringsuppgifterna som ska registrera poster i DNS-servern. Du kan använda ett verktyg som kallas `nsupdate`, som ingår i Bind-paketet, för att skicka DDNS programuppdateringar. Eftersom DDNS-protokollet är standardiserat, kan du använda `nsupdate` även när du inte använder bindning på DNS-servern.

Du kan använda krokar som tillhandahålls av DHCP-klient för att skapa och underhålla värdnamnsposten i DNS-servern. Under DHCP-cykel klienten kör skripten i */etc/dhcp/dhclient-exit-hooks.d/*. Du kan använda hookar för att registrera den nya IP-adress med hjälp av `nsupdate`. Exempel:

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

Du kan också använda den `nsupdate` kommando för att utföra säkra DDNS uppdaterar. Till exempel när du använder en binda DNS-server, ett offentligt / privat nyckelpar är [genereras](http://linux.yyz.us/nsupdate/). DNS-servern är [konfigurerats](http://linux.yyz.us/dns/ddns-server.html) med den offentliga delen av nyckeln så att den kan verifiera signaturen på begäran. Ange nyckel till `nsupdate`, använda den `-k` alternativ för DDNS Uppdateringsförfrågan signeras.

När du använder en Windows DNS-server kan du använda Kerberos-autentisering med den `-g` parameter i `nsupdate`, men det är inte tillgängligt i Windows-versionen av `nsupdate`. Använd för att använda Kerberos `kinit` att läsa in autentiseringsuppgifterna. Du kan till exempel läsa in autentiseringsuppgifter från en [keytab filen](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), sedan `nsupdate -g` hämtar autentiseringsuppgifter från cachen.

Om det behövs kan du lägga till en DNS-sökningssuffix till dina virtuella datorer. DNS-suffix anges i den */etc/resolv.conf* fil. De flesta Linux-distributioner hanterar automatiskt innehållet i den här filen så ofta du kan inte redigera den. Men du kan åsidosätta suffixet med hjälp av DHCP-klienten `supersede` kommando. Om du vill åsidosätta suffixet, lägger du till följande rad för att den */etc/dhcp/dhclient.conf* fil:

```
supersede domain-name <required-dns-suffix>;
```
