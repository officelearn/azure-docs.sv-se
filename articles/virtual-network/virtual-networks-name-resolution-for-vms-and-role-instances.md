---
title: "Lösning för virtuella datorer och Rollinstanser"
description: "Namn på lösning scenarier för Azure IaaS hybridlösningar mellan olika cloud services, Active Directory och använder egna DNS-server "
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 5d73edde-979a-470a-b28c-e103fcf07e3e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2016
ms.author: jdial
ms.openlocfilehash: 5a298f535308cff90ddd249594b7bb5e36909867
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="name-resolution-for-vms-and-role-instances"></a>Name Resolution for VMs and Role Instances (Namnmatchning för virtuella datorer och rollinstanser)
Beroende på hur du använder Azure som värd för IaaS och PaaS hybridlösningar, kan du behöva tillåta virtuella datorer och rollinstanser som du skapar för att kommunicera med varandra. Även om den här kommunikationen kan göras med hjälp av IP-adresser, är det mycket enklare att använda namn som enkelt kan registreras och ändras inte. 

När rollinstanser och virtuella datorer finns i Azure måste matcha domännamn och interna IP-adresser, kan de använda ett av två sätt:

* [Azure-tillhandahållna namnmatchning](#azure-provided-name-resolution)
* [Namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server) (som kan vidarebefordra frågor till Azure-tillhandahållna DNS-servrar) 

Typ av namnmatchning som du använder beror på hur dina virtuella datorer och rollinstanser måste kommunicera med varandra.

**Följande tabell visar scenarier och motsvarande name resolution lösningar:**

| **Scenario** | **Lösning** | **Suffix** |
| --- | --- | --- |
| Namnmatchning mellan rollinstanser eller virtuella datorer finns i samma molntjänst eller virtuellt nätverk |[Azure-tillhandahållna namnmatchning](#azure-provided-name-resolution) |värdnamn eller fullständigt domännamn |
| Namnmatchning från ett Azure App Service (Web App, funktion, Bot osv.) med hjälp av VNET-integrering med rollinstanser eller virtuella datorer finns i samma virtuella nätverk |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan vnet för matchning av Azure (DNS-proxy).  Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server) |Endast FQDN |
| Namnmatchning mellan rollinstanser eller virtuella datorer finns i olika virtuella nätverk |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan vnet för matchning av Azure (DNS-proxy).  Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server) |Endast FQDN |
| Matchning av lokal dator- och namn från rollinstanser eller virtuella datorer i Azure |Kundhanterad DNS-servrar (t.ex. lokala domänkontrollant, lokala skrivskyddade domänkontrollanten eller en sekundär DNS synkroniseras med zonöverföringar).  Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server) |Endast FQDN |
| Lösning med Azure värdnamn från lokala datorer |Vidarebefordra frågor till en kundhanterad DNS-proxyserver i motsvarande vnet proxyserverns vidarebefordrar frågor till Azure för matchning. Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server) |Endast FQDN |
| Omvänd DNS för interna IP-adresser |[Namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server) |Saknas |
| Namnmatchning mellan VM: ar eller rollinstanser som finns i olika molntjänster, inte i ett virtuellt nätverk |Inte tillämpligt. Anslutningen mellan virtuella datorer och rollinstanser i olika molntjänster kan inte användas utanför ett virtuellt nätverk. |Saknas |

## <a name="azure-provided-name-resolution"></a>Azure-tillhandahållna namnmatchning
Tillsammans med upplösning på offentliga DNS-namn erbjuder Azure intern namnmatchning för virtuella datorer och rollinstanser som finns i samma virtuella nätverk eller tjänst i molnet.  Virtuella datorer/instanser i en molntjänst delar samma DNS-suffixet (så att enbart värdnamnet räcker) men i annat moln för klassiska virtuella nätverk tjänster har olika DNS-suffix så FQDN krävs för att matcha namn mellan olika molntjänster.  DNS-suffix är konsekvent på det virtuella nätverket (så att FQDN inte behövs) och DNS-namn kan tilldelas till både nätverkskort och virtuella datorer i virtuella nätverk i Resource Manager-distributionsmodellen. Även om Azure-tillhandahållna namnmatchning inte kräver någon konfiguration, är det inte det bästa valet för alla distributionsscenarier, som visas i föregående tabell.

