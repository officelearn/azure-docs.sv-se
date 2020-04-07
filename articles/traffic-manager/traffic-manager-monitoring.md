---
title: Slutpunktsövervakning för Azure Traffic Manager | Microsoft-dokument
description: Den här artikeln kan hjälpa dig att förstå hur Traffic Manager använder slutpunktsövervakning och automatisk redundans för slutpunkter för att hjälpa Azure-kunder att distribuera program med hög tillgänglighet
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: rohink
ms.openlocfilehash: 61aafbe8cb12e93d72f5efd01155f06fb3ec0c28
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757271"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Slutpunktsövervakning för Traffic Manager

Azure Traffic Manager innehåller inbyggd slutpunktsövervakning och automatisk redundans för slutpunkter. Den här funktionen hjälper dig att leverera program med hög tillgänglighet som är motståndskraftiga mot slutpunktsfel, inklusive Azure-regionfel.

## <a name="configure-endpoint-monitoring"></a>Konfigurera slutpunktsövervakning

Om du vill konfigurera slutpunktsövervakning måste du ange följande inställningar för Traffic Manager-profilen:

* **Protokoll**. Välj HTTP, HTTPS eller TCP som det protokoll som Traffic Manager använder när du avsöker slutpunkten för att kontrollera dess hälsa. HTTPS-övervakning verifierar inte om ditt TLS/SSL-certifikat är giltigt – det kontrollerar bara att certifikatet finns.
* **Hamn**. Välj den port som används för begäran.
* **Sökväg**. Den här konfigurationsinställningen är endast giltig för HTTP- och HTTPS-protokollen, för vilka det krävs att ange sökvägsinställningen. Om du anger den här inställningen för TCP-övervakningsprotokollet uppstår ett fel. För HTTP- och HTTPS-protokoll anger du den relativa sökvägen och namnet på webbsidan eller filen som övervakningen är åtkomst till. Ett snedstreck (/) är en giltig post för den relativa sökvägen. Det här värdet innebär att filen finns i rotkatalogen (standard).
* **Inställningar för anpassat huvud** Den här konfigurationsinställningen hjälper dig att lägga till specifika HTTP-huvuden i hälsokontroller som Traffic Manager skickar till slutpunkter under en profil. De anpassade rubrikerna kan anges på en profilnivå som ska gälla för alla slutpunkter i den profilen och/eller på en slutpunktsnivå som endast gäller för den slutpunkten. Du kan använda anpassade rubriker för att hälsokontroller till slutpunkter i en miljö med flera innehavare dirigeras korrekt till deras mål genom att ange ett värdhuvud. Du kan också använda den här inställningen genom att lägga till unika rubriker som kan användas för att identifiera HTTP(S)-begäranden och bearbetar dem på olika sätt. Du kan ange upp till åtta rubrik:värdepar som är syvärda med ett kommatecken. Till exempel "header1:value1,header2:value2". 
* **Antal förväntade statuskodintervall** Med den här inställningen kan du ange flera kodintervall för lyckade lyckade i formatet 200-299, 301-301. Om dessa statuskoder tas emot som svar från en slutpunkt när en hälsokontroll initieras, markerar Traffic Manager dessa slutpunkter som felfria. Du kan ange högst 8 statuskodintervall. Den här inställningen gäller endast http- och HTTPS-protokoll och för alla slutpunkter. Den här inställningen är på profilnivå i Traffic Manager och som standard definieras värdet 200 som statuskoden för lyckade.
* **Sonderingsintervall**. Det här värdet anger hur ofta en slutpunkt kontrolleras för sin hälsa från en Traffic Manager-avslagsagent. Du kan ange två värden här: 30 sekunder (normal sondering) och 10 sekunder (snabb sondering). Om inga värden anges anger profilen ett standardvärde på 30 sekunder. Besök [traffic manager-prissidan](https://azure.microsoft.com/pricing/details/traffic-manager) om du vill veta mer om snabb avskrivningspriser.
* **Tolererat antal fel**. Det här värdet anger hur många fel en Traffic Manager-avbningsagent tolererar innan slutpunkten markeras som fel. Dess värde kan variera mellan 0 och 9. Ett värde på 0 innebär att ett enda övervakningsfel kan orsaka att slutpunkten markeras som fel. Om inget värde anges används standardvärdet 3.
* **Tidsgränsen för avsökning**. Den här egenskapen anger hur lång tid som Traffic Manager-avsökningsagenten ska vänta innan du överväger att kontrollera ett fel när en hälsokontrollavsökning skickas till slutpunkten. Om probningsintervallet är inställt på 30 sekunder kan du ställa in timeout-värdet mellan 5 och 10 sekunder. Om inget värde anges används ett standardvärde på 10 sekunder. Om probningsintervallet är inställt på 10 sekunder kan du ställa in timeout-värdet mellan 5 och 9 sekunder. Om inget timeout-värde anges används ett standardvärde på 9 sekunder.

    ![Slutpunktsövervakning för Traffic Manager](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Bild: Övervakning av Trafikhanterarens slutpunkt**

## <a name="how-endpoint-monitoring-works"></a>Så här fungerar övervakning av slutpunkter

Om övervakningsprotokollet har angetts som HTTP eller HTTPS gör Traffic Manager-avsökningsagenten en GET-begäran till slutpunkten med hjälp av den angivna protokollet, porten och den relativa sökvägen. Om det får tillbaka ett 200-OK-svar, eller något av svaren som konfigurerats i ** \*kodintervallen för förväntad status,** anses slutpunkten vara felfri. Om svaret är ett annat värde, eller om inget svar tas emot inom den angivna timeout-perioden, försöker Traffic Manager-avbningsagenten på nytt enligt inställningen Tolererat antal fel (inga omförsök görs om den här inställningen är 0). Om antalet på varandra följande fel är högre än inställningen Tolererat antal fel markeras slutpunkten som fel. 

Om övervakningsprotokollet är TCP initierar Traffic Manager-avbningsagenten en TCP-anslutningsbegäran med den angivna porten. Om slutpunkten svarar på begäran med ett svar för att upprätta anslutningen markeras hälsokontrollen som en lyckad och Traffic Manager-avbningsagenten återställer TCP-anslutningen. Om svaret är ett annat värde, eller om inget svar tas emot inom den angivna timeout-perioden, försöker Traffic Manager-avbningsagenten på nytt enligt inställningen Tolererat antal fel (inga omförsök görs om den här inställningen är 0). Om antalet på varandra följande fel är högre än inställningen Tolererat antal fel markeras slutpunkten felaktigt.

I samtliga fall avsöker Traffic Manager från flera platser och den på varandra följande felbestämningen sker inom varje region. Detta innebär också att slutpunkter tar emot hälsoavsökningar från Traffic Manager med en högre frekvens än den inställning som används för avsökningsintervall.

>[!NOTE]
>För HTTP- eller HTTPS-övervakningsprotokoll är en vanlig praxis på slutpunktssidan att implementera en anpassad sida i ditt program , till exempel /health.aspx. Med den här sökvägen för övervakning kan du utföra programspecifika kontroller, till exempel kontrollera prestandaräknare eller verifiera databastillgänglighet. Baserat på dessa anpassade kontroller returnerar sidan en lämplig HTTP-statuskod.

Alla slutpunkter i en Traffic Manager-profil delar övervakningsinställningar. Om du behöver använda olika övervakningsinställningar för olika slutpunkter kan du skapa [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Slutpunkt och profilstatus

Du kan aktivera och inaktivera Traffic Manager-profiler och slutpunkter. En ändring av slutpunktsstatus kan dock också ske som ett resultat av automatiska inställningar och processer i Traffic Manager.

### <a name="endpoint-status"></a>Slutpunktsstatus

Du kan aktivera eller inaktivera en viss slutpunkt. Den underliggande tjänsten, som fortfarande kan vara felfri, påverkas inte. Om du ändrar slutpunktsstatusen styr slutpunktens tillgänglighet i Traffic Manager-profilen. När en slutpunktsstatus är inaktiverad kontrollerar Traffic Manager inte dess hälsa och slutpunkten ingår inte i ett DNS-svar.

### <a name="profile-status"></a>Profilstatus

Med hjälp av profilstatusinställningen kan du aktivera eller inaktivera en viss profil. Slutpunktsstatus påverkar en enda slutpunkt, men profilstatus påverkar hela profilen, inklusive alla slutpunkter. När du inaktiverar en profil kontrolleras inte slutpunkterna för hälsotillstånd och inga slutpunkter inkluderas i ett DNS-svar. En [NXDOMAIN-svarskod](https://tools.ietf.org/html/rfc2308) returneras för DNS-frågan.

### <a name="endpoint-monitor-status"></a>Status för slutpunktsövervakaren

Slutpunktsövervakarstatus är ett Traffic Manager-genererat värde som visar slutpunktens status. Du kan inte ändra den här inställningen manuellt. Slutpunktsövervakarstatusen är en kombination av resultaten av slutpunktsövervakning och den konfigurerade slutpunktsstatusen. De möjliga värdena för slutpunktsövervakarstatus visas i följande tabell:

| Profilstatus | Slutpunktsstatus | Status för slutpunktsövervakaren | Anteckningar |
| --- | --- | --- | --- |
| Disabled |Enabled |Inaktiv |Profilen har inaktiverats. Även om slutpunktsstatusen är Aktiverad har profilstatusen (Inaktiverad) företräde. Slutpunkter i inaktiverade profiler övervakas inte. En NXDOMAIN-svarskod returneras för DNS-frågan. |
| &lt;alla&gt; |Disabled |Disabled |Slutpunkten har inaktiverats. Inaktiverade slutpunkter övervakas inte. Slutpunkten ingår inte i DNS-svar, därför tar den inte emot trafik. |
| Enabled |Enabled |Online |Slutpunkten övervakas och är felfri. Det ingår i DNS-svar och kan ta emot trafik. |
| Enabled |Enabled |Degraderad |Hälsokontroller för slutpunktsövervakning misslyckas. Slutpunkten ingår inte i DNS-svar och tar inte emot trafik. <br>Ett undantag till detta är om alla slutpunkter försämras, i vilket fall alla av dem anses returneras i frågesvaret).</br>|
| Enabled |Enabled |KontrolleraEndpoint |Slutpunkten övervakas, men resultaten av den första avsökningen har inte tagits emot ännu. CheckingEndpoint är ett tillfälligt tillstånd som vanligtvis inträffar omedelbart efter att du har lagt till eller aktiverat en slutpunkt i profilen. En slutpunkt i det här tillståndet ingår i DNS-svar och kan ta emot trafik. |
| Enabled |Enabled |Stoppad |Webbappen som slutpunkten pekar på körs inte. Kontrollera inställningarna för webbappar. Detta kan också inträffa om slutpunkten är av typen kapslad slutpunkt och den underordnade profilen är inaktiverad eller inaktiv. <br>En slutpunkt med stoppad status övervakas inte. Det ingår inte i DNS-svar och tar inte emot trafik. Ett undantag till detta är om alla slutpunkter försämras, i vilket fall alla av dem kommer att anses returneras i frågesvaret.</br>|

Mer information om hur slutpunktsövervakarstatus beräknas för kapslade slutpunkter finns [i kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

>[!NOTE]
> En stoppad slutpunktsövervakarestatus kan inträffa på App Service om webbprogrammet inte körs på standardnivån eller högre. Mer information finns i [Traffic Manager-integrering med App Service](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Status för profilövervakare

Profilövervakarstatusen är en kombination av den konfigurerade profilstatusen och statusvärdena för slutpunktsövervakaren för alla slutpunkter. Möjliga värden beskrivs i följande tabell:

| Profilstatus (som konfigurerad) | Status för slutpunktsövervakaren | Status för profilövervakare | Anteckningar |
| --- | --- | --- | --- |
| Disabled |&lt;eller&gt; en profil utan definierade slutpunkter. |Disabled |Profilen har inaktiverats. |
| Enabled |Statusen för minst en slutpunkt är Försämrad. |Degraderad |Granska de enskilda slutpunktsstatusvärdena för att avgöra vilka slutpunkter som kräver ytterligare uppmärksamhet. |
| Enabled |Status för minst en slutpunkt är Online. Inga slutpunkter har statusen Försämrad. |Online |Tjänsten tar emot trafik. Ingen ytterligare åtgärd krävs. |
| Enabled |Status för minst en slutpunkt är CheckingEndpoint. Inga slutpunkter är i status online eller försämrad. |KontrolleraEndpoints |Det här övergångstillståndet inträffar när en profil skapas eller aktiveras. Slutpunktshälsan kontrolleras för första gången. |
| Enabled |Statusen för alla slutpunkter i profilen är antingen Inaktiverade eller Stoppade, eller så har profilen inga definierade slutpunkter. |Inaktiv |Inga slutpunkter är aktiva, men profilen är fortfarande aktiverad. |

## <a name="endpoint-failover-and-recovery"></a>Redundans och återställning av slutpunkt

Traffic Manager kontrollerar regelbundet hälsotillståndet för varje slutpunkt, inklusive felaktiga slutpunkter. Traffic Manager identifierar när en slutpunkt blir felfri och för den tillbaka i rotation.

En slutpunkt är felaktig när någon av följande händelser inträffar:

- Om övervakningsprotokollet är HTTP eller HTTPS:
    - Ett svar som inte är 200 eller ett svar som inte innehåller det statusintervall som anges i inställningen **För förväntad statuskodintervall** tas emot (inklusive en annan 2xx-kod eller en 301/302-omdirigering).
- Om övervakningsprotokollet är TCP: 
    - Ett annat svar än ACK eller SYN-ACK tas emot som svar på SYN-begäran som skickas av Traffic Manager för att försöka en anslutningsetablering.
- Timeout. 
- Alla andra anslutningsproblem som resulterar i att slutpunkten inte kan nås.

Mer information om felsÃ¶kning av misslyckade kontroller finns i [Felsöka försämrad status i Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Tidslinjen i följande bild är en detaljerad beskrivning av övervakningsprocessen för Traffic Manager-slutpunkten som har följande inställningar: övervakningsprotokoll är HTTP, avsökningsintervallet är 30 sekunder, antalet tolererade fel är 3, timeout-värdet är 10 sekunder och DNS TTL är 30 sekunder.

![Redundans- och redundanssekvens för Traffic Manager-slutpunkt](./media/traffic-manager-monitoring/timeline.png)

**Bild: Redundans- och återställningssekvens för trafikhanteraren**

1. **FÅ**. För varje slutpunkt utför Traffic Manager-övervakningssystemet en GET-begäran på sökvägen som anges i övervakningsinställningarna.
2. **200 OK eller anpassat kodområde angiven Traffic Manager-profilövervakningsinställningar** . Övervakningssystemet förväntar sig att meddelandet http 200 OK eller det eller anpassade kodintervallet som anges meddelande om övervakning av Traffic Manager-profil ska returneras inom 10 sekunder. När den tar emot det här svaret erkänns att tjänsten är tillgänglig.
3. **30 sekunder mellan kontrollerna**. Hälsokontrollen av slutpunkten upprepas var 30:e sekund.
4. **Tjänsten är inte tillgänglig**. Tjänsten blir otillgänglig. Traffic Manager vet inte förrän nästa hälsokontroll.
5. **Försöker komma åt övervakningsvägen**. Övervakningssystemet utför en GET-begäran, men får inget svar inom tidsgränsen på 10 sekunder (alternativt kan ett svar som inte är 200 tas emot). Det försöker sedan tre gånger, med 30-sekunders intervall. Om ett av försöken lyckas återställs antalet försök.
6. **Status inställd på Försämrad**. Efter ett fjärde fel i följd markerar övervakningssystemet den otillgängliga slutpunktsstatusen som Degraderad.
7. **Trafiken omdirigeras till andra slutpunkter**. Dns-namnservrarna för Traffic Manager uppdateras och Traffic Manager returnerar inte längre slutpunkten som svar på DNS-frågor. Nya anslutningar dirigeras till andra, tillgängliga slutpunkter. Tidigare DNS-svar som innehåller den här slutpunkten kan dock fortfarande cachelagras av rekursiva DNS-servrar och DNS-klienter. Klienter fortsätter att använda slutpunkten tills DNS-cachen upphör att gälla. När DNS-cachen upphör att gälla gör klienter nya DNS-frågor och dirigeras till olika slutpunkter. Cachevaraktigheten styrs av TTL-inställningen i Traffic Manager-profilen, till exempel 30 sekunder.
8. **Hälsokontrollerna fortsätter**. Traffic Manager fortsätter att kontrollera hälsotillståndet för slutpunkten medan den har en försämrad status. Traffic Manager identifierar när slutpunkten återgår till hälsotillstånd.
9. **Tjänsten kommer tillbaka online**. Tjänsten blir tillgänglig. Slutpunkten behåller sin försämrade status i Traffic Manager tills övervakningssystemet utför sin nästa hälsokontroll.
10. **Trafiken till tjänsten återupptas**. Traffic Manager skickar en GET-begäran och får ett statussvar på 200 OK. Tjänsten har återgått till ett felfritt tillstånd. Traffic Manager-namnservrarna uppdateras och de börjar dela ut tjänstens DNS-namn i DNS-svar. Trafiken återgår till slutpunkten som cachelagrade DNS-svar som returnerar andra slutpunkter upphör att gälla och när befintliga anslutningar till andra slutpunkter avslutas.

    > [!NOTE]
    > Eftersom Traffic Manager fungerar på DNS-nivå kan det inte påverka befintliga anslutningar till någon slutpunkt. När trafik mellan slutpunkter (antingen genom ändrade profilinställningar eller under redundans eller redundans) dirigeras nya anslutningar till tillgängliga slutpunkter när den dirigeras mellan slutpunkter (antingen genom ändrade profilinställningar eller vid redundans eller redundans). Andra slutpunkter kan dock fortsätta att ta emot trafik via befintliga anslutningar tills dessa sessioner avslutas. Om du vill att trafiken ska kunna tömmas från befintliga anslutningar bör program begränsa sessionens varaktighet som används för varje slutpunkt.

## <a name="traffic-routing-methods"></a>Metoder för trafikdirigering

När en slutpunkt har status försämrad returneras den inte längre som svar på DNS-frågor. I stället väljs en alternativ slutpunkt och returneras. Den trafikroutningsmetod som konfigurerats i profilen avgör hur den alternativa slutpunkten väljs.

* **Prioritet**. Slutpunkter bildar en prioriterad lista. Den första tillgängliga slutpunkten i listan returneras alltid. Om en slutpunktsstatus försämras returneras nästa tillgängliga slutpunkt.
* **Viktad**. Alla tillgängliga slutpunkter väljs slumpmässigt baserat på deras tilldelade vikter och vikterna för de andra tillgängliga slutpunkterna.
* **Prestanda**. Den slutpunkt som är närmast slutanvändaren returneras. Om slutpunkten inte är tillgänglig flyttar Traffic Manager trafik till slutpunkterna i nästa närmaste Azure-region. Du kan konfigurera alternativa redundansplaner för prestandatrafikroutning med hjälp av [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geografiskt**. Slutpunkten som mappas för att betjäna den geografiska platsen baserat på IP-adressen för frågebegäran returneras. Om slutpunkten inte är tillgänglig markeras inte en annan slutpunkt för redundans, eftersom en geografisk plats bara kan mappas till en slutpunkt i en profil (mer information finns i [vanliga frågor](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)och svar ). Om du använder geografisk routning rekommenderar vi därför kunder att använda kapslade Traffic Manager-profiler med mer än en slutpunkt som slutpunkter för profilen.
* **Flera värden** Flera slutpunkter som mappats till IPv4/IPv6-adresser returneras. När en fråga tas emot för den här profilen returneras felfria slutpunkter baserat på **det maximala postantalet i svarsvärdet** som du har angett. Standardantalet svar är två slutpunkter.
* **Undernät** Slutpunkten som mappas till en uppsättning IP-adressintervall returneras. När en begäran tas emot från den IP-adressen är slutpunkten som returneras den som mappas för den IP-adressen. 

Mer information finns i [Trafikhanterarens trafikroutningsmetoder](traffic-manager-routing-methods.md).

> [!NOTE]
> Ett undantag från normalt trafikroutningsbeteende inträffar när alla kvalificerade slutpunkter har försämrad status. Traffic Manager gör ett "bästa" försök och *svarar som om alla degraderade statusslutpunkter faktiskt är i ett online-tillstånd*. Detta är att föredra framför alternativet, vilket skulle vara att inte returnera någon slutpunkt i DNS-svaret. Inaktiverade eller stoppade slutpunkter övervakas inte, därför anses de inte vara berättigade till trafik.
>
> Det här tillståndet orsakas ofta av felaktig konfiguration av tjänsten, till exempel:
>
> * En åtkomstkontrollista [ACL] som blockerar hälsokontrollerna i Traffic Manager.
> * En felaktig konfiguration av övervakningsporten eller -protokollet i traffic manager-profilen.
>
> Konsekvensen av detta är att om Traffic Manager hälsokontroller inte är korrekt konfigurerade, kan det visas från trafikroutningen som om Traffic *Manager* fungerar korrekt. I det här fallet kan dock redundans för slutpunkt inte inträffa vilket påverkar den övergripande programtillgängligheten. Det är viktigt att kontrollera att profilen visar en onlinestatus, inte en försämrad status. En onlinestatus anger att hälsokontrollerna i Traffic Manager fungerar som förväntat.

Mer information om felsökning av misslyckade hälsokontroller finns i [Felsöka försämrad status i Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Vanliga frågor och svar

* [Är Traffic Manager motståndskraftigt mot Azure-regionfel?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-traffic-manager-resilient-to-azure-region-failures)

* [Hur påverkar valet av resursgruppsplats Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Hur avgör jag den aktuella hälsan för varje slutpunkt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-determine-the-current-health-of-each-endpoint)

* [Kan jag övervaka HTTPS-slutpunkter?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-monitor-https-endpoints)

* [Använder jag en IP-adress eller ett DNS-namn när jag lägger till en slutpunkt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Vilka typer av IP-adresser kan jag använda när jag lägger till en slutpunkt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Kan jag använda olika slutpunktsadresseringstyper i en enda profil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Vad händer när en inkommande frågas posttyp skiljer sig från den posttyp som är associerad med slutpunkternas adresstyp?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Kan jag använda en profil med IPv4 / IPv6-adresserade slutpunkter i en kapslad profil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Jag stoppade en slutpunkt för webbprogram i min Traffic Manager-profil men jag får ingen trafik även efter att jag startat om den. Hur kan jag åtgärda detta?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Kan jag använda Traffic Manager även om mitt program inte har stöd för HTTP eller HTTPS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Vilka specifika svar krävs från slutpunkten när du använder TCP-övervakning?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Hur snabbt flyttar Traffic Manager mina användare bort från en felaktig slutpunkt?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Hur kan jag ange olika övervakningsinställningar för olika slutpunkter i en profil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Hur tilldelar jag HTTP-huvuden till Hälsokontrollerna i Traffic Manager till slutpunkterna?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Vad används hälsokontroller av slutpunkter?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-host-header-do-endpoint-health-checks-use)

* [Vilka IP-adresser kommer hälsokontrollerna från?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Hur många hälsokontroller till min slutpunkt kan jag förvänta mig av Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Hur får jag ett meddelande om en av mina slutpunkter går ner?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Nästa steg

Läs om [hur Traffic Manager fungerar](traffic-manager-how-it-works.md)

Läs mer om [de trafikroutningsmetoder](traffic-manager-routing-methods.md) som stöds av Traffic Manager

Läs om hur du [skapar en Traffic Manager-profil](traffic-manager-manage-profiles.md)

[Felsöka försämrad status](traffic-manager-troubleshooting-degraded.md) på en Traffic Manager-slutpunkt
