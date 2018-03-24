---
title: Med dynamisk DNS för att registrera värdnamn
description: Den här sidan ger information om hur du ställer in dynamisk DNS för att registrera värdnamn i DNS-servrarna.
services: dns
documentationcenter: na
author: GarethBradshawMSFT
manager: timlt
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: garbrad
ms.openlocfilehash: 5d62c40bfc909915fa222db12413634aa7ce7158
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Med dynamisk DNS för att registrera värdnamn i DNS-servern
[Azure tillhandahåller namnmatchning](virtual-networks-name-resolution-for-vms-and-role-instances.md) för virtuella datorer (VM) och rollinstanser. När din namnmatchning måste utöver de som tillhandahålls av Azure, kan du ange DNS-servrarna. Detta ger dig möjlighet att anpassa din DNS-lösning så att de passar dina egna behov. Du kan behöva åtkomst till lokala resurser via Active Directory-domänkontrollant.

När din anpassade DNS-servrar är värd för som virtuella Azure-datorer kan du vidarebefordra hostname frågor för samma virtuella nätverk till Azure för att matcha värdnamn. Om du inte vill använda den här vägen kan du registrera VM-värdnamn i DNS-servern med hjälp av dynamisk DNS.  Azure behöver inte möjligheten (t.ex. autentiseringsuppgifter) direkt skapa poster i DNS-servrar så att andra åtgärder krävs ofta. Här följer några vanliga scenarier med andra alternativ.

## <a name="windows-clients"></a>Windows-klienter
Icke-domänanslutna Windows-klienter försöker oskyddat dynamisk DNS (DDNS) uppdateringar när de startar eller när IP-adresser ändras. DNS-namnet är värdnamnet plus det primära DNS-suffixet. Azure låter primärt DNS-suffix som är tom, men du kan ange detta i den virtuella datorn den [UI](https://technet.microsoft.com/library/cc794784.aspx) eller [med hjälp av automation som beskrivs här](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Domänanslutna Windows-klienter för att registrera sina IP-adresser med domänkontrollanten med hjälp av säkra dynamiska DNS. Processen för domänanslutning anger primärt DNS-suffix på klienten och skapar och underhåller förtroenderelationen.

## <a name="linux-clients"></a>Linux-klienter
Linux-klienter normalt registrera inte sig själva med DNS-servern vid start, de förutsätter att DHCP-servern fungerar. Azures DHCP-servrar har inte möjlighet eller autentiseringsuppgifter för att registrera poster i DNS-servern.  Du kan använda ett verktyg som kallas *nsupdate*, som ingår i Bind-paketet, för att skicka dynamiska DNS-uppdateringar. Eftersom dynamisk DNS-protokollet är standardiserad, kan du använda *nsupdate* även när du inte använder Bind på DNS-servern.

Du kan använda de kod som tillhandahålls av DHCP-klient för att skapa och upprätthålla värdnamnsposten i DNS-servern. Under DHCP-cykel klienten kör skript i */etc/dhcp/dhclient-exit-hooks.d/*. Detta kan användas för att registrera den nya IP-adressen med hjälp av *nsupdate*. Exempel:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
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

        
        

Du kan också använda den *nsupdate* kommando för att utföra säkra dynamiska DNS-uppdateringar. När du använder en Bind DNS-server, ett privat-offentligt nyckelpar är exempelvis [genereras](http://linux.yyz.us/nsupdate/).  DNS-servern är [konfigurerats](http://linux.yyz.us/dns/ddns-server.html) med den offentliga delen av nyckeln så att den kan verifiera signaturen på begäran. Du måste använda den *-k* möjlighet att ange nyckel till *nsupdate* för dynamisk DNS-uppdateringsbegäran signeras.

När du använder en Windows DNS-server kan du använda Kerberos-autentisering med den *-g* parameter i *nsupdate* (inte tillgängligt i Windows-versionen av *nsupdate*). Det gör du genom att använda *kinit* att läsa in autentiseringsuppgifter (t.ex. från en [keytab filen](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Sedan *nsupdate -g* ska hämta autentiseringsuppgifter från cacheminnet.

Om det behövs kan du lägga till en DNS-sökningssuffix dina virtuella datorer. DNS-suffix anges i den */etc/resolv.conf* fil. De flesta distributioner av Linux hantera automatiskt innehållet för den här filen, så ofta du kan inte redigera den. Du kan dock åsidosätta suffixet med hjälp av DHCP-klient *ersätter* kommando. Du gör detta i */etc/dhcp/dhclient.conf*, lägga till:

        supersede domain-name <required-dns-suffix>;

