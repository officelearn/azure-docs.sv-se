---
title: Problem med anslutningen och nätverk för vanliga frågor om Microsoft Azure Cloud Services | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor om anslutningar och nätverk för Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: defd623eff76a4e37a9d88c4f59d2edaa71e34e0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227458"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problem med anslutningen och nätverk för Azure Cloud Services: vanliga frågor (FAQ)

Den här artikeln innehåller vanliga frågor om problem med anslutningen och nätverk för [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Storleksinformation finns i den [Cloud Services VM sida](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Jag kan inte reservera en IP-adress i en multi-VIP-molntjänst.
Kontrollera först att den virtuella datorinstansen som försök att reservera IP-Adressen för är aktiverad. Kontrollera dessutom att du använder reserverade IP-adresser för mellanlagrings-och produktionsdistributionerna. *Inte* ändra inställningarna när distributionen är att uppgradera.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Hur använder jag Fjärrskrivbord när jag har en NSG?
Lägga till regler i NSG som tillåter trafik på portarna **3389** och **20000**. Fjärrskrivbord är port **3389**. Cloud service-instanser är belastningsutjämnad, så du inte kan styra vilken instans som ska ansluta till direkt. Den *RemoteForwarder* och *RemoteAccess* agenter hantera Remote Desktop Protocol (RDP) trafik och tillåta att klienten kan skicka en RDP-cookie och ange en enskild instans att ansluta till. Den *RemoteForwarder* och *RemoteAccess* agenter kräver port **20000** för att öppna måste kan blockeras om du har en NSG.

## <a name="can-i-ping-a-cloud-service"></a>Kan jag pinga en molntjänst?

Nej, inte med normal ”pinga” / ICMP-protokollet. ICMP-protokollet är inte tillåtet via Azure-belastningsutjämnaren.

Om du vill testa anslutningen, rekommenderar vi att du gör en port-ping. Ping.exe använder ICMP, men du kan använda andra verktyg, till exempel PSPing, Nmap och telnet, för att testa anslutningen till en specifik TCP-port.

Mer information finns i [använder port pingar i stället ICMP för att testa anslutningen för Azure VM](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Hur förhindrar jag att som tar emot tusentals träffar från okänd IP-adresser som kan indikera skadlig kod till Molntjänsten?
Azure implementerar en multilayer nätverkssäkerhet för att skydda dess plattformstjänster mot distribuerade denial of service DDoS-attacker. Azure DDoS defense system är en del av Azures kontinuerlig övervakning processen, vilket förbättras kontinuerligt via penetrationstester. Det här systemet för DDoS-skydd är utformade för att klara inte bara attacker från utsidan utan också från andra Azure-klienter. Mer information finns i [Azure-nätverkssäkerhet](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Du kan också skapa en startåtgärd för att selektivt blockera vissa specifika IP-adresser. Mer information finns i [blockera en specifik IP-adress](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>När jag försöker RDP till min molntjänstinstans visas meddelandet ”användarkontot har gått ut”.
Du kan få felmeddelandet ”det här användarkontot har upphört att gälla” när du kringgå förfallodatumet som konfigureras i inställningarna för RDP. Du kan ändra utgångsdatumet från portalen genom att följa dessa steg:

1. Logga in på den [Azure-portalen](https://portal.azure.com), gå till din molntjänst och välj den **fjärrskrivbord** fliken.

2. Välj den **produktion** eller **mellanlagring** distributionsplats.

3. Ändra den **upphör att gälla på** datum och spara konfigurationen.

Du nu ska kunna använda RDP till din dator.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Varför är Azure Load Balancer balanserar inte trafiken jämnt?
Information om hur en intern belastningsutjämnare fungerar finns i [Azure Load Balancer nya Distributionsläge](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Distribution-algoritm som används är en 5-tuppel (käll-IP, källport, mål-IP, målport och protokolltyp) hash att mappa trafik till tillgängliga servrar. Det ger varaktighet endast inom en transportsession. Paket i samma TCP eller UDP-session dirigeras till samma datacenter IP (DIP) instans bakom Utjämning av nätverksbelastning-slutpunkten. När klienten stänger och sedan öppnar anslutningen igen eller startar en ny session från samma käll-IP, källport ändras och gör trafik att gå till en annan DIP-slutpunkt.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Hur kan jag för att dirigera inkommande trafik till-URL: Standardwebbadressen för min molntjänst till en anpassad URL? 

Den URL-modulen för omarbetning av IIS som kan användas för att omdirigera trafik som kommer till-URL: Standardadressen för Molntjänsten (till exempel \*. cloudapp.net) till vissa anpassade namn/URL. Eftersom modulen URL-Omskrivningsregler är aktiverat på web-roller som standard och dess regler har konfigurerats i programmets web.config, är det alltid tillgänglig på den virtuella datorn oavsett omstarter/avbildningen. Mer information finns i:

- [Skapa omskrivningsregler för modulen URL-Omskrivningsregler](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Ta bort en standardlänk](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hur kan jag blockera/inaktivera inkommande trafik till-URL: Standardwebbadressen för min molntjänst? 

Du kan förhindra att inkommande trafik till standard-URL/namnet på Molntjänsten (till exempel \*. cloudapp.net). Ange ett anpassat DNS-namn (till exempel www.MyCloudService.com) värdhuvudet under bindningen platskonfiguration i molnet (*.csdef) tjänstdefinitionsfilen, som anges: 
 

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
 
Eftersom den här värden huvud-bindningen är påtvingad igenom filen csdef, är tjänsten tillgänglig endast via anpassade namnet ”www.MyCloudService.com”. Alla inkommande begäranden till den ”*. cloudapp.net” domain alltid att misslyckas. Om du använder en anpassad avsökning för SLB eller en intern belastningsutjämnare i tjänsten, kan blockerar standard URL/namnet på tjänsten påverka beteendet sökning. 

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Hur kan jag kontrollera att den offentliga IP-adressen för en molntjänst ändras aldrig?

Om du vill se till att den offentliga IP-adressen för din molntjänst (även kallat en VIP) aldrig ändras så att det kan vara certifikatutfärdarinfrastruktur godkänd av några specifika klienter, rekommenderar vi att du har en reserverad IP-adress som är associerade med den. I annat fall har den virtuella IP-Adressen som tillhandahålls av Azure frigjorts från din prenumeration om du tar bort distributionen. För lyckad VIP-växlingen behöver du enskilda reserverade IP-adresser för både produktions- och mellanlagringsplatser. Utan att de misslyckas växlingen. För att reservera en IP-adress och koppla den till din molntjänst måste du läsa följande artiklar:
 
- [Reserverad IP-adressen för en befintlig molntjänst](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associera en reserverad IP-adress till en molntjänst med hjälp av en tjänstkonfigurationsfil](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Om du har fler än en instans för dina roller bör inte kopplar RIP till din molntjänst orsakar några driftstopp. Du kan också vitlista IP-intervallet för din Azure-datacenter. Du kan hitta alla Azure-IP-intervall på den [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Den här filen innehåller de IP-adressintervall (inklusive compute, SQL och storage-intervall) används i Azure-datacenter. En uppdaterad fil publiceras varje vecka som visar aktuella intervall och eventuella kommande ändringar till IP-adressintervall. De nya intervall som visas i filen används inte i datacenter för minst en vecka. Hämta den nya XML-filen varje vecka och gör nödvändiga ändringar på din plats för att kunna identifiera vilka tjänster som körs i Azure. Azure ExpressRoute-användare kan Observera att den här filen används för att uppdatera BGP-annonser Azure utrymme i den första veckan varje månad. 

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Hur kan jag använda Azure Resource Manager-nätverk med cloud services? 

Molntjänster kan inte placeras i Azure Resource Manager-nätverk. Virtuella nätverk i Resource Manager och klassiska virtuella nätverk kan anslutas via peering. Mer information finns i [peerkoppling av virtuella nätverk](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Hur hittar jag listan över offentliga IP-adresser som används av min molntjänster?

Du kan använda följande PS-skriptet för att hämta listan över offentliga IP-adresser för molntjänster i din prenumeration

    $services = Get-AzureService  | Group-Object -Property ServiceName

    foreach ($service in $services) 
    {
        "Cloud Service '$($service.Name)'"

        $deployment = Get-AzureDeployment -ServiceName $service.Name 
        "VIP - " +  $deployment.VirtualIPs[0].Address
        "================================="
    }
