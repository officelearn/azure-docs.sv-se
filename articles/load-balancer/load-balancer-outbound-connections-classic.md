---
title: Utgående anslutningar i Azure (klassisk) | Microsoft Docs
description: Den här artikeln förklarar hur kan användas för Azure cloud services för att kommunicera med offentliga internet-tjänster.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 7679fd253370d8ca9ca9ac57dc080806050f5c3c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="outbound-connections-classic"></a>Utgående anslutningar (klassisk)

Azure tillhandahåller utgående anslutning för kunddistributioner med flera olika mekanismer. Den här artikeln beskriver vad de är, när de använder, hur de fungerar och hur de hanteras.

>[!NOTE]
>Den här artikeln beskriver endast klassiska distributioner.  Granska [utgående anslutningar](load-balancer-outbound-connections.md) för alla distributionsscenarier för hanteraren för filserverresurser i Azure.

En distribution i Azure kan kommunicera med slutpunkter utanför Azure i det offentliga IP-adressutrymmet. När en instans initierar ett utgående flöde till ett mål i det offentliga IP-adressutrymmet, matchar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När du har skapat den här mappningen kan returnerade trafik för den här utgående har sitt ursprung flöde också nå den privata IP-adressen som flödet kom från.

Azure använder källa nätverksadresser (SNAT) för att utföra den här funktionen. När flera privata IP-adresser utger sig bakom en offentlig IP-adress, använder Azure [port nätverksadresser (PATRIK)](#pat) till maskeras privata IP-adresser. Tillfälliga portar används för PATRIK och är [förallokerade](#preallocatedports) baserat på poolstorleken.

Det finns flera [utgående scenarier](#scenarios). Du kan kombinera dessa scenarier efter behov. Läs dem noga för att förstå funktioner, begränsningar och mönster som de gäller för din distributionsmodell och program. Granska vägledning för [hantera dessa scenarier](#snatexhaust).

## <a name="scenarios"></a>Scenario-översikt

Azure tillhandahåller tre olika metoder för att uppnå utgående anslutning klassiska distributioner.  Inte alla klassiska distributioner har alla tre scenarier som är tillgängliga för dem:

| Scenario | Metod | Beskrivning | Worker-Webbroll | IaaS | 
| --- | --- | --- | --- | --- |
| [1. Virtuell dator med en offentlig IP på instansnivå adress](#ilpip) | SNAT, utger sig för porten inte används |Azure använder offentlig IP-adress som tilldelats virtuella datorn. Instansen har alla tillfälliga portar som är tillgängliga. | Nej | Ja |
| [2. offentlig slutpunkt för Utjämning av nätverksbelastning](#publiclbendpoint) | SNAT med port låtsas (PAT) till offentlig slutpunkt |Azure delar den offentliga IP-adress offentlig slutpunkten med flera privata slutpunkter. Azure använder tillfälliga portar för offentlig slutpunkt för PATRIK. | Ja | Ja |
| [3. Fristående VM ](#defaultsnat) | SNAT med port låtsas (PAT) | Azure automatiskt utser en offentlig IP-adress för SNAT delar den här offentliga IP-adressen med hela distributionen och använder tillfälliga portar för offentlig slutpunkt IP-adressen för PATRIK. Det här är en återställningsplats scenario för föregående scenarier. Vi rekommenderar inte den om du behöver synlighet och kontroll. | Ja | Ja|

Det här är en deluppsättning av utgående anslutning funktioner som är tillgängligt för Resource Manager distributioner i Azure.  

Olika distributioner i klassiskt har olika funktioner:

| Klassisk distribution | Funktionen är tillgänglig | 
| --- | --- |
| Virtuell dator | scenariot [1](#ilpip), [2](#publiclbendpoint), eller [3](#defaultsnat) |
| Worker-Webbroll | endast scenariot [2](#publiclbendpoint), [3](#defaultsnat) | 

[Minskningsstrategier](#snatexhaust) också ha samma skillnaderna.

Den [algoritm som används för Förallokering av tillfälliga portar](#ephemeralports) för PATRIK för klassiska distributioner är desamma som för Azure Resource Manager distributioner för resursen.

### <a name="ilpip"></a>Scenario 1: Virtuell dator med en offentlig IP på instansnivå adress

Den virtuella datorn har en instans nivå offentliga IP-går kopplade till den i det här scenariot. Utgående anslutningar är fråga det spelar ingen roll om den virtuella datorn har belastningsutjämnade endpoint eller inte. Det här scenariot har företräde framför andra. När en går används för den virtuella datorn använder går för alla utgående flöden.  

En offentlig IP som tilldelats till en virtuell dator är en 1:1 relation (inte 1:many) och implementeras som en tillståndslös 1:1-NAT.  Port imiterade (PATRIK) används inte och den virtuella datorn har alla tillfälliga portar som är tillgängliga för användning.

Om ditt program initierar många utgående flöden och SNAT port resursöverbelastning uppstår, kan du överväga att tilldela en [går att minimera SNAT begränsningar](#assignilpip). Granska [hantera SNAT resursuttömning](#snatexhaust) i sin helhet.

### <a name="publiclbendpoint"></a>Scenario 2: Offentlig belastningsutjämnade slutpunkt

I det här scenariot är VM eller Web Worker-rollen associerad med en offentlig IP-adress via slutpunkten Utjämning av nätverksbelastning. Den virtuella datorn har inte en offentlig IP-adress som tilldelats. 

När den belastningsutjämnade virtuella datorn skapar ett utgående flöde, översätter Azure privata källans IP-adress för utgående offentliga IP-adressen för den offentliga slutpunkten för Utjämning av nätverksbelastning. Azure använder SNAT för att utföra den här funktionen. Azure använder också [klappa lätt](#pat) ska maskeras flera privata IP-adresser bakom en offentlig IP-adress. 

Tillfälliga portar för belastningsutjämnarens offentliga IP-adress klientdel används för att särskilja enskilda flöden av den virtuella datorn har sitt ursprung. SNAT dynamiskt använder [förallokerade tillfälliga portar](#preallocatedports) när utgående flöden skapas. I den här kontexten kallas tillfälliga portarna som används för SNAT SNAT portar.

SNAT portar är förallokerade enligt beskrivningen i den [förstå SNAT och PATRIK](#snat) avsnitt. Det är en begränsad resurs som kan vara slut. Det är viktigt att förstå hur de är [används](#pat). För att förstå hur du designar för denna förbrukning och minska efter behov, granska [hantera SNAT resursuttömning](#snatexhaust).

När [flera offentliga slutpunkter för Utjämning av nätverksbelastning](load-balancer-multivip.md) finns, något av dessa offentliga IP-adresser är en [kandidat för utgående flöden](#multivipsnat), och en väljs slumpmässigt.  

### <a name="defaultsnat"></a>Scenario 3: Ingen offentlig IP-adress kopplad

I det här scenariot är virtuell dator eller Web Worker-rollen inte en del av en offentlig slutpunkt för Utjämning av nätverksbelastning.  Och för virtuell dator har inte tilldelats går adress. När den virtuella datorn skapar ett utgående flöde, översätter Azure privata källans IP-adress för utgående begränsas till en offentlig IP-källadress. Den offentliga IP-adressen som används för detta utgående flöde kan inte konfigureras och räknas inte mot prenumerationens offentliga IP-resursgräns.  Azure tilldelar automatiskt den här adressen.

Azure använder SNAT med utger sig för port ([klappa lätt](#pat)) att utföra den här funktionen. Det här scenariot liknar [scenario 2](#lb), förutom att det finns ingen kontroll över IP-adress som används. Det här är en återställningsplats scenario för när scenarier 1 och 2 finns inte. Vi rekommenderar inte det här scenariot om du vill ha kontroll över utgående adressen. Om utgående anslutningar är en viktig del av ditt program, bör du har valt ett annat scenario.

SNAT portar är förallokerade enligt beskrivningen i den [förstå SNAT och PATRIK](#snat) avsnitt.  Antalet virtuella datorer eller Web arbetsroller dela den offentliga IP-adressen anger antalet förallokerade tillfälliga portar.   Det är viktigt att förstå hur de är [används](#pat). För att förstå hur du designar för denna förbrukning och minska efter behov, granska [hantera SNAT resursuttömning](#snatexhaust).

## <a name="snat"></a>Förstå SNAT och PATRIK

### <a name="pat"></a>Port imiterade SNAT (PATRIK)

När en distribution gör en utgående anslutning, om varje utgående anslutningskälla. Källan om från privata IP-adressutrymmet till den offentliga IP-Adressen som är associerade med distribution (baserat på de scenarier som beskrivs ovan). 5-tuppel i flöde (källans IP-adress, källport, IP-transportprotokoll, mål-IP-adress, målport) måste vara unikt i det offentliga IP-adressutrymmet.  

Tillfälliga portar SNAT används för att uppnå detta efter att skriva om privata källans IP-adress, eftersom flera flöden kommer från en offentlig IP-adress. 

En SNAT port används per flöde till en enda IP-adress, port och protokoll. För flera flödar till samma mål-IP-adress, port och protokoll använder varje flöde en enda SNAT port. Detta säkerställer att flödena är unika när de kommer från samma offentliga IP-adress och gå till samma mål-IP-adress, port och protokoll. 

Flera flöden, till en annan IP-adress, port och protokoll, dela en enda SNAT port. Mål-IP-adress, port och protokoll gör flöden unikt utan att behöva ytterligare källa portar för att skilja flödena i det offentliga IP-adressutrymmet.

När SNAT port resurserna är uttömda misslyckas utgående flöden tills befintliga flöden släpper SNAT portar. Belastningsutjämnaren återtar SNAT portar när flödet stängs och använder en [4-minuters timeout för inaktivitet](#idletimeout) frigöra SNAT portar från inaktiv flödena.

Mönster för att minimera villkor som vanligtvis leda till uttömning av SNAT port, granska den [hantera SNAT](#snatexhaust) avsnitt.

### <a name="preallocatedports"></a>Tillfälliga Förallokering för porten som utger sig för SNAT (PATRIK)

Azure använder en algoritm och fastställa antalet förallokerade SNAT tillgängliga portar baserat på storleken på serverdelspoolen när du använder port imiterade SNAT ([klappa lätt](#pat)). SNAT portar är tillfälliga portar som är tillgängliga för en viss offentlig IP-källadress.

Azure preallocates SNAT portar när en instans distribueras baserat på hur många instanser av virtuell dator eller Web Arbetsrollen dela en angiven offentlig IP-adress.  När utgående flöden skapas [klappa lätt](#pat) dynamiskt förbrukar (högst förallokerade) och släpper de här portarna när flödet stängs eller [timeout vid inaktivitet](#ideltimeout) inträffa.

I följande tabell visas SNAT port preallocations för nivåerna för backend-pool storlekar:

| Instanser | Förallokerade SNAT portar per instans |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101 200 | 256 |
| 201 400 | 128 |

Kom ihåg att antalet tillgängliga portar för SNAT inte översätta direkt till antal flöden. En enskild port SNAT kan återanvändas för flera unika mål. Portar förbrukas endast om det är nödvändigt att göra flöden unika. Design och minskning instruktioner finns i avsnittet om [hantera icke förnybara resursen](#snatexhaust) och det avsnitt som beskriver [klappa lätt](#pat).

Ändra storlek på din distribution kan påverka vissa av dina etablerade flöden. Om poolstorleken backend ökar och övergår till nästa nivå, är hälften av din förallokerade SNAT portar frigöras under övergången till nästa större backend poolen nivå. Flöden som är associerade med en återvunnet SNAT port gör timeout och måste återskapas. Om ett nytt flöde görs lyckas flödet omedelbart som finns tillgängliga förallokerade portar.

Om övergångar till en lägre nivå, antalet tillgängliga portar för SNAT ökar och minskar den distributionen. I det här fallet befintliga tilldelade SNAT portar och deras respektive flöden påverkas inte.

SNAT portar allokeringar är IP-transportprotokollet specifika (TCP och UDP underhålls separat) och publiceras på följande villkor:

### <a name="tcp-snat-port-release"></a>Versionen för SNAT TCP-port

- Om både servern eller-klienten skickar Finland-ACK, släpps SNAT port efter 240 sekunder.
- Om en RST visas släpps SNAT port efter 15 sekunder.
- Tidsgränsen för inaktivitet har nåtts

### <a name="udp-snat-port-release"></a>Versionen för SNAT UDP-port

- Tidsgränsen för inaktivitet har nåtts

## <a name="problemsolving"></a> Problemlösning 

Det här avsnittet är avsedd för att minska SNAT resursuttömning och andra scenarier som kan uppstå med utgående anslutningar i Azure.

### <a name="snatexhaust"></a> Hantera SNAT (PATRIK) port resursuttömning
[Tillfälliga portar](#preallocatedports) används för [klappa lätt](#pat) är en icke förnybara resurs, enligt beskrivningen i [ingen offentlig IP-adress som är associerade](#defaultsnat) och [offentlig slutpunkt för Utjämning av nätverksbelastning](#publiclbendpoint).

Om du vet att du initiera många utgående TCP eller UDP-anslutningar till samma mål-IP-adress och port och du Observera misslyckas utgående anslutningar eller bör av support att du är lång körningstid förbrukar SNAT portar (förallokerade [tillfälliga portar](#preallocatedports) används av [klappa lätt](#pat)), har du flera alternativ för Allmänt skydd. Granska alternativen och bestämma vad som är tillgängliga och bäst för ditt scenario. Det är möjligt att en eller flera kan hantera det här scenariot.

Om du har svårt att förstå beteendet utgående anslutning kan använda du IP-stack-statistik (netstat). Eller så kan vara bra att se anslutningen beteenden med hjälp av paket insamlingar.

#### <a name="connectionreuse"></a>Ändra programmet att återanvända anslutningar 
Du kan minska behovet av tillfälliga portar som används för SNAT genom att återanvända anslutningar i ditt program. Detta gäller särskilt för protokoll som HTTP/1.1, där återanvändning av anslutningar är standard. Och andra protokoll som använder HTTP transport (till exempel REST) kan dra i sin tur. 

Återanvändning är alltid bättre än enskilda, atomiska TCP-anslutningar för varje begäran. Återanvända resulterar i flera performant, mycket effektivt TCP transaktioner.

#### <a name="connection pooling"></a>Ändra program att använda anslutningspooler
Du kan använda en anslutningspoolen schema i ditt program där förfrågningar internt är fördelade på en fast uppsättning anslutningar (varje återanvända där det är möjligt). Det här schemat begränsar antalet tillfälliga portar används och skapar en mer förutsägbar miljö. Det här schemat kan också öka genomflödet av begäranden genom att låta flera samtidiga åtgärder när en anslutning blockerar i svar för en åtgärd.  

Anslutningspooler kanske redan finns inom ramen som du använder för att utveckla ditt program eller konfigurationsinställningarna för tillämpningsprogrammet. Du kan kombinera anslutningspooler med återanvändning av anslutningar. Din flera begäranden sedan använda en fast, förutsägbar antalet portar till samma mål-IP-adress och port. Begäranden kan också dra nytta av effektiv användning av TCP-transaktioner som minskar svarstiden och Resursanvändning. UDP-transaktioner kan också dra eftersom hantera antal UDP flöden kan i sin tur undvika avgaser villkor och hantera hur SNAT port.

#### <a name="retry logic"></a>Ändra program att använda mindre aggressivt logik
När [förallokerade tillfälliga portar](#preallocatedports) används för [klappa lätt](#pat) är slut eller ett program fel uppstår, aggressivt eller brute force återförsök utan decay och backoff logik orsaka resursuttömning sker eller spara. Du kan minska behovet av tillfälliga portar med hjälp av en mindre aggressivt logik. 

Tillfälliga portar har ett 4-minuters timeout vid inaktivitet (inte justerbara). Om de nya försök för Aggressivt, har stoppas någon möjlighet att rensa på sin egen. Därför är överväger hur – och hur ofta--programmet försöker transaktioner en viktig del av designen.

#### <a name="assignilpip"></a>Tilldela en nivå offentliga IP-instans på varje virtuell dator
Tilldela en går ändras ditt scenario till [offentlig IP på instansnivå till en virtuell dator](#ilpip). Alla tillfälliga portar för den offentliga IP-Adressen som används för varje virtuell dator är tillgängliga för den virtuella datorn. (I stället för scenarier där tillfälliga portar på en offentlig IP-adress som delas med alla virtuella datorer som är associerade med respektive distribution.) Det finns avvägningarna att tänka på, till exempel den potentiella effekten av vitlistning av ett stort antal enskilda IP-adresser.

>[!NOTE] 
>Det här alternativet är inte tillgängligt för web arbetsroller.

### <a name="idletimeout"></a>Använda keepalive-överföringar för att återställa utgående tidsgränsen för inaktivitet

Utgående anslutningar har ett 4-minuters timeout för inaktivitet. Det här kan inte ställas in. Du kan dock använda transport (exempelvis TCP keepalive-överföringar)- eller programnivå keepalive-överföringar för att uppdatera en inaktiv flödet och Återställ den här tidsgränsen för inaktivitet om det behövs.  Kontrollera med leverantören av alla programvarupaket på om det finns stöd för eller hur du aktiverar den.  Vanligtvis behöver bara en sida generera keepalive-överföringar för att återställa timeout vid inaktivitet. 

## <a name="discoveroutbound"></a>Identifiering av offentliga IP-Adressen som använder en virtuell dator
Det finns många sätt att avgöra den offentliga IP-källadressen för en utgående anslutning. OpenDNS är en tjänst som kan visa den offentliga IP-adressen på den virtuella datorn. 

Du kan skicka en DNS-fråga för namnet myip.opendns.com till OpenDNS matcharen med hjälp av nslookup-kommando. Källans IP-adress som används för att skicka frågan returnerar tjänsten. När du kör följande fråga från den virtuella datorn är svaret offentliga IP-Adressen som används för den virtuella datorn:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [belastningsutjämnaren](load-balancer-overview.md) används i Resource Manager distributioner.
- Lär dig läge [utgående anslutning](load-balancer-outbound-connections.md) scenarier finns i Resource Manager distributioner.