> [!NOTE]
> Du kan också använda interna IP-adresserna för rollinstanser utifrån rollen namnet och instansen med hjälp av Azure Service Management REST API för webb- och arbetsroller roller. Mer information finns i [Service Management REST API-referens](https://msdn.microsoft.com/library/azure/ee460799.aspx).
> 
> 

### <a name="features-and-considerations"></a>Funktioner och överväganden
**Funktioner:**

* Användarvänlighet: ingen konfiguration krävs för att kunna använda Azure-tillhandahållna namnmatchning.
* Azure-tillhandahållna namnmatchningstjänst har hög tillgänglighet, vilket sparar dig behovet av att skapa och hantera kluster DNS-servrar.
* Kan användas tillsammans med DNS-servrar för att matcha både lokalt och Azure värdnamn.
* Namnmatchning tillhandahålls mellan rollen instanser/virtuella datorer i samma molntjänst utan behov av ett FQDN.
* Namnmatchning tillhandahålls mellan virtuella datorer i virtuella nätverk som använder Resource Manager distributionsmodellen, utan behov av FQDN. Virtuella nätverk i den klassiska distributionsmodellen kräver FQDN vid namnmatchning i olika molntjänster. 
* Du kan använda värdnamn som bäst beskriver dina distributioner i stället för att arbeta med automatiskt genererat namn.

**Att tänka på:**

* Skapa Azure DNS-suffix kan inte ändras.
* Du kan registrera dina egna poster manuellt.
* WINS och NetBIOS stöds inte. (Du kan inte se dina virtuella datorer i Utforskaren i Windows.)
* Värdnamn måste vara DNS-kompatibel (måste de använda endast 0-9, a – z och '-', och får inte inledas eller avslutas med en '-'. Avsnittet RFC 3696 2.)
* DNS-frågorna begränsas för varje virtuell dator. Detta bör inte påverka de flesta program.  Om begäran begränsning observeras, kontrollerar du att cachelagring på klientsidan är aktiverad.  Mer information finns i [få ut mesta möjliga av Azure-tillhandahållna namnmatchning](#Getting-the-most-from-Azure-provided-name-resolution).
* Endast virtuella datorer i de första 180 molntjänsterna har registrerats för varje virtuellt nätverk i en klassiska distributionsmodellen. Detta gäller inte för virtuella nätverk i Resource Manager distributionsmodellerna.

### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Få ut mesta möjliga av Azure-tillhandahållna namnmatchning
**Klientcachelagring:**

Inte alla DNS-fråga måste skickas över nätverket.  Klientcachelagring hjälper minskar svarstider och förbättrar återhämtning i nätverket signaler genom att lösa återkommande DNS-frågor från en lokal cache.  DNS-poster innehåller en Time-To-Live (TTL) som gör att cacheminnet för att lagra posten så länge som möjligt utan att påverka poster dokumentens så klientcachelagring lämpar sig för de flesta situationer.

Standard Windows DNS-klienten har en inbyggd DNS-cache.  Vissa Linux distributioner som inte inkluderar cachelagring som standard, rekommenderas att en läggs till varje Linux VM (när du har kontrollerat att det inte är ett lokalt cacheminne redan).

Det finns ett antal olika DNS-cache paket som är tillgängliga. Till exempel dnsmasq. Följande steg visar hur du installerar dnsmasq på de vanligaste distributioner:

* **Ubuntu (använder resolvconf)**:
  * installerar du bara dnsmasq paketet (”sudo lgh get installera dnsmasq”).
* **SUSE (använder netconf)**:
  * installera dnsmasq paketet (”sudo zypper installera dnsmasq”) 
  * Aktivera tjänsten dnsmasq (”systemctl aktivera dnsmasq.service”) 
  * Starta tjänsten dnsmasq (”systemctl start dnsmasq.service”) 
  * Redigera ”/ etc/sysconfig/nätverk/config” och ändra NETCONFIG_DNS_FORWARDER = ”” till ”dnsmasq”
  * Uppdatera resolv.conf (”netconfig uppdatering”) för att ställa in cachen som den lokala DNS-matchning
* **OpenLogic (uses NetworkManager)**:
  * installera dnsmasq paketet (”sudo yum installera dnsmasq”)
  * Aktivera tjänsten dnsmasq (”systemctl aktivera dnsmasq.service”)
  * Starta tjänsten dnsmasq (”systemctl start dnsmasq.service”)
  * Lägg till ”lägga domän-namnservrar 127.0.0.1”; till ”/etc/dhclient-eth0.conf”
  * Starta om nätverkstjänsten (”tjänsten network omstart”) för att ställa in cachen som den lokala DNS-matchning

> [!NOTE]
> Paketet 'dnsmasq' är endast ett av de många DNS-cacheminnet för Linux.  Innan du använder den, kontrollera dess lämplighet för dina specifika behov och att ingen annan cache är installerad.
> 
> 

**Klientsidans återförsök:**

DNS är främst ett UDP-protokoll.  Eftersom UDP-protokollet inte garantera meddelandeleverans, hanteras logik i själva DNS-protokollet.  Varje DNS-klient (operativsystemet) kan ha olika logik beroende på skapare-inställningar:

* Windows-operativsystem försök igen efter 1 sekund och sedan igen efter en annan 2, 4 och en annan 4 sekunder. 
* Standard Linux installationsprogrammet återförsöken efter fem sekunder.  Det rekommenderas att ändra den här att försöka 5 gånger på 1 sekunders intervall.  

Använd 'cat /etc/resolv.conf'-kommando för att kontrollera de aktuella inställningarna på en Linux-VM och titta på raden ”alternativ”, till exempel:

    options timeout:1 attempts:5

Filen resolv.conf är vanligtvis automatiskt genererade och bör inte redigeras.  De specifika stegen för att lägga till raden ”alternativ” varierar beroende på distro:

* **Ubuntu** (använder resolvconf):
  * Lägg till rad alternativ för ' / etc/resolveconf/resolv.conf.d/head' 
  * Kör resolvconf -u att uppdatera
* **SUSE** (använder netconf):
  * Lägg till 'timeout:1 försök: 5' till NETCONFIG_DNS_RESOLVER_OPTIONS = ”” parametern i ”/ etc/sysconfig/nätverk/config” 
  * Kör netconfig-uppdateringen att uppdatera
* **OpenLogic** (uses NetworkManager):
  * lägga till 'echo ”alternativ timeout:1 försök: 5”' ' / etc/NetworkManager/dispatcher.d/11-dhclient' 
  * Kör 'tjänsten network omstart' att uppdatera

## <a name="name-resolution-using-your-own-dns-server"></a>Namnmatchning med hjälp av DNS-servern
Det finns ett antal olika situationer där din namnmatchningen kan gå utöver funktionerna som tillhandahålls av Azure, till exempel när via Active Directory-domäner eller när du behöver DNS-matchning mellan virtuella nätverk.  Azure tillhandahåller möjligheten att använda DNS-servrarna så att den täcker dessa scenarier.  

DNS-servrar inom ett virtuellt nätverk kan vidarebefordra DNS-frågor till Azures rekursiv matchare att matcha värdnamn i det virtuella nätverket.  En domänkontrollanten (DC) körs i Azure kan svara på DNS-frågor för dess domäner och vidarebefordra alla frågor till Azure.  Detta gör att virtuella datorer att se både lokala resurser (via DC) och Azure-tillhandahållna värdnamn (via vidarebefordrare).  Åtkomst till Azures rekursiv matchare anges via den virtuella IP-Adressen 168.63.129.16.

DNS-vidarebefordran även gör DNS-matchning mellan virtuella nätverk och att dina lokala datorer att matcha värdnamn för Azure-tillhandahållna.  För att matcha värdnamnet för en virtuell dator, DNS-server VM måste finnas i samma virtuella nätverk och konfigureras för att vidarebefordra hostname frågor till Azure.  Som DNS-suffix skiljer sig åt i varje virtuellt nätverk, kan du använda villkorlig vidarebefordringsregler för att skicka DNS-frågor till rätt vnet för matchning.  Följande bild visar två virtuella nätverk och ett lokalt nätverk gör mellan virtuella nätverk DNS-matchning med den här metoden.  Ett exempel DNS-vidarebefordrare som är tillgängliga i den [Azure Quickstart mallgalleriet](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) och [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![Mellan virtuella nätverk DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

När du använder Azure-tillhandahållna namnmatchning, en intern DNS-suffix (*. internal.cloudapp.net) har angetts för varje virtuell dator med hjälp av DHCP.  Detta gör värdnamnsmatchning hostname posterna visas i zonen internal.cloudapp.net.  När du använder din egen lösning för name resolution anges IDN-suffix till virtuella datorer eftersom den stör andra DNS-arkitekturer (t.ex. domänanslutna scenarier).  Vi ger i stället en icke-fungerande platshållare (reddog.microsoft.com).  

Om det behövs, kan interna DNS-suffix fastställas med hjälp av PowerShell eller API:

* För virtuella nätverk i Resource Manager distributionsmodellerna suffixet är tillgängliga via den [nätverkskort](https://msdn.microsoft.com/library/azure/mt163668.aspx) resurs eller via den [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) cmdlet.    
* I klassiska distributionsmodeller suffixet är tillgängliga via den [hämta distribution API](https://msdn.microsoft.com/library/azure/ee460804.aspx) anropa eller via den [Get-AzureVM-Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx) cmdlet.

Om vidarebefordran av frågor till Azure inte passar dina behov, behöver du ange DNS-lösningen.  DNS-lösningen behöver du:

* Ange lämpliga värdnamnsmatchning, t.ex. [DDNS](virtual-networks-name-resolution-ddns.md).  Tänk på att om du använder DDNS som du kan behöva inaktivera DNS-posten rensning som Azures DHCP-lån är mycket lång och rensning kan ta bort DNS-poster för tidigt. 
* Ange lämpliga rekursiv matchning så att matchning av extern domännamn.
* Vara tillgänglig (TCP och UDP-port 53) från klienterna den fungerar och att kunna få åtkomst till internet.
* Skyddas mot åtkomst från internet, till att minska hot med externa agenter.

> [!NOTE]
> För bästa prestanda när du använder Azure virtuella datorer som DNS-servrar, IPv6 bör inaktiveras och en [offentlig IP på instansnivå](virtual-networks-instance-level-public-ip.md) ska tilldelas varje virtuell dator för DNS-server.  Om du väljer att använda Windows Server som DNS-servern, [i den här artikeln](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) ger ytterligare prestandaanalys och optimeringar.
> 
> 

### <a name="specifying-dns-servers"></a>Ange DNS-servrar
När du använder DNS-servrar, tillhandahåller Azure möjligheten att ange flera DNS-servrar per virtuellt nätverk eller per gränssnitt (Resource Manager) / molntjänst (klassisk).  DNS-servrar som angetts för ett moln service/nätverksgränssnitt får företräde över dem som anges för det virtuella nätverket.

> [!NOTE]
> Nätverk anslutningsegenskaper, t.ex. DNS-server IP-adresser, inte bör redigeras direkt i virtuella Windows-datorer som de kan hämta raderas under läka när det virtuella nätverkskortet ersätts. 
> 
> 

När du använder Resource Manager-distributionsmodellen, DNS-servrar kan anges i portalen API-mallar ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) eller PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [ NIC](https://msdn.microsoft.com/library/mt619370.aspx)).

När du använder den klassiska distributionsmodellen, DNS-servrar för det virtuella nätverket kan anges i portalen eller [den *nätverkskonfigurationen* filen](https://msdn.microsoft.com/library/azure/jj157100).  För molntjänster, DNS-servrar har angetts [den *tjänstkonfiguration* filen](https://msdn.microsoft.com/library/azure/ee758710) eller i PowerShell ([ny AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [!NOTE]
> Om du ändrar DNS-inställningarna för en virtuell nätverk/virtuell dator som redan har distribuerats, måste du starta om varje berörda VM för att ändringarna ska börja gälla.
> 
> 

## <a name="next-steps"></a>Nästa steg
Resource Manager-modellen:

* [Skapa eller uppdatera ett virtuellt nätverk](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Skapa eller uppdatera ett nätverkskort](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
* [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

Klassiska distributionsmodellen:

* [Azure Service Configuration Schema](https://msdn.microsoft.com/library/azure/ee758710)
* [Konfigurationsschemat för virtuellt nätverk](https://msdn.microsoft.com/library/azure/jj157100)
* [Konfigurera ett virtuellt nätverk med hjälp av en konfigurationsfil för nätverk](virtual-networks-using-network-configuration-file.md) 

