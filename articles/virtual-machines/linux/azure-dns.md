---
title: DNS-namnmatchningsalternativ för virtuella Linux-datorer
description: Namnmatchningsscenarier för virtuella Linux-datorer i Azure IaaS, inklusive dns-tjänster, hybrid extern DNS och Bring Your Own DNS-server.
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: 3d5ecaf67dcff182c7dace474b7bda45cdfd5c58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969323"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>DNS-namnmatchningsalternativ för virtuella Linux-datorer i Azure
Azure tillhandahåller DNS-namnmatchning som standard för alla virtuella datorer som finns i ett enda virtuellt nätverk. Du kan implementera din egen DNS-namnmatchningslösningslösning genom att konfigurera dina egna DNS-tjänster på dina virtuella datorer som Azure är värd för. Följande scenarier bör hjälpa dig att välja den som fungerar för din situation.

* [Namnmatchning som Azure tillhandahåller](#name-resolution-that-azure-provides)
* [Namnmatchning med din egen DNS-server](#name-resolution-using-your-own-dns-server)

Vilken typ av namnmatchning du använder beror på hur dina virtuella datorer och rollinstanser måste kommunicera med varandra.

I följande tabell visas scenarier och motsvarande lösningar för namnmatchning:

| **Scenario** | **Lösning** | **Suffix** |
| --- | --- | --- |
| Namnmatchning mellan rollinstanser eller virtuella datorer i samma virtuella nätverk |Namnmatchning som Azure tillhandahåller |värdnamn eller fullständigt kvalificerat domännamn (FQDN) |
| Namnmatchning mellan rollinstanser eller virtuella datorer i olika virtuella nätverk |Kundhanterade DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [Namnmatchning med din egen DNS-server](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Lösning av lokala datorer och tjänstnamn från rollinstanser eller virtuella datorer i Azure |Kundhanterade DNS-servrar (till exempel lokal domänkontrollant, lokal skrivskyddad domänkontrollant eller en sekundär DNS-synkronisering med hjälp av zonöverföringar). Se [Namnmatchning med din egen DNS-server](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Lösning av Azure-värdnamn från lokala datorer |Vidarebefordra frågor till en kundhanterad DNS-proxyserver i motsvarande virtuella nätverk. Proxyservern vidarebefordrar frågor till Azure för lösning. Se [Namnmatchning med din egen DNS-server](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Omvänd DNS för interna IPs |[Namnmatchning med din egen DNS-server](#name-resolution-using-your-own-dns-server) |Saknas |

## <a name="name-resolution-that-azure-provides"></a>Namnmatchning som Azure tillhandahåller
Tillsammans med matchning av offentliga DNS-namn tillhandahåller Azure intern namnmatchning för virtuella datorer och rollinstanser som finns i samma virtuella nätverk. I virtuella nätverk som baseras på Azure Resource Manager är DNS-suffixet konsekvent i hela det virtuella nätverket. FQDN behövs inte. DNS-namn kan tilldelas både nätverkskort (NÄTVERKSKORT) och virtuella datorer. Även om namnmatchningen som Azure tillhandahåller inte kräver någon konfiguration, är det inte rätt val för alla distributionsscenarier, vilket visas i tabellen föregående.

### <a name="features-and-considerations"></a>Funktioner och överväganden
**Funktioner:**

* Ingen konfiguration krävs för att använda namnmatchning som Azure tillhandahåller.
* Namnmatchningstjänsten som Azure tillhandahåller är mycket tillgänglig. Du behöver inte skapa och hantera kluster av dina egna DNS-servrar.
* Namnmatchningstjänsten som Azure tillhandahåller kan användas tillsammans med dina egna DNS-servrar för att lösa både lokala och Azure-värdnamn.
* Namnmatchning tillhandahålls mellan virtuella datorer i virtuella nätverk utan behov av FQDN.
* Du kan använda värdnamn som bäst beskriver dina distributioner i stället för att arbeta med automatiskt genererade namn.

**Överväganden:**

* DNS-suffixet som Azure skapar kan inte ändras.
* Du kan inte registrera dina egna poster manuellt.
* WINS och NetBIOS stöds inte.
* Värdnamn måste vara DNS-kompatibla.
    Namn får endast använda 0-9, a-z och "-", och de kan inte börja eller sluta med en "-". Se RFC 3696 avsnitt 2.
* DNS-frågetrafik begränsas för varje virtuell dator. Begränsning bör inte påverka de flesta program.  Om begäran begränsning observeras, se till att klientsidan cachelagring är aktiverad.  Mer information finns i [Få ut det mesta av namnmatchningen som Azure tillhandahåller](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Få ut det mesta av namnmatchningen som Azure tillhandahåller
**Cachelagring på klientsidan:**

Vissa DNS-frågor skickas inte över nätverket. Cachelagring på klientsidan bidrar till att minska svarstiden och förbättra motståndskraften mot nätverksinkonsekvenser genom att lösa återkommande DNS-frågor från en lokal cache. DNS-poster innehåller en Time-To-Live (TTL), som gör det möjligt för cachen att lagra posten så länge som möjligt utan att påverka postens färskhet. Som ett resultat är cachelagring på klientsidan lämplig för de flesta situationer.

Vissa Linux-distributioner inkluderar inte cachelagring som standard. Vi rekommenderar att du lägger till en cache till varje virtuell Linux-dator när du har kontrollerat att det inte redan finns en lokal cache.

Det finns flera olika DNS-cachelagringspaket, till exempel dnsmasq. Här är stegen för att installera dnsmasq på de vanligaste distributionerna:

**Ubuntu (använder resolvconf)**
  * Installera dnsmasq-paketet ("sudo apt-get install dnsmasq").

**SUSE (använder netconf):**
1. Installera dnsmasq-paketet ("sudo zypper install dnsmasq").
2. Aktivera dnsmasq-tjänsten ("systemctl enable dnsmasq.service").
3. Starta dnsmasq-tjänsten ("systemctl start dnsmasq.service").
4. Redigera "/etc/sysconfig/network/config", och ändra NETCONFIG_DNS_FORWARDER="" till "dnsmasq".
5. Update resolv.conf ("netconfig update") för att ställa in cachen som den lokala DNS-matcharen.

**CentOS av Rogue Wave Software (tidigare OpenLogic; använder NetworkManager)**
1. Installera dnsmasq-paketet ("sudo yum install dnsmasq").
2. Aktivera dnsmasq-tjänsten ("systemctl enable dnsmasq.service").
3. Starta dnsmasq-tjänsten ("systemctl start dnsmasq.service").
4. Lägg till "prepend domain-name-servers 127.0.0.1;" till "/etc/dhclient-eth0.conf".
5. Starta om nätverkstjänsten ("omstart av tjänstnätverket") för att ställa in cacheminnet som den lokala DNS-matcharen

> [!NOTE]
> : "dnsmasq"-paketet är bara en av de många DNS-cacheminnen som är tillgängliga för Linux. Innan du använder den kontrollerar du dess lämplighet för dina behov och att ingen annan cache är installerad.
>
>

**Återförsök på klientsidan**

DNS är i första hand ett UDP-protokoll. Eftersom UDP-protokollet inte garanterar meddelandeleverans hanterar SJÄLVA DNS-protokollet logiken för återförsök. Varje DNS-klient (operativsystem) kan uppvisa olika logik för återförsök beroende på skaparens önskemål:

* Windows-operativsystem försöker igen efter en sekund och sedan igen efter ytterligare två, fyra och ytterligare fyra sekunder.
* Standardinställningarna för Linux-installationen försöker igen efter fem sekunder.  Du bör ändra detta för att försöka igen fem gånger med en sekunds intervall.  

För att kontrollera de aktuella inställningarna på en Virtuell Linux-dator, "cat /etc/resolv.conf", och titta på "alternativ" linje, till exempel:

    options timeout:1 attempts:5

Filen resolv.conf genereras automatiskt och bör inte redigeras. De specifika stegen som lägger till raden "alternativ" varierar beroende på distribution:

**Ubuntu** (använder resolvconf)
1. Lägg till alternativraden i '/etc/resolveconf/resolv.conf.d/head'.
2. Kör resolvconf -u för att uppdatera.

**SUSE** (använder netconf)
1. Lägg till "timeout:1 attempts:5" i parametern NETCONFIG_DNS_RESOLVER_OPTIONS="" i '/etc/sysconfig/network/config'.
2. Kör "netconfig update" för att uppdatera.

**CentOS av Rogue Wave Software (tidigare OpenLogic)** (använder NetworkManager)
1. Lägg till timeout för RES_OPTIONS=":1:5"' i '/etc/sysconfig/network'.
2. Kör "omstart av tjänstnätverket" för att uppdatera.

## <a name="name-resolution-using-your-own-dns-server"></a>Namnmatchning med din egen DNS-server
Dina namnmatchningsbehov kan gå utöver de funktioner som Azure tillhandahåller. Du kan till exempel kräva DNS-lösning mellan virtuella nätverk. För att täcka det här scenariot kan du använda dina egna DNS-servrar.  

DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till rekursiva resolvers av Azure för att lösa värdnamn som finns i samma virtuella nätverk. En DNS-server som körs i Azure kan till exempel svara på DNS-frågor för sina egna DNS-zonfiler och vidarebefordra alla andra frågor till Azure. Med den här funktionen kan virtuella datorer se både dina poster i dina zonfiler och värdnamn som Azure tillhandahåller (via vidarebefordraren). Åtkomst till de rekursiva resolversna i Azure tillhandahålls via den virtuella IP 168.63.129.16.

DNS-vidarebefordran möjliggör också DNS-lösning mellan virtuella nätverk och gör det möjligt för lokala datorer att matcha värdnamn som Azure tillhandahåller. För att matcha en virtuell dators värdnamn måste den virtuella DNS-servern finnas i samma virtuella nätverk och konfigureras för att vidarebefordra värdnamnsfrågor till Azure. Eftersom DNS-suffixet är olika i varje virtuellt nätverk kan du använda regler för villkorsstyrd vidarebefordran för att skicka DNS-frågor till rätt virtuellt nätverk för lösning. Följande bild visar två virtuella nätverk och ett lokalt nätverk som gör DNS-upplösning mellan virtuella nätverk med den här metoden:

![DNS-lösning mellan virtuella nätverk](./media/azure-dns/inter-vnet-dns.png)

När du använder namnmatchning som Azure tillhandahåller tillhandahålls det interna DNS-suffixet till varje virtuell dator med hjälp av DHCP. När du använder din egen lösning för namnmatchning levereras inte det här suffixet till virtuella datorer eftersom suffixet stör andra DNS-arkitekturer. Om du vill referera till datorer med FQDN eller konfigurera suffixet på dina virtuella datorer kan du använda PowerShell eller API:et för att fastställa suffixet:

* För virtuella nätverk som hanteras av Azure Resource Manager är suffixet tillgängligt via [nätverkskortresursen.](https://msdn.microsoft.com/library/azure/mt163668.aspx) Du kan också `azure network public-ip show <resource group> <pip name>` köra kommandot för att visa information om din offentliga IP, som innehåller FQDN för nätverkskortet.

Om vidarebefordran av frågor till Azure inte passar dina behov måste du tillhandahålla din egen DNS-lösning.  Dns-lösningen måste:

* Ange lämplig värdnamnsmatchning, till exempel via [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Om du använder DDNS kan du behöva inaktivera DNS-post rensning. DHCP-lån av Azure är mycket långa och rensning kan ta bort DNS-poster i förtid.
* Ange lämplig rekursiv lösning för att tillåta lösning av externa domännamn.
* Var tillgänglig (TCP och UDP på port 53) från de klienter den betjänar och kunna komma åt Internet.
* Skyddas mot åtkomst från Internet för att minska hoten från externa agenter.

> [!NOTE]
> För bästa prestanda, när du använder virtuella datorer i Azure [DNS-servrar,](../../virtual-network/virtual-networks-instance-level-public-ip.md) inaktivera IPv6 och tilldela en offentlig IP på instansnivå till varje virtuell DNS-serverdator.  
>
>
