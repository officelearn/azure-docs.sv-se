---
title: Vad är Azure Private Link-tjänst?
description: Läs mer om Azure Private Link-tjänsten.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 2cc6c577abdb3698ef6aca1f1f04d239f09d119c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280435"
---
# <a name="what-is-azure-private-link-service"></a>Vad är Azure Private Link-tjänst?

Azure Private Link-tjänsten är referensen till din egen tjänst som drivs av Azure Private Link. Din tjänst som körs bakom [Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) kan aktiveras för privat länkåtkomst så att konsumenter till din tjänst kan komma åt den privat från sina egna virtuella nätverk. Dina kunder kan skapa en privat slutpunkt i sitt virtuella nätverk och mappa den till den här tjänsten. I den här artikeln förklaras begrepp som är relaterade till tjänsteleverantörens sida. 

## <a name="workflow"></a>Arbetsflöde

![Arbetsflöde för tjänsten Privat länk](media/private-link-service-overview/private-link-service-workflow.png)

### <a name="create-your-private-link-service"></a>Skapa din privata länktjänst

- Konfigurera programmet så att det körs bakom en standardbelastningsutjämnare i det virtuella nätverket. Om du redan har konfigurerat ditt program bakom en standardbelastningsutjämnare kan du hoppa över det här steget.   
- Skapa en privat länktjänst som refererar till belastningsutjämnaren ovan. I processen för val av belastningsutjämnare väljer du den frontend IP-konfiguration där du vill ta emot trafiken. Välj ett undernät för NAT-IP-adresser för Private Link-tjänsten. Vi rekommenderar att du har minst åtta NAT IP-adresser tillgängliga i undernätet. All konsumenttrafik verkar komma från den här poolen med privata IP-adresser till tjänsteleverantören. Välj lämpliga egenskaper/inställningar för tjänsten Privat länk.    

    > [!NOTE]
    > Azure Private Link Service stöds endast på Standard Load Balancer. 
    
### <a name="share-your-service"></a>Dela din tjänst

När du har skapat en private link-tjänst genererar Azure en globalt unik namngiven moniker som heter "alias" baserat på namnet du anger för din tjänst. Du kan dela antingen alias eller resurs-URI för din tjänst med dina kunder offline. Konsumenter kan starta en Privat länkanslutning med alias eller resurs-URI.
 
### <a name="manage-your-connection-requests"></a>Hantera dina anslutningsbegäranden

När en konsument har initierat en anslutning kan tjänsteleverantören acceptera eller avvisa anslutningsbegäran. Alla anslutningsbegäranden visas under **egenskapen privateendpointconnections** på private link-tjänsten.
 
### <a name="delete-your-service"></a>Ta bort tjänsten

Om tjänsten Privat länk inte längre används kan du ta bort den. Men innan du tar bort tjänsten, se till att det inte finns några privata slutpunktsanslutningar associerade med den. Du kan avvisa alla anslutningar och ta bort tjänsten.

## <a name="properties"></a>Egenskaper

En private link-tjänst anger följande egenskaper: 

|Egenskap |Förklaring  |
|---------|---------|
|Etableringstillstånd (etableringstat)  |En skrivskyddad egenskap som visar det aktuella etableringstillståndet för tjänsten Private Link. Tillämpliga etableringstillstånd är: "Ta bort; Misslyckades; Lyckades; Uppdatering". När etableringstillståndet är "lyckades" har du etablerat din private link-tjänst.        |
|Alias (alias)     | Alias är en globalt unik skrivskyddad sträng för din tjänst. Det hjälper dig att maskera kunddata för din tjänst och samtidigt skapar ett lätt att dela namn för din tjänst. När du skapar en private link-tjänst genererar Azure aliaset för din tjänst som du kan dela med dina kunder. Dina kunder kan använda det här aliaset för att begära en anslutning till din tjänst.          |
|Synlighet (synlighet)     | Synlighet är den egenskap som styr exponeringsinställningarna för tjänsten Private Link. Tjänstleverantörer kan välja att begränsa exponeringen för sin tjänst till prenumerationer med rbac-behörigheter (Role-based Access Control), en begränsad uppsättning prenumerationer eller alla Azure-prenumerationer.          |
|Automatiskt godkännande (autoApproval)    |   Automatiskt godkännande styr den automatiska åtkomsten till private link-tjänsten. De prenumerationer som anges i listan över automatiskt godkännande godkänns automatiskt när en anslutning begärs från privata slutpunkter i dessa prenumerationer.          |
|IP-konfiguration för belastningsutjämning frontend (loadBalancerFrontendIpConfigurations)    |    Private Link-tjänsten är knuten till klientdelens IP-adress för en standardbelastningsutjämningsapparat. All trafik avsedd för tjänsten kommer att nå frontend av SLB. Du kan konfigurera SLB-regler för att dirigera den här trafiken till lämpliga serverdpooler där dina program körs. IP-konfigurationer för belastningsutjämnare skiljer sig från NAT IP-konfigurationer.      |
|NAT IP-konfiguration (ipkonfigureringar)    |    Den här egenskapen refererar till IP-konfigurationen för NAT (Network Address Translation) för tjänsten Private Link. NAT IP kan väljas från vilket undernät som helst i en tjänsteleverantörs virtuella nätverk. Private Link-tjänsten utför NAT-ning på den privata länktrafiken. Detta säkerställer att det inte finns någon IP-konflikt mellan källadressutrymme (konsumentsidan) och måladressutrymmet (tjänsteleverantör). På målsidan (tjänstprovidersidan) visas NAT IP-adressen som käll-IP för alla paket som tas emot av tjänsten och mål-IP för alla paket som skickas av din tjänst.       |
|Privata slutpunktsanslutningar (privateEndpointConnections)     |  Den här egenskapen visar de privata slutpunkter som ansluter till private link-tjänsten. Flera privata slutpunkter kan ansluta till samma Private Link-tjänst och tjänsteleverantören kan styra tillståndet för enskilda privata slutpunkter.        |
|TCP Proxy V2 (EnableProxyProtocol)     |  Med den här egenskapen kan tjänsteleverantören använda tcp proxy v2 för att hämta anslutningsinformation om tjänstekonsumenten. Tjänsteleverantör ansvarar för att ställa in mottagare configs att kunna tolka proxyprotokoll v2 header.        |
|||


