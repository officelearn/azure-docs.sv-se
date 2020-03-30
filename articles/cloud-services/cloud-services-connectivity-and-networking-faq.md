---
title: Problem med anslutningar och nätverk
titleSuffix: Azure Cloud Services
description: I den här artikeln visas vanliga frågor om anslutning och nätverk för Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 7caeba0e88f63106eae80f7142b5d65463f8d7a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019408"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Anslutnings- och nätverksproblem för Azure Cloud Services: Vanliga frågor och svar (vanliga frågor)

Den här artikeln innehåller vanliga frågor och svar om anslutnings- och nätverksproblem för [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Storleksinformation finns på [sidan Storlekssida för molntjänster.](cloud-services-sizes-specs.md)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Jag kan inte reservera en IP i en molntjänst med flera VIP-tjänster.
Kontrollera först att den instans för virtuella datorer som du försöker reservera IP-adressen för är aktiverad. För det andra, se till att du använder reserverade IPs för både mellanlagring och produktionsdistributioner. *Ändra inte* inställningarna medan distributionen uppgraderas.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Hur använder jag Fjärrskrivbord när jag har en NSG?
Lägg till regler för NSG som tillåter trafik på portarna **3389** och **20000**. Fjärrskrivbord använder port **3389**. Molntjänstinstanser är belastningsbalanserade, så du kan inte styra vilken instans som ska anslutas till. *RemoteForwarder-* och *RemoteAccess-agenter* hanterar RDP-trafik (Remote Desktop Protocol) och tillåter klienten att skicka en RDP-cookie och ange en enskild instans att ansluta till. *RemoteForwarder-* och *RemoteAccess-agenter* kräver att port **20000** är öppen, vilket kan blockeras om du har en NSG.

## <a name="can-i-ping-a-cloud-service"></a>Kan jag pinga en molntjänst?

Nej, inte genom att använda det normala "ping"/ICMP-protokollet. ICMP-protokollet är inte tillåtet via Azure-belastningsutjämnaren.

Om du vill testa anslutningen rekommenderar vi att du gör en portping. Medan Ping.exe använder ICMP kan du använda andra verktyg, till exempel PSPing, Nmap och telnet, för att testa anslutningen till en viss TCP-port.

Mer information finns i [Använda portpingar i stället för ICMP för att testa Azure VM-anslutning](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Hur förhindrar jag att ta emot tusentals träffar från okända IP-adresser som kan tyda på en skadlig attack mot molntjänsten?
Azure implementerar en nätverkssäkerhet med fleralager för att skydda sina plattformstjänster mot ddos-attacker (Distributed Denial-of-Service). Azure DDoS-försvarssystemet är en del av Azures kontinuerliga övervakningsprocess, som kontinuerligt förbättras genom penetrationstestning. Detta DDoS-försvarssystem är utformat för att inte bara klara attacker utifrån utan även från andra Azure-klienter. Mer information finns i [Azure-nätverkssäkerhet](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Du kan också skapa en startuppgift för att selektivt blockera vissa specifika IP-adresser. Mer information finns i [Blockera en specifik IP-adress](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>När jag försöker rdp till min molntjänstinstans får jag meddelandet "Användarkontot har gått ut".
Felmeddelandet "Det här användarkontot har gått ut" när du kringgår utgångsdatumet som har konfigurerats i rdp-inställningarna. Du kan ändra utgångsdatumet från portalen genom att följa dessa steg:

1. Logga in på [Azure-portalen,](https://portal.azure.com)gå till din molntjänst och välj fliken **Fjärrskrivbord.**

2. Välj **distributionsplats för produktion** eller **mellanlagring.**

3. Ändra **datum för förfallodatum** och spara konfigurationen.

Du bör nu kunna RDP till din maskin.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Varför balanserar inte Azure Load Balancer ut trafiken jämnt?
Information om hur en intern belastningsutjämnare fungerar finns i [Azure Load Balancer nya distributionsläge](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Distributionsalgoritmen som används är en 5-tuppel (käll-IP, källport, mål-IP, målport och protokolltyp) för att mappa trafik till tillgängliga servrar. Det ger klibbighet endast inom en transportsession. Paket i samma TCP- eller UDP-session dirigeras till samma DATACENTER IP -instans (DIP) bakom den belastningsbalanserade slutpunkten. När klienten stänger och öppnar anslutningen igen eller startar en ny session från samma käll-IP ändras källporten och trafiken går till en annan DIP-slutpunkt.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Hur omdirigerar jag inkommande trafik till standard-URL:en för min molntjänst till en anpassad WEBBADRESS?

URL-omskrivningsmodulen för IIS kan användas för att omdirigera trafik som kommer \*till standard-URL:en för molntjänsten (till exempel .cloudapp.net) till något eget namn/URL. Eftersom modulen URL-omskrivning är aktiverad på webbroller som standard och dess regler är konfigurerade i programmets web.config, är den alltid tillgänglig på den virtuella datorn oavsett omstarter/ombilder. Mer information finns i:

- [Skapa omskrivningsregler för modulen URL-omskrivning](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Ta bort en standardlänk](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hur kan jag blockera/inaktivera inkommande trafik till standard-URL:en för min molntjänst?

Du kan förhindra inkommande trafik till standard-URL:en/namnet \*på molntjänsten (till exempel .cloudapp.net). Ange värdhuvudet till ett anpassat DNS-namn (till exempel www\.MyCloudService.com) under platsbindningskonfiguration i molntjänstdefinitionen (*.csdef) fil, som anges:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Eftersom den här värdhuvudbindningen tillämpas via csdef-filen är tjänsten endast tillgänglig via det anpassade namnet "www.MyCloudService.com". Alla inkommande begäranden till domänen "*.cloudapp.net" misslyckas alltid. Om du använder en anpassad SLB-avsökning eller en intern belastningsutjämnare i tjänsten kan det störa avsökningsbeteendet om du blockerar tjänstens standard-URL/namn.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Hur kan jag se till att den offentliga IP-adressen för en molntjänst aldrig ändras?

För att se till att den offentliga IP-adressen för din molntjänst (även känd som en VIP) aldrig ändras så att den vanligtvis kan vitlistas av några specifika klienter, rekommenderar vi att du har en reserverad IP-kopplad till den. Annars är den virtuella IP som tillhandahålls av Azure hanteras från din prenumeration om du tar bort distributionen. För en lyckad VIP-växling behöver du individuella reserverade VIP-adresser för både produktions- och mellanlagringsplatser. Utan dem misslyckas växlingsåtgärden. Om du vill reservera en IP-adress och associera den med din molntjänst läser du följande artiklar:

- [Reservera IP-adressen för en befintlig molntjänst](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associera en reserverad IP till en molntjänst med hjälp av en tjänstkonfigurationsfil](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Om du har mer än en instans för dina roller bör kopplingen mellan RIP och molntjänsten inte orsaka några driftstopp. Alternativt kan du lägga till IP-intervallet för ditt Azure-datacenter i en tillåt-lista. Du hittar alla Azure IP-intervall i [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Den här filen innehåller IP-adressintervall (inklusive beräknings-, SQL- och lagringsintervall) som används i Azure-datacenter. En uppdaterad fil publiceras varje vecka som återspeglar de för närvarande distribuerade intervallen och eventuella kommande ändringar av IP-intervallen. Nya intervall som visas i filen används inte i datacenter i minst en vecka. Hämta den nya XML-filen varje vecka och utför nödvändiga ändringar på webbplatsen för att identifiera tjänster som körs i Azure på rätt sätt. Azure ExpressRoute-användare kan notera att den här filen används för att uppdatera BGP-annonsen för Azure-utrymme under den första veckan i varje månad.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Hur kan jag använda virtuella Azure Resource Manager-nätverk med molntjänster?

Molntjänster kan inte placeras i virtuella Azure Resource Manager-nätverk. Virtuella nätverk i Resource Manager och virtuella distributionsnätverk kan anslutas via peering. Mer information finns i [Virtual Network peering](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Hur kan jag få en lista över offentliga IPs som används av mina molntjänster?

Du kan använda följande PS-skript för att få en lista över offentliga IPs för molntjänster under din prenumeration

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```
