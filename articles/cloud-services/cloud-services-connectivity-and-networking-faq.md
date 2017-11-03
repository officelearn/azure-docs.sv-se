---
title: "Problem med anslutningen och nätverk för Microsoft Azure Cloud Services FAQ | Microsoft Docs"
description: "Den här artikeln innehåller vanliga frågor om anslutning och nätverk för Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: genli
ms.openlocfilehash: 7b435b6904b05228a63e3ed3a9fed78747b843c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problem med anslutningen och nätverk för Azure Cloud Services: vanliga frågor (FAQ)

Den här artikeln innehåller vanliga frågor om problem med anslutningen och nätverk för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också kontakta den [Cloud Services VM-storlek sidan](cloud-services-sizes-specs.md) storlek information.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Det går inte att reservera en IP-adress i en multi-VIP-molntjänst
Kontrollera först att den virtuella dator-instans som du försöker att reservera IP-Adressen för är aktiverad. Därefter kontrollerar du att du använder reserverade IP-adresser för både mellanlagring och produktion distributioner. **Inte** ändra inställningarna när distributionen är att uppgradera.

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>Hur gör jag Fjärrskrivbord när jag har en NSG?
Lägg till regler som tillåter trafik på portarna NSG **3389** och **20000**.  Fjärrskrivbord använder port **3389**.  Molnet tjänstinstanser belastningsutjämnas, så du inte kan styra vilken instans att ansluta till direkt.  Den *RemoteForwarder* och *RemoteAccess* agenter hantera RDP-trafik och tillåter klienten att skicka en RDP-cookie och ange en enskild instans att ansluta till.  Den *RemoteForwarder* och *RemoteAccess* agenter kräver att port **20000** öppnas, där blockeras om du har en NSG.

## <a name="can-i-ping-a-cloud-service"></a>Kan jag pinga en tjänst i molnet?

Nej, inte genom att använda normalt ”pinga” / ICMP-protokollet. ICMP-protokollet är inte tillåtet via Azure belastningsutjämnare.

Om du vill testa anslutningen rekommenderar vi att du gör en port-ping. Medan Ping.exe använder ICMP, kan andra verktyg, till exempel PSPing, Nmap och telnet du testa anslutningen till en specifik TCP-port.