### <a name="details"></a>Information

- Tjänsten Private Link kan nås från godkända privata slutpunkter i samma region. Den privata slutpunkten kan nås från samma virtuella nätverk, regionalt peered virtuella nätverk, globalt peered virtuella nätverk och lokalt med hjälp av privata VPN- eller ExpressRoute-anslutningar. 
 
- När du skapar en privat länktjänst skapas ett nätverksgränssnitt för resursens livscykel. Det här gränssnittet kan inte hanteras av kunden.
 
- Tjänsten För privat länk måste distribueras i samma region som det virtuella nätverket och standardbelastningsutjämningstjänsten.  
 
- En enda private link-tjänst kan nås från flera privata slutpunkter som tillhör olika virtuella nätverk, prenumerationer och/eller Active Directory-klienter. Anslutningen upprättas via ett anslutningsarbetsflöde. 
 
- Flera privata länktjänster kan skapas på samma standardbelastningsutjämnar med olika ip-konfigurationer. Det finns gränser för antalet privata länktjänster som du kan skapa per standardbelastningsutjämningstagare och per prenumeration. Mer information finns i [Azure-gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).
 
- Private Link-tjänsten kan ha mer än en NAT IP-konfiguration kopplad till den. Om du väljer mer än en NAT IP-konfiguration kan tjänstleverantörer skalas. Idag kan tjänsteleverantörer tilldela upp till åtta NAT IP-adresser per private link-tjänst. Med varje NAT IP-adress kan du tilldela fler portar för dina TCP-anslutningar och därmed skala ut. När du har lagt till flera NAT-IP-adresser i en private link-tjänst kan du inte ta bort NAT-IP-adresserna. Detta görs för att säkerställa att aktiva anslutningar inte påverkas när NAT-IP-adresserna tas bort.


## <a name="alias"></a>Alias

**Alias** är ett globalt unikt namn för din tjänst. Det hjälper dig att maskera kunddata för din tjänst och samtidigt skapar ett lätt att dela namn för din tjänst. När du skapar en Private Link-tjänst genererar Azure ett alias för din tjänst som du kan dela med dina kunder. Dina kunder kan använda det här aliaset för att begära en anslutning till din tjänst.

Aliaset består av tre delar: *Prefix*. *GUID*. *Suffix*

- Prefix är tjänstnamnet. Du kan välja ditt eget prefix. När "Alias" har skapats kan du inte ändra det, så välj prefixet på rätt sätt.  
- GUID kommer att tillhandahållas av plattformen. Detta bidrar till att göra namnet globalt unikt. 
- Suffix läggs till av Azure: *region*.azure.privatelinkservice 

Komplett alias: *Prefix*. {GUID}. *regionen*.azure.privatelinkservice  

## <a name="control-service-exposure"></a>Kontroll tjänst exponering

Private Link-tjänsten ger dig alternativ för att styra exponeringen av din tjänst genom inställningen "Synlighet". Du kan göra tjänsten privat för förbrukning från olika virtuella nätverk som du äger (endast RBAC-behörigheter), begränsa exponeringen för en begränsad uppsättning prenumerationer som du litar på eller göra den offentlig så att alla Azure-prenumerationer kan begära anslutningar på den privata länken Tjänst. Dina synlighetsinställningar avgör om en konsument kan ansluta till din tjänst eller inte. 

