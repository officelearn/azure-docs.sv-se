---
title: DNS-namnmatchningsalternativ för Linux virtuella datorer i Azure
description: Namnet upplösning scenarier för Linux virtuella datorer i Azure IaaS, inklusive tillhandahålls DNS-tjänster, hybrid externa DNS- och ta med din egen DNS-server.
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: jeconnoc
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: d899e037a144892d6d2c843fec08d63a9e3e514a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "30836103"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Alternativ för DNS-namnmatchningen för Linux virtuella datorer i Azure
Azure tillhandahåller DNS-namnmatchning som standard för alla virtuella datorer som är i ett enda virtuellt nätverk. Du kan implementera din egen lösning för DNS-namnet lösning genom att konfigurera dina egna DNS-tjänster på dina virtuella datorer som är värd för Azure. Följande scenarier bör hjälpa dig att välja det som passar din situation.

* [Namnmatchning som Azure tillhandahåller](#azure-provided-name-resolution)
* [Namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server)

Typ av namnmatchning som du använder beror på hur dina virtuella datorer och rollinstanser måste kommunicera med varandra.

Följande tabell visar scenarier och motsvarande name resolution lösningar:

| **Scenario** | **Lösning** | **Suffix** |
| --- | --- | --- |
| Namnmatchning mellan rollinstanser eller virtuella datorer i samma virtuella nätverk |[Namnmatchning som Azure tillhandahåller](#azure-provided-name-resolution) |värdnamn eller fullständigt kvalificerat domännamn (FQDN) |
| Namnmatchning mellan rollinstanser eller virtuella datorer i olika virtuella nätverk |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Matchning av lokala datorer och tjänstnamn från rollinstanser eller virtuella datorer i Azure |Kundhanterad DNS-servrar (till exempel lokala domänkontrollant, lokala skrivskyddade domänkontrollanten eller en sekundär DNS synkroniseras med hjälp av zonöverföringar). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Lösning med Azure värdnamn från lokala datorer |Vidarebefordra frågor till en kundhanterad DNS-proxyserver i motsvarande virtuella nätverk. Proxyservern vidarebefordrar frågor till Azure för matchning. Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Omvänd DNS för interna IP-adresser |[Namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server) |saknas |

## <a name="name-resolution-that-azure-provides"></a>Namnmatchning som Azure tillhandahåller
Azure erbjuder intern namnmatchning för virtuella datorer och rollinstanser som finns i samma virtuella nätverk för tillsammans med matchning av offentliga DNS-namn. I virtuella nätverk som baseras på Azure Resource Manager, är DNS-suffix konsekvent på det virtuella nätverket. FQDN behövs inte. DNS-namn kan tilldelas virtuella datorer och nätverkskort (NIC). Även om namnmatchning som Azure tillhandahåller inte kräver någon konfiguration, är det inte valet för alla distributionsscenarier, som visas i tabellen ovan.

### <a name="features-and-considerations"></a>Funktioner och överväganden
**Funktioner:**

* Ingen konfiguration krävs för att använda namnmatchning som Azure tillhandahåller.
* Den namnmatchningstjänst som Azure tillhandahåller har hög tillgänglighet. Du behöver inte skapa och hantera kluster DNS-servrar.
* Den namnmatchningstjänst som Azure tillhandahåller kan användas tillsammans med DNS-servrar för att matcha både lokalt och Azure värdnamn.
* Namnmatchning tillhandahålls mellan virtuella datorer i virtuella nätverk utan krävs för FQDN.
* Du kan använda värdnamn som bäst beskriver dina distributioner i stället för att arbeta med automatiskt genererat namn.

**Att tänka på:**

* DNS-suffix som Azure skapar ändras inte.
* Du kan registrera dina egna poster manuellt.
* WINS och NetBIOS stöds inte.
* Värdnamn måste vara kompatibel med DNS.
    Namn måste använda endast 0-9, a-z och '-', och de får inte inledas eller avslutas med en '-'. Avsnittet RFC 3696 2.
* DNS-frågorna begränsas för varje virtuell dator. Begränsning bör inte påverka de flesta program.  Om begäran begränsning observeras, kontrollerar du att cachelagring på klientsidan är aktiverad.  Mer information finns i [få ut mesta möjliga av namnmatchning som Azure tillhandahåller](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Få ut mesta möjliga av namnmatchning som Azure tillhandahåller
**Klientcachelagring:**

Några DNS-frågor skickas inte över nätverket. Klientcachelagring hjälper minskar svarstider och förbättrar återhämtning i nätverket inkonsekvenser genom att lösa återkommande DNS-frågor från en lokal cache. DNS-poster innehåller en Time-To-Live (TTL), vilket gör att cacheminnet för att lagra posten så länge som möjligt utan att påverka poster dokumentens. Därför lämpar klientcachelagring sig för de flesta situationer.

Vissa Linux-distributioner som innehåller inte cachelagring som standard. Vi rekommenderar att du lägger till en cache varje virtuell Linux-dator när du har kontrollerat att det inte är ett lokalt cacheminne redan.

Det finns flera olika DNS cachelagring paket, till exempel dnsmasq. Här följer stegen för att installera dnsmasq på de vanligaste distributioner:

**Ubuntu (använder resolvconf)**
  * Installera dnsmasq-paketet (”sudo lgh get installera dnsmasq”).

**SUSE (använder netconf)**:
1. Installera dnsmasq-paketet (”sudo zypper installera dnsmasq”).
2. Aktivera tjänsten dnsmasq (”systemctl aktivera dnsmasq.service”).
3. Starta tjänsten dnsmasq (”systemctl start dnsmasq.service”).
4. Redigera ”/ etc/sysconfig/nätverk/config” och ändra NETCONFIG_DNS_FORWARDER = ”” till ”dnsmasq”.
5. Uppdatera resolv.conf (”netconfig uppdatering”) för att ställa in cachen som den lokala DNS-matchning.

**CentOS av falska Wave programvara (tidigare OpenLogic; använder NetworkManager)**
1. Installera dnsmasq-paketet (”sudo yum installera dnsmasq”).
2. Aktivera tjänsten dnsmasq (”systemctl aktivera dnsmasq.service”).
3. Starta tjänsten dnsmasq (”systemctl start dnsmasq.service”).
4. Lägg till ”lägga domän-namnservrar 127.0.0.1”; till ”/etc/dhclient-eth0.conf”.
5. Starta om nätverkstjänsten (”tjänsten network omstart”) för att ställa in cachen som den lokala DNS-matchning

> [!NOTE]
> : 'Dnsmasq' paketet är endast ett av många DNS-cacheminnet som är tillgängliga för Linux. Innan du använder det. Kontrollera dess lämplighet för dina behov och att ingen annan cache är installerad.
>
>

**Klientsidans återförsök**

DNS är främst ett UDP-protokoll. Eftersom UDP-protokollet inte garantera meddelandeleverans, hanterar logik i själva DNS-protokollet. Varje DNS-klient (operativsystemet) kan ha olika logik beroende på den person som skapade inställningar:

* Windows-operativsystem försök igen efter att en andra och sedan igen efter en annan två, fyra och ett annat fyra sekunder.
* Standard Linux installationsprogrammet återförsöken efter fem sekunder.  Du bör ändra detta att försöka fem gånger på en sekunds intervall.  

Kontrollera de aktuella inställningarna på en Linux virtuella 'cat /etc/resolv.conf' och titt på raden ”alternativ”, till exempel:

    options timeout:1 attempts:5

Filen resolv.conf genereras automatiskt och bör inte redigeras. De specifika stegen som lägger till raden ”alternativ” varierar beroende på distribution:

**Ubuntu** (använder resolvconf)
1. Lägg till rad alternativ för ' / etc/resolveconf/resolv.conf.d/head'.
2. Kör resolvconf -u att uppdatera.

**SUSE** (använder netconf)
1. Lägg till 'timeout:1 försök: 5' till NETCONFIG_DNS_RESOLVER_OPTIONS = ”” parametern i ”/ etc/sysconfig/nätverk/config”.
2. Kör netconfig-uppdateringen att uppdatera.

**CentOS av falska Wave-programvara (tidigare OpenLogic)** (använder NetworkManager)
1. Lägg till ' RES_OPTIONS = ”timeout:1 försök: 5” ”till” / etc/sysconfig/nätverk ”.
2. Kör 'tjänsten network omstart' att uppdatera.

## <a name="name-resolution-using-your-own-dns-server"></a>Namnmatchning med hjälp av DNS-servern
Din namnmatchningen kan utöver de funktioner som Azure tillhandahåller. Du kan till exempel kräva DNS-matchning mellan virtuella nätverk. Du kan använda DNS-servrar för att täcka det här scenariot.  

DNS-servrar inom ett virtuellt nätverk kan vidarebefordra DNS-frågor till rekursiv matchare Azure att matcha värdnamn som finns i samma virtuella nätverk. En DNS-server som körs i Azure kan svara på DNS-frågor för DNS-zon för filerna och vidarebefordra alla frågor till Azure. Den här funktionen kan virtuella datorer finns i både din poster i zonen filer och värdnamn som Azure tillhandahåller (via vidarebefordrare). Åtkomst till de rekursiva matchare Azure anges via den virtuella IP-Adressen 168.63.129.16.

DNS-vidarebefordran också aktiverar DNS-matchning mellan virtuella nätverk och gör att dina lokala datorer att matcha värdnamn som Azure tillhandahåller. För att lösa värdnamnet för en virtuell dators virtuella för DNS-server måste finnas i samma virtuella nätverk och konfigureras för att vidarebefordra hostname frågor till Azure. Eftersom DNS-suffix skiljer sig åt i varje virtuellt nätverk, kan du använda regler för villkorlig vidarebefordran skicka DNS-frågor till rätt virtuellt nätverk för matchning. Följande bild visar två virtuella nätverk och ett lokalt nätverk som gör att DNS-matchning mellan virtuella nätverk med hjälp av den här metoden:

![DNS-matchning mellan virtuella nätverk](./media/azure-dns/inter-vnet-dns.png)

När du använder namnmatchning som Azure tillhandahåller har interna DNS-suffixet angetts för varje virtuell dator med hjälp av DHCP. När du använder din egen lösning för name resolution detta suffix anges till virtuella datorer eftersom suffixet stör andra DNS-arkitekturer. Att referera till datorer efter FQDN eller konfigurera suffixet på dina virtuella datorer, kan du använda PowerShell eller API: et för att fastställa suffixet:

* För virtuella nätverk som hanteras av Azure Resource Manager suffixet är tillgängliga via den [nätverkskort](https://msdn.microsoft.com/library/azure/mt163668.aspx) resurs. Du kan också köra den `azure network public-ip show <resource group> <pip name>` kommandot för att visa information om din offentliga IP som innehåller FQDN för nätverkskortet.

Om vidarebefordran av frågor till Azure inte passar dina behov, måste du ange DNS-lösningen.  DNS-lösningen behöver:

* Ange lämpliga värdnamnsmatchning, till exempel [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Om du använder DDNS kan behöva du inaktivera rensning av DNS-post. DHCP-lån för Azure är mycket långa och rensning kan ta bort DNS-poster för tidigt.
* Ange lämpliga rekursiv matchning så att matchning av extern domännamn.
* Vara tillgänglig (TCP och UDP-port 53) från klienterna den fungerar och att kunna få åtkomst till Internet.
* Skyddas mot åtkomst från Internet för att minimera hot med externa agenter.

> [!NOTE]
> För bästa prestanda när du använder virtuella datorer i Azure DNS-servrar, inaktivera IPv6 och tilldela en [offentlig IP på instansnivå](../../virtual-network/virtual-networks-instance-level-public-ip.md) till varje virtuell dator med DNS-server.  
>
>
