---
title: Vad är Azure Firewall?
description: Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 04/23/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 81adf005c2e39d9bb585c005f51ab3f89e4c8ff0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82209136"
---
# <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

![ICSA-certifiering](media/overview/icsa-cert-firewall-small.png)

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillståndskänslig tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet.

![Översikt över brandväggar](media/overview/firewall-threat.png)

Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för din virtuella nätverksresurser som tillåter att externa brandväggar identifierar trafik som kommer från ditt virtuella nätverk.  Tjänsten är helt integrerad med Azure Monitor för loggning och analys.

Azure Firewall erbjuder följande funktioner:

## <a name="built-in-high-availability"></a>Inbyggd hög tillgänglighet

Hög tillgänglighet är inbyggt, så inga ytterligare belastnings utjämning krävs och det finns inget behov av att konfigurera.

## <a name="availability-zones"></a>Tillgänglighetszoner

Azure-brandväggen kan konfigureras under distributionen för att omfatta flera Tillgänglighetszoner för ökad tillgänglighet. Med Tillgänglighetszoner ökar din tillgänglighet till 99,99% drift tid. Mer information finns i Azure Firewall [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). SLA för 99,99% drift tid erbjuds när två eller fler Tillgänglighetszoner har valts.

Du kan också associera Azure-brandväggen till en speciell zon, precis för närhets skäl, med service standard service avtalet för 99,95%.

