---
title: Namnmatchning för virtuella datorer och rollinstanser
description: Name resolution scenarier för Azure IaaS hybridlösningar mellan olika cloud services, Active Directory och med hjälp av DNS-servern.
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
ms.date: 02/14/2018
ms.author: jdial
ms.openlocfilehash: e46f6617b1a6d73ace00d4eafa1410785315a8c8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="name-resolution-for-virtual-machines-and-role-instances"></a>Namnmatchning för virtuella datorer och rollinstanser

Beroende på hur du använder Azure som värd för IaaS och PaaS hybridlösningar, kanske du måste tillåta de virtuella datorerna (VM) och rollen instanser som du skapar för att kommunicera med varandra. Även om du kan aktivera den här kommunikationen med hjälp av IP-adresser, är det mycket enklare att använda namn som enkelt kan registreras och ändras inte. 

När rollinstanser och virtuella datorer finns i Azure måste matcha domännamn och interna IP-adresser, kan de använda ett av två sätt:

* [Azure-tillhandahållna namnmatchning](#azure-provided-name-resolution)
* [Namnmatchning som använder DNS-servern](#name-resolution-that-uses-your-own-dns-server) (som kan vidarebefordra frågor till Azure-tillhandahållna DNS-servrar) 

Typ av namnmatchning som du använder beror på hur dina virtuella datorer och rollinstanser måste kommunicera med varandra. Följande tabell visar scenarier och motsvarande name resolution lösningar:

> [!NOTE]
> Beroende på ditt scenario kanske du vill använda funktionen Azure privata DNS-zoner som för närvarande är tillgänglig som förhandsversion. Mer information finns [med hjälp av Azure DNS för privata domäner](../dns/private-dns-overview.md)
>

| **Scenario** | **Lösning** | **Suffix** |
| --- | --- | --- |
| Namn matchning mellan rollinstanser eller virtuella datorer finns i samma molntjänst eller virtuella nätverk. | [Azure DNS privata zoner](../dns/private-dns-overview.md) eller [Azure-tillhandahållna namnmatchning](#azure-provided-name-resolution) |värdnamn eller fullständigt domännamn |
| Namnmatchning mellan rollinstanser eller virtuella datorer finns i olika virtuella nätverk. |[Azure DNS privata zoner](../dns/private-dns-overview.md) eller kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namnmatchning från ett Azure App Service (Web App, funktion eller Bot) med hjälp av virtuell nätverksintegration rollinstanser eller virtuella datorer finns i samma virtuella nätverk. |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namnmatchning från App Service Web Apps till virtuella datorer finns i samma virtuella nätverk. |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namnmatchning från App Service Web Apps till virtuella datorer finns i ett annat virtuellt nätverk. |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server-for-web-apps). |Endast FQDN |
| Matchning av lokal dator- och namn från rollinstanser eller virtuella datorer i Azure. |Kundhanterad DNS-servrar (lokal domänkontrollant, lokala skrivskyddade domänkontrollanten eller en sekundär DNS synkroniseras med zonöverföringar, till exempel). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Lösning med Azure värdnamn från lokala datorer. |Vidarebefordra frågor till en kundhanterad DNS-proxyserver i motsvarande virtuella nätverk proxyservern vidarebefordrar frågor till Azure för matchning. Se [namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Omvänd DNS för interna IP-adresser. |[Namnmatchning med hjälp av DNS-servern](#name-resolution-that-uses-your-own-dns-server). |Inte tillämpligt |
| Namnmatchning mellan VM: ar eller rollinstanser som finns i olika molntjänster, inte i ett virtuellt nätverk. |Inte tillämpligt. Anslutningen mellan virtuella datorer och rollinstanser i olika molntjänster kan inte användas utanför ett virtuellt nätverk. |Inte tillämpligt|

## <a name="azure-provided-name-resolution"></a>Azure-tillhandahållna namnmatchning

Tillsammans med upplösning på offentliga DNS-namn erbjuder Azure intern namnmatchning för virtuella datorer och rollinstanser som finns i samma virtuella nätverk eller tjänst i molnet. Virtuella datorer och instanser i en molntjänst delar samma DNS-suffixet (så att enbart värdnamnet räcker). Men i virtuella nätverk som använder den klassiska distributionsmodellen olika molntjänster har olika DNS-suffix. I den här situationen måste du FQDN för namnmatchning mellan olika molntjänster. I virtuella nätverk i Azure Resource Manager-distributionsmodellen är DNS-suffix konsekvent på det virtuella nätverket (så att FQDN inte behövs). DNS-namn kan tilldelas både nätverkskort och virtuella datorer. Även om Azure-tillhandahållna namnmatchning inte kräver någon konfiguration, är det inte det bästa valet för alla distributionsscenarier, som visas i föregående tabell.

> [!NOTE]
> När du använder webb-och arbetsroller kan du också komma åt interna IP-adresserna för rollinstanser. Detta baseras på rollen namnet och instansen nummer, med hjälp av Azure Service Management REST API. Mer information finns i [Service Management REST API-referens](https://msdn.microsoft.com/library/azure/ee460799.aspx).
> 
> 

### <a name="features"></a>Funktioner

Azure-tillhandahållna namnmatchning innehåller följande funktioner:
* Användarvänlighet. Ingen konfiguration krävs.
* Hög tillgänglighet. Du behöver inte skapa och hantera kluster DNS-servrar.
* Du kan använda tjänsten tillsammans med dina egna DNS-servrar för att matcha både lokalt och Azure värdnamn.
* Du kan använda namnmatchning mellan rollinstanser och virtuella datorer i samma Molntjänsten, utan att behöva ett fullständigt domännamn.
* Du kan använda matchning mellan virtuella datorer i virtuella nätverk som använder Azure Resource Manager distributionsmodellen, utan behov av ett fullständigt domännamn. Virtuella nätverk i den klassiska distributionsmodellen kräver ett fullständigt domännamn när du matcha namn i olika molntjänster. 
* Du kan använda värdnamn som bäst beskriver dina distributioner i stället för att arbeta med automatiskt genererat namn.

### <a name="considerations"></a>Överväganden

Här följer saker att tänka på när du använder Azure-tillhandahållna namnmatchning:
* Skapa Azure DNS-suffix kan inte ändras.
* Du kan registrera dina egna poster manuellt.
* WINS- och NetBIOS stöds inte (du kan inte se dina virtuella datorer i Utforskaren).
* Värdnamn måste vara kompatibel med DNS. Namn måste använda endast 0-9, a-z och '-', och får inte inledas eller avslutas med en '-'.
* DNS-frågorna begränsas för varje virtuell dator. Begränsning bör inte påverka de flesta program. Om begäran begränsning observeras, kontrollerar du att cachelagring på klientsidan är aktiverad. Mer information finns i [DNS-klientkonfiguration](#dns-client-configuration).
* Endast virtuella datorer i de första 180 molntjänsterna har registrerats för varje virtuellt nätverk i en klassiska distributionsmodellen. Den här gränsen gäller inte för virtuella nätverk i Azure Resource Manager.

## <a name="dns-client-configuration"></a>DNS-klientkonfiguration

Det här avsnittet beskriver klientcachelagring och klientsidan återförsök.

### <a name="client-side-caching"></a>Cachelagring på klientsidan

Inte alla DNS-fråga måste skickas över nätverket. Klientcachelagring hjälper minskar svarstider och förbättrar återhämtning i nätverket signaler genom att lösa återkommande DNS-frågor från en lokal cache. DNS-poster innehåller en time-to-live (TTL) mekanism som gör att cacheminnet för att lagra posten så länge som möjligt utan att påverka poster dokumentens. Därför är klientcachelagring lämplig för de flesta situationer.

Standard Windows DNS-klienten har en inbyggd DNS-cache. Vissa Linux-distributioner som innehåller inte cachelagring som standard. Om du upptäcker att det inte är ett lokalt cacheminne redan, kan du lägga till en DNS-cache för varje Linux-VM.

Det finns ett antal olika DNS-cache paket som är tillgängliga (till exempel dnsmasq). Här är hur du installerar dnsmasq på de vanligaste distributioner:

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
  * Lägg till *lägga domän-namnservrar 127.0.0.1;* till **/etc/dhclient-eth0.conf**.
  * Starta om nätverkstjänsten med `service network restart`, för att ställa in cachen som den lokala DNS-matchning.

> [!NOTE]
> Dnsmasq paketet är endast ett av många DNS-cacheminnet som är tillgängliga för Linux. Kontrollera dess lämplighet för dina specifika behov innan du använder den, och kontrollera att inga andra cache är installerat.
> 
> 
    
### <a name="client-side-retries"></a>Klientsidans återförsök

DNS är främst ett UDP-protokoll. Eftersom UDP-protokollet inte garantera meddelandeleverans, hanteras logik i själva DNS-protokollet. Varje DNS-klient (operativsystemet) kan ha olika logik, beroende på den person som skapade inställningar:

* Windows-operativsystem försök igen efter att en andra och sedan efter en annan två sekunderna fyra sekunder och en annan fyra sekunder. 
* Standard Linux installationsprogrammet återförsöken efter fem sekunder. Vi rekommenderar att du ändrar försök specifikationer till fem gånger på en sekunds intervall.

Kontrollera de aktuella inställningarna på en Linux-VM med `cat /etc/resolv.conf`. Titta på den *alternativ* rad, till exempel:

```bash
options timeout:1 attempts:5
```

Filen resolv.conf är vanligtvis genereras automatiskt och bör inte redigeras. Specifika anvisningar för att lägga till den *alternativ* rad varierar beroende på distributionsplatser:

* **Ubuntu** (använder resolvconf):
  1. Lägg till den *alternativ* rad till **/etc/resolveconf/resolv.conf.d/head**.
  2. Kör `resolvconf -u` att uppdatera.
* **SUSE** (använder netconf):
  1. Lägg till *timeout:1 försök: 5* till den **NETCONFIG_DNS_RESOLVER_OPTIONS = ””** parameter i **/etc/sysconfig/network/config**. 
  2. Kör `netconfig update` att uppdatera.
* **OpenLogic** (uses NetworkManager):
  1. Lägg till *echo ”alternativ timeout:1 försök: 5”* till **/etc/NetworkManager/dispatcher.d/11-dhclient**. 
  2. Uppdatera med `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Namnmatchning som använder DNS-servern

Det här avsnittet beskriver virtuella datorer och rollinstanser och webbappar.

### <a name="vms-and-role-instances"></a>Virtuella datorer och rollinstanser

Din namnmatchningen kan utöver funktionerna som tillhandahålls av Azure. Detta kan exempelvis vara fallet när du använder Active Directory-domäner eller när du behöver DNS-matchning mellan virtuella nätverk. Azure tillhandahåller möjligheten att använda DNS-servrarna så att den täcker dessa scenarier.

DNS-servrar inom ett virtuellt nätverk kan vidarebefordra DNS-frågor till de rekursiva matchare i Azure. På så sätt kan du matcha värdnamn i det virtuella nätverket. Till exempel en domänkontrollant (DC) som körs i Azure svara på DNS-frågor för dess domäner och vidarebefordra alla frågor till Azure. Vidarebefordran av frågor kan virtuella datorer att se både lokala resurser (via DC) och Azure-tillhandahållna värdnamn (via vidarebefordrare). Åtkomst till de rekursiva matchare i Azure anges via den virtuella IP-Adressen 168.63.129.16.

Vidarebefordran av DNS kan du också gör DNS-matchning mellan virtuella nätverk, och att dina lokala datorer att matcha värdnamn för Azure-tillhandahållna. DNS-servern VM för att matcha värdnamnet för en virtuell dator måste finnas i samma virtuella nätverk och konfigureras för att vidarebefordra värden namnfrågor till Azure. Eftersom DNS-suffix skiljer sig åt i varje virtuellt nätverk, kan du använda regler för villkorlig vidarebefordran skicka DNS-frågor till rätt virtuellt nätverk för matchning. Följande bild visar två virtuella nätverk och ett lokalt nätverk som gör att DNS-matchning mellan virtuella nätverk med hjälp av den här metoden. Ett exempel DNS-vidarebefordrare som är tillgängliga i den [Azure Quickstart mallgalleriet](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) och [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> En rollinstans kan utföra någon namnmatchning för virtuella datorer i samma virtuella nätverk. Detta sker med hjälp av FQDN som består av den virtuella datorns värdnamn och **internal.cloudapp.net** DNS-suffix. I det här fallet namnmatchning är dock endast lyckas om instansen har VM-namn som definierats i den [roll Schema (.cscfg-fil)](https://msdn.microsoft.com/library/azure/jj156212.aspx). 
>    <Role name="<role-name>" vmName="<vm-name>">
> 
> Rollinstanser som behöver utföra någon namnmatchning för virtuella datorer i ett annat virtuellt nätverk (FQDN med hjälp av den **internal.cloudapp.net** suffix) måste du göra det med den metod som beskrivs i det här avsnittet (anpassad DNS-servrar som vidarebefordrar mellan de två virtuella nätverk).
>

![Diagram över DNS mellan virtuella nätverk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

När du använder Azure-tillhandahållna namnmatchning Azure DHCP Dynamic Host Configuration Protocol () innehåller en intern DNS-suffix (**. internal.cloudapp.net**) på varje virtuell dator. Det här suffixet kan matcha värdnamn eftersom namnet värdposter i den **internal.cloudapp.net** zon. När du använder din egen lösning för name resolution detta suffix anges till virtuella datorer eftersom den stör andra DNS-arkitekturer (t.ex. domänanslutna scenarier). I stället Azure tillhandahåller en icke-fungerande platshållare (*reddog.microsoft.com*).

Om det behövs kan du bestämma interna DNS-suffixet med hjälp av PowerShell eller API.

* För virtuella nätverk i Azure Resource Manager distributionsmodellerna suffixet är tillgängliga via den [nätverksgränssnittet REST API](/rest/api/virtualnetwork/networkinterfaces/get), [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) PowerShell-cmdlet och [az nätverket nic visa](/cli/azure/network/nic#az-network-nic-show) Azure CLI-kommando.
* I klassiska distributionsmodeller suffixet är tillgängliga via den [hämta distribution API](https://msdn.microsoft.com/library/azure/ee460804.aspx) anropa eller [Get-AzureVM-Debug](/powershell/module/azure/get-azurevm) cmdlet.

Om vidarebefordran av frågor till Azure inte passar dina behov, bör du ange DNS-lösningen. DNS-lösningen behöver:

* Ange lämpliga värden namnmatchning [DDNS](virtual-networks-name-resolution-ddns.md), till exempel. Observera att om du använder DDNS kan du behöva inaktivera DNS-postrensning. Azure DHCP-lån är långa och rensning kan ta bort DNS-poster för tidigt. 
* Ange lämpliga rekursiv matchning så att matchning av extern domännamn.
* Vara tillgänglig (TCP och UDP-port 53) från klienterna den fungerar och att kunna få åtkomst till internet.
* Skyddas mot åtkomst från internet, till att minska hot med externa agenter.

> [!NOTE]
> För bästa prestanda när du använder Azure virtuella datorer som DNS-servrar, ska IPv6 inaktiveras. En [offentlig IP på instansnivå](virtual-networks-instance-level-public-ip.md) ska tilldelas varje virtuell dator för DNS-server. För ytterligare prestandaanalys och optimeringar när du använder Windows Server som DNS-servern, se [Name resolution prestanda för en rekursiv Windows DNS-Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Webbappar
Anta att du behöver utföra namnmatchning från ditt webbprogram som skapas med hjälp av App Service är kopplad till ett virtuellt nätverk för virtuella datorer i samma virtuella nätverk. Förutom att konfigurera en anpassad DNS-server som har en DNS-vidarebefordrare som vidarebefordrar frågor till Azure (virtuell IP 168.63.129.16), utför följande steg:
1. Aktivera virtuell nätverksintegration för ditt webbprogram om det inte redan har gjort, enligt beskrivningen i [integrera din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. I Azure-portalen för App Service-plan värd för webbapp väljer **Sync nätverket** under **nätverk**, **virtuell nätverksintegration**.

    ![Skärmbild av namnmatchning för virtuella nätverk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Anta att du behöver utföra namnmatchning från ditt webbprogram som skapas med hjälp av App Service är kopplad till ett virtuellt nätverk för virtuella datorer i ett annat virtuellt nätverk. Detta kräver användning av anpassade DNS-servrar på båda virtuella nätverk på följande sätt: 
* Konfigurera en DNS-server i ditt virtuella nätverk i mål, på en virtuell dator som kan också vidarebefordra frågor till den rekursiva matcharen i Azure (virtuella IP-Adressen 168.63.129.16). Ett exempel DNS-vidarebefordrare som är tillgängliga i den [Azure Quickstart mallgalleriet](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) och [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Konfigurera en DNS-vidarebefordrare i källan virtuella nätverk på en virtuell dator. Konfigurera den här DNS-vidarebefordrare för att vidarebefordra frågor till DNS-servern i det virtuella målnätverket.
* Konfigurera DNS-källservern i källan virtuella nätverkets inställningar.
* Aktivera virtuell nätverksintegration för ditt webbprogram att länka till ett virtuellt nätverk källa och följa anvisningarna i [integrera din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* I Azure-portalen för App Service-plan värd för webbapp väljer **Sync nätverket** under **nätverk**, **virtuell nätverksintegration**. 

## <a name="specify-dns-servers"></a>Ange DNS-servrar
När du använder DNS-servrar, tillhandahåller Azure möjligheten att ange flera DNS-servrar per virtuellt nätverk. Du kan också göra detta per nätverksgränssnitt (för Azure Resource Manager), eller tjänst i molnet (för den klassiska distributionsmodellen). DNS-servrar som angetts för ett gränssnitt för molnet tjänsten eller nätverket få företräde över DNS-servrar som angetts för det virtuella nätverket.

> [!NOTE]
> Egenskaper för anslutning, till exempel IP-adresser, DNS-server bör inte redigeras direkt i virtuella Windows-datorer. Detta beror på att de kan hämta raderas under läka när det virtuella nätverkskortet ersätts. 
> 
> 

När du använder Azure Resource Manager-distributionsmodellen, anger du DNS-servrar i Azure-portalen. Se [virtuella nätverk](https://msdn.microsoft.com/library/azure/mt163661.aspx) och [nätverksgränssnitt](https://msdn.microsoft.com/library/azure/mt163668.aspx). Du kan också göra detta i PowerShell. Se [för virtuella nätverk](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork) och [nätverksgränssnittet](/powershell/module/azurerm.network/new-azurermnetworkinterface).

När du använder den klassiska distributionsmodellen kan du ange DNS-servrar för det virtuella nätverket i Azure-portalen eller [nätverkskonfigurationen filen](https://msdn.microsoft.com/library/azure/jj157100). För molntjänster, kan du ange DNS-servrar via den [Service konfigurationsfilen](https://msdn.microsoft.com/library/azure/ee758710) eller med hjälp av PowerShell med [ny AzureVM](/powershell/module/azure/new-azurevm).

> [!NOTE]
> Om du ändrar DNS-inställningarna för ett virtuellt nätverk eller en virtuell dator som redan har distribuerats, måste du starta om varje berörda VM för att ändringarna ska börja gälla.
> 
> 

## <a name="next-steps"></a>Nästa steg

Azure Resource Manager-modellen:

* [Skapa eller uppdatera ett virtuellt nätverk](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Skapa eller uppdatera ett nätverkskort](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)
* [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)

Klassiska distributionsmodellen:

* [Azure Service Configuration Schema](https://msdn.microsoft.com/library/azure/ee758710)
* [Konfigurationsschemat för virtuellt nätverk](https://msdn.microsoft.com/library/azure/jj157100)
* [Konfigurera ett virtuellt nätverk med hjälp av en konfigurationsfil för nätverk](virtual-networks-using-network-configuration-file.md)
