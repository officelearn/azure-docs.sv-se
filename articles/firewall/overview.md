---
title: Vad är Azure Firewall?
description: Läs mer om Azure Firewall-funktioner.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 09/4/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: c685b2314d15e431ccac3470fd337ca92697e1a5
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241167"
---
# <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillstånds känslig brand vägg som en tjänst med inbyggd hög tillgänglighet och obegränsad moln skalbarhet.

![Översikt över brandväggar](media/overview/firewall-threat.png)

Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för din virtuella nätverksresurser som tillåter att externa brandväggar identifierar trafik som kommer från ditt virtuella nätverk.  Tjänsten är helt integrerad med Azure Monitor för loggning och analys.

Azure Firewall erbjuder följande funktioner:

## <a name="built-in-high-availability"></a>Inbyggd hög tillgänglighet

Hög tillgänglighet är inbyggt, så inga ytterligare belastnings utjämning krävs och det finns inget behov av att konfigurera.

## <a name="availability-zones"></a>Tillgänglighetszoner

Azure-brandväggen kan konfigureras under distributionen för att omfatta flera Tillgänglighetszoner för ökad tillgänglighet. Med Tillgänglighetszoner ökar din tillgänglighet till 99,99% drift tid. Mer information finns i Azure Firewall [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). SLA för 99,99% drift tid erbjuds när två eller fler Tillgänglighetszoner har valts.

Du kan också associera Azure-brandväggen till en speciell zon, precis för närhets skäl, med service standard service avtalet för 99,95%.

