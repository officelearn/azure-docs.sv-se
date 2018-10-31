---
title: Azure Traffic Manager endpoint monitoring | Microsoft Docs
description: Den här artikeln kan hjälpa dig att förstå hur Traffic Manager använder slutpunktsövervakning och redundans för automatiska slutpunkt för att Azure-kunder distribuera program med hög tillgänglighet
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2017
ms.author: kumud
ms.openlocfilehash: 15b609bbf4ab048722f2512371eeffece92b3245
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138148"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager endpoint monitoring

Med Azure Traffic Manager innehåller inbyggda slutpunktsövervakning och redundansväxling för automatisk slutpunkt. Den här funktionen hjälper dig att tillhandahålla program med hög tillgänglighet som är motståndskraftig mot slutpunktsfel, inklusive Azure-region fel.

## <a name="configure-endpoint-monitoring"></a>Konfigurera slutpunktsövervakning

Om du vill konfigurera slutpunktsövervakning, måste du ange följande inställningar på din Traffic Manager-profil:

* **Protokollet**. Välj HTTP, HTTPS eller TCP som protokoll att Traffic Manager använder när din slutpunkt-avsökning för att kontrollera dess hälsa. Övervakning av HTTPS kontrollerar inte om SSL-certifikatet är giltigt – endast kontrolleras att certifikatet är installerat.
* **Port**. Välj den port som används för begäran.
* **Sökvägen**. Den här inställningen gäller endast för HTTP och HTTPS-protokollen, för vilka att ange sökvägen inställning är obligatorisk. Ta del av den här inställningen för TCP övervakning protokollet uppstår ett fel. Ge den relativa sökvägen och namnet på webbsidan eller den fil som ansluter till för övervakning för HTTP och HTTPS-protokoll. Ett snedstreck (/) är en giltig post för den relativa sökvägen. Det här värdet anger att filen är i rotkatalogen (standard).
* **Inställningar för anpassade huvud** denna konfigurationsinställning kan du lägga till specifika HTTP-huvuden hälsa kontrollerar att Traffic Manager skickar till slutpunkter i en profil. Anpassade huvuden kan anges på en profilnivå för att gälla för alla slutpunkter i profilen and / or på slutpunkten nivå gäller endast för slutpunkten. Du kan använda anpassade huvuden för att ha hälsokontroller till slutpunkter i en miljö med flera innehavare dirigeras korrekt till sina mål genom att ange ett värdhuvud. Du kan också använda den här inställningen genom att lägga till unika rubriker som kan användas för att identifiera Traffic Manager har sitt ursprung HTTP (S)-begäranden och bearbetar dem på olika sätt.
* **Förväntade statuskodintervall** den här inställningen kan du ange flera lyckades kod intervall i formatet 200 299, 301 301. Om dessa statuskoder tas emot som svar från en slutpunkt när en hälsokontroll initieras, markerar Traffic Manager de slutpunkterna som felfri. Du kan ange upp till 8 statuskodintervall. Den här inställningen gäller endast för HTTP och HTTPS-protokollet och för alla slutpunkter. Den här inställningen är på nivån för Traffic Manager-profil och som standard värdet 200 definieras som statuskod lyckades.
* **Kontrollintervall**. Det här värdet anger hur ofta en slutpunkt har markerats för dess hälsa från en sökning Traffic Manager-agent. Du kan ange två värden här: 30 sekunder (vanlig sökning) och 10 sekunder (snabb avsökning). Om inga värden har angetts, anger profilen till ett standardvärde på 30 sekunder. Gå till den [Traffic Manager priser](https://azure.microsoft.com/pricing/details/traffic-manager) att lära dig mer om snabb sökning priser.
* **Ska tolereras antalet fel**. Det här värdet anger hur många fel som en sökning Traffic Manager-agenten kan tolerera innan du markerar slutpunkten som skadad. Värdet kan variera mellan 0 och 9. Värdet 0 innebär att ett enstaka övervakning fel kan orsaka att slutpunkten markeras som felaktig. Om inget värde anges används standardvärdet 3.
* **Tidsgräns för kontroll**. Denna egenskap anger hur lång tid att avsöknings Traffic Manager-agenten ska vänta innan som kontrollerar ett fel när en kontroll hälsoavsökning skickas till slutpunkten. Om intervallet avsökning är inställt på 30 sekunder, kan du ange Timeout-värde mellan 5 och 10 sekunder. Om inget värde anges används standardvärdet 10 sekunder. Om intervallet avsökning har angetts till 10 sekunder, kan du ange Timeout-värde mellan 5 och 9 sekunder. Om inget Timeout-värde anges används standardvärdet 9 sekunder.

    ![Traffic Manager endpoint monitoring](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Bild: Traffic Manager-slutpunktsövervakning**

## <a name="how-endpoint-monitoring-works"></a>Så här fungerar slutpunktsövervakning

Om protokollet övervakning har angetts som HTTP eller HTTPS, gör en GET-begäran till slutpunkten via protokollet, porten och relativ sökväg som anges i avsöknings Traffic Manager-agenten. Om den får tillbaka ett svar med 200 OK, eller någon av svar som konfigurerats i den **förväntat statuskod \*intervall** och sedan att slutpunkten anses vara felfritt. Om svaret är ett annat värde, eller, om inget svar tas emot inom tidsgränsen för angivna måste Traffic Manager-avsökning agent försöker igen enligt inställningen ska tolereras antalet fel (inget nytt försök görs om den här inställningen är 0). Om antalet på varandra följande fel är högre än inställningen ska tolereras antalet fel, sedan markeras att slutpunkten som felaktig. 

Om övervakning protokollet är TCP, initierar avsöknings Traffic Manager-agenten en TCP-anslutningsbegäran med hjälp av den angivna porten. Om slutpunkten som svarar på begäran med ett svar för att upprätta anslutningen, att hälsokontrollen har markerats som ett lyckat test och avsöknings Traffic Manager-agenten återställer TCP-anslutningen. Om svaret är ett annat värde, eller om inget svar tas emot inom den angivna tiden har angetts, Traffic Manager-avsökning agent försöker igen enligt inställningen ska tolereras antalet fel (inget nytt försök görs om den här inställningen är 0). Om antalet på varandra följande fel är högre än inställningen ska tolereras antalet fel, sedan att slutpunkten har ett dåligt.

I samtliga fall Traffic Manager-avsökningar från flera platser och på varandra följande fel bestämning sker i varje region. Det innebär också att slutpunkter får hälsokontroller av slutpunkter från Traffic Manager med en frekvens som är högre än inställningen används för avsökning intervall.

>[!NOTE]
>För HTTP eller HTTPS övervakning är vanligt på slutpunkten sida att implementera en anpassad sida i ditt program – till exempel /health.aspx. Med hjälp av den här sökvägen för övervakning, kan du utföra programspecifika kontroller, till exempel kontrollerar prestandaräknare eller verifiera databastillgänglighet. Sidan returnerar baserat på dessa anpassade kontroller, lämpliga HTTP-statuskoden.

Alla slutpunkter i en Traffic Manager-profil dela inställningarna för övervakning. Om du vill använda olika övervakningsinställningarna för olika slutpunkter kan du skapa [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Status för slutpunkten och profil

Du kan aktivera och inaktivera Traffic Manager-profiler och slutpunkter. Men kan en ändring i slutpunktsstatus också uppstå som ett resultat av Traffic Manager automatiserad inställningar och processer.

### <a name="endpoint-status"></a>Slutpunktsstatus

Du kan aktivera eller inaktivera en viss slutpunkt. Den underliggande tjänsten kan vara felfritt, påverkas inte. Ändra statusen för slutpunkten kontrollerar tillgängligheten för slutpunkt i Traffic Manager-profilen. När en slutpunktsstatus inaktiveras Traffic Manager kontrollerar inte dess hälsa och slutpunkten ingår inte i ett DNS-svar.

### <a name="profile-status"></a>Profilstatus

Använder profilinställningen för status kan du aktivera eller inaktivera en specifik profil. När statusen för slutpunkten påverkar en enda slutpunkt, påverkar profilstatus hela profilen, inklusive alla slutpunkter. När du inaktiverar en profil slutpunkterna kontrolleras inte om hälsotillstånd och inga slutpunkter som ingår i ett DNS-svar. En [NXDOMAIN](https://tools.ietf.org/html/rfc2308) svarskoden returneras för DNS-frågan.

### <a name="endpoint-monitor-status"></a>Slutpunkten monitor-status

Övervakarstatus för endpoint är en Traffic Manager-genererade värde som visar status för slutpunkten. Du kan inte ändra den här inställningen manuellt. Övervaka statusen för slutpunkten är en kombination av resultatet från slutpunktsövervakning och statusen för konfigurerade slutpunkten. De möjliga värdena för slutpunkten monitor-status visas i följande tabell:

| Profilstatus | Slutpunktsstatus | Slutpunkten monitor-status | Anteckningar |
| --- | --- | --- | --- |
| Disabled |Enabled |Inaktiv |Profilen har inaktiverats. Även om slutpunkten är aktiverad, företräde profilstatus (inaktiverad). Slutpunkter i inaktiverad profiler övervakas inte. Ett NXDOMAIN svarskoden returneras för DNS-frågan. |
| &lt;Alla&gt; |Disabled |Disabled |Slutpunkten har inaktiverats. Inaktiverad slutpunkter övervakas inte. Slutpunkten ingår inte i DNS-svar, därför kan den emot inte trafik. |
| Enabled |Enabled |Online |Slutpunkten som övervakas och är felfri. Den ingår i DNS-svar och kan ta emot trafik. |
| Enabled |Enabled |Degraderad |Övervakning hälsokontroller av slutpunkter misslyckas. Slutpunkten ingår inte i DNS-svar och tar inte emot trafik. <br>Ett undantag är om alla slutpunkterna är försämrade då alla betraktas som ska returneras i svaret på frågan).</br>|
| Enabled |Enabled |CheckingEndpoint |Slutpunkten som övervakas, men resultatet av den första sensorn ännu inte tagits emot. CheckingEndpoint är ett tillfälligt tillstånd som vanligtvis sker omedelbart efter att lägga till eller aktivera en slutpunkt i profilen. En slutpunkt i det här tillståndet ingår i DNS-svar och kan ta emot trafik. |
| Enabled |Enabled |Stoppad |Tjänsten eller web molnappen som slutpunkten pekar körs inte. Kontrollera inställningarna för cloud service eller web app. Detta kan också inträffa om slutpunkten är en typ som är kapslad slutpunkt och underordnade profilen är inaktiverad eller är inaktiv. <br>En slutpunkt med statusen stoppad övervakas inte. Det ingår inte i DNS-svar och tar inte emot trafik. Ett undantag är om alla slutpunkterna är försämrade då alla betraktas som ska returneras i svaret på frågan.</br>|

Mer information om hur endpoint monitor-status beräknas för kapslade slutpunkter finns [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

>[!NOTE]
> En stoppad Endpoint monitor-status kan inträffa på App Service om webbprogrammet inte körs på nivån Standard eller högre. Mer information finns i [Traffic Manager-integrering med App Service](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Profilen monitor-status

Profilstatus för övervakaren är en kombination av status konfigurerad profil och slutpunkt övervakaren statusvärdena för alla slutpunkter. I följande tabell beskrivs möjliga värden:

| Profilstatus (enligt konfigurationen) | Slutpunkten monitor-status | Profilen monitor-status | Anteckningar |
| --- | --- | --- | --- |
| Disabled |&lt;alla&gt; eller en profil med några definierade slutpunkter. |Disabled |Profilen har inaktiverats. |
| Enabled |Status för minst en slutpunkt har degraderats. |Degraderad |Granska enskilda slutpunktsvärdena status för att fastställa vilka slutpunkter kräva ytterligare åtgärder. |
| Enabled |Status för minst en slutpunkt är Online. Inga slutpunkter har statusen degraderad. |Online |Tjänsten tar emot trafik. Ingen ytterligare åtgärd krävs. |
| Enabled |Status för minst en slutpunkt är CheckingEndpoint. Inga slutpunkter är Online eller degraderad status. |CheckingEndpoints |Den här övergångstillstånd inträffar när en profil om skapade eller aktiverade. Hälsotillståndet för slutpunkt kontrolleras för första gången. |
| Enabled |Status för alla slutpunkter i profilen är inaktiverad eller stoppad eller profilen har inte några definierade slutpunkter. |Inaktiv |Inga slutpunkter är aktiva, men profilen är fortfarande aktiverat. |

## <a name="endpoint-failover-and-recovery"></a>Slutpunkt för redundans och återställning

Traffic Manager kontrollerar med jämna mellanrum hälsotillståndet för varje slutpunkt, inklusive feltillstånd slutpunkter. Traffic Manager identifierar när en slutpunkt blir felfritt och hämtar tillbaka till rotation.

En slutpunkt är i feltillstånd när någon av följande händelser inträffar:
- Om övervakning protokollet HTTP eller HTTPS:
    - Ett icke-200-svar eller ett svar som inte innehåller status-intervallet som angetts i den **förväntade statuskodintervall** inställningen tas emot (inklusive en annan 2xx-kod eller en 301/302-omdirigering).
- Om övervakning protokollet är TCP: 
    - Än ACK eller SYN ACK svar som svar på SYNC-begäran som skickats av Traffic Manager att försöka upprätta en anslutning.
- Timeout. 
- Alla andra anslutningsproblem leder det inte går att nå slutpunkten.

Mer information om felsökning av misslyckade kontroller finns i [felsöka degraderat tillstånd i Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Tidslinje i följande bild är en detaljerad beskrivning av övervakningsprocessen för Traffic Manager-slutpunkt som har följande inställningar: protokoll är HTTP, sökning är 30 sekunder, antalet tolererade fel är 3, timeout-värdet är 10 sekunder och TTL för DNS är 30 sekunder.

![Traffic Manager endpoint redundans och återställning efter fel sekvens](./media/traffic-manager-monitoring/timeline.png)

**Bild: Traffic manager endpoint redundans och återställning sekvens**

1. **HÄMTA**. För varje slutpunkt utför Traffic Manager övervakningssystem en GET-begäran på den sökväg som anges i inställningarna för övervakning.
2. **200 OK eller anpassad kod intervallet anges Traffic Manager-profil övervakningsinställningarna** . Övervakningssystemet förväntar sig en HTTP 200 OK eller eller anpassad kod intervallet anges Traffic Manager-profil som övervakning av för inställningsmeddelandet som ska returneras inom 10 sekunder. När den har fått det här svaret identifierar att tjänsten är tillgänglig.
3. **30 sekunder mellan kontroller**. Slutpunkt-hälsokontroll upprepas med 30 sekunders mellanrum.
4. **Tjänsten är inte tillgänglig**. Tjänsten blir otillgänglig. Traffic Manager märker inte förrän nästa hälsokontrollen.
5. **Försöker få åtkomst till övervakningssökvägen**. Övervakningssystemet utför en GET-begäran, men inte emot något svar inom tidsgränsen på 10 sekunder (du kan också ett icke-200-svar kan tas emot). Den försöker sedan tre gånger med 30 sekunders mellanrum. Om en av försök lyckas, återställs antalet försök.
6. **Statusen degraderad**. Efter ett fjärde på varandra följande fel markerar övervakningssystemet statusen för otillgänglig slutpunkten som degraderad.
7. **Trafiken distribueras till andra slutpunkter**. Traffic Manager DNS-namnservrarna uppdateras och Traffic Manager inte längre returnerar slutpunkten som svar på DNS-frågor. Nya anslutningar dirigeras till andra, tillgängliga slutpunkter. Föregående DNS-svar som innehåller den här slutpunkten kan dock fortfarande cachelagras av rekursiva DNS-servrar och DNS-klienter. Klienter fortsätta att använda slutpunkten tills DNS-cacheminnet upphör att gälla. Eftersom DNS-cacheminnet upphör att gälla klienter skapar nya DNS-frågor och dirigeras till olika slutpunkter. Cachevaraktigheten styrs av TTL-inställning i Traffic Manager-profilen, till exempel 30 sekunder.
8. **Hälsokontroller fortsätta**. Traffic Manager fortsätter att kontrollera hälsotillståndet för slutpunkten när den har statusen degraderad. Traffic Manager identifierar när slutpunkten returnerar hälsa.
9. **Tjänsten är online igen**. Tjänsten blir tillgänglig. Slutpunkten behåller statusen degraderad i Traffic Manager förrän övervakningssystemet genomför dess nästa hälsokontrollen.
10. **Trafik till tjänsten återupptar**. Traffic Manager skickar en GET-begäran och får ett statussvar med 200 OK. Tjänsten returnerade till felfritt tillstånd. Traffic Managers namnservrar uppdateras och de börjar att dela ut tjänstens DNS-namnet i DNS-svar. Trafik som returnerar till slutpunkten som cachelagrade DNS-svar som returnerar andra slutpunkter upphör att gälla och som befintliga anslutningar till andra slutpunkter avslutas.

    > [!NOTE]
    > Eftersom Traffic Manager fungerar på DNS-nivå, kan det påverka befintliga anslutningar till valfri slutpunkt. När den dirigerar trafik mellan slutpunkter (antingen genom att ändrade inställningar, eller under redundansväxling eller återställning efter fel) dirigerar Traffic Manager nya anslutningar till tillgängliga slutpunkter. Andra slutpunkter kan dock fortsätta att ta emot trafik via befintliga anslutningar förrän dessa sessioner avslutas. Om du vill aktivera trafik till tömma från befintliga anslutningar bör program begränsa Sessionslängd som används med varje slutpunkt.

## <a name="traffic-routing-methods"></a>Trafikroutningsmetoder

När en slutpunkt har statusen degraderad, returneras den inte längre som svar på DNS-frågor. I stället är en annan slutpunkt valt och returneras. Metoden routning av nätverkstrafik som konfigurerats i profilen avgör hur den alternativa slutpunkten väljs.

* **Prioritet**. Slutpunkter utgör en prioriterad lista. Den första tillgängliga slutpunkten i listan returneras alltid. Nästa tillgängliga slutpunkten returneras om en slutpunktsstatus har degraderats.
* **Viktat**. Valfri tillgänglig slutpunkt väljs slumpmässigt baserat på deras tilldelade vikt och vikten av de tillgängliga slutpunkterna.
* **Prestanda**. Slutpunkten som är närmast användaren returneras. Om slutpunkten är tillgänglig, flyttar Traffic Manager trafik till slutpunkterna i nästa närmsta Azure-region. Du kan konfigurera alternativa redundans planer för routning av prestanda-trafik med hjälp av [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geografisk**. Den slutpunkt som mappats för att leverera den geografiska plats baserat på fråga IP-adress returneras. Om slutpunkten är tillgänglig, en annan slutpunkt markeras inte ska gå över till, eftersom en geografisk plats kan endast mappas till en slutpunkt i en profil (Mer information finns i den [vanliga frågor och svar](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Som bästa praxis när du använder geografisk routning, rekommenderar vi kunder att använda kapslade Traffic Manager-profiler med mer än en slutpunkt som slutpunkter för profilen.
* **Flervärden är** flera slutpunkter mappas till IPv4/IPv6-adresser som returneras. När en fråga tas emot för den här profilen, felfria slutpunkter returneras baserat på den **maximalt antal poster i svaret** värde som du har angett. Standardvärdet för antal svar är två slutpunkter.
* **Undernät** returneras den slutpunkt som mappats till en uppsättning IP-adressintervall. När en begäran tas emot från IP-adress, returnerade slutpunkten mappas det för IP-adress. 

Mer information finns i [Traffic Manager trafikroutningsmetoder](traffic-manager-routing-methods.md).

> [!NOTE]
> Ett undantag till routning av nätverkstrafik normalt inträffar när alla berättigade slutpunkter har statusen degraderad. Traffic Manager gör ”bästa prestanda” försök och *svarar som om alla slutpunkterna för degraderad-status är faktiskt i ett onlinetillstånd*. Det här beteendet är bättre att Alternativt kan du, som är att inte returnera valfri slutpunkt i DNS-svaret. Inaktiverad eller stoppad slutpunkter övervakas inte, därför kan de anses inte vara berättigad trafik.
>
> Detta tillstånd orsakas vanligtvis av felaktig konfigurering av tjänsten, till exempel:
>
> * En åtkomstkontrollista [ACL] blockerar hälsokontroller Traffic Manager.
> * En felaktig konfigurering av övervakning port eller protokoll i Traffic manager-profil.
>
> En följd av det här beteendet är att om Traffic Manager-hälsokontroller inte har konfigurerats korrekt, visas den från den trafiken dirigeras som Traffic Manager *är* fungerar korrekt. Men i det här fallet kan inte endpoint redundans inträffa som påverkar programmets övergripande tillgänglighet. Det är viktigt att kontrollera att profilen visar statusen Online inte statusen degraderad. En onlinestatus anger att hälsokontroller Traffic Manager fungerar som förväntat.

Mer information om hur du felsöker misslyckades hälsokontroller, se [felsöka degraderat tillstånd i Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>Nästa steg

Lär dig [så här fungerar Traffic Manager](traffic-manager-how-it-works.md)

Läs mer om den [trafikroutningsmetoder](traffic-manager-routing-methods.md) stöds av Traffic Manager

Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-manage-profiles.md)

[Felsöka status degraderad](traffic-manager-troubleshooting-degraded.md) på en Traffic Manager-slutpunkt
