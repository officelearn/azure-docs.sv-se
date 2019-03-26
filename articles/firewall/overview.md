---
title: Vad är Azure Firewall?
description: Läs mer om Azure Firewall-funktioner.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 3/25/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: be490299d09e396e4bc589ebf777f64ce084d320
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418728"
---
# <a name="what-is-azure-firewall"></a>Vad är Azure Firewall?

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en helt tillståndskänslig brandvägg som en tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet. 

![Översikt över brandväggar](media/overview/firewall-threat.png)

Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för din virtuella nätverksresurser som tillåter att externa brandväggar identifierar trafik som kommer från ditt virtuella nätverk.  Tjänsten är helt integrerad med Azure Monitor för loggning och analys.

## <a name="features"></a>Funktioner

Azure Firewall erbjuder följande funktioner:

### <a name="built-in-high-availability"></a>Inbyggd hög tillgänglighet

Hög tillgänglighet är inbyggt, så det krävs inga ytterligare lastbalanserare och du behöver inte konfigurera något.

### <a name="unrestricted-cloud-scalability"></a>Obegränsad molnskalbarhet

Azure Firewall kan skala upp så mycket du behöver för att hantera föränderliga nätverkstrafikflöden, så du behöver inte budgetera för hög trafikbelastning.

### <a name="application-fqdn-filtering-rules"></a>Programmets FQDN-filtreringsregler

Du kan begränsa utgående HTTP/S-trafik till en angiven lista med fullständigt kvalificerade domännamn (FQDN), inklusive jokertecken. Den här funktionen kräver inte SSL-avslutning.

### <a name="network-traffic-filtering-rules"></a>Regler för filtrering av nätverkstrafik

Du kan centralt skapa nätverksfiltreringsreglerna *tillåt* eller *neka* efter källans och målets IP-adress, port och protokoll. Azure Firewall är helt tillståndskänslig så att den kan identifiera legitima paket för olika typer av anslutningar. Regler tillämpas och loggas i flera prenumerationer och virtuella nätverk.

### <a name="fqdn-tags"></a>FQDN-taggar

FQDN-taggar gör det enkelt att tillåta välkänd Azure-tjänstnätverkstrafik via brandväggen. Anta exempelvis att du vill tillåta Windows Update-nätverkstrafik via brandväggen. Du skapar en programregel och inkluderar Windows Update-taggen. Nätverkstrafik från Windows Update kan nu flöda genom brandväggen.

### <a name="service-tags"></a>Tjänsttaggar

En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa egna tjänsttaggar, och du kan inte heller ange vilka IP-adresser som ska finnas i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

### <a name="threat-intelligence"></a>Hotinformation

Threat intelligence-baserad filtrering kan aktiveras för brandväggen för att Avisera och nekar trafik från/till känd skadlig IP-adresser och domäner. IP-adresser och domäner kommer från Microsoft Hotinformationen feed.

### <a name="outbound-snat-support"></a>Stöd för utgående SNAT

Alla IP-adresser för utgående trafik över virtuellt nätverk översätts till den offentliga Azure Firewall-IP-adressen (Source Network Address Translation). Du kan identifiera och tillåta trafik som kommer från ditt virtuella nätverk till fjärranslutna Internetmål.

### <a name="inbound-dnat-support"></a>Stöd för inkommande DNAT

Inkommande nätverkstrafik till din brandväggs offentliga IP-adress översätts (Destination Network Address Translation) och filtreras till de privata IP-adresserna på dina virtuella nätverk. 

### <a name="azure-monitor-logging"></a>Azure Monitor-loggning

Alla händelser är integrerade med Azure Monitor, vilket gör att du kan arkivera loggar till ett lagringskonto, strömma händelser till din händelsehubb eller skicka dem till Azure Monitor-loggar.

## <a name="known-issues"></a>Kända problem

Azure Firewall har följande kända problem:

|Problem  |Beskrivning  |Åtgärd  |
|---------|---------|---------|
|Konflikt med Azure Security Center (ASC) Just-in-Time-funktionen (JIT)|Om åtkomst till en virtuell dator sker via JIT, och den är i ett undernät med en användardefinierad väg som pekar på Azure Firewall som en standard-gateway, fungerar inte ASC JIT. Det här är ett resultat av asymmetrisk routning – ett paket kommer in via den virtuella datorn offentliga IP-Adressen (JIT öppnas åtkomst), men den returnera sökvägen är via brandväggen som släpper paketet eftersom det finns inga upprättad session i brandväggen.|Du kan kringgå problemet genom att placera de virtuella JIT-datorerna på ett separat undernät som inte har en användardefinierad väg till brandväggen.|
Nätverksfiltreringsregler för icke-TCP-/UDP-protokoll (till exempel ICMP) fungerar inte för Internetbunden trafik|Nätverksfiltreringsregler för icke-TCP-/UDP-protokoll fungerar inte med SNAT till din offentliga IP-adress. Icke-TCP-/UDP-protokoll stöds mellan ekerundernät och virtuella nätverk.|Azure Firewall använder Standard Load Balancer, [som för närvarande inte stöder SNAT för IP-protokoll](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Vi utforskar alternativ för att stödja det här scenariot i en framtida version.|
|Saknat PowerShell- och CLI-stöd för ICMP|Azure PowerShell och CLI stöder inte ICMP som ett giltigt protokoll i nätverksregler.|Du kan fortfarande använda ICMP som protokoll via portalen och REST API. Vi jobbar på att lägga till ICMP i PowerShell och CLI snart.|
|FQDN-taggar kräver att protokoll: port anges|Programregler med FQDN-taggar kräver port: protokoll-definition.|Du kan använda **https** som port: protokoll-värde. Vi jobbar på att göra det här fältet valfritt när FQDN-taggar används.|
|Det går inte att flytta en brandvägg till en annan resursgrupp eller prenumeration|Det går inte att flytta en brandvägg till en annan resursgrupp eller prenumeration.|Stöd för den här funktionen finns i vår planering. För att kunna flytta en brandvägg till en annan resursgrupp eller prenumeration måste du ta bort den aktuella instansen och återskapa den i den nya resursgruppen eller prenumerationen.|
|Portintervall i nätverk och regler|Portar som är begränsade till 64 000 eftersom höga portar är reserverade för hantering och diagnostiska sökningar. |Vi arbetar för att minska den här begränsningen.|
|Threat intelligence aviseringar kan hämta maskeras|Nätverksregler med mål 80/443 för utgående filtrering masker hot intelligence aviseringar när konfigurerad för att aviseringen läge.|Skapa utgående filtrering för 80/443 med hjälp av regler för program. Eller ändra threat intelligence läge till **Avisera och neka**.|

## <a name="next-steps"></a>Nästa steg

- [Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal](tutorial-firewall-deploy-portal.md)
- [Distribuera Azure Firewall med hjälp av en mall](deploy-template.md)
- [Skapa en testmiljö för Azure Firewall](scripts/sample-create-firewall-test.md)