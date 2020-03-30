---
title: Azure Internet Analyzer | Microsoft-dokument
description: Läs mer om Azure Internet Analyzer
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 3d9d1240e31d40a3d9c75d56314acc1721d90b99
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73501948"
---
# <a name="what-is-internet-analyzer-preview"></a>Vad är Internet Analyzer? (Förhandsversion)

Internet Analyzer är en mätplattform på klientsidan för att testa hur nätverksinfrastrukturförändringar påverkar kundernas prestanda. Oavsett om du migrerar från lokala resurser till Azure eller om du utvärderar en ny Azure-tjänst kan du med Internet Analyzer få information från dina användares data och Microsofts omfattande analyser för att bättre förstå och optimera din nätverksarkitektur med Azure – innan du migrerar.

Internet Analyzer använder en liten JavaScript-klient inbäddad i webbprogrammet för att mäta svarstiden från dina slutanvändare till din valda uppsättning nätverksdestinationer, vi _anropar slutpunkter_. Med Internet Analyzer kan du ställa in flera tester sida vid sida, så att du kan utvärdera en mängd olika scenarier när din infrastruktur och dina kundbehov utvecklas. Internet Analyzer tillhandahåller anpassade och förkonfigurerade slutpunkter, vilket ger dig både bekvämlighet och flexibilitet att fatta tillförlitliga prestandabeslut för dina slutanvändare. 


> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Snabba & anpassningsbara tester

