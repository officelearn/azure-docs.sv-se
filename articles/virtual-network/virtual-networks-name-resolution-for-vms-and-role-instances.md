---
title: "Namnmatchning för virtuella datorer och rollinstanser"
description: "Name resolution scenarier för Azure IaaS hybridlösningar mellan olika cloud services, Active Directory och med hjälp av DNS-servern."
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
ms.openlocfilehash: 5ea3e4ad68fd37ccaa6e081febe4827bdb2e196d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="name-resolution-for-vms-and-role-instances"></a>Namnmatchning för virtuella datorer och rollinstanser

Beroende på hur du använder Azure som värd för IaaS och PaaS hybridlösningar, kan du behöva tillåta virtuella datorer och rollinstanser som du skapar för att kommunicera med varandra. Även om den här kommunikationen kan göras med hjälp av IP-adresser, är det mycket enklare att använda namn som enkelt kan registreras och ändras inte. 

När rollinstanser och virtuella datorer finns i Azure måste matcha domännamn och interna IP-adresser, kan de använda ett av två sätt:

* [Azure-tillhandahållna namnmatchning](#azure-provided-name-resolution)
* [Namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server) (som kan vidarebefordra frågor till Azure-tillhandahållna DNS-servrar) 

Typ av namnmatchning som du använder beror på hur dina virtuella datorer och rollinstanser måste kommunicera med varandra. Följande tabell visar scenarier och motsvarande name resolution lösningar:

| **Scenario** | Lösning | **Suffix** |
| --- | --- | --- |
| Namn matchning mellan rollinstanser eller virtuella datorer finns i samma molntjänst eller virtuella nätverk. |[Azure-tillhandahållna namnmatchning](#azure-provided-name-resolution) |värdnamn eller fullständigt domännamn |
| Namnmatchning från ett Azure App Service (Web App, funktion eller Bot) med hjälp av virtuell nätverksintegration rollinstanser eller virtuella datorer finns i samma virtuella nätverk. |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Namnmatchning mellan rollinstanser eller virtuella datorer finns i olika virtuella nätverk. |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Namnmatchning från App Service Web Apps till virtuella datorer finns i samma virtuella nätverk. |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Namnmatchning från App Service Web Apps till virtuella datorer finns i ett annat virtuellt nätverk. |Kundhanterad DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för matchning av Azure (DNS-proxy). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server-for-web-apps). |Endast FQDN |
| Matchning av lokal dator- och namn från rollinstanser eller virtuella datorer i Azure. |Kundhanterad DNS-servrar (lokal domänkontrollant, lokala skrivskyddade domänkontrollanten eller en sekundär DNS synkroniseras med zonöverföringar, till exempel). Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Lösning med Azure värdnamn från lokala datorer. |Vidarebefordra frågor till en kundhanterad DNS-proxyserver i motsvarande virtuella nätverk proxyservern vidarebefordrar frågor till Azure för matchning. Se [namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Omvänd DNS för interna IP-adresser. |[Namnmatchning med hjälp av DNS-servern](#name-resolution-using-your-own-dns-server). |Inte tillämpligt |
| Namnmatchning mellan VM: ar eller rollinstanser som finns i olika molntjänster, inte i ett virtuellt nätverk. |Inte tillämpligt. Anslutningen mellan virtuella datorer och rollinstanser i olika molntjänster kan inte användas utanför ett virtuellt nätverk. |Inte tillämpligt|

## <a name="azure-provided-name-resolution"></a>Azure-tillhandahållna namnmatchning

Tillsammans med upplösning på offentliga DNS-namn erbjuder Azure intern namnmatchning för virtuella datorer och rollinstanser som finns i samma virtuella nätverk eller tjänst i molnet. Virtuella datorer/instanser i en molntjänst delar samma DNS-suffixet (så att enbart värdnamnet räcker) men i annat moln för klassiska virtuella nätverk tjänster har olika DNS-suffix så FQDN krävs för att matcha namn mellan olika molntjänster. DNS-suffix är konsekvent på det virtuella nätverket (så att FQDN inte behövs) och DNS-namn kan tilldelas till både nätverkskort och virtuella datorer i virtuella nätverk i Resource Manager-distributionsmodellen. Även om Azure-tillhandahållna namnmatchning inte kräver någon konfiguration, är det inte det bästa valet för alla distributionsscenarier, som visas i föregående tabell.

> [!NOTE]
> När du använder webb-och arbetsroller kan du också komma åt interna IP-adresserna för rollinstanser utifrån rollen namnet och instansen med hjälp av Azure Service Management REST API. Mer information finns i [Service Management REST API-referens](https://msdn.microsoft.com/library/azure/ee460799.aspx).
> 
> 

### <a name="features"></a>Funktioner

* Användarvänlighet: ingen konfiguration krävs för att kunna använda Azure-tillhandahållna namnmatchning.
* Azure-tillhandahållna namnmatchningstjänst har hög tillgänglighet, vilket sparar dig behovet av att skapa och hantera kluster DNS-servrar.
* Kan användas tillsammans med DNS-servrar för att matcha både lokalt och Azure värdnamn.
* Namnmatchning tillhandahålls mellan rollen instanser/virtuella datorer i samma molntjänst utan krävs ett fullständigt domännamn.
* Namnmatchning tillhandahålls mellan virtuella datorer i virtuella nätverk som använder Resource Manager distributionsmodellen, utan behov av ett fullständigt domännamn. Virtuella nätverk i den klassiska distributionsmodellen kräver ett fullständigt domännamn när matcha namn i olika molntjänster. 
* Du kan använda värdnamn som bäst beskriver dina distributioner i stället för att arbeta med automatiskt genererat namn.

### <a name="considerations"></a>Överväganden

* Skapa Azure DNS-suffix kan inte ändras.
* Du kan registrera dina egna poster manuellt.
* WINS- och NetBIOS stöds inte (du kan inte se dina virtuella datorer i Utforskaren).
* Värdnamn måste vara kompatibel med DNS. Namn måste använda endast 0-9, a – z och '-', och får inte inledas eller avslutas med en '-'. Se RFC 3696 avsnitt 2.
* DNS-frågorna begränsas för varje virtuell dator. Begränsning bör inte påverka de flesta program. Om begäran begränsning observeras, kontrollerar du att cachelagring på klientsidan är aktiverad. Mer information finns i [få ut mesta möjliga av Azure-tillhandahållna namnmatchning](#Getting-the-most-from-Azure-provided-name-resolution).
* Endast virtuella datorer i de första 180 molntjänsterna har registrerats för varje virtuellt nätverk i en klassiska distributionsmodellen. Den här gränsen gäller inte för virtuella nätverk i Resource Manager distributionsmodellerna.

## <a name="dns-client-configuration"></a>DNS-klientkonfiguration

### <a name="client-side-caching"></a>Cachelagring på klientsidan

Inte alla DNS-fråga måste skickas över nätverket. Klientcachelagring hjälper minskar svarstider och förbättrar återhämtning i nätverket signaler genom att lösa återkommande DNS-frågor från en lokal cache. DNS-posterna innehåller en time-to-live (TTL) vilket gör att cacheminnet för att lagra posten så länge som möjligt utan att påverka poster dokumentens så klientcachelagring lämpar sig för de flesta situationer.

Standard Windows DNS-klienten har en inbyggd DNS-cache. Vissa Linux-distributioner omfattar inte cachelagring som standard. Lägga till en DNS-cache för varje Linux VM (när du har kontrollerat att det inte är ett lokalt cacheminne redan), rekommenderas.

Det finns ett antal olika DNS-cache paket som är tillgängliga. Till exempel dnsmasq. Följande steg visar hur du installerar dnsmasq på de vanligaste distributioner:

* **Ubuntu (använder resolvconf)**:
  * Installera paketet dnsmasq med `sudo apt-get install dnsmasq`.
* **SUSE (använder netconf)**:
  * Installera paketet dnsmasq med `sudo zypper install dnsmasq`.
  * Aktivera tjänsten dnsmasq med `systemctl enable dnsmasq.service`. 
  * Starta tjänsten dnsmasq med `systemctl start dnsmasq.service`. 
  * Redigera **/etc/sysconfig/network/config** och ändra *NETCONFIG_DNS_FORWARDER = ””* till *dnsmasq*.
  * Uppdatera resolv.conf med `netconfig update` att ställa in cachen som den lokala DNS-matchning.
* **OpenLogic (uses NetworkManager)**:
  * Installera paketet dnsmasq med `sudo yum install dnsmasq`.
  * Aktivera tjänsten dnsmasq med `systemctl enable dnsmasq.service`.
  * Starta tjänsten dnsmasq med `systemctl start dnsmasq.service`.
  * Lägg till *lägga domän-namnservrar 127.0.0.1;* till **/etc/dhclient-eth0.conf**.
  * Starta om nätverkstjänsten med `service network restart` att ställa in cachen som den lokala DNS-matchning.

> [!NOTE]
> Paketet 'dnsmasq' är endast ett av de många DNS-cacheminnet för Linux. Kontrollera dess lämplighet för dina specifika behov innan du använder den, och att inga andra cache är installerad.
> 
> 
    
### <a name="client-side-retries"></a>Klientsidans återförsök

DNS är främst ett UDP-protokoll. Eftersom UDP-protokollet inte garantera meddelandeleverans, hanteras logik i själva DNS-protokollet. Varje DNS-klient (operativsystemet) kan ha olika logik beroende på den person som skapade inställningar:

* Windows-operativsystem försök igen efter att en andra och sedan igen efter en annan 2, 4 och en annan fyra sekunder. 
* Standard Linux installationsprogrammet återförsöken efter fem sekunder. Ändra åtgärden till fem gånger intervaller 1 sekund, rekommenderas.

Kontrollera de aktuella inställningarna på en Linux-VM med `cat /etc/resolv.conf`. Titta på den *alternativ* rad, till exempel:

```bash
options timeout:1 attempts:5
```

Filen resolv.conf är vanligtvis automatiskt genererade och bör inte redigeras. Specifika anvisningar för att lägga till den *alternativ* rad varierar beroende på distro:

* **Ubuntu** (använder resolvconf):
  * Lägg till rad alternativ till **/etc/resolveconf/resolv.conf.d/head**.
  * Kör `resolvconf -u` att uppdatera.
* **SUSE** (använder netconf):
  * Lägg till *timeout:1 försök: 5* till den **NETCONFIG_DNS_RESOLVER_OPTIONS = ””** parameter i **/etc/sysconfig/network/config**. 
  * Kör `netconfig update` att uppdatera.
* **OpenLogic** (uses NetworkManager):
  * Lägg till *echo ”alternativ timeout:1 försök: 5”* till **/etc/NetworkManager/dispatcher.d/11-dhclient**. 
  * Uppdatera med `service network restart`.

## <a name="name-resolution-using-your-own-dns-server"></a>Namnmatchning med hjälp av DNS-servern

### <a name="vms-and-role-instances"></a>Virtuella datorer och rollinstanser

Det finns ett antal olika situationer där din namnmatchningen kan gå utöver funktionerna som tillhandahålls av Azure, till exempel när via Active Directory-domäner eller när du behöver DNS-matchning mellan virtuella nätverk. Azure tillhandahåller möjligheten att använda DNS-servrarna så att den täcker dessa scenarier.

DNS-servrar inom ett virtuellt nätverk kan vidarebefordra DNS-frågor till Azures rekursiv matchare att matcha värdnamn i det virtuella nätverket. En domänkontrollant (DC) som körs i Azure kan svara på DNS-frågor för dess domäner och vidarebefordra alla frågor till Azure. Vidarebefordran av frågor kan virtuella datorer att se både lokala resurser (via DC) och Azure-tillhandahållna värdnamn (via vidarebefordrare). Åtkomst till Azures rekursiv matchare anges via den virtuella IP-Adressen 168.63.129.16.

DNS-vidarebefordran även gör DNS-matchning mellan virtuella nätverk och att dina lokala datorer att matcha värdnamn för Azure-tillhandahållna. För att matcha värdnamnet för en virtuell dator, DNS-server VM måste finnas i samma virtuella nätverk och konfigureras för att vidarebefordra hostname frågor till Azure. Som DNS-suffix skiljer sig åt i varje virtuellt nätverk, kan du använda regler för villkorlig vidarebefordran skicka DNS-frågor till rätt virtuellt nätverk för matchning. Följande bild visar två virtuella nätverk och ett lokalt nätverk gör mellan virtuella nätverk DNS-matchning med den här metoden. Ett exempel DNS-vidarebefordrare som är tillgängliga i den [Azure Quickstart mallgalleriet](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) och [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Rollinstanser kan utföra någon namnmatchning för virtuella datorer i samma virtuella nätverk med hjälp av FQDN som används på det Virtuella datornamnet tillsammans med DNS-suffixet ”internal.cloudapp.net”. I det här fallet namnmatchning är dock endast lyckas om instansen har VM-namn som definierats i den [roll Schema (.cscfg-fil)](https://msdn.microsoft.com/library/azure/jj156212.aspx). 
>    <Role name="<role-name>" vmName="<vm-name>">
> 
> Rollinstanser som behöver utföra någon namnmatchning för virtuella datorer i ett annat virtuellt nätverk (FQDN med hjälp av den **internal.cloudapp.net** suffix) har att göra det via anpassade DNS-servrar som vidarebefordrar mellan två virtuella nätverk, som beskrivs i Det här avsnittet.
>

![Mellan virtuella nätverk DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

När du använder Azure-tillhandahållna namnmatchning, en intern DNS-suffix (`*.internal.cloudapp.net`) har angetts för varje virtuell dator med Azures DHCP. Det här suffixet gör värdnamnsmatchning hostname posterna visas i den *internal.cloudapp.net* zon. När du använder din egen lösning för name resolution detta suffix anges till virtuella datorer eftersom den stör andra DNS-arkitekturer (t.ex. domänanslutna scenarier). I stället Azure tillhandahåller en icke-fungerande platshållare (*reddog.microsoft.com*).

Om det behövs, kan interna DNS-suffix fastställas med hjälp av PowerShell eller API:

* För virtuella nätverk i Resource Manager distributionsmodellerna suffixet är tillgängliga via den [nätverkskort](virtual-network-network-interface.md) resurs eller via den [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) cmdlet.
* I klassiska distributionsmodeller suffixet är tillgängliga via den [hämta distribution API](https://msdn.microsoft.com/library/azure/ee460804.aspx) anropa eller via den [Get-AzureVM-Debug](/powershell/module/azure/get-azurevm) cmdlet.

Om vidarebefordran av frågor till Azure inte passar dina behov, måste du ange DNS-lösningen. DNS-lösningen behöver:

* Ange lämpliga värdnamnsmatchning [DDNS](virtual-networks-name-resolution-ddns.md), till exempel. Observera att om det finns poster med DDNS som du kan behöva inaktivera DNS-posten rensning som Azures DHCP-lån är långa och rensning kan ta bort DNS för tidigt. 
* Ange lämpliga rekursiv matchning så att matchning av extern domännamn.
* Vara tillgänglig (TCP och UDP-port 53) från klienterna den fungerar och att kunna få åtkomst till internet.
* Skyddas mot åtkomst från internet, till att minska hot med externa agenter.

> [!NOTE]
> För bästa prestanda när du använder Azure virtuella datorer som DNS-servrar, IPv6 bör inaktiveras och en [offentlig IP på instansnivå](virtual-networks-instance-level-public-ip.md) ska tilldelas varje virtuell dator för DNS-server. Ytterligare prestandaanalys och optimeringar när du använder Windows Server som DNS-servern finns [Name resolution prestanda för en rekursiv Windows DNS-Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Web Apps
Om du behöver utföra någon namnmatchning från din App Service Web App kopplad till ett virtuellt nätverk för virtuella datorer i samma virtuella nätverk, sedan förutom att konfigurera en anpassad DNS-server som har en DNS-vidarebefordrare vidarebefordrar som frågor till Azure (virtuella IP-Adressen 168.63.129.16) , du måste också utföra följande steg:
* Aktivera virtuell nätverksintegration för din App Service Web App om du inte redan har gjort, enligt beskrivningen i [integrera din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* I Azure-portalen för programtjänstplan värd Web App väljer **Sync nätverket** under **nätverk**, **virtuell nätverksintegration**, enligt följande bild:

    ![Namnmatchning för Web Apps virtuellt nätverk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Namnmatchning från din App Service Web App kopplad till ett virtuellt nätverk, till virtuella datorer i ett annat virtuellt nätverk kräver användningen av anpassade DNS-servrar på båda virtuella nätverk på följande sätt:
* Konfigurera en DNS-server i det virtuella målnätverket på en virtuell dator som kan också vidarebefordra frågor till Azures rekursiv matcharen (virtuell IP-168.63.129.16). Ett exempel DNS-vidarebefordrare som är tillgängliga i den [Azure Quickstart mallgalleriet](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) och [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Konfigurera en DNS-vidarebefordrare i källan virtuella nätverk på en virtuell dator. Konfigurera den här DNS-vidarebefordrare för att vidarebefordra frågor till DNS-servern i det virtuella målnätverket.
* Konfigurera DNS-källservern i källan virtuella nätverkets inställningar.
* Aktivera virtuell nätverksintegration för din App Service Web App att länka till ett virtuellt nätverk källa och följa anvisningarna i [integrera din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* I Azure-portalen för programtjänstplan värd för webbapp väljer **Sync nätverket** under **nätverk**, **virtuell nätverksintegration**. 

## <a name="specifying-dns-servers"></a>Ange DNS-servrar
När du använder DNS-servrar, tillhandahåller Azure möjligheten att ange flera DNS-servrar per virtuellt nätverk eller per gränssnitt (Resource Manager) / molntjänst (klassisk). DNS-servrar som angetts för ett moln service/nätverksgränssnitt får företräde över DNS-servrar som angetts för det virtuella nätverket.

> [!NOTE]
> Nätverk anslutningsegenskaper, t.ex. DNS-server IP-adresser, inte bör redigeras direkt i virtuella Windows-datorer som de kan hämta raderas under läka när det virtuella nätverkskortet ersätts. 
> 
> 

När du använder Resource Manager-distributionsmodellen, DNS-servrar kan anges i portalen API-mallar: [för virtuella nätverk](https://msdn.microsoft.com/library/azure/mt163661.aspx) och [nätverksgränssnittet](https://msdn.microsoft.com/library/azure/mt163668.aspx), eller PowerShell: [virtuellt nätverk ](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork) och [nätverksgränssnittet](/powershell/module/azurerm.network/new-azurermnetworkinterface).

När du använder den klassiska distributionsmodellen, DNS-servrar för det virtuella nätverket kan anges i portalen eller [den *nätverkskonfigurationen* filen](https://msdn.microsoft.com/library/azure/jj157100). För molntjänster, DNS-servrar har angetts [den *tjänstkonfiguration* filen](https://msdn.microsoft.com/library/azure/ee758710) eller med hjälp av PowerShell med [ny AzureVM](/powershell/module/azure/new-azurevm).

> [!NOTE]
> Om du ändrar DNS-inställningarna för en virtuell nätverk/virtuell dator som redan har distribuerats, måste du starta om varje berörda VM för att ändringarna ska börja gälla.
> 
> 

## <a name="next-steps"></a>Nästa steg

Resource Manager-modellen:

* [Skapa eller uppdatera ett virtuellt nätverk](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Skapa eller uppdatera ett nätverkskort](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)
* [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)

Klassiska distributionsmodellen:

* [Azure Service Configuration Schema](https://msdn.microsoft.com/library/azure/ee758710)
* [Konfigurationsschemat för virtuellt nätverk](https://msdn.microsoft.com/library/azure/jj157100)
* [Konfigurera ett virtuellt nätverk med hjälp av en konfigurationsfil för nätverk](virtual-networks-using-network-configuration-file.md)
