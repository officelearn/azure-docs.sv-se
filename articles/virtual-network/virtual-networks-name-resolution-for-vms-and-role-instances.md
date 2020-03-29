---
title: Namnmatchning för resurser i virtuella nätverk i Azure
titlesuffix: Azure Virtual Network
description: Namnmatchningsscenarier för Azure IaaS, hybridlösningar, mellan olika molntjänster, Active Directory och använda din egen DNS-server.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.openlocfilehash: 20a5c4befaa30383c54ac9536a3fd26dce3db4d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059975"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Namnmatchning för resurser i virtuella nätverk i Azure

Beroende på hur du använder Azure som värd för IaaS-, PaaS- och hybridlösningar kan du behöva tillåta virtuella datorer och andra resurser som distribueras i ett virtuellt nätverk att kommunicera med varandra. Även om du kan aktivera kommunikation med hjälp av IP-adresser är det mycket enklare att använda namn som lätt kan kommas ihåg och inte ändras. 

När resurser som distribueras i virtuella nätverk behöver matcha domännamn till interna IP-adresser kan de använda en av tre metoder:

* [Privata Azure DNS-zoner](../dns/private-dns-overview.md)
* [Namnmatchning av Azure-förutsatt](#azure-provided-name-resolution)
* [Namnmatchning som använder din egen DNS-server](#name-resolution-that-uses-your-own-dns-server) (som kan vidarebefordra frågor till DNS-servrar som tillhandahålls av Azure)

Vilken typ av namnmatchning du använder beror på hur dina resurser behöver kommunicera med varandra. I följande tabell visas scenarier och motsvarande lösningar för namnmatchning:

> [!NOTE]
> Privata Azure DNS-zoner är den lösning som föredras och ger dig flexibilitet i hanteringen av dina DNS-zoner och -poster. Mer information finns på sidan om att [använda Azure DNS för privata domäner](../dns/private-dns-overview.md).

> [!NOTE]
> Om du använder Azure Provided DNS tillämpas lämpliga DNS-suffix automatiskt på dina virtuella datorer. För alla andra alternativ måste du antingen använda FQDN (Full Qualified Domain Names) eller manuellt tillämpa lämpliga DNS-suffix på dina virtuella datorer.

| **Scenario** | **Lösning** | **DNS-suffix** |
| --- | --- | --- |
| Namnmatchning mellan virtuella datorer som finns i samma virtuella nätverk eller Azure Cloud Services-rollinstanser i samma molntjänst. | [Azure DNS privata zoner](../dns/private-dns-overview.md) eller [Azure-medföljande namnmatchning](#azure-provided-name-resolution) |Värdnamn eller FQDN |
| Namnmatchning mellan virtuella datorer i olika virtuella nätverk eller rollinstanser i olika molntjänster. |[Azure DNS privata zoner](../dns/private-dns-overview.md) eller kundhanterade DNS-servrar vidarebefordra frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [Namnmatchning med din egen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namnmatchning från en Azure App Service (Web App, Funktion eller Bot) med hjälp av integrering av virtuellt nätverk till rollinstanser eller virtuella datorer i samma virtuella nätverk. |Kundhanterade DNS-servrar vidarebefordra frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [Namnmatchning med din egen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namnmatchning från App Service Web Apps till virtuella datorer i samma virtuella nätverk. |Kundhanterade DNS-servrar vidarebefordra frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [Namnmatchning med din egen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namnmatchning från App Service Web Apps i ett virtuellt nätverk till virtuella datorer i ett annat virtuellt nätverk. |Kundhanterade DNS-servrar vidarebefordra frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [Namnmatchning med din egen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Lösning av lokala dator- och tjänstnamn från virtuella datorer eller rollinstanser i Azure. |Kundhanterade DNS-servrar (lokal domänkontrollant, lokal skrivskyddad domänkontrollant eller en sekundär DNS-synkronisering med hjälp av zonöverföringar, till exempel). Se [Namnmatchning med din egen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Lösning av Azure-värdnamn från lokala datorer. |Vidarebefordra frågor till en kundhanterad DNS-proxyserver i motsvarande virtuella nätverk, proxyservern vidarebefordrar frågor till Azure för lösning. Se [Namnmatchning med din egen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Omvänd DNS för interna IPs. |[Azure DNS privata zoner](../dns/private-dns-overview.md) eller [Azure-angivna namnmatchning](#azure-provided-name-resolution) eller [Namnmatchning med din egen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Inte tillämpligt |
| Namnmatchning mellan virtuella datorer eller rollinstanser som finns i olika molntjänster, inte i ett virtuellt nätverk. |Inte tillämpligt. Anslutning mellan virtuella datorer och rollinstanser i olika molntjänster stöds inte utanför ett virtuellt nätverk. |Inte tillämpligt|

## <a name="azure-provided-name-resolution"></a>Namnmatchning av Azure-förutsatt

Azure-förutsatt namnmatchning ger endast grundläggande auktoritära DNS-funktioner. Om du använder det här alternativet hanteras DNS-zonnamn och -poster automatiskt av Azure och du kan inte styra DNS-zonnamnen eller livscykeln för DNS-poster. Om du behöver en fullfjädrad DNS-lösning för dina virtuella nätverk måste du använda [Azure DNS-privata zoner](../dns/private-dns-overview.md) eller [kundhanterade DNS-servrar](#name-resolution-that-uses-your-own-dns-server).

Tillsammans med matchning av offentliga DNS-namn tillhandahåller Azure intern namnmatchning för virtuella datorer och rollinstanser som finns inom samma virtuella nätverk eller molntjänst. Virtuella datorer och instanser i en molntjänst delar samma DNS-suffix, så själva värdnamnet är tillräckligt. Men i virtuella nätverk som distribueras med den klassiska distributionsmodellen har olika molntjänster olika DNS-suffix. I det här fallet behöver du FQDN för att matcha namn mellan olika molntjänster. I virtuella nätverk som distribueras med hjälp av distributionsmodellen för Azure Resource Manager är DNS-suffixet konsekvent över alla virtuella datorer i ett virtuellt nätverk, så FQDN behövs inte. DNS-namn kan tilldelas både virtuella datorer och nätverksgränssnitt. Även om namnmatchning som tillhandahålls av Azure inte kräver någon konfiguration, är det inte rätt val för alla distributionsscenarier, som beskrivs i den tidigare tabellen.

> [!NOTE]
> När du använder webb- och arbetsroller för molntjänster kan du även komma åt interna IP-adresser för rollinstanser med HJÄLP AV REST-API:ET för Azure Service Management. Mer information finns i [REST API-referensen för SERVICEHantering.](https://msdn.microsoft.com/library/azure/ee460799.aspx) Adressen baseras på rollnamnet och förekomstnumret. 
>

### <a name="features"></a>Funktioner

Namnmatchning som tillhandahålls av Azure innehåller följande funktioner:
* Användarvänlighet. Ingen konfiguration krävs.
* Hög tillgänglighet. Du behöver inte skapa och hantera kluster av dina egna DNS-servrar.
* Du kan använda tjänsten tillsammans med dina egna DNS-servrar för att matcha både lokala och Azure-värdnamn.
* Du kan använda namnmatchning mellan virtuella datorer och rollinstanser inom samma molntjänst, utan att det behövs ett FQDN.
* Du kan använda namnmatchning mellan virtuella datorer i virtuella nätverk som använder Distributionsmodellen för Azure Resource Manager, utan att behöva en FQDN. Virtuella nätverk i den klassiska distributionsmodellen kräver en FQDN när du löser namn i olika molntjänster. 
* Du kan använda värdnamn som bäst beskriver dina distributioner i stället för att arbeta med automatiskt genererade namn.

### <a name="considerations"></a>Överväganden

Pekar på när du använder namnmatchning som tillhandahålls av Azure:
* Det går inte att ändra det Azure-skapade DNS-suffixet.
* DNS-sökningen är begränsad till ett virtuellt nätverk. DNS-namn som skapats för ett virtuellt nätverk kan inte matchas från andra virtuella nätverk.
* Du kan inte registrera dina egna poster manuellt.
* WINS och NetBIOS stöds inte. Du kan inte se dina virtuella datorer i Utforskaren.
* Värdnamn måste vara DNS-kompatibla. Namnen får endast använda 0-9, a-z och "-", och kan inte börja eller sluta med en "-".
* DNS-frågetrafik begränsas för varje virtuell dator. Begränsning bör inte påverka de flesta program. Om begäran begränsning observeras, se till att klientsidan cachelagring är aktiverad. Mer information finns i [DNS-klientkonfiguration](#dns-client-configuration).
* Endast virtuella datorer i de första 180 molntjänsterna registreras för varje virtuellt nätverk i en klassisk distributionsmodell. Den här gränsen gäller inte för virtuella nätverk i Azure Resource Manager.
* Azure DNS IP-adressen är 168.63.129.16. Detta är en statisk IP-adress och kommer inte att ändras.

### <a name="reverse-dns-considerations"></a>Omvänd DNS-överväganden
Omvänd DNS stöds i alla ARM-baserade virtuella nätverk. Du kan utfärda omvända DNS-frågor (PTR-frågor) för att mappa IP-adresser för virtuella datorer till FQDN för virtuella datorer.
* Alla PTR-frågor för IP-adresser för virtuella datorer \[returnerar\]FQDN för formulär vmname .internal.cloudapp.net
* Sökning framåt på FQDN:er för vmname \[\].internal.cloudapp.net kommer att lösa till IP-adress som tilldelats den virtuella datorn.
* Om det virtuella nätverket är länkat till en [Azure DNS-privatzon](../dns/private-dns-overview.md) som ett virtuellt nätverk för registrering returnerar de omvända DNS-frågorna två poster. En post kommer att \[utgöras av formuläret vmname\]. [priatednszonename] och andra skulle \[vara\]av formuläret vmname .internal.cloudapp.net
* Omvänd DNS-sökning begränsas till ett visst virtuellt nätverk även om det är peered till andra virtuella nätverk. Omvänd DNS-frågor (PTR-frågor) för IP-adresser för virtuella datorer som finns i peered virtuella nätverk returnerar NXDOMAIN.

> [!NOTE]
> Om du vill att omvänd DNS-sökning ska sträcka sig över virtuella nätverk kan du skapa en omvänd sökningszon (i-addr.arpa) [Azure DNS-privata zoner](../dns/private-dns-overview.md) och länkar den till flera virtuella nätverk. Du måste dock manuellt hantera omvända DNS-poster för de virtuella datorerna.
>


## <a name="dns-client-configuration"></a>DNS-klientkonfiguration

Det här avsnittet omfattar cachelagring på klientsidan och återförsök på klientsidan.

### <a name="client-side-caching"></a>Cachelagring på klientsidan

Alla DNS-frågor behöver inte skickas över nätverket. Cachelagring på klientsidan bidrar till att minska svarstiden och förbättra motståndskraften mot nätverkslypsar genom att lösa återkommande DNS-frågor från en lokal cache. DNS-poster innehåller en time-to-live (TTL) mekanism, som gör att cachen för att lagra posten så länge som möjligt utan att påverka postens färskhet. Således är klientsidan caching lämplig för de flesta situationer.

Standardklienten för Windows DNS har en inbyggd DNS-cache. Vissa Linux-distributioner inkluderar inte cachelagring som standard. Om du upptäcker att det inte redan finns en lokal cache lägger du till en DNS-cache i varje Virtuell Linux-dator.

Det finns ett antal olika DNS-cachelagringspaket tillgängliga (till exempel dnsmasq). Så här installerar du dnsmasq på de vanligaste distributionerna:

* **Ubuntu (använder resolvconf):**
  * Installera dnsmasq-paketet `sudo apt-get install dnsmasq`med .
* **SUSE (använder netconf):**
  * Installera dnsmasq-paketet `sudo zypper install dnsmasq`med .
  * Aktivera dnsmasq-tjänsten `systemctl enable dnsmasq.service`med . 
  * Starta dnsmasq-tjänsten `systemctl start dnsmasq.service`med . 
  * Redigera **/etc/sysconfig/network/config**och ändra *NETCONFIG_DNS_FORWARDER=""* till *dnsmasq*.
  * Update resolv.conf `netconfig update`med , för att ställa in cacheminnet som den lokala DNS-matcharen.
* **CentOS (använder NetworkManager)**:
  * Installera dnsmasq-paketet `sudo yum install dnsmasq`med .
  * Aktivera dnsmasq-tjänsten `systemctl enable dnsmasq.service`med .
  * Starta dnsmasq-tjänsten `systemctl start dnsmasq.service`med .
  * Lägg *till prepend domännamn-servrar 127.0.0.1;* till **/etc/dhclient-eth0.conf**.
  * Starta om nätverkstjänsten med `service network restart`för att ställa in cacheminnet som lokal DNS-resolver.

> [!NOTE]
> Dnsmasq-paketet är bara en av många DNS-cacheminnen tillgängliga för Linux. Innan du använder den, kontrollera dess lämplighet för dina särskilda behov och kontrollera att ingen annan cache är installerad.

    
### <a name="client-side-retries"></a>Återförsök på klientsidan

DNS är i första hand ett UDP-protokoll. Eftersom UDP-protokollet inte garanterar meddelandeleverans hanteras logiken för återförsök i själva DNS-protokollet. Varje DNS-klient (operativsystem) kan uppvisa olika logik för återförsök, beroende på skaparens önskemål:

* Windows-operativsystem försöker igen efter en sekund och sedan igen efter ytterligare två sekunder, fyra sekunder och ytterligare fyra sekunder. 
* Standardinställningarna för Linux-installationen försöker igen efter fem sekunder. Vi rekommenderar att du ändrar specifikationerna för återförsök till fem gånger, med en sekunds intervall.

Kontrollera de aktuella inställningarna `cat /etc/resolv.conf`på en Virtuell Linux med . Titta på *options* alternativraden, till exempel:

```bash
options timeout:1 attempts:5
```

Filen resolv.conf genereras vanligtvis automatiskt och bör inte redigeras. De specifika stegen för att lägga till *alternativraden* varierar beroende på distribution:

* **Ubuntu** (använder resolvconf):
  1. Lägg till *alternativraden* i **/etc/resolvconf/resolv.conf.d/tail**.
  2. Kör `resolvconf -u` för att uppdatera.
* **SUSE** (använder netconf):
  1. Lägg till *timeout:1-försök:5* i parametern **NETCONFIG_DNS_RESOLVER_OPTIONS=""** i **/etc/sysconfig/network/config**.
  2. Kör `netconfig update` för att uppdatera.
* **CentOS** (använder NetworkManager):
  1. Lägg till *eko "alternativ timeout:1 försök:5"* till **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Uppdatera `service network restart`med .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Namnmatchning som använder din egen DNS-server

Det här avsnittet omfattar virtuella datorer, rollinstanser och webbappar.

### <a name="vms-and-role-instances"></a>Virtuella datorer och rollinstanser

Dina namnmatchningsbehov kan gå utöver de funktioner som tillhandahålls av Azure. Du kan till exempel behöva använda Active Directory-domäner i Microsoft Windows Server, matcha DNS-namn mellan virtuella nätverk. För att täcka dessa scenarier ger Azure dig möjlighet att använda dina egna DNS-servrar.

DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till rekursiva resolvers i Azure. På så sätt kan du matcha värdnamn i det virtuella nätverket. En domänkontrollant (DC) som körs i Azure kan till exempel svara på DNS-frågor för sina domäner och vidarebefordra alla andra frågor till Azure. Vidarebefordringsfrågor gör det möjligt för virtuella datorer att se både dina lokala resurser (via domänkontrollanten) och Azure-tillförda värdnamn (via vidarebefordraren). Åtkomst till rekursiva resolvers i Azure tillhandahålls via den virtuella IP 168.63.129.16.

DNS-vidarebefordran möjliggör också DNS-lösning mellan virtuella nätverk och gör att dina lokala datorer kan lösa Azure-angivna värdnamn. För att matcha en virtuell dators värdnamn måste DEN virtuella DNS-servern finnas i samma virtuella nätverk och konfigureras för att vidarebefordra värdnamnsfrågor till Azure. Eftersom DNS-suffixet är olika i varje virtuellt nätverk kan du använda regler för villkorsstyrd vidarebefordran för att skicka DNS-frågor till rätt virtuellt nätverk för lösning. Följande bild visar två virtuella nätverk och ett lokalt nätverk som gör DNS-upplösning mellan virtuella nätverk med den här metoden. Det finns ett exempel på DNS-vidarebefordrare i [azure quickstart-mallgalleriet](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) och [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> En rollinstans kan utföra namnmatchning av virtuella datorer i samma virtuella nätverk. Det gör det med hjälp av FQDN, som består av den virtuella datorns värdnamn och **internal.cloudapp.net** DNS-suffix. I det här fallet lyckas dock namnmatchning endast om rollinstansen har vm-namnet definierat i [rollschemat (.cscfg-filen)](https://msdn.microsoft.com/library/azure/jj156212.aspx).
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Rollinstanser som behöver utföra namnmatchning av virtuella datorer i ett annat virtuellt nätverk (FQDN med hjälp av suffixet **internal.cloudapp.net)** måste göra det med hjälp av den metod som beskrivs i det här avsnittet (anpassad DNS-servrar som vidarebefordrar mellan de två virtuella nätverken).
>

![Diagram över DNS mellan virtuella nätverk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

När du använder azure-provided-namnmatchning tillhandahåller Azure Dynamic Host Configuration Protocol (DHCP) ett internt DNS-suffix (**.internal.cloudapp.net**) till varje virtuell dator. Det här suffixet aktiverar värdnamnsmatchning eftersom värdnamnsposterna finns i **zonen internal.cloudapp.net.** När du använder din egen lösning för namnmatchning levereras inte det här suffixet till virtuella datorer eftersom det stör andra DNS-arkitekturer (till exempel domänanslutna scenarier). I stället tillhandahåller Azure en icke-fungerande platshållare (*reddog.microsoft.com*).

Om det behövs kan du bestämma det interna DNS-suffixet med hjälp av PowerShell eller API:et:

* För virtuella nätverk i Azure Resource Manager-distributionsmodeller är suffixet tillgängligt via [REST API för nätverksgränssnittet,](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces) [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) PowerShell-cmdlet och [az-nätverkskortet visa](/cli/azure/network/nic#az-network-nic-show) Azure CLI-kommandot.
* I klassiska distributionsmodeller är suffixet tillgängligt via Api-anropet [Get](https://msdn.microsoft.com/library/azure/ee460804.aspx) Deployment eller [Cmdlet Get-AzureVM -Debug.](/powershell/module/servicemanagement/azure/get-azurevm)

Om vidarebefordran av frågor till Azure inte passar dina behov bör du tillhandahålla din egen DNS-lösning. Dns-lösningen måste:

* Ange lämplig värdnamnsmatchning, till exempel via [DDNS.](virtual-networks-name-resolution-ddns.md) Om du använder DDNS kan du behöva inaktivera DNS-post rensning. Azure DHCP-lån är långa och rensning kan ta bort DNS-poster i förtid. 
* Ange lämplig rekursiv lösning för att tillåta lösning av externa domännamn.
* Var tillgänglig (TCP och UDP på port 53) från de kunder den betjänar, och kunna komma åt Internet.
* Skyddas mot tillgång från Internet, för att minska hot som externa agenter utgör.

> [!NOTE]
> För bästa prestanda bör IPv6 inaktiveras när du använder Virtuella Azure-datorer som DNS-servrar. En [offentlig IP-adress](virtual-network-public-ip-address.md) ska tilldelas varje virtuell DNS-server. 
> 

### <a name="web-apps"></a>Webbappar
Anta att du måste utföra namnmatchning från webbappen som skapats med hjälp av App Service, länkad till ett virtuellt nätverk, till virtuella datorer i samma virtuella nätverk. Förutom att konfigurera en anpassad DNS-server som har en DNS-vidarebefordrare som vidarebefordrar frågor till Azure (virtuell IP 168.63.129.16), utför du följande steg:
1. Aktivera integrering av virtuella nätverk för din webbapp, om den inte redan görs, enligt beskrivningen i [Integrera din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. I Azure-portalen väljer du Synkronisera nätverk under **Nätverksintegrering**för apptjänstplanen som är värd för webbappen **.** **Sync Network**

    ![Skärmbild av upplösningen av det virtuella nätverkets namn](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Om du behöver utföra namnmatchning från webbappen som skapats med hjälp av App Service, länkad till ett virtuellt nätverk, till virtuella datorer i ett annat virtuellt nätverk, måste du använda anpassade DNS-servrar i båda virtuella nätverken enligt följande:

* Konfigurera en DNS-server i ditt virtuella målnätverk på en virtuell dator som också kan vidarebefordra frågor till den rekursiva matcharen i Azure (virtuell IP 168.63.129.16). Det finns ett exempel på DNS-vidarebefordrare i [azure quickstart-mallgalleriet](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) och [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Konfigurera en DNS-vidarebefordrare i det virtuella källnätverket på en virtuell dator. Konfigurera den här DNS-vidarebefordraren för att vidarebefordra frågor till DNS-servern i ditt virtuella målnätverk.
* Konfigurera källans DNS-server i inställningarna för det virtuella källnätverket.
* Aktivera integrering av virtuella nätverk för din webbapp för att länka till källvirtualnätet, enligt instruktionerna i [Integrera din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* I Azure-portalen väljer du Synkronisera nätverk under **Nätverksintegrering**för apptjänstplanen som är värd för webbappen **.** **Sync Network**

## <a name="specify-dns-servers"></a>Ange DNS-servrar
När du använder dina egna DNS-servrar ger Azure möjlighet att ange flera DNS-servrar per virtuellt nätverk. Du kan också ange flera DNS-servrar per nätverksgränssnitt (för Azure Resource Manager) eller per molntjänst (för den klassiska distributionsmodellen). DNS-servrar som angetts för ett nätverksgränssnitt eller molntjänst får företräde framför DNS-servrar som angetts för det virtuella nätverket.

> [!NOTE]
> Egenskaper för nätverksanslutning, till exempel DNS-server-IPs, bör inte redigeras direkt i virtuella datorer. Detta beror på att de kan få raderas under tjänsten läka när det virtuella nätverket adaptern ersätts. Detta gäller både virtuella Windows- och Linux-datorer.

När du använder distributionsmodellen för Azure Resource Manager kan du ange DNS-servrar för ett virtuellt nätverk och ett nätverksgränssnitt. Mer information finns i [Hantera ett virtuellt nätverk](manage-virtual-network.md) och Hantera ett [nätverksgränssnitt](virtual-network-network-interface.md).

> [!NOTE]
> Om du väljer anpassad DNS-server för ditt virtuella nätverk måste du ange minst en DNS-server-IP-adress. Annars ignorerar det virtuella nätverket konfigurationen och använder Azure-dns i stället.

När du använder den klassiska distributionsmodellen kan du ange DNS-servrar för det virtuella nätverket i Azure-portalen eller [nätverkskonfigurationsfilen](https://msdn.microsoft.com/library/azure/jj157100). För molntjänster kan du ange DNS-servrar via [tjänstkonfigurationsfilen](https://msdn.microsoft.com/library/azure/ee758710) eller med hjälp av PowerShell med [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Om du ändrar DNS-inställningarna för ett virtuellt nätverk eller en virtuell dator som redan har distribuerats måste du för att de nya DNS-inställningarna ska börja gälla utföra en FÖRNYELSE AV DHCP-lånet på alla berörda virtuella datorer i det virtuella nätverket. För virtuella datorer som kör Windows OS kan `ipconfig /renew` du göra detta genom att skriva direkt i den virtuella datorn. Stegen varierar beroende på operativsystemet. Se relevant dokumentation för din OS-typ.

## <a name="next-steps"></a>Nästa steg

Distributionsmodell för Azure Resource Manager:

* [Hantera ett virtuellt nätverk](manage-virtual-network.md)
* [Hantera ett nätverksgränssnitt](virtual-network-network-interface.md)

Klassisk distributionsmodell:

* [Konfigurationsschema för Azure-tjänst](https://msdn.microsoft.com/library/azure/ee758710)
* [Konfigurationsschema för virtuellt nätverk](https://msdn.microsoft.com/library/azure/jj157100)
* [Konfigurera ett virtuellt nätverk med hjälp av en nätverkskonfigurationsfil](virtual-networks-using-network-configuration-file.md)