Mer information finns i [använda port pingar i stället ICMP för att testa anslutningen för virtuella Azure-datorn](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>Hur förhindrar mottagning tusentals träffar från okända IP-adresser som indikerar någon form av skadlig kod till Molntjänsten?
Azure implementerar en multilayer nätverkssäkerhet för att skydda dess plattformstjänster mot distribuerade denial of service (DDoS)-attacker. Systemets Azure DDoS-skydd är en del av Azures kontinuerlig övervakning processen, som kontinuerligt förbättras genom intrång tester. DDoS-skydd systemet är utformade för att klara inte bara angrepp utifrån utan också från andra Azure-klienter. Mer information finns i [nätverkssäkerhet för Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Du kan också skapa en startåtgärd för att selektivt blockera vissa specifika IP-adresser. Mer information finns i [blockera en specifik IP-adress](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>När jag försöker RDP Mina moln tjänstinstansen meddelandet, ”användarkontot har upphört att gälla”.
Du kan få felmeddelandet ”det här användarkontot har upphört att gälla” när du kringgå förfallodatumet som konfigurerats i RDP-inställningar. Du kan ändra utgångsdatumet från portalen genom att följa dessa steg:
1. Logga in på Azure-hanteringskonsolen (https://manage.windowsazure.com), navigera till Molntjänsten och markera den **konfigurera** fliken.
2. Välj **Remote**.
3. Ändra datumet för ”upphör att gälla på” och sedan spara konfigurationen.

Du nu ska kunna RDP till din dator.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>Varför är LoadBalancer inte NLB trafiken jämnt?
Information om hur interna load balancer fungerar finns i [Azure belastningsutjämnare nya distribution läget](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Distribution-algoritm som används är 5-tuppel (käll-IP, källport, mål-IP, målport protokolltyp) hash för att mappa trafik till tillgängliga servrar. Det ger varaktighet endast inom en transportsession. Paket i samma TCP eller UDP-session dirigeras till samma datacenter IP-Adressen (DIP)-instans bakom Utjämning av nätverksbelastning slutpunkten. När klienten stänger och öppnar anslutningen igen eller startar en ny session från samma käll-IP, källport ändras och gör att trafik att gå till en annan DIP-slutpunkt.

## <a name="how-can-i-redirect-the-incoming-traffic-to-my-default-url-of-cloud-service-to-a-custom-url"></a>Hur kan dirigera inkommande trafik till min standard URL: en molntjänst i en anpassad URL? 

URL till omskrivning av modul för IIS kan användas för att omdirigera trafik till standard-URL för Molntjänsten (till exempel \*. cloudapp.net) till några anpassade DNS-namn-URL. Eftersom den URL-modulen för omarbetning är aktiverat på Web-roller som standard och dess regler är konfigurerade i programmets web.config, skulle den alltid vara tillgänglig på den virtuella datorn oavsett omstarter/reimages. Mer information finns i:

- [Skapa omarbetning regler för URL: en omskrivning av modul](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Ta bort standard länk](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-the-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hur kan jag blockera och inaktivera den inkommande trafiken till standard-URL för min Molntjänsten? 

Du kan förhindra att den inkommande trafiken till standard URL/namnet på din molntjänst (till exempel \*. cloudapp.net) genom att ange värdadressen till en anpassad DNS-namn (till exempel www. MyCloudService.com) under bindningen platskonfiguration i molnet (*.csdef) tjänstdefinitionsfilen som anges nedan: 
 

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
 
Eftersom denna värd sidhuvud bindning tillämpas via csdef filen tjänsten skulle endast nås via det egna namnet www.MyCloudService.com, medan inkommande begäranden till den ' *. cloudapp.net' domän alltid skulle misslyckas. Som sägs om du däremot använder en anpassad SLB avsökning eller en intern belastningsutjämnare i tjänsten som, kan blockerar standard URL/namn för tjänsten störa avsöknings beteende. 

## <a name="how-to-make-sure-the-public-facing-ip-address-of-a-cloud-service-aka-vip-never-changes-so-that-it-could-be-customarily-whitelisted-by-few-specific-clients"></a>Hur du kontrollerar att offentliga Internetriktade IP-adressen för en tjänst i molnet (aka, VIP) aldrig ändras så att det kan vara vanligtvis godkända av några specifika klienter?

För vitlistning av IP-adressen för Cloud Services rekommenderas det att du har en reserverad IP-adress som är kopplade till den på annat sätt den virtuella IP-Adressen som tillhandahålls av Azure kommer att frigöra från din prenumeration om du tar bort distributionen. Observera att för lyckad VIP-växlingen måste enskilda reserverade IP-adresser för produktion och mellanlagring fack i frånvaron av vilka växlingen misslyckas åtgärden. Följ de här artiklarna om du vill reservera en IP-adress och associera den med dina molntjänster:  
 
- [Reserverad IP-adressen för en befintlig molntjänst](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associera en reserverad IP-adress till en molntjänst med hjälp av en konfigurationsfil för tjänsten](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Så länge som du har mer än en instans för rollerna får inte kopplar RIP till Molntjänsten orsaka driftavbrott. Du kan också godkända IP-adressintervall för Azure-Datacenter. Du hittar alla Azure-IP-adressintervall [här](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Den här filen innehåller de IP-adressintervall (inklusive Compute, SQL och Storage-intervall) som används i Microsoft Azure-datacentren. Varje vecka publiceras en uppdaterad fil med aktuella intervall och eventuella kommande ändringar av IP-adressintervallen. De nya intervall som anges i filen kommer inte att börja användas i datacentren förrän om minst en vecka. Ladda ned den nya XML-filen varje vecka och gör nödvändiga ändringar på din webbplats för att kunna identifiera vilka tjänster som körs i Azure. ExpressRoute-användare minns kanske att den här filen användes för att uppdatera Azures BGP-annonser under den första veckan varje månad. 

## <a name="how-can-i-use-azure-resource-manager-vnets-with-cloud-services"></a>Hur kan jag använda Azure Resource Manager VNets med molntjänster? 

Molntjänster kan inte placeras i en Azure Resource Manager VNets, men en Azure Resource Manager VNets och klassiska Vnet kan anslutas via peering. Mer information finns i [virtuella nätverk peering](../virtual-network/virtual-network-peering-overview.md).