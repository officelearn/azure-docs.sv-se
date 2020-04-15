---
title: Vad är Azure Firewall?
description: Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 04/08/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: bb4b654bd0b3591ebaa1bd217020095319a4938c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381918"
---
# <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

![ICSA-certifiering](media/overview/icsa-cert-firewall-small.png)

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillståndskänslig tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet.

![Översikt över brandväggar](media/overview/firewall-threat.png)

Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för din virtuella nätverksresurser som tillåter att externa brandväggar identifierar trafik som kommer från ditt virtuella nätverk.  Tjänsten är helt integrerad med Azure Monitor för loggning och analys.

Azure Firewall erbjuder följande funktioner:

## <a name="built-in-high-availability"></a>Inbyggd hög tillgänglighet

Hög tillgänglighet är inbyggd, så inga ytterligare belastningsutjämnare krävs och det finns inget du behöver konfigurera.

## <a name="availability-zones"></a>Tillgänglighetszoner

Azure-brandväggen kan konfigureras under distributionen för att sträcka sig över flera tillgänglighetszoner för ökad tillgänglighet. Med tillgänglighetszoner ökar tillgängligheten till 99,99 % drifttid. Mer information finns i [Service Level Agreement (SLA) (Azure](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)Firewall Service Level Agreement). Serviceavtalet på 99,99 % drifttid erbjuds när två eller flera tillgänglighetszoner väljs.

Du kan också associera Azure-brandväggen till en viss zon bara av närhetsskäl med hjälp av servicestandarden 99,95 % SLA.

