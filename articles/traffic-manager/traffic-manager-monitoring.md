---
title: "Azure Traffic Manager och slutpunktsövervakning | Microsoft Docs"
description: "Den här artikeln kan hjälpa dig att förstå hur Traffic Manager använder slutpunktsövervakning och automatisk endpoint redundans för att Azure-kunder distribuera program med hög tillgänglighet"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: fff25ac3-d13a-4af9-8916-7c72e3d64bc7
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2017
ms.author: kumud
ms.openlocfilehash: 3b30aa04854b779c25582abafc0f9ebba65b71ba
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="traffic-manager-endpoint-monitoring"></a>Slutpunktsövervakning av Traffic Manager

Azure Traffic Manager innehåller inbyggda slutpunktsövervakning och automatisk endpoint redundans. Den här funktionen hjälper dig att leverera program med hög tillgänglighet som är motståndskraftiga mot slutpunkten fel, inklusive Azure-region fel.

## <a name="configure-endpoint-monitoring"></a>Konfigurera slutpunktsövervakning

För att konfigurera endpoint övervakning, måste du ange följande inställningar på Traffic Manager-profilen:

* **Protokollet**. Välj HTTP, HTTPS eller TCP som protokoll att Traffic Manager använder när avsökning av slutpunkten för att kontrollera dess tillstånd. Övervakning av HTTPS kontrollerar inte om SSL-certifikatet är giltigt--den endast kontrollerar att certifikatet är installerat.
* **Port**. Välj den port som används för begäran.
* **Sökvägen**. Den här inställningen gäller bara för HTTP och HTTPS-protokoll, för vilka att ange sökvägen inställningen är obligatorisk. Att erbjuda den här inställningen för TCP övervakning protokollet resulterar i ett fel. Ge den relativa sökvägen och namnet på webbsidan eller filen som ansluter till övervakning för TCP-protokoll. Ett snedstreck (/) är ett giltigt för den relativa sökvägen. Det här värdet innebär att filen är i rotkatalogen (standard).
* **Avsökning av intervall**. Det här värdet anger hur ofta en slutpunkt är markerat för dess hälsa från en avsöknings Traffic Manager-agent. Du kan ange två värden här: 30 sekunder (vanlig sökning) och 10 sekunder (snabb avsökning). Om inga värden har angetts, anger profilen till standardvärdet 30 sekunder. Besök den [Traffic Manager priser](https://azure.microsoft.com/pricing/details/traffic-manager) sidan om du vill veta mer om snabb avsöknings priser.
* **Antalet fel tolereras**. Det här värdet anger hur många fel som en avsöknings Traffic Manager-agenten kan tolerera innan du markerar den slutpunkten som ohälsosamt. Värdet kan variera mellan 0 och 9. Värdet 0 innebär ett enstaka övervakning fel kan orsaka att slutpunkten markeras som ohälsosam. Om inget värde anges används standardvärdet 3.
* **Övervaka Timeout**. Den här egenskapen anger hur lång tid avsöknings Traffic Manager-agenten ska vänta innan som kontrollerar ett fel när en avsökning för kontroll av hälsotillstånd skickas till slutpunkten. Om sökning-intervallet är 30 sekunder, kan du ange Timeout-värde mellan 5 och 10 sekunder. Om inget värde anges används standardvärdet 10 sekunder. Om sökning-intervallet är 10 sekunder kan du ange Timeout-värde mellan 5 och 9 sekunder. Om inget Timeout-värde har angetts används standardvärdet 9 sekunder.

![Slutpunktsövervakning av Traffic Manager](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

**Bild 1: Traffic Manager-slutpunktsövervakning**

## <a name="how-endpoint-monitoring-works"></a>Så här fungerar slutpunktsövervakning

Om övervakning protokollet är HTTP eller HTTPS, gör avsöknings Traffic Manager-agenten en GET-begäran till slutpunkten med hjälp av protokollet, porten och relativ sökväg som anges. Om du hämtar tillbaka ett svar 200 OK, och sedan denna slutpunkt anses felfri. Om svaret är ett annat värde eller, om inget svar tas emot inom tidsgränsen som angetts i Traffic Manager avsökning av agenten försöker på nytt enligt inställningen tolereras antal misslyckanden (inget nytt försök görs om den här inställningen är 0). Om antalet upprepade fel är högre än inställningen tolereras antalet fel, sedan markeras denna slutpunkt som ohälsosam. 

Om övervakning protokollet är TCP, initierar avsöknings Traffic Manager-agenten en TCP-anslutningsbegäran med den angivna porten. Om slutpunkten svarar på begäran med ett svar för att upprätta en anslutning, som hälsokontroll har markerats som lyckas och avsöknings Traffic Manager-agenten återställer TCP-anslutningen. Om svaret är ett annat värde, eller om inget svar tas emot inom den angivna tiden anges i Traffic Manager avsökning av agenten försöker på nytt enligt inställningen tolereras antal misslyckanden (inget nytt försök görs om den här inställningen är 0). Om antalet upprepade fel är högre än inställningen tolereras antalet fel, markeras den slutpunkten feltillstånd.

I samtliga fall Traffic Manager-avsökningar från flera platser och på varandra följande fel bestämning sker inom varje region. Det innebär också att slutpunkter får hälsoavsökningar från Traffic Manager med en frekvens som är högre än inställningen används för avsökning av intervallet.

>[!NOTE]
>För HTTP eller HTTPS övervakning är vanligt på slutpunkten sida att implementera en anpassad sida i ditt program – till exempel /health.aspx. Du kan utföra programspecifika kontroller, till exempel kontrollerar prestandaräknare eller Verifiera databasens tillgänglighet med hjälp av den här sökvägen för övervakning. Sidan returnerar baserat på dessa anpassade kontroller, en lämplig HTTP-statuskod.

Alla slutpunkter i en Traffic Manager-profil dela inställningarna för övervakning. Om du behöver använda olika övervakningsinställningarna för olika slutpunkter kan du skapa [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Status för slutpunkt och profil

Du kan aktivera och inaktivera Traffic Manager-profiler och slutpunkter. Men inträffa en ändring i slutpunkt status även som ett resultat av Traffic Manager automatiserade inställningar och processer.

### <a name="endpoint-status"></a>Status för endpoint

Du kan aktivera eller inaktivera en viss slutpunkt. Den underliggande tjänsten som kanske ändå felfri, påverkas inte. Ändra status för endpoint kontrollerar tillgängligheten för slutpunkt i Traffic Manager-profilen. När en slutpunkt status är inaktiverad Traffic Manager kontrollerar inte dess hälsa och slutpunkten ingår inte i ett DNS-svar.

### <a name="profile-status"></a>Profilstatusen

Använder profilinställningen för status kan du aktivera eller inaktivera en viss profil. När status för endpoint påverkar en enda slutpunkt, påverkar profilstatusen hela profil, inklusive alla slutpunkter. När du inaktiverar en profil slutpunkterna kontrolleras inte för hälsotillstånd och inga slutpunkter ingår i ett DNS-svar. En [NXDOMAIN](https://tools.ietf.org/html/rfc2308) svarskoden returneras för DNS-frågan.

### <a name="endpoint-monitor-status"></a>Övervaka status för endpoint

Övervaka status för endpoint är ett Traffic Manager-värde som visar status för slutpunkten. Du kan inte ändra den här inställningen manuellt. Status för endpoint övervakaren är en kombination av resultaten av slutpunktsövervakning och konfigurerad slutpunkt status. Övervaka status för endpoint möjliga värden visas i följande tabell:

| Profilstatusen | Status för endpoint | Övervaka status för endpoint | Anteckningar |
| --- | --- | --- | --- |
| Disabled |Enabled |Inaktiva |Profilen har inaktiverats. Även om slutpunkten är aktiverad, företräde profilstatusen (inaktiverat). Slutpunkter i inaktiverad profiler övervakas inte. Ett NXDOMAIN svarskoden returneras för DNS-frågan. |
| &lt;alla&gt; |Disabled |Disabled |Slutpunkten har inaktiverats. Inaktiverad slutpunkter övervakas inte. Slutpunkten ingår inte i DNS-svar, därför kan den tar emot inte trafik. |
| Enabled |Enabled |Online |Slutpunkten övervakas och är felfri. Den ingår i DNS-svar och kan ta emot trafik. |
| Enabled |Enabled |Försämrad |Slutpunkten övervakning hälsokontroller misslyckas. Slutpunkten ingår inte i DNS-svar och tar inte emot trafik. <br>Ett undantag är om alla slutpunkter är försämrade då alla betraktas som ska returneras i svaret på frågan).</br>|
| Enabled |Enabled |CheckingEndpoint |Slutpunkten övervakas, men resultatet av den första avsökningen ännu inte tagits emot. CheckingEndpoint är ett tillfälligt tillstånd som vanligtvis sker omedelbart efter att lägga till eller aktivera en slutpunkt i profilen. En slutpunkt i det här tillståndet ingår i DNS-svar och kan ta emot trafik. |
| Enabled |Enabled |Stoppad |Tjänsten eller web molnappen som slutpunkten pekar på är inte igång. Kontrollera inställningarna för cloud service eller web app. Detta kan också inträffa om slutpunkten är av typen kapslade slutpunkt och underordnade profilen har inaktiverats eller är inaktiv. <br>En slutpunkt med status stoppad övervakas inte. Den ingår inte i DNS-svar och ta emot inte trafik. Ett undantag är om alla slutpunkter är försämrade då alla betraktas som returneras i svaret på frågan.</br>|

Mer information om hur övervaka status för endpoint beräknas för kapslade slutpunkter finns [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

### <a name="profile-monitor-status"></a>Övervaka profilstatusen

Profilstatusen för övervakaren är en kombination av konfigurerade profilstatusen och värdena endpoint övervaka status för alla slutpunkter. I följande tabell beskrivs möjliga värden:

| Profilstatusen (som har konfigurerats) | Övervaka status för endpoint | Övervaka profilstatusen | Anteckningar |
| --- | --- | --- | --- |
| Disabled |&lt;alla&gt; eller en profil med några definierade slutpunkter. |Disabled |Profilen har inaktiverats. |
| Enabled |Status för minst en slutpunkt har degraderats. |Försämrad |Granska statusvärden enskilda slutpunkt för att avgöra vilka slutpunkter kräva ytterligare åtgärder. |
| Enabled |Status för minst en slutpunkt är Online. Inga slutpunkter har statusen degraderad. |Online |Tjänsten tar emot trafik. Ingen ytterligare åtgärd krävs. |
| Enabled |Status för minst en slutpunkt är CheckingEndpoint. Inga slutpunkter är Online eller degraderad status. |CheckingEndpoints |Den här övergångstillstånd inträffar när en profil om skapade eller aktiverade. Slutpunkten hälsa kontrolleras för första gången. |
| Enabled |Status för alla slutpunkter i profilen är inaktiverad eller stoppad eller att profilen har inga definierade slutpunkter. |Inaktiva |Inga slutpunkter är aktiv, men profilen är fortfarande aktiverat. |

## <a name="endpoint-failover-and-recovery"></a>Slutpunkten redundans och återställning

Traffic Manager kontrollerar med jämna mellanrum hälsotillståndet för varje slutpunkt, inklusive ohälsosamt slutpunkter. Traffic Manager identifierar när en slutpunkt blir felfritt och ger dig tillbaka till rotation.

En slutpunkt är ohälsosamt när någon av följande händelser inträffar:
- Om övervakning protokollet HTTP eller HTTPS:
    - Icke-200 svar (inklusive en annan 2xx-kod eller en 301/302-omdirigering).
- Om övervakning protokollet är TCP: 
    - Än ACK eller SYN ACK svar som svar på SYNC-begäran som skickas av Traffic Manager försöker upprätta en anslutning.
- Timeout. 
- Alla andra anslutningsproblem ledde till slutpunkten som inte kan nås.

Mer information om felsökning av misslyckade kontroller finns [felsökning försämrad status på Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Följande tidslinjen i bild 2 är en detaljerad beskrivning av hur övervakningen Traffic Manager-slutpunkt som har följande inställningar: övervaka protokollet är HTTP, avsöknings är 30 sekunder, antalet tillåten fel är 3, timeout-värdet är 10 sekunder och DNS TTL är 30 sekunder.

![Traffic Manager-slutpunkt redundans och återställning efter fel-sekvens](./media/traffic-manager-monitoring/timeline.png)

**Bild 2: Traffic manager-slutpunkten redundans och återställning sekvens**

1. **HÄMTA**. För varje slutpunkt utför i Traffic Manager övervakningssystem en GET-begäran på sökvägen som anges i inställningarna för övervakning.
2. **200 OK**. Övervakningssystemet förväntar sig en HTTP-200 OK-meddelande som ska returneras inom 10 sekunder. När den tar emot svaret identifierar att tjänsten är tillgänglig.
3. **30 sekunder mellan kontroller**. Hälsokontroll endpoint upprepas var 30: e sekund.
4. **Tjänsten är inte tillgänglig**. Tjänsten blir otillgänglig. Traffic Manager märker inte förrän nästa hälsokontroll.
5. **Försöker få åtkomst till övervakningssökvägen**. Övervakningssystemet utför en GET-begäran, men inte tar emot ett svar inom tidsgränsen på 10 sekunder (du kan också ett icke-200 svar kan tas emot). Försöker den med tre gånger, med 30 sekunders intervall. Om en av försöker lyckas, återställs antalet försök.
6. **Statusen degraderad**. Efter en fjärde på varandra följande fel markerar övervakningssystemet status otillgänglig endpoint som degraderad.
7. **Trafik distribueras till andra slutpunkter**. Traffic Manager DNS-namnservrar uppdateras och Traffic Manager inte längre returnerar slutpunkten som svar på DNS-frågor. Nya anslutningar dirigeras till andra tillgängliga slutpunkter. Tidigare DNS-svar som innehåller den här slutpunkten kan dock fortfarande cachelagras av rekursiva DNS-servrar och DNS-klienter. Fortsätter klienter att använda slutpunkten förrän DNS-cachen upphör att gälla. Eftersom DNS-cachen upphör att gälla klienter gör den nya DNS-frågor och dirigeras till olika slutpunkter. Cache-varaktighet styrs av TTL-inställningen i Traffic Manager-profilen, till exempel 30 sekunder.
8. **Hälsotillstånd kontrollerar fortsätta**. Traffic Manager fortsätter att kontrollera hälsotillståndet för slutpunkten när den har statusen degraderad. Traffic Manager identifierar när slutpunkten återgår till hälsa.
9. **Tjänsten är online igen**. Tjänsten blir tillgänglig. Slutpunkten behåller statusen degraderad i Traffic Manager tills övervakningssystemet utför dess nästa hälsokontroll.
10. **Trafik till tjänsten återupptar**. Traffic Manager skickar en GET-begäran och tar emot ett statussvar 200 OK. Tjänsten returnerade till felfritt tillstånd. Traffic Manager-namnservrar uppdateras och de börjar att dela ut tjänstens DNS-namnet i DNS-svar. Trafik returnerar till slutpunkten som cachelagrade DNS-svar som returnerar andra slutpunkter upphör att gälla och som befintliga anslutningar till andra slutpunkter avslutas.

    > [!NOTE]
    > Eftersom Traffic Manager fungerar på DNS-nivå, kan det påverka befintliga anslutningar till valfri slutpunkt. När den dirigerar trafik mellan slutpunkter (antingen av ändrade inställningar eller under växling vid fel eller återställning efter fel) leder nya anslutningar till tillgängliga slutpunkterna i Traffic Manager. Slutpunkter kan dock fortsätta att ta emot trafik via befintliga anslutningar förrän sessionerna avslutas. Om du vill aktivera trafik att tömma från befintliga anslutningar begränsa program varaktighet för sessionen som används med varje slutpunkt.

## <a name="traffic-routing-methods"></a>Routning av nätverkstrafik metoder

När en slutpunkt har status degraderad kan tillbaka den inte längre som svar på DNS-frågor. I stället är en annan slutpunkt valt och returneras. Metoden routning av nätverkstrafik som konfigurerats i profilen avgör hur den alternativa slutpunkten är valt.

* **Prioritet**. Slutpunkter utgör en prioriterad lista. Den första tillgängliga slutpunkten på listan returneras alltid. Om statusen för slutpunkten är försämrad returneras nästa slutpunkt.
* **Viktade**. Alla tillgängliga endpoint väljs slumpmässigt baserat på deras tilldelade vikt och vikten av de tillgängliga slutpunkterna.
* **Prestanda**. Slutpunkten som är närmast slutanvändaren returneras. Om den är inte tillgänglig, flyttar Traffic Manager trafik till slutpunkter i nästa närmaste Azure-region. Du kan konfigurera alternativa redundans planer för routning av prestanda-trafik med hjälp av [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geografisk**. Slutpunkten mappas om du vill hantera den geografiska plats baserat på fråga IP-adresserna returneras. Om den är inte tillgänglig, en annan slutpunkt markeras inte ska gå över till, eftersom en geografisk plats kan endast mappas till en slutpunkt i en profil (Mer information finns i den [vanliga frågor och svar](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Som bästa praxis, när du använder geografiska routning, rekommenderar vi kunderna att använda kapslade Traffic Manager-profiler med mer än en slutpunkt som slutpunkter för profilen.

Mer information finns i [Traffic Manager routning av nätverkstrafik metoder](traffic-manager-routing-methods.md).

> [!NOTE]
> Routning av nätverkstrafik normalt ett undantag inträffar när alla berättigade slutpunkter har statusen degraderad. Traffic Manager gör ”bästa prestanda” försök och *som om alla slutpunkter för degraderad-status faktiskt är i ett onlinetillstånd*. Det här beteendet är bättre att alternativ som är att returnera inte någon slutpunkt i DNS-svaret. Inaktiverad eller stoppad slutpunkter övervakas inte, därför kan de anses inte vara berättigad till trafik.
>
> Detta tillstånd orsakas vanligen av felaktig konfiguration av tjänsten, som:
>
> * En ACL access control list [] blockerar hälsokontroller Traffic Manager.
> * En felaktig konfiguration av övervakning port eller protokollet i Traffic manager-profil.
>
> En följd av detta beteende är att om Traffic Manager-hälsokontroller inte är korrekt konfigurerad, kan det verka från den trafik som routning Traffic Manager *är* fungerar korrekt. Men i det här fallet kan endpoint växling vid fel inträffa som påverkar övergripande tillgänglighet. Det är viktigt att kontrollera att profilen visar statusen Online inte statusen degraderad. En Online-status anger att hälsokontroller Traffic Manager fungerar som förväntat.

Mer information om hur du felsöker misslyckades hälsokontroller, se [felsökning försämrad status på Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>Nästa steg

Läs [hur Traffic Manager fungerar](traffic-manager-how-traffic-manager-works.md)

Lär dig mer om den [routning av nätverkstrafik metoder](traffic-manager-routing-methods.md) stöds av Traffic Manager

Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-manage-profiles.md)

[Felsöka status degraderad](traffic-manager-troubleshooting-degraded.md) på en Traffic Manager-slutpunkt
