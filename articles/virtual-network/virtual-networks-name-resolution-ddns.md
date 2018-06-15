---
title: Med dynamisk DNS för att registrera värdnamn i Azure | Microsoft Docs
description: Lär dig mer om att konfigurera dynamisk DNS för att registrera värdnamn i DNS-servrarna.
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
ms.openlocfilehash: bbbce45b7c321fd4934374c76f2a4421b125d46f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31600962"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Använda dynamisk DNS för att registrera värdnamn i DNS-servern

[Azure tillhandahåller namnmatchning](virtual-networks-name-resolution-for-vms-and-role-instances.md) för virtuella datorer (VM) och rollinstanser. När din namnmatchning måste överskrida funktionerna i Azures standard DNS, kan du ange DNS-servrarna. Med hjälp av DNS-servrar ger dig möjlighet att anpassa din DNS-lösning så att de passar dina egna behov. Du kan behöva åtkomst till lokala resurser via Active Directory-domänkontrollant.

När din anpassade DNS-servrar finns som virtuella Azure-datorer, kan du vidarebefordra hostname frågor för samma virtuella nätverk till Azure för att matcha värdnamn. Om du inte vill använda det här alternativet kan du registrera VM-värdnamn i DNS-servern med hjälp av dynamisk DNS (DDNS). Azure har inte autentiseringsuppgifterna som direkt skapa poster i DNS-servrar så att andra åtgärder krävs ofta. Några vanliga scenarier med alternativ så:

## <a name="windows-clients"></a>Windows-klienter
Icke-domänanslutna Windows-klienter försöker oskyddat DDNS uppdateringar när de startar eller när IP-adresser ändras. DNS-namnet är värdnamnet plus det primära DNS-suffixet. Azure låter primärt DNS-suffix som är tom, men du kan ange suffixet i den virtuella datorn den [användargränssnittet](https://technet.microsoft.com/library/cc794784.aspx) eller [PowerShell](/powershell/module/dnsclient/set-dnsclient).

Domänanslutna Windows-klienter för att registrera sina IP-adresser med domänkontrollanten med hjälp av säkra DDNS. Processen för domänanslutning anger primärt DNS-suffix på klienten och skapar och underhåller förtroenderelationen.

## <a name="linux-clients"></a>Linux-klienter
Linux-klienter normalt registrera inte sig själva med DNS-servern vid start, de förutsätter att DHCP-servern fungerar. Azures DHCP-servrar har inte autentiseringsuppgifterna som ska registrera poster i DNS-servern. Du kan använda ett verktyg som kallas `nsupdate`, som ingår i Bind-paketet, för att skicka DDNS uppdateringar. Eftersom protokollet DDNS standardiserade, kan du använda `nsupdate` även när du inte använder Bind på DNS-servern.

Du kan använda de kod som tillhandahålls av DHCP-klient för att skapa och upprätthålla värdnamnsposten i DNS-servern. Under DHCP-cykel klienten kör skript i */etc/dhcp/dhclient-exit-hooks.d/*. Du kan använda hook för att registrera en ny IP-adress med hjälp av `nsupdate`. Exempel:

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

Du kan också använda den `nsupdate` kommando för att utföra säker DDNS uppdateras. När du använder en Bind DNS-server, ett privat-offentligt nyckelpar är exempelvis [genereras](http://linux.yyz.us/nsupdate/). DNS-servern är [konfigurerats](http://linux.yyz.us/dns/ddns-server.html) med den offentliga delen av nyckeln, så att den kan verifiera signaturen på begäran. Ange nyckel till `nsupdate`, använda den `-k` alternativ för DDNS uppdatera begäran ska vara signerade.

När du använder en Windows DNS-server kan du använda Kerberos-autentisering med den `-g` parameter i `nsupdate`, men det är inte tillgängliga i Windows-versionen av `nsupdate`. Om du vill använda Kerberos `kinit` att läsa in autentiseringsuppgifterna. Du kan till exempel läsa in autentiseringsuppgifter från en [keytab filen](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), sedan `nsupdate -g` hämtar autentiseringsuppgifter från cacheminnet.

Om det behövs kan du lägga till en DNS-sökningssuffix dina virtuella datorer. DNS-suffix anges i den */etc/resolv.conf* fil. De flesta distributioner av Linux hantera automatiskt innehållet för den här filen, så ofta du kan inte redigera den. Du kan dock åsidosätta suffixet med hjälp av DHCP-klient `supersede` kommando. Om du vill åsidosätta suffixet lägger du till följande rad i den */etc/dhcp/dhclient.conf* fil:

```
supersede domain-name <required-dns-suffix>;
```