Det kostar inget extra att distribuera en brand vägg i en tillgänglighets zon. Det finns dock ytterligare kostnader för inkommande och utgående data överföringar som är kopplade till Tillgänglighetszoner. Mer information finns i [pris information om bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure Firewall-Tillgänglighetszoner är tillgängliga i regioner som stöder Tillgänglighetszoner. Mer information finns i [Vad är Tillgänglighetszoner i Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Tillgänglighetszoner kan bara konfigureras under distributionen. Du kan inte konfigurera en befintlig brand vägg att inkludera Tillgänglighetszoner.

Mer information om Tillgänglighetszoner finns [i vad är Tillgänglighetszoner i Azure?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Obegränsad molnskalbarhet

Azure Firewall kan skala upp så mycket du behöver för att hantera föränderliga nätverkstrafikflöden, så du behöver inte budgetera för hög trafikbelastning.

## <a name="application-fqdn-filtering-rules"></a>Programmets FQDN-filtreringsregler

Du kan begränsa utgående HTTP/S-trafik eller Azure SQL-trafik (för hands version) till en angiven lista med fullständigt kvalificerade domän namn (FQDN), inklusive jokertecken. Den här funktionen kräver inte SSL-avslutning.

## <a name="network-traffic-filtering-rules"></a>Regler för filtrering av nätverkstrafik

Du kan centralt skapa nätverksfiltreringsreglerna *tillåt* eller *neka* efter källans och målets IP-adress, port och protokoll. Azure Firewall är helt tillståndskänslig så att den kan identifiera legitima paket för olika typer av anslutningar. Regler tillämpas och loggas i flera prenumerationer och virtuella nätverk.

## <a name="fqdn-tags"></a>Taggar för fullständigt domännamn

FQDN-taggar gör det enkelt att tillåta välkänd Azure-tjänstnätverkstrafik via brandväggen. Anta exempelvis att du vill tillåta Windows Update-nätverkstrafik via brandväggen. Du skapar en programregel och inkluderar Windows Update-taggen. Nätverkstrafik från Windows Update kan nu flöda genom brandväggen.

## <a name="service-tags"></a>Tjänsttaggar

En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa en egen service tag eller ange vilka IP-adresser som ska ingå i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

## <a name="threat-intelligence"></a>Hotinformation

Hotinformationsbaserad filtrering kan aktiveras för brandväggen för att avisera och avvisa trafik från/till kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-feeden.

## <a name="outbound-snat-support"></a>Stöd för utgående SNAT

Alla IP-adresser för utgående trafik över virtuellt nätverk översätts till den offentliga Azure Firewall-IP-adressen (Source Network Address Translation). Du kan identifiera och tillåta trafik som kommer från ditt virtuella nätverk till fjärranslutna Internetmål. Azure-brandväggen är inte SNAT när målets IP-adress är ett privat IP-intervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Om din organisation använder ett offentligt IP-adressintervall för privata nätverk, kommer Azure-brandväggen att besvara trafiken till någon av de privata IP-adresserna för brand väggen i AzureFirewallSubnet.

## <a name="inbound-dnat-support"></a>Stöd för inkommande DNAT

Inkommande nätverkstrafik till din brandväggs offentliga IP-adress översätts (Destination Network Address Translation) och filtreras till de privata IP-adresserna på dina virtuella nätverk.

## <a name="multiple-public-ip-addresses"></a>Flera offentliga IP-adresser

> [!IMPORTANT]
> Azure-brandväggen med flera offentliga IP-adresser är tillgänglig via Azure Portal, Azure PowerShell, Azure CLI, REST och mallar.


Du kan associera flera offentliga IP-adresser (upp till 100) med brand väggen.

Detta möjliggör följande scenarier:

- **DNAt** – du kan översätta flera standard port instanser till backend-servrarna. Om du till exempel har två offentliga IP-adresser kan du översätta TCP-port 3389 (RDP) för båda IP-adresserna.
- **SNAT** -ytterligare portar är tillgängliga för utgående SNAT-anslutningar, vilket minskar risken för SNAT-port överbelastning. För tillfället väljer Azure Firewall slumpmässigt den offentliga IP-adress som ska användas för en anslutning. Om du har filtrering av underordnade i nätverket måste du tillåta alla offentliga IP-adresser som är kopplade till din brand vägg.

## <a name="azure-monitor-logging"></a>Azure Monitor-loggning

Alla händelser är integrerade med Azure Monitor, vilket gör att du kan arkivera loggar till ett lagringskonto, strömma händelser till din händelsehubb eller skicka dem till Azure Monitor-loggar.

## <a name="pci-soc-and-iso-compliant"></a>PCI, SOC och ISO-kompatibel

Azure Firewall är ett PCI-kort (Payment Card Industry), SOC (Service Organization Controls) och Internationella standardiseringsorganisationen (ISO). För närvarande stöds SOC 1 typ 2, SOC 2, SOC 3, PCI DSS och ISO 27001, 27018, 20000-1, 22301, 9001, 27017.

Mer information finns i [Microsoft Compliance guide](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide).

## <a name="known-issues"></a>Kända problem

Azure Firewall har följande kända problem:

|Problem  |Beskrivning  |Åtgärd  |
|---------|---------|---------|
Nätverksfiltreringsregler för icke-TCP-/UDP-protokoll (till exempel ICMP) fungerar inte för Internetbunden trafik|Nätverksfiltreringsregler för icke-TCP-/UDP-protokoll fungerar inte med SNAT till din offentliga IP-adress. Icke-TCP-/UDP-protokoll stöds mellan ekerundernät och virtuella nätverk.|Azure Firewall använder Standard Load Balancer, [som för närvarande inte stöder SNAT för IP-protokoll](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Vi ska utforska alternativ för att stödja det här scenariot i en framtida version.|
|Saknat PowerShell- och CLI-stöd för ICMP|Azure PowerShell och CLI stöder inte ICMP som ett giltigt protokoll i nätverksregler.|Det går fortfarande att använda ICMP som protokoll via portalen och REST API. Vi arbetar snart med att lägga till ICMP i PowerShell och CLI.|
|FQDN-taggar kräver att protokoll: port anges|Program regler med FQDN-Taggar kräver port: protokoll definition.|Du kan använda **https** som port: protokoll-värde. Vi arbetar för att göra det här fältet valfritt när FQDN-Taggar används.|
|Det finns inte stöd för att flytta en brand vägg till en annan resurs grupp eller prenumeration|Det finns inte stöd för att flytta en brand vägg till en annan resurs grupp eller prenumeration.|Stöd för den här funktionen finns i vår planering. För att kunna flytta en brandvägg till en annan resursgrupp eller prenumeration måste du ta bort den aktuella instansen och återskapa den i den nya resursgruppen eller prenumerationen.|
|Portintervall i nätverk och regler|Portar som är begränsade till 64 000 eftersom höga portar är reserverade för hantering och diagnostiska sökningar. |Vi arbetar för att minska den här begränsningen.|
|Hot informations aviseringar kan komma att maskeras|Nätverks regler med destination 80/443 för utgående filtrering maskar hot informations aviseringar när de är konfigurerade för enbart aviserings läge.|Skapa utgående filtrering för 80/443 med hjälp av program regler. Eller så kan du ändra hot informations läge till **varning och neka**.|
|Azure-brandväggen använder Azure DNS endast för namn matchning|Azure-brandväggen löser FQDN: er med endast Azure DNS. En anpassad DNS-Server stöds inte. Det finns ingen inverkan på DNS-matchning på andra undernät.|Vi arbetar för att minska den här begränsningen.|
|Azure Firewall SNAT/DNAT fungerar inte för privata IP-mål|Azure Firewall SNAT/DNAT-supporten är begränsad till utgående/ingångar från Internet. SNAT/DNAT fungerar för närvarande inte för privata IP-mål. Till exempel eker till ekrar.|Detta är en aktuell begränsning.|
|Det går inte att ta bort den första offentliga IP-konfigurationen|Varje offentlig IP-adress för Azure Firewall tilldelas en *IP-konfiguration*.  Den första IP-konfigurationen tilldelas under brand Väggs distributionen och innehåller vanligt vis en referens till brand Väggs under nätet (om den inte uttryckligen har kon figurer ATS på annat sätt via en mall distribution). Du kan inte ta bort den här IP-konfigurationen eftersom den inte skulle allokera brand väggen. Du kan fortfarande ändra eller ta bort den offentliga IP-adressen som är associerad med den här IP-konfigurationen om brand väggen har minst en offentlig IP-adress som är tillgänglig för användning.|Det här är avsiktligt.|
|Tillgänglighets zoner kan bara konfigureras under distributionen.|Tillgänglighets zoner kan bara konfigureras under distributionen. Du kan inte konfigurera Tillgänglighetszoner när en brand vägg har distribuerats.|Det här är avsiktligt.|
|SNAT på inkommande anslutningar|Förutom DNAT är anslutningar via den offentliga brand Väggs adressen (inkommande) SNATed till en av brand väggens privata IP-adresser. Detta krav idag (även för aktiva/aktiva NVA) för att säkerställa symmetrisk routning.|Överväg att använda [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) -rubriker för att bevara den ursprungliga källan för http/S. Du kan till exempel använda en tjänst som till exempel [Azure-frontend](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) framför brand väggen. Du kan också lägga till WAF som en del av Azures frontend-dörr och-kedja i brand väggen.
|Stöd för SQL-FQDN-filtrering i proxyläge (port 1433)|För Azure SQL Database, Azure SQL Data Warehouse och Azure SQL-hanterad instans:<br><br>Under förhands granskningen stöds SQL-FQDN-filtrering endast i proxy-läge (port 1433).<br><br>För Azure SQL-IaaS:<br><br>Om du använder portar som inte är standard kan du ange dessa portar i program reglerna.|För SQL i omdirigeringsläge, som är standard om du ansluter inifrån Azure, kan du i stället Filtrera åtkomst med SQL-tjänstprogrammet som en del av nätverks reglerna i Azure Firewall.
|Utgående trafik på TCP-port 25 tillåts inte| Utgående SMTP-anslutningar som använder TCP-port 25 blockeras. Port 25 används främst för oautentiserad e-postleverans. Detta är standard beteendet för plattformen för virtuella datorer. Mer information finns i mer [fel sökning av utgående SMTP-anslutningsproblem i Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Till skillnad från virtuella datorer är det dock inte möjligt för tillfället att aktivera den här funktionen i Azure-brandväggen.|Följ den rekommenderade metoden för att skicka e-post enligt beskrivningen i artikeln om SMTP-felsökning. Du kan också undanta den virtuella datorn som behöver utgående SMTP-åtkomst från din standard väg till brand väggen, och i stället konfigurera utgående åtkomst direkt till Internet.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal](tutorial-firewall-deploy-portal.md)
- [Distribuera Azure Firewall med hjälp av en mall](deploy-template.md)
- [Skapa en testmiljö för Azure Firewall](scripts/sample-create-firewall-test.md)