Internet Analyzer tar upp prestandarelaterade frågor för molnmigrering, distribuerar till nya eller ytterligare Azure-regioner eller testar nya program- och innehållsleveransplattformar i Azure, till exempel [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) och Microsoft Azure [CDN](https://azure.microsoft.com/services/cdn/). 

Varje test som du skapar i Internet Analyzer består av två slutpunkter – slutpunkt A och slutpunkt B. Slutpunkt B:s prestanda analyseras i förhållande till slutpunkt A. 

Du kan antingen konfigurera din egen anpassade slutpunkt eller välja från en mängd olika förkonfigurerade Azure-slutpunkter. Anpassade slutpunkter bör användas för att utvärdera lokala arbetsbelastningar, dina instanser i andra molnleverantörer eller dina anpassade Azure-konfigurationer. Tester kan bestå av två anpassade slutpunkter. Minst en anpassad slutpunkt måste dock finnas i Azure. Förkonfigurerade Azure-slutpunkter är ett snabbt och enkelt sätt att utvärdera prestanda för populära Azure-nätverksplattformar som Azure Front Door, Azure Traffic Manager och Azure CDN. 

Under förhandsgranskningen är följande förkonfigurerade slutpunkter tillgängliga: 

* **Azure-regioner**
    * Brasilien, södra
    * Indien, centrala
    * USA, centrala
    * Asien, östra
    * USA, östra
    * Japan, västra
    * Europa, norra
    * Sydafrika North
    * Sydostasien 
    * Uae Norra
    * Storbritannien, västra  
    * Europa, västra
    * USA, västra 
    * USA, västra 2
* **Kombinationer av flera Azure-regioner** 
    * Östra USA, Södra Brasilien 
    * Östra USA, Östasien 
    * Västeuropa, Södra Brasilien
    * Västeuropa, Sydostasien
    * Västeuropa, UAE North
    * Västra USA, Östra USA 
    * Västra USA, Västeuropa
    * Västra USA, UAE North
    * Västeuropa, UAE North, Sydostasien
    * Västra USA, Västeuropa, Östasien
    * Västra USA, Norra Europa, Sydostasien, UAE North, Sydafrika Nord 
* **Azure + Azure Ytterdörr** - distribueras på enstaka eller flera Azure-regionkombinationer som anges ovan
* **Azure + Azure CDN från Microsoft** – distribueras på en enskild kombination av Azure-regioner som anges ovan
* **Azure + Azure Traffic Manager** – distribueras på alla kombinationer av Flera Azure-regioner som anges ovan

## <a name="suggested-test-scenarios"></a>Föreslagna testscenarier 

För att hjälpa dig att fatta de bästa prestandabesluten för dina kunder kan du med Internet Analyzer utvärdera två slutpunkter för din specifika population av slutanvändare. 

Internet Analyzer kan svara på en mängd frågor, men några av de vanligaste är: 
* Vad är prestandaeffekten av att migrera till molnet? 
    * *Föreslaget test: Anpassad (din nuvarande lokala infrastruktur) jämfört med Azure (alla förkonfigurerade slutpunkter)*
* Vad är värdet av att sätta mina data på kanten kontra i ett datacenter? 
    *  *Föreslaget test: Azure vs. Azure Ytterdörr, Azure vs Azure CDN från Microsoft*
* Vad är prestandafördelarna med Azure Front Door?
    *  *Föreslaget test: Anpassad/ Azure/ CDN jämfört med Azure ytterdörr*
* Vad är prestandafördelarna med Azure CDN från Microsoft? 
    *  *Föreslaget test: Anpassad/ Azure/ AFD vs Azure CDN från Microsoft*
* Hur stack Azure CDN från Microsoft upp? 
    *  *Föreslaget test: Anpassad (annan CDN-slutpunkt) jämfört med Azure CDN från Microsoft*
* Vilket är det bästa molnet för din slutanvändare i varje region? 
    *  *Föreslaget test: Anpassad (annan molntjänst) jämfört med Azure (alla förkonfigurerade slutpunkter)*

## <a name="how-it-works"></a>Hur det fungerar

Om du vill använda Internet Analyzer konfigurerar du en Internet Analyzer-resurs i Microsoft Azure-portalen och installerar den lilla JavaScript-klienten i ditt program. Klienten mäter svarstiden från slutanvändarna till dina valda slutpunkter genom att hämta en bild med en bild via EN bild via HTTPS. När du har samlat in svarstidsmätningar skickar klienten mätdata till Internet Analyzer.

När en användare besöker webbprogrammet väljer JavaScript-klienten två slutpunkter som ska mätas i alla konfigurerade tester. För varje slutpunkt utför klienten en _kall_ och _varm_ mätning. Den _kalla_ mätningen medför ytterligare svarstid bredvid den rena nätverksfördröjningen mellan användaren och slutpunkten, till exempel DNS-upplösning, TCP-anslutningshandskakning och SSL/TLS-förhandling. Den _varma_ mätningen följer strax efter den _kalla_ mätningen är klar och drar nytta av moderna webbläsares ihållande TCP-anslutningshantering för att få ett korrekt mått på end-to-end latens. När det stöds av användarens webbläsare används W3C-resurstimings-API:et för korrekt mättid. För närvarande används endast mätningar av varm latens för analys.

![Arkitektur](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Styrkort 

När ett test startar visas telemetridata i Internet Analyzer-resursen under fliken Styrkort. Dessa data aggregeras alltid. Använd följande filter för att ändra vilken vy av data du ser: 

* **Test:** Välj det test som du vill visa resultat för. Testdata visas när det finns tillräckligt med data för att slutföra analysen – i de flesta fall inom 24 timmar. 
* **Tidsperiod & slutdatum:** Internet Analyzer genererar tre styrkort dagligen – varje styrkort återspeglar en annan aggregeringsperiod – 24 timmar före (dag), sju dagar före (vecka) och 30 dagar före (månad). Använd filtret Slutdatum för att välja den tidsperiod du vill visa. 
* **Land:** Använd det här filtret om du vill visa data som är specifika för slutanvändare som är bosatta i ett land. Det globala filtret visar data över alla geografiska områden.  

Mer information om styrkort finns på sidan [Tolka ditt styrkort.](internet-analyzer-scorecard.md) 


## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar din första Internet Analyzer-resurs](internet-analyzer-create-test-portal.md).
* Läs [vanliga frågor om Internet Analyzer](internet-analyzer-faq.md). 
