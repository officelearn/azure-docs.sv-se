---
title: Alternativ för DNS-namnmatchning för virtuella Linux-datorer i Azure
description: Namnet scenarier för Linux-datorer i Azure IaaS, inklusive tillhandahålls DNS-tjänst, hybrid externa DNS- och ta med din egen DNS-server.
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
ms.openlocfilehash: ae8315b2a484cddc500b5c2dd02a019cb4f46d8e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127097"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Alternativ för DNS-namnmatchning för virtuella Linux-datorer i Azure
Azure tillhandahåller DNS-namnmatchning som standard för alla virtuella datorer som finns i ett enda virtuellt nätverk. Du kan implementera en egen DNS-namnet lösning lösning genom att konfigurera dina egna DNS-tjänster på dina virtuella datorer som är värd för Azure. Följande scenarier bör hjälpa dig att välja det alternativ som passar din situation.

* [Namnmatchning som Azure tillhandahåller](#name-resolution-that-azure-provides)
* [Namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server)

Typ av namnmatchning som du använder beror på hur dina virtuella datorer och rollinstanser måste kommunicera med varandra.

I följande tabell visar scenarier och motsvarande name resolution lösningar:

| **Scenario** | **Lösning** | **Suffix** |
| --- | --- | --- |
| Namnmatchningen mellan rollinstanser och virtuella datorer i samma virtuella nätverk |Namnmatchning som Azure tillhandahåller |värdnamn eller fullständigt kvalificerade domännamnet (FQDN) |
| Namnmatchning mellan rollinstanser eller virtuella datorer i olika virtuella nätverk |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Matcha namn från rollinstanser eller virtuella datorer i Azure och lokala datorer |Kundhanterad DNS-servrar (till exempel en lokal domänkontrollant, lokala skrivskyddade domänkontrollanten eller en sekundär DNS har synkroniserats med hjälp av zonöverföringar). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Lösning av Azure värdnamn från lokala datorer |Vidarebefordra frågor till en kundhanterad DNS-proxyserver i motsvarande virtuella nätverk. Proxyservern vidarebefordrar frågor till Azure för matchning. Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Omvänd DNS för interna IP-adresser |[Namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server) |Saknas |

## <a name="name-resolution-that-azure-provides"></a>Namnmatchning som Azure tillhandahåller
Azure tillhandahåller intern namnmatchning för virtuella datorer och rollinstanser som finns i samma virtuella nätverk tillsammans med matchning av offentliga DNS-namn. I virtuella nätverk som baseras på Azure Resource Manager, är DNS-suffixet konsekvent på det virtuella nätverket. Det fullständiga Domännamnet behövs inte. DNS-namn kan tilldelas virtuella datorer och nätverkskort (NIC). Även om namnmatchningen som Azure tillhandahåller inte kräver någon konfiguration, är det inte valet för alla distributionsscenarier, som visas i tabellen ovan.

### <a name="features-and-considerations"></a>Funktioner och överväganden
**Funktioner:**

* Ingen konfiguration krävs för att använda namnmatchning som Azure tillhandahåller.
* Den namnmatchningstjänst som Azure tillhandahåller har hög tillgänglighet. Du behöver inte skapa och hantera kluster för din egen DNS-servrar.
* Namnmatchningstjänst som Azure tillhandahåller kan användas tillsammans med dina egna DNS-servrar för att lösa både lokala och Azure värdnamn.
* Namnmatchning tillhandahålls mellan virtuella datorer i virtuella nätverk utan behov av det fullständiga Domännamnet.
* Du kan använda värdnamn som bäst beskriver dina distributioner i stället för att arbeta med autogenererade namn.

**Att tänka på:**

* DNS-suffix som skapar Azure kan inte ändras.
* Du kan inte registrera dina egna poster manuellt.
* WINS- och NetBIOS stöds inte.
* Värdnamn måste vara DNS-kompatibla.
    Namn måste använda bara 0-9, a – z och '-', och de får inte börja eller sluta med en '-'. See RFC 3696 Section 2.
* DNS-frågorna är begränsad för varje virtuell dator. Begränsning bör inte påverka de flesta program.  Se till att cachelagring på klientsidan är aktiverad om begärandebegränsning observeras.  Mer information finns i [Stackautomatisering namnmatchning som Azure tillhandahåller](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Få ut maximalt av namnmatchning som Azure tillhandahåller
**Klientcachelagring:**

Vissa DNS-frågor skickas inte över nätverket. Klientcachelagring kan minska svarstiden och förbättra återhämtning till inkonsekvenser i nätverket genom att lösa återkommande DNS-frågor från en lokal cache. DNS-poster innehåller en Time To Live (TTL), vilket gör att cacheminnet för att lagra posten så länge som möjligt utan att påverka poster färskhet. Därför är klientcachelagring lämplig för de flesta situationer.

Vissa Linux-distributioner omfattar inte cachelagring som standard. Vi rekommenderar att du lägger till en cache till varje Linux-dator när du har kontrollerat att det inte är ett lokalt cacheminne redan.

Flera olika DNS-cachen paket, som till exempel dnsmasq, är tillgängliga. Här följer stegen för att installera dnsmasq på de vanligaste distributionerna:

**Ubuntu (använder resolvconf)**
  * Installera dnsmasq-paketet (”sudo apt-get install dnsmasq”).

**SUSE (använder netconf)**:
1. Installera dnsmasq-paketet (”sudo zypper installera dnsmasq”).
2. Aktivera tjänsten dnsmasq (”systemctl aktivera dnsmasq.service”).
3. Starta tjänsten dnsmasq (”systemctl start dnsmasq.service”).
4. Redigera ”/ etc/sysconfig/nätverk/config”, och ändra NETCONFIG_DNS_FORWARDER = ”” till ”dnsmasq”.
5. Uppdatera resolv.conf (”netconfig update”) för att ställa in cachen som den lokala DNS-matchning.

**CentOS falsk Wave Software (tidigare OpenLogic; använder NetworkManager)**
1. Installera dnsmasq-paketet (”sudo yum install dnsmasq”).
2. Aktivera tjänsten dnsmasq (”systemctl aktivera dnsmasq.service”).
3. Starta tjänsten dnsmasq (”systemctl start dnsmasq.service”).
4. Lägg till ”Lägg till åtkomstgruppen domän-namnservrarna 127.0.0.1”; till ”/etc/dhclient-eth0.conf”.
5. Starta om nätverkstjänsten (”tjänsten network omstart”) för att ställa in cachen som den lokala DNS-matchning

> [!NOTE]
> : Paketet 'dnsmasq' är bara en av de många DNS-cacheminnen som är tillgängliga för Linux. Innan du använder den, kontrollera dess lämplighet för dina behov och som ingen annan cachelagring är installerad.
>
>

**Klientsidan återförsök**

DNS är främst UDP-protokollet. Eftersom UDP-protokollet inte garanterar meddelandeleverans, hanterar logik för omprövning i själva DNS-protokollet. Varje DNS-klient (operativsystemet) kan ha olika omprövningslogiken beroende på Skaparens inställningar:

* Windows-operativsystem försök igen efter en sekund och sedan igen efter en annan två, fyra och ett annat fyra sekunder.
* Standard Linux installationsprogrammet återförsöken efter fem sekunder.  Du bör ändra detta att försöka fem gånger på en sekunds intervall.  

Kontrollera de aktuella inställningarna på en Linux-dator, ”cat /etc/resolv.conf' och titt på raden” alternativ ”till exempel:

    options timeout:1 attempts:5

Filen resolv.conf genereras automatiskt och bör inte redigeras. Vilka specifika åtgärder som Lägg till rad-alternativ ”varierar beroende på distribution:

**Ubuntu** (använder resolvconf)
1. Lägg till rad alternativ till ' / etc/resolveconf/resolv.conf.d/head ”.
2. Kör resolvconf -u att uppdatera.

**SUSE** (uses netconf)
1. Lägg till 'timeout:1 försök: 5' till NETCONFIG_DNS_RESOLVER_OPTIONS = ”” parametern i ”/ etc/sysconfig/nätverk/config”.
2. Kör netconfig-uppdateringen att uppdatera.

**CentOS falsk Wave Software (tidigare OpenLogic)** (använder NetworkManager)
1. Lägg till ”RES_OPTIONS =” timeout:1 försök: 5 ”” till ”/ etc/sysconfig/nätverk”.
2. Kör ”service nätverk omstart” att uppdatera.

## <a name="name-resolution-using-your-own-dns-server"></a>Namnmatchning med hjälp av DNS-servern
Din namnmatchningen kan utöver de funktioner som Azure tillhandahåller. Du kan till exempel kräva DNS-matchning mellan virtuella nätverk. Du kan använda din egen DNS-servrar för att täcka det här scenariot.  

DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till rekursiva matchare av Azure för att matcha värdnamn som finns i samma virtuella nätverk. Exempelvis kan en DNS-server som körs i Azure svara på DNS-frågor för en egen DNS-zonfiler och vidarebefordra alla frågor till Azure. Den här funktionen gör det möjligt för virtuella datorer för att se båda dina poster i zonen filer och värdnamn som Azure tillhandahåller (via vidarebefordrare). Åtkomst till de rekursiva matchare Azure tillhandahålls via den virtuella IP-Adressen 168.63.129.16.

Vidarebefordran av DNS även aktiverar DNS-matchning mellan virtuella nätverk och gör att dina lokala datorer för att matcha värdnamn som Azure tillhandahåller. För att lösa värdnamnet för en virtuell dator kan DNS-server-datorn måste finnas i samma virtuella nätverk och konfigureras för att vidarebefordra värdnamn frågor till Azure. Eftersom DNS-suffix är olika i varje virtuellt nätverk kan använda du villkorlig vidarebefordringsregler för att skicka DNS-frågor till rätt virtuellt nätverk för matchning. Följande bild visar två virtuella nätverk och ett lokalt nätverk som gör DNS-matchning mellan virtuella nätverk med hjälp av den här metoden:

![DNS-matchning mellan virtuella nätverk](./media/azure-dns/inter-vnet-dns.png)

När du använder namnmatchning som Azure tillhandahåller tillhandahålls interna DNS-suffixet för varje virtuell dator med hjälp av DHCP. När du använder en egen lösning för name resolution anges suffixet till virtuella datorer eftersom suffixet stör andra DNS-arkitekturer. Att referera till datorer efter FQDN eller för att konfigurera suffixet på dina virtuella datorer, kan du använda PowerShell eller API: et för att fastställa suffixet:

* För virtuella nätverk som hanteras av Azure Resource Manager suffixet är tillgängligt via den [nätverkskort](https://msdn.microsoft.com/library/azure/mt163668.aspx) resurs. Du kan också köra den `azure network public-ip show <resource group> <pip name>` kommando för att visa information om din offentliga IP-adress som innehåller det fullständiga Domännamnet för nätverkskortet.

Om vidarebefordran av frågor till Azure inte passar dina behov, måste du ange en egen DNS-lösning.  Din DNS-lösningen behöver:

* Ange lämpliga värdnamnsmatchning, till exempel [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Om du använder DDNS, kan du behöva inaktivera DNS-post rensning. DHCP-lån för Azure är mycket långa och rensning kan ta bort DNS-poster för tidigt.
* Ange lämpliga rekursiv matchning för att tillåta matchning av extern domännamn.
* Vara tillgänglig (TCP och UDP på port 53) från klienterna den fungerar och att kunna få åtkomst till Internet.
* Skyddas mot åtkomst från Internet för att identifiera hot som externa agenter.

> [!NOTE]
> För bästa prestanda när du använder virtuella datorer i Azure DNS-servrar, inaktivera IPv6 och tilldela en [offentlig IP på instansnivå](../../virtual-network/virtual-networks-instance-level-public-ip.md) varje DNS-server-dator.  
>
>