Det finns ingen extra kostnad för en brandvägg som distribueras i en tillgänglighetszon. Det finns dock extra kostnader för inkommande och utgående dataöverföringar som är associerade med tillgänglighetszoner. Mer information finns i [Information om bandbreddspriser](https://azure.microsoft.com/pricing/details/bandwidth/).

Tillgänglighetszoner för Azure-brandväggen är tillgängliga i regioner som stöder tillgänglighetszoner. Mer information finns [i Vad är tillgänglighetszoner i Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Tillgänglighetszoner kan bara konfigureras under distributionen. Du kan inte konfigurera en befintlig brandvägg så att den innehåller tillgänglighetszoner.

Mer information om tillgänglighetszoner finns [i Vad är tillgänglighetszoner i Azure?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Obegränsad molnskalbarhet

Azure Firewall kan skala upp så mycket du behöver för att hantera föränderliga nätverkstrafikflöden, så du behöver inte budgetera för hög trafikbelastning.

## <a name="application-fqdn-filtering-rules"></a>Programmets FQDN-filtreringsregler

Du kan begränsa utgående HTTP/S-trafik eller Azure SQL-trafik (förhandsversion) till en angiven lista över fullständigt kvalificerade domännamn (FQDN) inklusive jokertecken. Den här funktionen kräver inte TLS-avslutning.

## <a name="network-traffic-filtering-rules"></a>Regler för filtrering av nätverkstrafik

Du kan centralt skapa nätverksfiltreringsreglerna *tillåt* eller *neka* efter källans och målets IP-adress, port och protokoll. Azure Firewall är helt tillståndskänslig så att den kan identifiera legitima paket för olika typer av anslutningar. Regler tillämpas och loggas i flera prenumerationer och virtuella nätverk.

## <a name="fqdn-tags"></a>FQDN-taggar

FQDN-taggar gör det enkelt för dig att tillåta välkänd Azure-tjänst nätverkstrafik genom brandväggen. Anta exempelvis att du vill tillåta Windows Update-nätverkstrafik via brandväggen. Du skapar en programregel och inkluderar Windows Update-taggen. Nätverkstrafik från Windows Update kan nu flöda genom brandväggen.

## <a name="service-tags"></a>Tjänsttaggar

En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa ett eget servicetag eller ange vilka IP-adresser som ska ingå i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

## <a name="threat-intelligence"></a>Hotinformation

Hot intelligens-baserad filtrering kan aktiveras för brandväggen att varna och neka trafik från / till kända skadliga IP-adresser och domäner. IP-adresser och domäner kommer från Microsoft Threat Intelligence-feeden.

## <a name="outbound-snat-support"></a>Stöd för utgående SNAT

Alla IP-adresser för utgående trafik över virtuellt nätverk översätts till den offentliga Azure Firewall-IP-adressen (Source Network Address Translation). Du kan identifiera och tillåta trafik som kommer från ditt virtuella nätverk till fjärranslutna Internetmål. Azure-brandväggen snat inte när mål-IP är ett privat IP-intervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Om din organisation använder ett offentligt IP-adressintervall för privata nätverk kommer Azure Firewall att SNAT-trafiken till en av brandväggens privata IP-adresser i AzureFirewallSubnet. Du kan konfigurera Azure-brandväggen så att **den inte** är ditt offentliga IP-adressintervall. Mer information finns i [Azure Firewall SNAT privata IP-adressintervall](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Stöd för inkommande DNAT

Inkommande Internet-nätverkstrafik till brandväggens offentliga IP-adress översätts (Destination Network Address Translation) och filtreras till privata IP-adresser i dina virtuella nätverk.

## <a name="multiple-public-ip-addresses"></a>Flera offentliga IP-adresser

Du kan associera flera offentliga IP-adresser (upp till 100) med brandväggen.

Detta möjliggör följande scenarier:

- **DNAT** - Du kan översätta flera standardportinstanser till dina serverdservrar. Om du till exempel har två offentliga IP-adresser kan du översätta TCP-port 3389 (RDP) för båda IP-adresserna.
- **SNAT** - Ytterligare portar är tillgängliga för utgående SNAT-anslutningar, vilket minskar risken för SNAT-portutmattning. För närvarande väljer Azure Firewall slumpmässigt den offentliga käll-IP-adress som ska användas för en anslutning. Om du har någon nedströmsfiltrering i nätverket måste du tillåta alla offentliga IP-adresser som är associerade med brandväggen.

## <a name="azure-monitor-logging"></a>Azure Monitor-loggning

Alla händelser är integrerade med Azure Monitor, vilket gör att du kan arkivera loggar till ett lagringskonto, strömma händelser till din händelsehubb eller skicka dem till Azure Monitor-loggar.

## <a name="certifications"></a>Certifieringar

Azure Firewall är PCI (Payment Card Industry), Service Organization Controls (SOC), International Organization for Standardization (ISO) och ICSA Labs-kompatibel. Mer information finns i [Azure Firewall compliance certifications](compliance-certifications.md).


## <a name="known-issues"></a>Kända problem

Azure Firewall har följande kända problem:

|Problem  |Beskrivning  |Åtgärd  |
|---------|---------|---------|
Nätverksfiltreringsregler för icke-TCP-/UDP-protokoll (till exempel ICMP) fungerar inte för Internetbunden trafik|Nätverksfiltreringsregler för protokoll som inte är TCP/UDP fungerar inte med SNAT till din offentliga IP-adress. Icke-TCP-/UDP-protokoll stöds mellan ekerundernät och virtuella nätverk.|Azure Firewall använder Standard Load Balancer, [som för närvarande inte stöder SNAT för IP-protokoll](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Vi undersöker alternativ för att stödja det här scenariot i en framtida version.|
|Saknat PowerShell- och CLI-stöd för ICMP|Azure PowerShell och CLI stöder inte ICMP som ett giltigt protokoll i nätverksregler.|Det är fortfarande möjligt att använda ICMP som ett protokoll via portalen och REST API. Vi arbetar för att lägga till ICMP i PowerShell och CLI snart.|
|FQDN-taggar kräver att protokoll: port anges|Programregler med FQDN-taggar kräver port: protokolldefinition.|Du kan använda **https** som port: protokoll-värde. Vi arbetar för att göra det här fältet valfritt när FQDN-taggar används.|
|Det går inte att flytta en brandvägg till en annan resursgrupp eller prenumeration|Det går inte att flytta en brandvägg till en annan resursgrupp eller prenumeration.|Stöd för den här funktionen finns i vår planering. För att kunna flytta en brandvägg till en annan resursgrupp eller prenumeration måste du ta bort den aktuella instansen och återskapa den i den nya resursgruppen eller prenumerationen.|
|Hotinformationsvarningar kan bli maskerade|Nätverksregler med mål 80/443 för utgående filtrering masker hot intelligens varningar när konfigureras för att varna bara läge.|Skapa utgående filtrering för 80/443 med hjälp av programregler. Eller ändra hotinformationsläget till **Alert and Deny**.|
|Azure-brandväggen använder Azure DNS endast för namnmatchning|Azure-brandväggen matchar FQDN endast med Azure DNS. En anpassad DNS-server stöds inte. Dns-upplösningen påverkas inte på andra undernät.|Vi arbetar för att slappna av denna begränsning.|
|Azure Firewall SNAT/DNAT fungerar inte för privata IP-destinationer|Azure Firewall SNAT/DNAT-stöd är begränsat till Internet-utgående/ingående. SNAT/DNAT fungerar för närvarande inte för privata IP-destinationer. Talade till exempel med.|Detta är en aktuell begränsning.|
|Det går inte att ta bort den första offentliga IP-konfigurationen|Varje offentlig IP-adress för Azure-brandväggen tilldelas en *IP-konfiguration*.  Den första IP-konfigurationen tilldelas under brandväggsdistributionen och innehåller vanligtvis också en referens till brandväggsundernätet (såvida den inte konfigureras uttryckligen på olika sätt via en malldistribution). Du kan inte ta bort den här IP-konfigurationen eftersom brandväggen skulle avallokeras. Du kan fortfarande ändra eller ta bort den offentliga IP-adress som är associerad med den här IP-konfigurationen om brandväggen har minst en annan offentlig IP-adress tillgänglig att använda.|Det här är avsiktligt.|
|Tillgänglighetszoner kan bara konfigureras under distributionen.|Tillgänglighetszoner kan bara konfigureras under distributionen. Du kan inte konfigurera tillgänglighetszoner när en brandvägg har distribuerats.|Det här är avsiktligt.|
|SNAT på inkommande anslutningar|Förutom DNAT, anslutningar via brandväggen offentliga IP-adress (inkommande) är SNATed till en av brandväggen privata IP-adresser. Detta krav idag (även för aktiva/aktiva NVAs) för att säkerställa symmetrisk routing.|Om du vill bevara den ursprungliga källan för HTTP/S bör du överväga att använda [XFF-huvuden.](https://en.wikipedia.org/wiki/X-Forwarded-For) Använd till exempel en tjänst som [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) eller Azure Application [Gateway](../application-gateway/rewrite-http-headers.md) framför brandväggen. Du kan också lägga till WAF som en del av Azure Front Door och kedja till brandväggen.
|Stöd för SQL FQDN-filtrering endast i proxyläge (port 1433)|För Azure SQL Database, Azure SQL Data Warehouse och Azure SQL Managed Instance:<br><br>Under förhandsgranskningen stöds SQL FQDN-filtrering endast i proxyläge (port 1433).<br><br>För Azure SQL IaaS:<br><br>Om du använder portar som inte är standard kan du ange dessa portar i programreglerna.|För SQL i omdirigeringsläge (standard om du ansluter från Azure) kan du i stället filtrera åtkomst med SQL-tjänsttaggen som en del av Azure Firewall-nätverksregler.
|Utgående trafik på TCP-port 25 är inte tillåtet| Utgående SMTP-anslutningar som använder TCP-port 25 är blockerade. Port 25 används främst för oautentiserade e-postleverans. Detta är standardplattformsbeteendet för virtuella datorer. Mer information finns i fler [felsökningsproblem för utgående SMTP-anslutning i Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Till skillnad från virtuella datorer är det dock för närvarande inte möjligt att aktivera den här funktionen i Azure-brandväggen. Om du vill tillåta autentiserade SMTP (port 587) eller SMTP över en annan port än 25, se till att du konfigurerar en nätverksregel och att inte en programregel eftersom SMTP-inspektion inte stöds just nu.|Följ den rekommenderade metoden för att skicka e-post, som dokumenteras i felsökningsartikeln för SMTP. Du kan också utesluta den virtuella datorn som behöver utgående SMTP-åtkomst från standardvägen till brandväggen. Konfigurera i stället utgående åtkomst direkt till Internet.
|Aktiv FTP stöds inte|Aktiv FTP är inaktiverad på Azure-brandväggen för att skydda mot FTP-avvisningsattacker med kommandot FTP PORT.|Du kan använda Passiv FTP i stället. Du måste fortfarande uttryckligen öppna TCP-portar 20 och 21 på brandväggen.
|SNAT-portutnyttjandemått visar 0%|Azure Firewall SNAT-portutnyttjandemått kan visa 0% användning även när SNAT-portar används. I det här fallet ger användning av måttet som en del av brandväggens hälsomått ett felaktigt resultat.|Problemet har åtgärdats och utbyggnaden av produktionen är avsedd för maj 2020. I vissa fall löser brandväggsomfördelningen problemet, men det är inte konsekvent. Som en mellanliggande lösning använder du bara brandväggens hälsotillstånd för att leta efter *status=försämrad*, inte för *status=felfritt*. Portutmattning visas som *degraderad*. *Inte felfri* är reserverad för framtida användning när det finns fler mått för att påverka brandväggens hälsa.
|DNAT stöds inte med Forcerad tunnelaktiverad|Brandväggar som distribueras med forcerad tunnelkoppling aktiverad kan inte stödja inkommande åtkomst från Internet på grund av asymmetrisk routning.|Detta är avsiktligt på grund av asymmetrisk routning. Retursökvägen för inkommande anslutningar går via den lokala brandväggen, som inte har sett anslutningen upprättas.
|Utgående passiv FTP fungerar inte för brandväggar med flera offentliga IP-adresser.|Passiv FTP upprättar olika anslutningar för kontroll- och datakanaler. När en brandvägg med flera offentliga IP-adresser skickar data utgående väljer den slumpmässigt en av sina offentliga IP-adresser för källans IP-adress. FTP misslyckas när data- och kontrollkanaler använder olika käll-IP-adresser.|En explicit SNAT-konfiguration planeras. Under tiden bör du överväga att använda en enda IP-adress i den här situationen.|
|NetworkRuleHit-måttet saknar en protokolldimension|Mätvärden ApplicationRuleHit tillåter filtreringsbaserat protokoll, men den här funktionen saknas i motsvarande NetworkRuleHit-mått.|En fix utreds.|
|Konfigurationsuppdateringar kan ta i genomsnitt fem minuter.|En konfigurationsuppdatering för Azure-brandväggen kan ta i genomsnitt tre till fem minuter och parallella uppdateringar stöds inte.|En fix utreds.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal](tutorial-firewall-deploy-portal.md)
- [Distribuera Azure Firewall med hjälp av en mall](deploy-template.md)
- [Skapa en testmiljö för Azure Firewall](scripts/sample-create-firewall-test.md)
