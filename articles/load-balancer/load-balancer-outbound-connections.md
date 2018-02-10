---
title: "Förstå utgående anslutningar i Azure | Microsoft Docs"
description: "Den här artikeln förklarar hur Azure kan användas för virtuella datorer att kommunicera med offentliga Internet-tjänster."
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2018
ms.author: kumud
ms.openlocfilehash: e1a2b4c281194ec4c70e4d9fe1bc97c5aa61436e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>Förstå utgående anslutningar i Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]


Azure tillhandahåller utgående anslutning för kunddistributioner med flera olika mekanismer.  Den här artikeln beskriver vad de är, när de använder, hur de fungerar och hur de hanteras.

En distribution i Azure kan kommunicera med slutpunkter utanför Azure i offentliga IP-adressutrymme. När en instans initierar ett utgående flöde till ett mål i offentliga IP-adressutrymme, matchar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress.  När den här mappningen har skapats, kan returnerar trafik för den här utgående har sitt ursprung flöde också nå den privata IP-adressen som flödet kom från.

Azure använder källa nätverksadresser (SNAT) för att utföra den här funktionen.  När flera privata IP-adresser utger sig bakom en offentlig IP-adress, använder Azure [port nätverksadresser (PATRIK)](#pat) till maskeras privata IP-adresser.  Tillfälliga portar används för PATRIK och är [förallokerade](#preallocatedports) baserat på poolstorleken.

Det finns flera [utgående scenarier](#scenarios). Dessa scenarier kan kombineras efter behov. Läs dem noga för att förstå funktioner, begränsningar och mönster som de gäller för din distributionsmodell och program.  Granska vägledning för [hantera dessa scenarier](#snatexhaust).

## <a name="scenarios"></a>Scenario-översikt

Azure har två huvudsakliga distributionsmodeller: Azure Resource Manager och klassisk. Belastningsutjämnare och relaterade resurser definieras explicit när du använder [Azure Resource Manager resurser](#arm).  Klassiska distributioner abstrakt konceptet för en belastningsutjämnare och express en liknande funktion via definitionen av slutpunkter av en [Molntjänsten](#classic). Den tillämpliga [scenarier](#scenarios) för distributionen beroende på vilken distribution modellen används.

### <a name="arm"></a>Azure Resource Manager

Azure tillhandahåller tre olika metoder för att uppnå utgående anslutning för Azure Resource Manager-resurser för närvarande.  [Klassiska](#classic) distributioner har en delmängd av dessa scenarier.

| Scenario | Metod | Beskrivning |
| --- | --- | --- |
| [1. Virtuell dator med offentlig IP på instansnivå adress (med eller utan belastningsutjämnare)](#ilpip) | SNAT, utger sig för porten inte används |Azure använder offentlig IP-adress som tilldelats IP-adresskonfigurationen för nätverkskortet för den instansen.  Instansen har alla tillfälliga portar som är tillgängliga. |
| [2. Offentliga belastningsutjämnare som är kopplad till virtuell dator (ingen offentlig IP på instansnivå adress på instansen)](#lb) | SNAT med port låtsas (PAT) med hjälp av belastningsutjämnaren frontend(s) |Azure delar offentliga belastningsutjämnare frontend(s) med flera privata IP-adresser och använder tillfälliga portar för frontend(s) till PATRIK offentliga IP-adress. |
| [3. Standalone VM (någon belastningsutjämnare, ingen offentlig IP på instansnivå adress)](#defaultsnat) | SNAT med port låtsas (PAT) | Azure är en offentlig IP-adress för SNAT, delar den här offentliga IP-adressen med flera privata IP-adresser i Tillgänglighetsuppsättningen automatiskt och använder tillfälliga portar för den här offentliga IP-adressen.  Det här scenariot är en återställningsplats scenario för scenarier som föregår 1 och 2 och bör inte om du behöver synlighet och kontroll. |

Du kan använda Nätverkssäkerhetsgrupp grupper (NSG) för att blockera åtkomst efter behov om du inte vill att en virtuell dator för att kommunicera med slutpunkter utanför Azure i offentliga IP-adressutrymme.  Med hjälp av NSG: er beskrivs i detalj i [förhindrar en utgående anslutning](#preventoutbound).  Design vägledning och implementering detalj för hur du utformar och hanterar ett virtuellt nätverk utan utgående åtkomst ligger utanför omfånget för den här artikeln.

### <a name="classic"></a>Klassisk (cloud services)

Scenarier som är tillgängligt för klassiska distributioner är en delmängd av scenarier som är tillgängliga för [Azure Resource Manager](#arm) distributioner och läsa in belastningsutjämning grundläggande.

En klassisk virtuell dator har samma tre grundläggande scenarier som beskrivs för Azure Resource Manager-resurser ([1](#ilpip), [2](#lb), [3](#defaultsnat)). En klassiska Worker-Webbroll har bara två scenarier ([2](#lb), [3](#defaultsnat)).  [Minskningsstrategier](#snatexhaust) också ha samma skillnaderna.

Den algoritm som används för [Förallokering av tillfälliga portar](#ephemeralprots) för PATRIK för klassiska distributioner är desamma som för Azure Resource Manager distributioner för resursen.  

### <a name="ilpip"></a>Scenario 1: Virtuell dator med en offentlig IP på instansnivå adress

Den virtuella datorn har en instans nivå offentliga IP-går kopplade till den i det här scenariot. Det spelar ingen roll om den virtuella datorn är belastningsutjämnad eller inte utgående anslutningar är fråga.  Det här scenariot har företräde framför andra. När en går används för den virtuella datorn använder går för alla utgående flöden.  

Port imiterade (PATRIK) används inte och den virtuella datorn har alla tillfälliga portar som är tillgängliga för användning.

Om ditt program initierar många utgående flöden och SNAT port resursöverbelastning uppstår, kan du tilldela en [går att minimera SNAT begränsningar](#assignilpip). Granska [hantera SNAT resursuttömning](#snatexhaust) den sin helhet.

### <a name="lb"></a>Scenario 2: Belastningsutjämnade virtuella datorn utan offentlig IP på instansnivå adress

I det här scenariot är en del av en offentlig belastningsutjämnare serverdelspool i den virtuella datorn.  Den virtuella datorn har inte en offentlig IP-adress som tilldelats. Resursen belastningsutjämnare måste konfigureras med en regel för belastningsutjämnare för att skapa en länk mellan den offentliga IP-klientdelen med serverdelspoolen. Om du inte slutföra den här regelkonfigurationen, beteendet är enligt beskrivningen i scenariot för [Standalone VM med ingen offentlig IP på instansnivå](#defaultsnat).  Det är inte nödvändigt för att regeln ska ha en fungerande lyssnare i serverdelspoolen eller hälsa avsökningen ska lyckas.

När den belastningsutjämnade virtuella datorn skapar ett utgående flöde, översätter Azure privata källans IP-adress för utgående offentliga belastningsutjämnare klientdel offentliga IP-adressen. Azure använder källa Network Address Translation (SNAT) för att utföra den här funktionen samt [port nätverksadresser (PATRIK)](#pat) till maskeras flera privata IP-adresser bakom en offentlig IP-adress. Tillfälliga portar för Belastningsutjämnarens offentliga IP-adress klientdel används för att särskilja enskilda flöden av den virtuella datorn har sitt ursprung. SNAT dynamiskt använder [förallokerade tillfälliga portar](#preallocatedports) när utgående flöden skapas. I den här kontexten kallas tillfälliga portarna som används för SNAT SNAT portar.

SNAT portar är förallokerade enligt beskrivningen i den [förstå SNAT och PATRIK](#snat) avsnittet och är en begränsad resurs som kan vara slut. Det är viktigt att förstå hur de är [används](#pat). Granska [hantera SNAT resursuttömning](#snatexhaust) att förstå hur du utformar för och minska efter behov.

När [flera (offentliga) IP-adresser som är associerade med en Load Balancer grundläggande](load-balancer-multivip-overview.md), någon av dessa offentliga IP-adresser är en [kandidat för utgående flöden och en väljs](#multivipsnat).  

Du kan använda [Log Analytics för belastningsutjämnaren](load-balancer-monitor-log.md) och [avisering händelseloggar för att övervaka SNAT port uttömning meddelanden](load-balancer-monitor-log.md#alert-event-log) för övervakning av utgående anslutningar med Load Balancer grundläggande.

### <a name="defaultsnat"></a>Scenario 3: Standalone VM utan offentlig IP på instansnivå adress

Den virtuella datorn är inte en del av en pool med Azure belastningsutjämnare och har inte en instans nivå offentliga IP-går-adress som tilldelats till den. När den virtuella datorn skapar ett utgående flöde, översätter Azure privata källans IP-adress för utgående begränsas till en offentlig IP-källadress. Den offentliga IP-adressen som används för detta utgående flöde kan inte konfigureras och räknas inte mot prenumerationens offentliga IP-resursgräns. Azure använder källa Network Address Translation (SNAT) med port låtsas ([klappa lätt](#pat)) att utföra den här funktionen. Det här scenariot liknar [scenario 2](#lb), förutom att det finns ingen kontroll över IP-adress som används.  Det här är en återställningsplats scenario för när scenarier 1 och scenario 2 finns inte.  Det här scenariot rekommenderas inte eventuellt kontroll över utgående adressen.

SNAT portar är förallokerade enligt beskrivningen i den [förstå SNAT och PATRIK](#snat) avsnittet och är en begränsad resurs som kan vara slut. Det är viktigt att förstå hur de är [används](#pat). Granska [hantera SNAT resursuttömning](#snatexhaust) att förstå hur du utformar för och minska efter behov.

### <a name="combinations"></a>Flera kombinerade scenarier

Scenarier som beskrivs i föregående avsnitt kan kombineras för att uppnå ett visst resultat.  När det finns flera scenarier, gäller en prioritetsordning: [scenario 1](#ilpip) företräde framför [scenario 2](#lb) och [3](#defaultsnat) (endast Azure Resource-Manager), och [scenario 2](#lb) åsidosätter [scenario 3](#defaultsnat) (Azure Resource Manager och klassisk).

Ett exempel är en Azure Resource Manager distribution där programmet kraftigt beroende av utgående anslutningar till ett begränsat antal mål men också tar emot inkommande flöden via en klientdel på belastningsutjämnaren. I det här fallet kan du kombinera scenarier 1 och 2 för befrielse.  Granska [hantera SNAT resursuttömning](#snatexhaust) för fler mönster.

### <a name="multivipsnat"></a>Flera frontends för utgående flöden

Load Balancer grundläggande väljer en enda klientdel ska använda utgående flöden när [flera (offentliga) IP-frontends](load-balancer-multivip-overview.md) lämpar sig för utgående flöden.  Det går inte att konfigurera det här urvalet och algoritmen markeringen ska anses vara slumpmässiga.  Du kan ange en specifik IP-adress för utgående, enligt beskrivningen i [kombineras scenarier](#combinations).

## <a name="snat"></a>Förstå SNAT och PATRIK

### <a name="pat"></a>Port imiterade SNAT (PATRIK)

När en offentlig belastningsutjämnare resurs associeras med VM-instanser, om varje utgående anslutningskälla. Källan om från det virtuella privata IP-adressutrymmet frontend offentliga IP-adressen för belastningsutjämnaren. 5-tuppel i flöde (källans IP-adress, källport, IP-transportprotokoll, mål-IP-adress, målport) måste vara unikt i det offentliga IP-adressutrymmet.  Tillfälliga portar används för att uppnå detta efter att skriva om privata källans IP-adress eftersom flera flöden kommer från en offentlig IP-adress.  Dessa tillfälliga portar kallas SNAT portar. 

En SNAT port används per flöde till en enda IP-adress, port och protokoll. För flera flödar till samma mål-IP-adress, port och protokoll använder varje flöde en enda SNAT port. Detta säkerställer att flödena är unika när kommer från samma offentliga IP-adress till samma mål-IP-adress, port och protokoll. 

Flera flöden, till en annan IP-adress, port och protokoll kommer att dela en enda SNAT port. Mål-IP-adress, port och protokoll gör flöden unika utan att behöva ytterligare källa portar som används för att skilja flödena i offentliga IP-adressutrymme.

När SNAT port resurserna är uttömda misslyckas utgående flöden tills SNAT portar släpps av befintliga flöden. Belastningsutjämnaren återtar SNAT portar när flödet stängs och använder en [4-minuters timeout för inaktivitet](#idletimeout) frigöra SNAT portar från inaktiv flödena.

Granska de [hantera SNAT](#snatexhaust) avsnitt att minimera förhållanden som ofta leda till uttömning av SNAT port-mönster.

### <a name="preallocatedports"></a>Tillfälliga Förallokering för porten som utger sig för SNAT (PATRIK)

Azure använder en algoritm och fastställa antalet förallokerade SNAT tillgängliga portar baserat på storleken på serverdelspoolen när du använder port imiterade SNAT ([klappa lätt](#pat)).  SNAT portar är tillfälliga portar som är tillgängliga för en viss offentlig IP-källadress.

Azure preallocates SNAT portar till IP-konfigurationen för nätverkskort på varje virtuell dator. När en IP-konfiguration läggs till i poolen, förallokerade SNAT portar för den här IP-konfigurationen som baseras på backend-poolstorleken. För klassiska Web arbetsroller är allokeringen per rollinstans.  När utgående flöden skapas [klappa lätt](#pat) dynamiskt förbrukar (högst förallokerade) och släpper de här portarna när flödet stängs eller [timeout vid inaktivitet](#ideltimeout).

I följande tabell visas SNAT port preallocations för nivåer med backend-adresspool:

| Poolstorleken (VM-instanser) | Förallokerade SNAT portar per IP-konfiguration|
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

Det är viktigt att komma ihåg att antalet tillgängliga portar för SNAT inte översätta direkt till antalet anslutningar. En enskild port SNAT kan återanvändas för flera unika mål. Portar som endast används om det är nödvändigt att göra flöden unika. Referera till [hantera icke förnybara resursen](#snatexhaust) för design och minskning vägledning samt avsnitt som beskriver [klappa lätt](#pat).

Ändra storlek på din serverdelspool kan påverka vissa av dina etablerade flöden. Om backend-poolens storlek ökar och övergår till nästa nivå, är hälften av din förallokerade SNAT portar frigöras under övergången till nästa större backend poolen nivå. Flöden som är associerade med en återvunnet SNAT port kommer timeout och måste återskapas. Nya anslutningsförsök lyckas omedelbart som finns tillgängliga förallokerade portar.

Om backend-poolstorleken minskar och övergår till en lägre nivå, ökar antalet tillgängliga SNAT portar. I det här fallet befintliga tilldelade SNAT portar och deras respektive flöden påverkas inte.

## <a name="snatexhaust"></a>Hantera SNAT (PATRIK) port resursuttömning

[Tillfälliga portar](#preallocatedports) används för [klappa lätt](#pat) är en icke förnybara resurs som beskrivs i [Standalone VM utan offentlig IP på instansnivå adress](#defaultsnat) och [belastningsutjämnade virtuella datorn utan Instansen nivå offentliga IP-adressen](#lb).

Om du vet du initierar många utgående TCP eller UDP-anslutningar till samma mål-IP-adress och port, och se misslyckas utgående anslutningar eller bör av support du är lång körningstid förbrukar SNAT portar (förallokerade [tillfälliga portar](#preallocatedports)används av [klappa lätt](#pat)), har du flera alternativ för Allmänt skydd.  Granska alternativen och bestämma vad som är tillgängliga och bäst för ditt scenario.  Det är möjligt eller mer kan hantera det här scenariot.

Om du har svårt att förstå beteendet utgående anslutning kan du använda IP-stack-statistik (netstat) eller det kan vara bra att se anslutningen beteenden med hjälp av paket insamlingar.  Du kan utföra dessa paket insamlingar i gästoperativsystemet för din instans eller använda [Nätverksbevakaren för paketinsamling](../network-watcher/network-watcher-packet-capture-manage-portal.md).

### <a name="connectionreuse"></a>Ändra program att återanvända anslutningar 
Du kan minska behovet av tillfälliga portar som används för SNAT genom att återanvända anslutningar i ditt program.  Detta gäller särskilt för protokoll som HTTP/1.1 där anslutning återanvända är standard.  Och andra protokoll med HTTP-transport (d.v.s. REST) kan dra i sin tur.  Återanvändning är alltid bättre än enskilda, atomiska TCP-anslutningar för varje begäran och resulterar i flera performant, mycket effektivt TCP transaktioner.

### <a name="connection pooling"></a>Ändra program att använda anslutningspooler
Du kan använda en anslutningspoolen schema i ditt program där förfrågningar internt är fördelade på en fast uppsättning anslutningar (varje återanvända där det är möjligt).  Den här begränsningarna antalet tillfälliga portar används och skapar en mer förutsägbar miljö.  Detta kan också öka genomflödet av begäranden genom att låta flera samtidiga åtgärder när en anslutning blockerar i svar för en åtgärd.  Anslutningspooler kanske redan finns inom ramen som du använder för att utveckla ditt program eller konfigurationsinställningarna för tillämpningsprogrammet.  Du kan kombinera det med återanvändning av anslutningar och dina flera begäranden använda en fast, förutsägbar antalet portar med samma mål-IP-adress och port när också utnyttjar mycket effektiv användning av TCP-transaktioner som minskar svarstiden och resurs användning.  UDP-transaktioner kan också dra som att hantera hur många av UDP kan flöden i sin tur undvika avgaser villkor och hantera hur SNAT port.

### <a name="retry logic"></a>Ändra program att använda mindre aggressivt logik
När [förallokerade tillfälliga portar](#preallocatedports) används för [klappa lätt](#pat) är slut eller ett program fel uppstår, aggressivt eller brute force återförsök utan decay och backoff logik orsaka resursuttömning sker eller spara. Du kan minska behovet av tillfälliga portar med hjälp av en mindre aggressivt logik.   Tillfälliga portar har ett 4-minuters timeout vid inaktivitet (inte justerbara) och om de nya försök för Aggressivt, stoppas har någon möjlighet att rensa på sin egen.  Därför är överväger hur och hur ofta programmet försöker transaktioner en viktig faktor för designen.

### <a name="assignilpip"></a>Tilldela en offentlig IP på instansnivå på varje virtuell dator
Detta ändrar ditt scenario till [instansnivå offentliga IP-Adressen till en virtuell dator](#ilpip).  Alla tillfälliga portar för den offentliga IP-Adressen som används för varje virtuell dator är tillgängliga för den virtuella datorn (i stället för scenarier där tillfälliga portar på en offentlig IP-adress som delas med alla Virtuella kopplade till respektive serverdelspoolen).  Det finns avvägningarna att tänka på, till exempel ytterligare kostnaden för offentliga IP-adresser och potentiella effekten av vitlistning av ett stort antal enskilda IP-adresser.

>[!NOTE] 
>Det här alternativet är inte tillgängligt för Web arbetsroller.

### <a name="idletimeout"></a>Använda keepalive-överföringar för att återställa utgående timeout för inaktivitet

Utgående anslutningar har ett 4-minuters timeout för inaktivitet. Detta kan inte ställas in. Men du kan använda transport (d.v.s. TCP keep-alive-meddelanden) eller application layer keepalive-överföringar att uppdatera en inaktiv flödet och återställa den här tidsgränsen för inaktivitet om krävs.

## <a name="discoveroutbound"></a>Identifiering av offentliga IP-Adressen som används av en viss virtuell dator
Det finns många sätt att avgöra den offentliga IP-källadressen för en utgående anslutning. OpenDNS är en tjänst som kan visa den offentliga IP-adressen på den virtuella datorn. Använda nslookup-kommando, kan du skicka en DNS-fråga för namnet myip.opendns.com till OpenDNS matcharen. Källans IP-adress som används för att skicka frågan returnerar tjänsten. När du kör följande fråga från den virtuella datorn är svaret offentliga IP-Adressen som används för den virtuella datorn.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Förhindrar en utgående anslutning
Ibland det inte önskvärt en virtuell dator för att kunna skapa ett utgående flöde eller det kan finnas ett krav för att hantera vilka destinationer kan nås med utgående flöden eller vilka destinationer kan börja inkommande flöden. I så fall måste du använda [Nätverkssäkerhetsgrupp grupper (NSG)](../virtual-network/virtual-networks-nsg.md) att hantera mål som den virtuella datorn kan nå samt vilken offentliga destination kan starta inkommande flöden. När du kopplar en NSG till en belastningsutjämnad VM, måste du vara uppmärksam på den [standard taggar](../virtual-network/virtual-networks-nsg.md#default-tags) och [standard regler](../virtual-network/virtual-networks-nsg.md#default-rules).

Du måste se till att den virtuella datorn kan ta emot hälsa avsökningen begäranden från Azure-belastningsutjämnaren. Om en NSG blockerar hälsa avsökningen begäranden från Standardetiketten AZURE_LOADBALANCER, din VM hälsoavsökningen misslyckas och den virtuella datorn markeras. Belastningsutjämnaren slutar att skicka nya flödar till den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Load Balancer grundläggande](load-balancer-overview.md).
- Lär dig mer om [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md).
- Lär dig mer om den andra nyckeln [nätverk](../networking/networking-overview.md) i Azure.