## <a name="control-service-access"></a>Åtkomst till kontrolltjänsten

Konsumenter som har exponering (kontrolleras av synlighetsinställningen) till din Private Link-tjänst kan skapa en privat slutpunkt i sina virtuella nätverk och begära en anslutning till din Private Link-tjänst. Den privata slutpunktsanslutningen skapas i ett "Väntande" tillstånd på tjänstobjektet Private Link. Tjänsteleverantören ansvarar för att agera på anslutningsbegäran. Du kan antingen godkänna anslutningen, avvisa anslutningen eller ta bort anslutningen. Endast anslutningar som är godkända kan skicka trafik till tjänsten Private Link.

Åtgärden att godkänna anslutningarna kan automatiseras med hjälp av egenskapen för automatiskt godkännande på tjänsten Private Link. Automatiskt godkännande är en möjlighet för tjänsteleverantörer att förhandsgodkänna en uppsättning prenumerationer för automatisk åtkomst till deras tjänst. Kunderna måste dela sina prenumerationer offline för att tjänsteleverantörer ska kunna lägga till i listan över automatiskt godkännande. Automatiskt godkännande är en delmängd av synlighetsmatrisen. Synligheten styr exponeringsinställningarna medan automatisk godkännande styr godkännandeinställningarna för tjänsten. Om en kund begär en anslutning från en prenumeration i listan över automatiskt godkännande godkänns anslutningen automatiskt och anslutningen upprättas. Tjänstleverantörer behöver inte godkänna begäran manuellt längre. Å andra sidan, om en kund begär en anslutning från en prenumeration i synlighetsmatrisen och inte i matrisen för automatiskt godkännande, kommer begäran att nå tjänsteleverantören, men tjänsteleverantören måste godkänna anslutningarna manuellt.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Hämta anslutningsinformation med TCP Proxy v2

När du använder privat länktjänst är käll-IP-adressen för paketen som kommer från den privata slutpunkten nätverksadress översätts (NAT) på tjänsteprovidersidan med hjälp av NAT IP som tilldelats från leverantörens virtuella nätverk. Därav programmen får den tilldelade NAT IP-adressen i stället för den faktiska käll-IP-adressen för tjänstekonsumenterna. Om ditt program behöver faktisk käll-IP-adress från konsumentsidan kan du aktivera Proxy-protokollet på tjänsten och hämta informationen från proxyprotokollhuvudet. Förutom källans IP-adress har proxyprotokollhuvudet också länk-ID för den privata slutpunkten. Kombination av käll-IP-adress och LinkID kan hjälpa tjänsteleverantörer att unikt identifiera sina konsumenter. För mer information om Proxy Protocol, besök [här](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Den här informationen kodas med hjälp av en anpassad TLV-vektor (Type-Length-Value) enligt följande:

Anpassade TLV-detaljer:

|Field |Längd (oktetter)  |Beskrivning  |
|---------|---------|----------|
|Typ  |1        |PP2_TYPE_AZURE (0xEE)|
|Längd  |2      |Värdelängd|
|Värde  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 byte) som representerar LINKID för den privata slutpunkten. Kodad i lite endian format.|

 > [!NOTE]
 > Tjänsteleverantören ansvarar för att tjänsten bakom standardbelastningsutjämnaren är konfigurerad för att tolka proxyprotokollhuvudet enligt [specifikationen](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) när proxyprotokollet är aktiverat på privat länktjänst. Begäran misslyckas om proxyprotokollinställningen är aktiverad på privata länktjänster, men tjänsteleverantörens tjänst är inte konfigurerad för att tolka huvudet. På samma sätt misslyckas begäran om tjänsteleverantörens tjänst förväntar sig ett proxyprotokollhuvud medan inställningen inte är aktiverad på den privata länktjänsten. När proxyprotokollinställningen är aktiverad inkluderas proxyprotokollhuvudet också i HTTP/TCP-hälsoavsökningar från värd till servernamns virtuella datorer, även om det inte kommer att finnas någon klientinformation i huvudet. 

## <a name="limitations"></a>Begränsningar

Följande är de kända begränsningarna när du använder tjänsten Private Link:
- Stöds endast på standardbelastningsutjämning 
- Stöder endast IPv4-trafik
- Stöder endast TCP-trafik

## <a name="next-steps"></a>Nästa steg
- [Skapa en privat länktjänst med Azure PowerShell](create-private-link-service-powershell.md)
- [Skapa en privat länktjänst med Azure CLI](create-private-link-service-cli.md)
