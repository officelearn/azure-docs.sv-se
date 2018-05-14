---
title: Problem med anslutningen och nätverk för Microsoft Azure Cloud Services FAQ | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor om anslutning och nätverk för Microsoft Azure Cloud Services.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: ab0fa22e9ba776db3d4af301499545f6e0822478
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problem med anslutningen och nätverk för Azure Cloud Services: vanliga frågor (FAQ)

Den här artikeln innehåller vanliga frågor om problem med anslutningen och nätverk för [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Storleksinformation finns i [Cloud Services VM sida](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Det går inte att reservera en IP-adress i en multi-VIP-molntjänst.
Kontrollera först att den virtuella dator-instans som du försöker att reservera IP-Adressen för är aktiverad. Därefter kontrollerar du att du använder reserverade IP-adresser för både mellanlagring och produktion distributioner. *Inte* ändra inställningarna när distributionen är att uppgradera.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Hur använder jag Fjärrskrivbord när jag har en NSG?
Lägg till regler som tillåter trafik på portarna NSG **3389** och **20000**. Fjärrskrivbord använder port **3389**. Molnet tjänstinstanser belastningsutjämnas, så du inte kan styra vilken instans att ansluta till direkt. Den *RemoteForwarder* och *RemoteAccess* agenter hantera trafik för Remote Desktop Protocol (RDP) och tillåter klienten att skicka en RDP-cookie och ange en enskild instans att ansluta till. Den *RemoteForwarder* och *RemoteAccess* agenter kräver port **20000** för att öppna kan blockeras om du har en NSG.

## <a name="can-i-ping-a-cloud-service"></a>Kan jag pinga en tjänst i molnet?

Nej, inte genom att använda normalt ”pinga” / ICMP-protokollet. ICMP-protokollet är inte tillåtet via Azure belastningsutjämnare.

Om du vill testa anslutningen rekommenderar vi att du gör en port-ping. Ping.exe använder ICMP, men du kan använda andra verktyg, till exempel PSPing, Nmap och telnet, för att testa anslutningen till en specifik TCP-port.

Mer information finns i [använda port pingar i stället ICMP för att testa anslutningen för virtuella Azure-datorn](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Hur förhindrar mottagning tusentals träffar från okända IP-adresser som kan indikera skadlig kod till Molntjänsten?
Azure implementerar en multilayer nätverkssäkerhet för att skydda dess plattformstjänster mot distribuerade denial of service (DDoS)-attacker. Systemets Azure DDoS-skydd är en del av Azures kontinuerlig övervakning processen, som kontinuerligt förbättras genom intrång tester. DDoS-skydd systemet är utformade för att klara inte bara angrepp utifrån utan också från andra Azure-klienter. Mer information finns i [Azure nätverkssäkerhet](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Du kan också skapa en startåtgärd för att selektivt blockera vissa specifika IP-adresser. Mer information finns i [blockera en specifik IP-adress](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>När jag försöker RDP Mina moln tjänstinstansen visas meddelandet ”användarkontot har upphört att gälla”.
Du kan få felmeddelandet ”det här användarkontot har upphört att gälla” när du kringgå förfallodatumet som konfigurerats i RDP-inställningar. Du kan ändra utgångsdatumet från portalen genom att följa dessa steg:

1. Logga in på den [Azure-portalen](https://portal.azure.com), gå till Molntjänsten och välj den **fjärrskrivbord** fliken.

2. Välj den **produktion** eller **mellanlagring** distributionsplatsen.

3. Ändra den **upphör att gälla på** datum och spara sedan konfigurationen.

Du nu ska kunna RDP till din dator.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Varför är Azure belastningsutjämnare inte NLB trafiken jämnt?
Information om hur en intern belastningsutjämnare fungerar finns i [Azure belastningsutjämnare nya distribution läget](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Distribution-algoritm som används är en 5-tuppel (käll-IP, källport, mål-IP, målport och protokolltyp) hash för att mappa trafik till tillgängliga servrar. Det ger varaktighet endast inom en transportsession. Paket i samma TCP eller UDP-session dirigeras till samma datacenter IP-Adressen (DIP) instans bakom Utjämning av nätverksbelastning-slutpunkten. När klienten stänger och öppnar en ny anslutningen eller startar en ny session från samma käll-IP, källport ändras och gör att trafik att gå till en annan DIP-slutpunkt.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Hur kan dirigera inkommande trafik till standard-URL för min Molntjänsten att en anpassad URL? 

Den URL-modulen för omarbetning av IIS som kan användas för att omdirigera trafik som kommer till standard-URL för Molntjänsten (till exempel \*. cloudapp.net) till vissa anpassade namn-URL. Eftersom URL-omskrivning om modulen är aktiverat på webbroller som standard och dess regler är konfigurerade i programmets web.config kan är det alltid tillgängligt på den virtuella datorn oavsett omstarter/reimages. Mer information finns i:

- [Skapa omarbetning regler för URL-omskrivning om modulen](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Ta bort en standardlänk](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hur kan jag blockera och inaktivera inkommande trafik till standard-URL för min Molntjänsten? 

Du kan förhindra att inkommande trafik till standard URL/namnet på din tjänst i molnet (exempelvis \*. cloudapp.net). Ange värdadressen till en anpassad DNS-namn (till exempel www.MyCloudService.com) under bindningen platskonfiguration i molnet (*.csdef) tjänstdefinitionsfilen, som anges: 
 

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
 
Eftersom denna värd sidhuvud bindning tillämpas via filen csdef är tjänsten tillgänglig endast via det egna namnet ”www.MyCloudService.com”. Alla inkommande förfrågningar till den ”*. cloudapp.net” domain alltid att misslyckas. Om du använder en anpassad SLB avsökning eller en intern belastningsutjämnare i tjänsten, kan blockerar standard URL/namn för tjänsten påverka avsöknings beteende. 

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Hur kan jag kontrollera att aldrig ändras offentliga IP-adressen för en tjänst i molnet?

Om du vill kontrollera offentliga IP-adressen för din molntjänst (även kallat VIP) aldrig ändras så att det kan vara vanligtvis godkända av några specifika klienter, rekommenderar vi att du har en reserverad IP-adress som är kopplade till den. Annars har virtuella IP-Adressen som tillhandahålls av Azure frigjorts från din prenumeration om du tar bort distributionen. Du måste enskilda reserverade IP-adresser för produktion och mellanlagring fack för lyckad VIP-växlingen. Utan dem inte byte. Om du vill reservera en IP-adress och koppla den till Molntjänsten, finns i följande artiklar:
 
- [Reserverad IP-adressen för en befintlig molntjänst](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associera en reserverad IP-adress till en molntjänst med hjälp av en konfigurationsfil för tjänsten](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Om du har mer än en instans för rollerna får inte kopplar RIP till Molntjänsten orsaka driftavbrott. Du kan också godkända IP-adressintervall för Azure-datacenter. Du hittar alla Azure-IP-adressintervall på den [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Den här filen innehåller de IP-adressintervall (inklusive adressintervall beräkning, SQL och lagring) används i Azure-datacenter. En uppdaterad fil skickas varje vecka som avspeglar de för tillfället distribuerade intervall och eventuella kommande ändringar till IP-adressintervall. Nya adressintervall som visas i filen används inte i datacenter för minst en vecka. Hämta XML-filen med ny varje vecka och utföra nödvändiga ändringar på din plats för att identifiera tjänster som körs i Azure. Azure ExpressRoute-användare kan Observera att den här filen används för att uppdatera BGP-annons Azure utrymme i den första veckan i månaden. 

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Hur kan jag använda Azure Resource Manager virtuella nätverk med molntjänster? 

Molntjänster kan inte placeras i Azure Resource Manager virtuella nätverk. Virtuella nätverk för Resource Manager och klassisk distribution virtuella nätverk kan vara ansluten via peering. Mer information finns i [virtuella nätverk peering](../virtual-network/virtual-network-peering-overview.md).
