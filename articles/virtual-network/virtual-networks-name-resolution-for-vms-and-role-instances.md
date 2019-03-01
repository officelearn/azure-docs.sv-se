---
title: Namnmatchning för resurser i Azure-nätverk
titlesuffix: Azure Virtual Network
description: Namn på lösning scenarier för Azure IaaS, hybridlösningar och mellan olika molntjänster, Active Directory och använder en egen DNS-server.
services: virtual-network
documentationcenter: na
author: subsarma
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: subsarma
ms.openlocfilehash: 9130fef895d4f9cd31f643b20a735c0e821923b8
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194001"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Namnmatchning för resurser i Azure-nätverk

Beroende på hur du använder Azure som värd för IaaS, PaaS och hybridlösningar, kan du behöva tillåta de virtuella datorerna (VM) och andra resurser som har distribuerats i ett virtuellt nätverk kan kommunicera med varandra. Du kan aktivera kommunikation med hjälp av IP-adresser, är det mycket enklare att använda namn som kan vara lätt att komma ihåg och ändras inte. 

När resurser som har distribuerats i virtuella nätverk behöver matcha domännamn och interna IP-adresser, kan de använda en av två metoder:

* [Azure-tillhandahållen namnmatchning](#azure-provided-name-resolution)
* [Namnmatchning som använder en egen DNS-server](#name-resolution-that-uses-your-own-dns-server) (som kan vidarebefordra frågor till Azure-tillhandahållna DNS-servrar)

Typ av namnmatchning som du använder beror på hur dina resurser behöver kommunicera med varandra. I följande tabell visar scenarier och motsvarande name resolution lösningar:

> [!NOTE]
> Beroende på ditt scenario kan du använda funktionen Azure DNS Private Zones finns för närvarande i offentlig förhandsversion. Mer information finns på sidan om att [använda Azure DNS för privata domäner](../dns/private-dns-overview.md).
>

| **Scenario** | **Lösning** | **Suffix** |
| --- | --- | --- |
| Namnmatchning mellan virtuella datorer som finns i samma virtuella nätverk eller Azure Cloud Services rollinstanser i samma molntjänst. | [Azure DNS Private Zones](../dns/private-dns-overview.md) eller [Azure-tillhandahållen namnmatchning](#azure-provided-name-resolution) |Värdnamn eller fullständigt domännamn |
| Namnmatchning mellan virtuella datorer i olika virtuella nätverk eller rollinstanser i olika molntjänster. |[Azure DNS Private Zones](../dns/private-dns-overview.md) eller kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namnmatchning från en Azure App Service (Web App, funktion eller Bot) med hjälp av virtual network-integration rollinstanser eller virtuella datorer i samma virtuella nätverk. |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namnet lösningen från App Service Web Apps till virtuella datorer i samma virtuella nätverk. |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namnet lösningen från App Service Web Apps i ett virtuellt nätverk till virtuella datorer i ett annat virtuellt nätverk. |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se namnmatchning med hjälp av DNS-servern. |Endast FQDN |
| Upplösning på lokal dator- och tjänstnamn från virtuella datorer eller rollinstanser i Azure. |Kundhanterad DNS-servrar (den lokala domänkontrollanten, lokala skrivskyddade domänkontrollanten eller en sekundär DNS har synkroniserats med zonöverföringar, till exempel). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Lösning av Azure värdnamn från lokala datorer. |Vidarebefordra frågor till en kundhanterad DNS-proxyserver i motsvarande virtuella nätverk proxyservern vidarebefordrar frågor till Azure för matchning. Se [namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Omvänd DNS för interna IP-adresser. |[Namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Inte tillämpligt |
| Namnmatchning mellan virtuella datorer eller rollinstanser i olika molntjänster, inte i ett virtuellt nätverk. |Inte tillämpligt. Anslutning mellan virtuella datorer och rollinstanser i olika molntjänster stöds inte utanför ett virtuellt nätverk. |Inte tillämpligt|

## <a name="azure-provided-name-resolution"></a>Azure-tillhandahållen namnmatchning

Azure tillhandahåller intern namnmatchning för virtuella datorer och rollinstanser som finns i samma virtuella nätverk eller tjänst i molnet tillsammans med matchning av offentliga DNS-namn. Virtuella datorer och instanser i en molntjänst delar samma DNS-suffixet så enbart värdnamnet är tillräckliga. Men i virtuella nätverk som distribueras med den klassiska distributionsmodellen kan ha olika molntjänster andra DNS-suffix. I så fall behöver du FQDN för namnmatchning mellan olika molntjänster. I virtuella nätverk som distribuerats med hjälp av Azure Resource Manager-distributionsmodellen, är DNS-suffixet konsekventa i det virtuella nätverket, så att FQDN inte behövs. DNS-namn kan tilldelas till både virtuella datorer och nätverksgränssnitt. Även om Azure-tillhandahållen namnmatchning inte kräver någon konfiguration, men det är inte valet för alla distributionsscenarier, enligt beskrivningen i föregående tabell.

> [!NOTE]
> När webb-och arbetsroller med hjälp av cloud services, kan du också komma åt interna IP-adresserna för rollinstanser med hjälp av Azure Service Management REST API. Mer information finns i den [Service Management REST API-referens](https://msdn.microsoft.com/library/azure/ee460799.aspx). Adressen är baserad på rollen och instanstalet tal. 
>
>

### <a name="features"></a>Funktioner

Azure-tillhandahållen namnmatchning innehåller följande funktioner:
* Användarvänlighet. Det krävs ingen konfiguration.
* Hög tillgänglighet. Du behöver inte skapa och hantera kluster för din egen DNS-servrar.
* Du kan använda tjänsten tillsammans med din egen DNS-servrar för att lösa både lokala och Azure värdnamn.
* Du kan använda namnmatchningen mellan virtuella datorer och rollinstanser inom samma molntjänst, utan att behöva ett fullständigt domännamn.
* Du kan använda matchning mellan virtuella datorer i virtuella nätverk som använder Azure Resource Manager-distributionsmodellen, utan behov av ett fullständigt domännamn. Virtuella nätverk i den klassiska distributionsmodellen kräver ett fullständigt domännamn när du matcha namn i olika molntjänster. 
* Du kan använda värdnamn som bäst beskriver dina distributioner i stället för att arbeta med autogenererade namn.

### <a name="considerations"></a>Överväganden

Saker att tänka på när du använder Azure-tillhandahållen namnmatchning:
* Azure-skapade DNS-suffix kan inte ändras.
* Du kan inte registrera dina egna poster manuellt.
* WINS- och NetBIOS stöds inte. Du kan inte se dina virtuella datorer i Windows Explorer.
* Värdnamn måste vara DNS-kompatibla. Namn måste använda bara 0-9, a – z och '-', och får inte börja eller sluta med en '-'.
* DNS-frågorna är begränsad för varje virtuell dator. Begränsning bör inte påverka de flesta program. Se till att cachelagring på klientsidan är aktiverad om begärandebegränsning observeras. Mer information finns i [DNS-klientkonfiguration](#dns-client-configuration).
* Endast virtuella datorer i de första 180 molntjänsterna är registrerade för varje virtuellt nätverk i en klassisk distributionsmodell. Den här begränsningen gäller inte för virtuella nätverk i Azure Resource Manager.

## <a name="dns-client-configuration"></a>DNS-klientkonfiguration

Det här avsnittet beskriver klientcachelagring och klientsidan återförsök.

### <a name="client-side-caching"></a>Klientcachelagring

Inte alla DNS-fråga måste skickas över nätverket. Klientcachelagring kan minska svarstiden och förbättra återhämtning till nätverkssignaler, genom att lösa återkommande DNS-frågor från en lokal cache. DNS-poster innehåller en time to live (TTL) mekanism, vilket gör att cacheminnet för att lagra posten så länge som möjligt utan att påverka poster färskhet. Därför är klientcachelagring lämplig för de flesta situationer.

Standard Windows DNS-klienten har en inbyggd DNS-cache. Vissa Linux-distributioner omfattar inte cachelagring som standard. Om du upptäcker att det inte är ett lokalt cacheminne redan, kan du lägga till en DNS-cache för varje Linux-VM.

Det finns ett antal olika DNS-cachen paket som är tillgängliga (till exempel dnsmasq). Här är hur du installerar dnsmasq på de vanligaste distributionerna:

* **Ubuntu (använder resolvconf)**:
  * Installera paketet dnsmasq med `sudo apt-get install dnsmasq`.
* **SUSE (använder netconf)**:
  * Installera paketet dnsmasq med `sudo zypper install dnsmasq`.
  * Aktivera tjänsten dnsmasq med `systemctl enable dnsmasq.service`. 
  * Starta tjänsten dnsmasq med `systemctl start dnsmasq.service`. 
  * Redigera **/etc/sysconfig/network/config**, och ändra *NETCONFIG_DNS_FORWARDER = ””* till *dnsmasq*.
  * Uppdatera resolv.conf med `netconfig update`, för att ställa in cachen som den lokala DNS-matchning.
* **OpenLogic (uses NetworkManager)**:
  * Installera paketet dnsmasq med `sudo yum install dnsmasq`.
  * Aktivera tjänsten dnsmasq med `systemctl enable dnsmasq.service`.
  * Starta tjänsten dnsmasq med `systemctl start dnsmasq.service`.
  * Lägg till *åtkomstgruppen för domän-namnservrarna 127.0.0.1;* till **/etc/dhclient-eth0.conf**.
  * Starta om nätverkstjänsten med `service network restart`, för att ställa in cachen som den lokala DNS-matchning.

> [!NOTE]
> Dnsmasq paketet är endast ett av många DNS cacheminnen som är tillgängliga för Linux. Innan du använder den, kontrollera dess lämplighet för dina specifika behov och kontrollera att inga andra cache är installerat.
>
>
    
### <a name="client-side-retries"></a>Klientsidan återförsök

DNS är främst UDP-protokollet. Eftersom UDP-protokollet inte garanterar meddelandeleverans, hanteras logik för omprövning i själva DNS-protokollet. Varje DNS-klient (operativsystemet) kan ha olika omprövningslogiken, beroende på Skaparens inställningar:

* Windows operativsystem försök efter en sekund, och sedan igen efter en annan två sekunder, fyra sekunder och en annan fyra sekunder. 
* Standard Linux installationsprogrammet återförsöken efter fem sekunder. Vi rekommenderar att du ändrar återförsök-specifikationer till fem gånger på en sekunds intervall.

Kontrollera de aktuella inställningarna på en Linux VM med `cat /etc/resolv.conf`. Titta på den *alternativ* rad, till exempel:

```bash
options timeout:1 attempts:5
```

Filen resolv.conf genereras vanligtvis automatiskt och bör inte redigeras. Vilka specifika åtgärder för att lägga till den *alternativ* rad varierar beroende på distribution:

* **Ubuntu** (använder resolvconf):
  1. Lägg till den *alternativ* rad till **/etc/resolvconf/resolv.conf.d/tail**.
  2. Kör `resolvconf -u` att uppdatera.
* **SUSE** (använder netconf):
  1. Lägg till *timeout:1 försök: 5* till den **NETCONFIG_DNS_RESOLVER_OPTIONS = ””** parameter i **/etc/sysconfig/network/config**.
  2. Kör `netconfig update` att uppdatera.
* **OpenLogic** (använder NetworkManager):
  1. Lägg till *echo ”alternativ timeout:1 försök: 5”* till **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Uppdatera med `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Namnmatchning som använder en egen DNS-server

Det här avsnittet beskriver virtuella datorer och rollinstanser webbappar.

### <a name="vms-and-role-instances"></a>Virtuella datorer och rollinstanser

Din namnmatchningen kan utöver funktionerna som tillhandahålls av Azure. Du kan behöva använda Microsoft Windows Server Active Directory-domäner kan matcha DNS-namn mellan virtuella nätverk. Azure ger möjlighet att använda din egen DNS-servrar för att täcka dessa scenarier.

DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till de rekursiva matchare i Azure. På så sätt kan du matcha värdnamn i det virtuella nätverket. Exempelvis kan en domänkontrollant (DC) som körs i Azure svara på DNS-frågor för dess domäner och vidarebefordra alla frågor till Azure. Vidarebefordran av frågor kan virtuella datorer finns i både lokala resurser (via DC) och Azure-tillhandahållen värdnamn (via vidarebefordrare). Åtkomst till de rekursiva matchare i Azure tillhandahålls via den virtuella IP-Adressen 168.63.129.16.

Vidarebefordran av DNS kan du även gör DNS-matchning mellan virtuella nätverk, och att dina lokala datorer för att matcha värdnamn som medföljer Azure. DNS-server-dator för att lösa värdnamnet för en virtuell dator, måste finnas i samma virtuella nätverk och konfigureras för att vidarebefordra värd-namnfrågor till Azure. Eftersom DNS-suffix är olika i varje virtuellt nätverk kan använda du villkorlig vidarebefordringsregler för att skicka DNS-frågor till rätt virtuellt nätverk för matchning. Följande bild visar två virtuella nätverk och ett lokalt nätverk som gör DNS-matchning mellan virtuella nätverk, med hjälp av den här metoden. Ett exempel DNS-vidarebefordrare är tillgänglig i den [Azure-Snabbstartsmallar galleriet](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) och [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> En rollinstans kan utföra namnmatchning för virtuella datorer i samma virtuella nätverk. Detta sker med hjälp av FQDN som består av den Virtuella datorns värdnamn och **internal.cloudapp.net** DNS-suffix. Men i det här fallet namnmatchning lyckas bara om rollinstansen har VM-namn som definierats i den [Rollschema (.cscfg-fil)](https://msdn.microsoft.com/library/azure/jj156212.aspx).
> <Role name="<role-name>" vmName="<vm-name>">
>
> Rollinstanser som måste utföra namnmatchning för virtuella datorer i ett annat virtuellt nätverk (FQDN med hjälp av den **internal.cloudapp.net** suffix) har att göra detta med hjälp av den metod som beskrivs i det här avsnittet (anpassade DNS-servrar som vidarebefordrar mellan de två virtuella nätverk).
>

![Diagram över DNS mellan virtuella nätverk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

När du använder Azure-tillhandahållen namnmatchning, Azure DHCP Dynamic Host Configuration Protocol () innehåller en intern DNS-suffix (**. internal.cloudapp.net**) till varje virtuell dator. Detta suffix kan matcha värdnamn eftersom namnet värdposter finns i den **internal.cloudapp.net** zon. När du använder en egen lösning för name resolution anges suffixet till virtuella datorer eftersom den stör andra DNS-arkitekturer (t.ex. domänanslutna scenarier). I stället Azure tillhandahåller en icke-fungerande platshållare (*reddog.microsoft.com*).

Om det behövs kan bestämma du interna DNS-suffixet med hjälp av PowerShell eller API: et.

* För virtuella nätverk i Azure Resource Manager-distributionsmodeller suffixet är tillgängligt via den [nätverksgränssnittet REST API](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces), [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) PowerShell-cmdleten och [ AZ network nic show](/cli/azure/network/nic#az-network-nic-show) Azure CLI-kommando.
* I klassiska distributionsmodeller suffixet är tillgängligt via den [få distribution API](https://msdn.microsoft.com/library/azure/ee460804.aspx) anropa eller [Get-AzureVM-felsöka](/powershell/module/servicemanagement/azure/get-azurevm) cmdlet.

Om vidarebefordran av frågor till Azure inte passar dina behov, bör du ange en egen DNS-lösning. Din DNS-lösningen behöver:

* Ange lämpliga värden namnmatchning [DDNS](virtual-networks-name-resolution-ddns.md), till exempel. Om du använder DDNS, kan du behöva inaktivera DNS-post rensning. Azure DHCP-lån är långa och rensning kan ta bort DNS-poster för tidigt. 
* Ange lämpliga rekursiv matchning för att tillåta matchning av extern domännamn.
* Vara tillgänglig (TCP och UDP på port 53) från klienterna den fungerar och att kunna få åtkomst till internet.
* Skyddas mot åtkomst från internet, för att identifiera hot som externa agenter.

> [!NOTE]
> För bästa prestanda när du använder virtuella Azure-datorer som DNS-servrar, ska IPv6 vara inaktiverat. En [offentliga IP-adressen](virtual-network-public-ip-address.md) ska tilldelas till varje virtuell dator för DNS-server. För ytterligare analys och optimeringar när du använder Windows Server som din DNS-server finns i [Name resolution prestanda för en rekursiv Windows DNS-Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Webbappar
Anta att du behöver utföra namnmatchning från ditt program skapas med hjälp av App Service är länkad till ett virtuellt nätverk för virtuella datorer i samma virtuella nätverk. Förutom att konfigurera en anpassad DNS-server som har en DNS-vidarebefordrare som vidarebefordrar frågor till Azure (virtuell IP-adressen 168.63.129.16), utför följande steg:
1. Aktivera virtual network-integration för din webbapp, om inte redan har gjort, enligt beskrivningen i [integrera din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. I Azure-portalen för App Service-plan som är värd för webbapp väljer **synkronisera nätverk** under **nätverk**, **virtuell nätverksintegrering**.

    ![Skärmbild av namnmatchning för virtuella nätverk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Om du behöver utföra namnmatchning från ditt program skapas med hjälp av App Service är länkad till ett virtuellt nätverk för virtuella datorer i ett annat virtuellt nätverk som du behöver använda anpassade DNS-servrar på båda virtuella nätverken enligt följande:

* Konfigurera en DNS-server i ditt virtuella nätverk i målet, på en virtuell dator som kan också att vidarebefordra frågor till den rekursiva matcharen i Azure (virtuell IP-adressen 168.63.129.16). Ett exempel DNS-vidarebefordrare är tillgänglig i den [Azure-Snabbstartsmallar galleriet](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) och [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Konfigurera en DNS-vidarebefordrare i det virtuella nätverket källa på en virtuell dator. Konfigurera den här DNS-vidarebefordrare för att vidarebefordra frågor till DNS-servern i det virtuella nätverket för målet.
* Konfigurera DNS-källservern i inställningarna för källa virtuella nätverket.
* Aktivera virtual network-integration för din webbapp att länka till det virtuella nätverket källa, följa anvisningarna i [integrera din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* I Azure-portalen för App Service-plan som är värd för webbapp väljer **synkronisera nätverk** under **nätverk**, **virtuell nätverksintegrering**.

## <a name="specify-dns-servers"></a>Ange DNS-servrar
När du använder en egen DNS-servrar, ger möjligheten att ange flera DNS-servrar per virtuellt nätverk i Azure. Du kan också ange flera DNS-servrar per nätverksgränssnitt (för Azure Resource Manager) eller per molntjänst (för den klassiska distributionsmodellen). DNS-servrar som angetts för en nätverkstjänst för gränssnittet eller molnet få prioritet med DNS-servrar som angetts för det virtuella nätverket.

> [!NOTE]
> Nätverksegenskaper för anslutning, till exempel IP-adresser, DNS-server bör inte redigeras direkt i Windows-datorer. Detta beror på att de kan få bort under tjänsten reparation av nodtjänst när det virtuella nätverkskortet ersätts.
>
>

När du använder Azure Resource Manager-distributionsmodellen, anger du DNS-servrar för ett virtuellt nätverk och ett nätverksgränssnitt. Mer information finns i [hantera ett virtuellt nätverk](manage-virtual-network.md) och [hantera ett nätverksgränssnitt](virtual-network-network-interface.md).

> [!NOTE]
> Om du väljer för anpassad DNS-server för det virtuella nätverket måste du ange minst en DNS-serverns IP-adress; i annat fall ignoreras konfigurationen virtuellt nätverk och Använd Azure-tillhandahållna DNS i stället.
>
>

När du använder den klassiska distributionsmodellen kan du ange DNS-servrar för det virtuella nätverket i Azure-portalen eller [nätverkskonfigurationsfilen](https://msdn.microsoft.com/library/azure/jj157100). För cloud services, kan du ange DNS-servrar via den [Service konfigurationsfilen](https://msdn.microsoft.com/library/azure/ee758710) eller med hjälp av PowerShell med [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Om du ändrar DNS-inställningarna för ett virtuellt nätverk eller en virtuell dator som redan har distribuerats kan behöva du starta om varje berörda virtuella datorn för att ändringarna ska börja gälla.
>
>

## <a name="next-steps"></a>Nästa steg

Azure Resource Manager-distributionsmodell:

* [Hantera ett virtuellt nätverk](manage-virtual-network.md)
* [Hantera ett nätverksgränssnitt](virtual-network-network-interface.md)

Klassisk distributionsmodell:

* [Azure-Tjänstkonfigurationens Schema](https://msdn.microsoft.com/library/azure/ee758710)
* [Konfigurationsschema för virtuellt nätverk](https://msdn.microsoft.com/library/azure/jj157100)
* [Konfigurera ett virtuellt nätverk med hjälp av en nätverkskonfigurationsfil](virtual-networks-using-network-configuration-file.md)