Det kostar inget extra att distribuera en brand vägg i en tillgänglighets zon. Det finns dock ytterligare kostnader för inkommande och utgående data överföringar som är kopplade till Tillgänglighetszoner. Mer information finns i [pris information om bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure Firewall-Tillgänglighetszoner är tillgängliga i regioner som stöder Tillgänglighetszoner. Mer information finns i [regioner som stöder Tillgänglighetszoner i Azure](../availability-zones/az-region.md)

> [!NOTE]
> Tillgänglighetszoner kan bara konfigureras under distributionen. Du kan inte konfigurera en befintlig brand vägg att inkludera Tillgänglighetszoner.

Mer information om Tillgänglighetszoner finns [i regioner och Tillgänglighetszoner i Azure](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Obegränsad molnskalbarhet

Azure Firewall kan skala upp så mycket du behöver för att hantera föränderliga nätverkstrafikflöden, så du behöver inte budgetera för hög trafikbelastning.

## <a name="application-fqdn-filtering-rules"></a>Programmets FQDN-filtreringsregler

Du kan begränsa utgående HTTP/S-trafik eller Azure SQL-trafik (för hands version) till en angiven lista med fullständigt kvalificerade domän namn (FQDN), inklusive jokertecken. Den här funktionen kräver ingen TLS-avslutning.

## <a name="network-traffic-filtering-rules"></a>Regler för filtrering av nätverkstrafik

Du kan centralt skapa nätverksfiltreringsreglerna *tillåt* eller *neka* efter källans och målets IP-adress, port och protokoll. Azure Firewall är helt tillståndskänslig så att den kan identifiera legitima paket för olika typer av anslutningar. Regler tillämpas och loggas i flera prenumerationer och virtuella nätverk.

## <a name="fqdn-tags"></a>FQDN-taggar

FQDN-Taggar gör det enkelt för dig att tillåta en välkänd Azure-tjänstenätverks trafik genom brand väggen. Anta exempelvis att du vill tillåta Windows Update-nätverkstrafik via brandväggen. Du skapar en programregel och inkluderar Windows Update-taggen. Nätverkstrafik från Windows Update kan nu flöda genom brandväggen.

## <a name="service-tags"></a>Tjänsttaggar

En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa en egen service tag eller ange vilka IP-adresser som ska ingå i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

## <a name="threat-intelligence"></a>Hotinformation

Threat Intelligence-baserad filtrering kan aktive ras för brand väggen för att varna och neka trafik från/till kända skadliga IP-adresser och domäner. IP-adresserna och domänerna är källor från Microsoft Threat Intelligence-flödet.

## <a name="outbound-snat-support"></a>Stöd för utgående SNAT

Alla IP-adresser för utgående trafik över virtuellt nätverk översätts till den offentliga Azure Firewall-IP-adressen (Source Network Address Translation). Du kan identifiera och tillåta trafik som kommer från ditt virtuella nätverk till fjärranslutna Internetmål. Azure-brandväggen är inte SNAT när målets IP-adress är ett privat IP-intervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Om din organisation använder ett offentligt IP-adressintervall för privata nätverk, kommer Azure-brandväggen att besvara trafiken till någon av de privata IP-adresserna för brand väggen i AzureFirewallSubnet. Du kan konfigurera Azure-brandväggen så att den **inte är** SNAT för ditt offentliga IP-adressintervall. Mer information finns i [Azure FIREWALL SNAT privata IP-adressintervall](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Stöd för inkommande DNAT

Inkommande Internet nätverks trafik till din offentliga brand Väggs IP-adress är översatt (målets nätverks adress översättning) och filtreras till de privata IP-adresserna i dina virtuella nätverk.

## <a name="multiple-public-ip-addresses"></a>Flera offentliga IP-adresser

Du kan associera flera offentliga IP-adresser (upp till 100) med brand väggen.

Detta möjliggör följande scenarier:

- **DNAt** – du kan översätta flera standard port instanser till backend-servrarna. Om du till exempel har två offentliga IP-adresser kan du översätta TCP-port 3389 (RDP) för båda IP-adresserna.
- **SNAT** -ytterligare portar är tillgängliga för utgående SNAT-anslutningar, vilket minskar risken för SNAT-port överbelastning. För tillfället väljer Azure Firewall slumpmässigt den offentliga IP-adress som ska användas för en anslutning. Om du har filtrering av underordnade i nätverket måste du tillåta alla offentliga IP-adresser som är kopplade till din brand vägg.

## <a name="azure-monitor-logging"></a>Azure Monitor-loggning

Alla händelser är integrerade med Azure Monitor, vilket gör att du kan arkivera loggar till ett lagringskonto, strömma händelser till din händelsehubb eller skicka dem till Azure Monitor-loggar.

## <a name="certifications"></a>Certifieringar

Azure Firewall är ett PCI-kort (Payment Card Industry), Service Organization Controls (SOC), Internationella standardiseringsorganisationen (ISO) och ICSA Labs-kompatibel. Mer information finns i [certifieringar för Azure Firewall-kompatibilitet](compliance-certifications.md).


## <a name="known-issues"></a>Kända problem

Azure Firewall har följande kända problem:

|Problem  |Beskrivning  |Åtgärd  |
|---------|---------|---------|
Nätverksfiltreringsregler för icke-TCP-/UDP-protokoll (till exempel ICMP) fungerar inte för Internetbunden trafik|Nätverks filtrerings regler för icke-TCP/UDP-protokoll fungerar inte med SNAT till din offentliga IP-adress. Icke-TCP-/UDP-protokoll stöds mellan ekerundernät och virtuella nätverk.|Azure Firewall använder Standard Load Balancer, [som för närvarande inte stöder SNAT för IP-protokoll](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Vi ska utforska alternativ för att stödja det här scenariot i en framtida version.|
|Saknat PowerShell- och CLI-stöd för ICMP|Azure PowerShell och CLI stöder inte ICMP som ett giltigt protokoll i nätverks regler.|Det går fortfarande att använda ICMP som protokoll via portalen och REST API. Vi arbetar snart med att lägga till ICMP i PowerShell och CLI.|
|FQDN-taggar kräver att protokoll: port anges|Program regler med FQDN-Taggar kräver port: protokoll definition.|Du kan använda **https** som port: protokoll-värde. Vi arbetar för att göra det här fältet valfritt när FQDN-Taggar används.|
|Det finns inte stöd för att flytta en brand vägg till en annan resurs grupp eller prenumeration|Det finns inte stöd för att flytta en brand vägg till en annan resurs grupp eller prenumeration.|Stöd för den här funktionen finns i vår planering. För att kunna flytta en brandvägg till en annan resursgrupp eller prenumeration måste du ta bort den aktuella instansen och återskapa den i den nya resursgruppen eller prenumerationen.|
|Hot informations aviseringar kan komma att maskeras|Nätverks regler med destination 80/443 för utgående filtrering maskar hot informations aviseringar när de är konfigurerade för enbart aviserings läge.|Skapa utgående filtrering för 80/443 med hjälp av program regler. Eller så kan du ändra hot informations läge till **varning och neka**.|
|Azure-brandväggen använder Azure DNS endast för namn matchning|Azure-brandväggen löser FQDN: er med endast Azure DNS. En anpassad DNS-Server stöds inte. Det finns ingen inverkan på DNS-matchning på andra undernät.|Vi arbetar för att minska den här begränsningen.|
|Azure Firewall-DNAT fungerar inte för privata IP-mål|Azure Firewall DNAT-stöd är begränsat till utgående/ingångs händelser på Internet. DNAT fungerar för närvarande inte för privata IP-mål. Till exempel eker till ekrar.|Detta är en aktuell begränsning.|
|Det går inte att ta bort den första offentliga IP-konfigurationen|Varje offentlig IP-adress för Azure Firewall tilldelas en *IP-konfiguration*.  Den första IP-konfigurationen tilldelas under brand Väggs distributionen och innehåller vanligt vis en referens till brand Väggs under nätet (om den inte uttryckligen har kon figurer ATS på annat sätt via en mall distribution). Du kan inte ta bort den här IP-konfigurationen eftersom den inte skulle allokera brand väggen. Du kan fortfarande ändra eller ta bort den offentliga IP-adressen som är associerad med den här IP-konfigurationen om brand väggen har minst en offentlig IP-adress som är tillgänglig för användning.|Det här är avsiktligt.|
|Tillgänglighets zoner kan bara konfigureras under distributionen.|Tillgänglighets zoner kan bara konfigureras under distributionen. Du kan inte konfigurera Tillgänglighetszoner när en brand vägg har distribuerats.|Det här är avsiktligt.|
|SNAT på inkommande anslutningar|Förutom DNAT är anslutningar via den offentliga brand Väggs adressen (inkommande) SNATed till en av brand väggens privata IP-adresser. Detta krav idag (även för aktiva/aktiva NVA) för att säkerställa symmetrisk routning.|Överväg att använda [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) -rubriker för att bevara den ursprungliga källan för http/S. Du kan till exempel använda en tjänst som till exempel [Azure-frontend](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) eller [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) framför brand väggen. Du kan också lägga till WAF som en del av Azures frontend-dörr och-kedja i brand väggen.
|Stöd för SQL-FQDN-filtrering i proxyläge (port 1433)|För Azure SQL Database, Azure SQL Data Warehouse och Azure SQL-hanterad instans:<br><br>Under förhands granskningen stöds SQL-FQDN-filtrering endast i proxy-läge (port 1433).<br><br>För Azure SQL-IaaS:<br><br>Om du använder portar som inte är standard kan du ange dessa portar i program reglerna.|För SQL i omdirigeringsläge (standard om du ansluter inifrån Azure) kan du i stället Filtrera åtkomst med SQL-tjänstprogrammet som en del av nätverks reglerna i Azure Firewall.
|Utgående trafik på TCP-port 25 tillåts inte| Utgående SMTP-anslutningar som använder TCP-port 25 blockeras. Port 25 används främst för oautentiserad e-postleverans. Detta är standard beteendet för plattformen för virtuella datorer. Mer information finns i mer [fel sökning av utgående SMTP-anslutningsproblem i Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Till skillnad från virtuella datorer är det dock inte möjligt för tillfället att aktivera den här funktionen i Azure-brandväggen. Obs! Om du vill tillåta autentiserad SMTP (port 587) eller SMTP över en annan port än 25, se till att du konfigurerar en nätverks regel och inte en program regel eftersom SMTP-kontrollen inte stöds för tillfället.|Följ den rekommenderade metoden för att skicka e-post, enligt beskrivningen i artikeln SMTP-felsökning. Eller utelämna den virtuella datorn som behöver utgående SMTP-åtkomst från din standard väg till brand väggen. Konfigurera i stället utgående åtkomst direkt till Internet.
|Active FTP stöds inte|Active FTP är inaktiverat på Azure-brandväggen för att skydda mot FTP-attacker med kommandot FTP-PORT.|Du kan använda passiv FTP i stället. Du måste fortfarande öppna TCP-portarna 20 och 21 i brand väggen.
|Mått för SNAT-port användning visar 0%|Måttet för Azure Firewall SNAT-port användning kan visa 0% användning även när SNAT-portar används. I det här fallet är det ett felaktigt resultat om du använder måttet som en del av hälso måttet för brand väggen.|Det här problemet har åtgärd ATS och distributionen till produktion är avsedd för maj 2020. I vissa fall löser brand Väggs distributionen problemet, men det är inte konsekvent. Som en mellanliggande lösning använder du endast brand väggens hälso tillstånd för att söka efter *status = degraderad*, inte för *status = ej felfri*. Port överbelastning visas som *degraderad*. *Inte felfri* är reserverad för framtida användning när är fler mått för att påverka brand väggens hälsa.
|DNAT stöds inte med Tvingad tunnel trafik aktive rad|Brand väggar som distribueras med Tvingad tunnel trafik aktive rad kan inte stödja inkommande åtkomst från Internet på grund av asymmetrisk routning.|Detta är avsiktligt på grund av asymmetrisk routning. Retur Sök vägen för inkommande anslutningar skickas via den lokala brand väggen, vilket inte visade anslutningen.
|Utgående passiv FTP fungerar inte för brand väggar med flera offentliga IP-adresser.|Passiv FTP upprättar olika anslutningar för kontroll-och data kanaler. När en brand vägg med flera offentliga IP-adresser skickar data utgående väljer den slumpmässigt en av dess offentliga IP-adresser för käll-IP-adressen. FTP fungerar inte när data-och kontroll kanaler använder olika käll-IP-adresser.|En explicit SNAT-konfiguration planeras. Under tiden kan du överväga att använda en enda IP-adress i den här situationen.|
|NetworkRuleHit-måttet saknar en protokoll dimension|ApplicationRuleHit-måttet tillåter filtrering baserat protokoll, men den här funktionen saknas i motsvarande NetworkRuleHit-mått.|En korrigering undersökas.|
|Konfigurations uppdateringar kan ta fem minuter i genomsnitt.|En konfigurations uppdatering för Azure Firewall kan ta tre till fem minuter i genomsnitt, och parallella uppdateringar stöds inte.|En korrigering undersökas.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal](tutorial-firewall-deploy-portal.md)
- [Distribuera Azure Firewall med hjälp av en mall](deploy-template.md)
- [Skapa en testmiljö för Azure Firewall](scripts/sample-create-firewall-test.md)
