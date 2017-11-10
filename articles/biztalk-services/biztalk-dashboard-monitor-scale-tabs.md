---
title: "Instrumentpanelen, övervaka, skala, konfigurera och Hybridanslutningar i BizTalk-tjänst | Microsoft Docs"
description: "Läs mer om kontroller och övervaka prestanda för BizTalk-tjänst"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7a1815db-0de2-4274-8be0-198c1b077324
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 351809cd5f165a863dc02bfadf78fa59cbaabfd7
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Granska flikarna instrumentpanel, övervaka, skala, konfigurera och hybridanslutning

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

När du skapar BizTalk Service och distribuera ditt program kan du ändra vissa inställningar BizTalk Service och övervaka programprestanda. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Detta öppnar ett nytt fönster med följande flikar. Det här avsnittet beskrivs de här flikarna.

## <a name="quickstart-quickstartquickstart"></a>Snabbstart (![Snabbstart][Quickstart])
Beroende på utgåva BizTalk-tjänster kanske inte alla alternativ som finns tillgängliga. 

<table border="1">
    <tr>
        <td><strong>Skaffa dig verktyg</strong></td>
        <td>Hämta BizTalk Services SDK för att installera Visual Studio-projektmallar på utvecklingsdatorn lokalt. De här mallarna skapar den <strong>BizTalk-tjänst</strong> (brygga) och <strong>BizTalk-tjänstens artefakter</strong> (Transform) Visual Studio-projekt som har distribuerats till BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Hur börjar jag använda Azure BizTalk Services SDK </a> och <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">installerar Azure BizTalk Services SDK</a> innehåller instruktioner om hur du kommer igång.
        </td>
    </tr>
    <tr>
        <td><strong>Skapa partner avtal</strong></td>
        <td>Öppnar Azure BizTalk-Services-portalen i Azure där du lägger till partner och skapa X12 AS2-, och EDI EDIFACT-avtal.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurera komponenter för EDI-meddelanden på BizTalk-Services-portalen</a> innehåller instruktioner om hur du kommer igång.
        </td>
    </tr>

<tr>
        <td><strong>Mer information om BizTalk-tjänst</strong></td>
        <td>Gå till den <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">learning center</a> lära dig mer om Azure BizTalk-tjänst.</td>
</tr>
</table>


I Aktivitetsfältet längst ned kan du:

<table border="1">

<tr>
<td><strong>Hantera</strong> programdistributionen</td>
<td>Öppnar BizTalk-tjänst för Azure-portalen. BizTalk-Services-portalen är ingången till EDI-konfigurationen, inklusive lägga till partner och skapa X12 AS2-, och EDIFACT-avtal.
<br/><br/>
Det här är samma som <strong>skapa partner avtal</strong> på den <strong>Snabbstart</strong> fliken.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurera komponenter för EDI-meddelanden på BizTalk-Services-portalen</a> finns mer information om BizTalk-Services-portalen.</td>
</tr>

<tr>
<td><strong>Anslutningsinformationen</strong> av Access Control Namespace</td>
<td>När du väljer anslutningsinformationen visas Access Control Namespace, standard utfärdaren och som standard nyckel. Du kan kopiera dessa värden.
<br/><br/>
Du kan också öppna Access Control-portalen. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Skapa en åtkomstkontroll Namespace</a> finns mer information om Access Control-portalen.</td>
</tr>

<tr>
<td><strong>Synkronisera nycklar</strong> i Storage-konto</td>
<td>När du skapar ett lagringskonto skapas automatiskt en primär nyckel och en sekundär nyckel. Dessa krypteringsnycklar styra åtkomsten till ditt Lagringskonto. BizTalk Service använder automatiskt den primärnyckeln. <strong>Synkronisera nycklar</strong> användarna att växla mellan primärnyckeln och sekundärnyckeln utan att störa BizTalk Service.
<br/><br/>
Till exempel du BizTalk Service att använda en ny primärnyckel för Lagringskontot. Gör så här:
<br/><br/>
<ol>
<li>Välj BizTalk Service och välj <strong>synkronisera nycklar</strong>. Välj den sekundära nyckeln. När du gör detta startar BizTalk Service med hjälp av den sekundärnyckeln.</li>
<li>Välj ditt lagringskonto och återskapa den primärnyckeln. Kom ihåg att BizTalk Service med hjälp av den sekundärnyckeln.</li>
<li>Välj BizTalk Service och välj <strong>synkronisera nycklar</strong>. Nu väljer du den primärnyckeln. Det här är den nya primärnyckeln du återskapas.</li>
<li>Välj ditt lagringskonto och återskapa den sekundärnyckeln.</li>
</ol>
<br/>
Den här processen kallas ”förnyelse nycklar”. Syftet är att användarna ska växla mellan primärnyckeln och sekundärnyckeln utan att störa BizTalk Service.</td>
</tr>

<tr>
<td><strong>Ta bort</strong> ditt program</td>
<td>När du väljer Ta bort BizTalk Service och alla objekt som har distribuerats till det tas bort.</td>
</tr>
</table>


## <a name="dashboard"></a>Instrumentpanel
Beroende på utgåva BizTalk-tjänster kanske inte alla alternativ som finns tillgängliga. 

När du väljer BizTalk Service namn visas fliken instrumentpanel. I instrumentpanelen kan du:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Översikt för användning: Visar antalet använda Hybridanslutningar
Visar även hur data i GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Mått diagrammet: Visar en fast lista med prestandamått
De här måtten ange realtid värden om hälsotillståndet för BizTalk Service. Du kan också välja den **relativa** eller **absolut** värden och tidsintervallet **intervall** mätvärden som visas i diagrammet. 

En beskrivning av dessa prestandamått, gå till [tillgängliga mått](#Metrics) i det här avsnittet.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Snabböversikten: Visar en lista över BizTalk Service-egenskaper
<table border="1">

<tr>
<td><strong>Uppdatera autentiseringsuppgifterna för spårning av databasen</strong></td>
<td>Ändrar användarnamn och lösenord som används för att logga in på databasen för spårning.</td>
</tr>
<tr>
<td><strong>Uppdatera SSL-certifikat</strong></td>
<td>Uppdatera BizTalk Service om du vill använda ett annat SSL-certifikat. Ett självsignerat certifikat för SSL skapas automatiskt när du <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">skapa BizTalk Service</a>.</td>
</tr>
<tr>
<td><strong>Hämta certifikatet</strong></td>
<td>Du kan hämta SSL-certifikatet som används av din BizTalk Service till en lokal dator.</td>
</tr>
<tr>
<td><strong>Status</strong></td>
<td>Visar den aktuella statusen för BizTalk Service. Se <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk-tjänst: tjänsten tillstånd diagram</a>. </td>
</tr>
<tr>
<td><strong>Tjänst-URL</strong></td>
<td>URL till BizTalk-tjänst. Det här är samma som den <strong>domän-URL</strong> anges när BizTalk Service skapas.</td>
</tr>
<tr>
<td><strong>Offentliga virtuella IP (VIP)-adressen</strong></td>
<td>Den IP-adress som tilldelats BizTalk Service. Den används för alla inkommande slutpunkter och är källadress för utgående trafik. IP-adressen hör till BizTalk Service så länge den har skapats. Om du tar bort BizTalk Service har IP-adress tilldelats en annan BizTalk Service.</td>
</tr>
<tr>
<td><strong>ACS-Namespace</strong></td>
<td>Verifierar med BizTalk-tjänst.</td>
</tr>
<tr>
<td><strong>Utgåva</strong></td>
<td>Visar utgåvan anges när BizTalk Service skapas.</td>
</tr>
<tr>
<td><strong>Plats</strong></td>
<td>Visar den geografiska region som är värd för BizTalk Service.</td>
</tr>
<tr>
<td><strong>Skapa</strong></td>
<td>Visar datum och tid BizTalk Service skapades.</td>
</tr>
<tr>
<td><strong>Spårning av databasen</strong></td>
<td>Azure SQL Database-namnet som lagrar spårningstabeller som används av BizTalk Service. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Krav för Explained</a> innehåller information om spårning av databasen.</td>
</tr>
<tr>
<td><strong>Övervaka/arkivering lagring</strong></td>
<td>Azure Storage-kontonamnet som lagrar övervakning utdata från BizTalk Service.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Krav för Explained</a> innehåller information om lagringskontot.</td>
</tr>
<tr>
<td><strong>Prenumerationsnamn</strong></td>
<td>Visar den prenumeration som är värd för BizTalk Service. Prenumerationen reglerar åtkomsten.</td>
</tr>
<tr>
<td><strong>Prenumerations-ID</strong></td>
<td>Ett prenumerations-ID genereras automatiskt när en prenumeration har skapats. När du använder REST API: er kan behöva du ange prenumerations-ID.</td>
</tr>
</table>

[BizTalk-tjänst: Etablering](http://go.microsoft.com/fwlink/p/?LinkID=302280) visar hur du skapar en BizTalk Service.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Hantera anslutningsinformationen, synkronisera nycklar, och ta bort i Aktivitetsfältet:
<table border="1">

<tr>
<td><strong>Hantera</strong> programdistributionen</td>
<td>Öppnar Azure BizTalk-Services-portalen. BizTalk-Services-portalen är ingången till EDI-konfigurationen, inklusive lägga till partner och skapa X12 AS2-, och EDIFACT-avtal.
<br/><br/>
Det här är samma som <strong>skapa partner avtal</strong> på den <strong>Snabbstart</strong> fliken.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurera komponenter för EDI-meddelanden på BizTalk-Services-portalen</a> finns mer information om BizTalk-Services-portalen.</td>
</tr>
<tr>
<td><strong>Anslutningsinformationen</strong> av Access Control Namespace</td>
<td>Visar Access Control Namespace, standard utfärdaren och som standard nyckeln värden. som kan kopieras.
<br/><br/>
Du kan också öppna Access Control-portalen. Access Control portalen är detsamma som att använda Active Directory-alternativet i det vänstra navigeringsfönstret.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Hantera din ACS-Namespace</a> finns mer information om Access Control-portalen.</td>
</tr>
<tr>
<td><strong>Synkronisera nycklar</strong> i Storage-konto</td>
<td>När du skapar ett lagringskonto skapas automatiskt en primär nyckel och en sekundär nyckel. Dessa krypteringsnycklar styra åtkomsten till ditt Lagringskonto. BizTalk Service använder automatiskt den primärnyckeln. <strong>Synkronisera nycklar</strong> användarna att växla mellan primärnyckeln och sekundärnyckeln utan att störa BizTalk Service.
<br/><br/>
Till exempel du BizTalk Service att använda en ny primärnyckel för Lagringskontot. Gör så här:
<br/><br/>
<ol>
<li>Välj BizTalk Service och välj <strong>synkronisera nycklar</strong>. Välj den sekundära nyckeln. När du gör detta startar BizTalk Service med hjälp av den sekundärnyckeln.</li>
<li>Välj ditt lagringskonto och återskapa den primärnyckeln. Kom ihåg att BizTalk Service med hjälp av den sekundärnyckeln.</li>
<li>Välj BizTalk Service och välj <strong>synkronisera nycklar</strong>. Nu väljer du den primärnyckeln. Det här är den nya primärnyckeln du återskapas.</li>
<li>Välj ditt lagringskonto och återskapa den sekundärnyckeln.</li>
</ol>
<br/>
Den här processen kallas ”förnyelse nycklar”. Syftet är att användarna ska växla mellan primärnyckeln och sekundärnyckeln utan att störa BizTalk Service.</td>
</tr>

<tr>
<td><strong>Ta bort</strong> ditt program</td>
<td>BizTalk Service och alla objekt som har distribuerats till det tas bort.</td>
</tr>
</table>


## <a name="monitor"></a>Övervaka
Gäller inte för den kostnadsfria versionen.

När du markerar du namnet på BizTalk Service övervakningsfliken är tillgängligt och visar följande:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>: Mått visas valda prestandamått
De här måtten ange realtid värden om hälsotillståndet för BizTalk Service. Du kan välja vilka prestandamått visas. Maximalt sex prestandamått kan visas samtidigt. 

Du kan också välja den **relativa** eller **absolut** värden och tidsintervallet **intervall** mätvärden som visas. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Att ta bort eller visa mått i diagrammet:
1. Välj den **övervakaren** fliken.
2. Välj **lägga till mätvärden** i Aktivitetsfältet:  
   ![Välj Lägg till mått][AddMetrics]
3. Kontrollera prestandamått som du vill visa.
4. Välj på bockmarkeringen för att återgå till den **övervakaren** fliken.
5. Välj cirkel bredvid mått att visa den mått i diagrammet.  
   
    Till exempel den **CPU-användning** mått är nedtonad; utdata visas inte i diagrammet:  
   ![CPU-användning mått är nedtonad][GrayedMetric]  
   
    Välj den nedtonad ut cirkel om du vill aktivera den **CPU-användning** mått att visa dess utdata i diagrammet:  
   ![CPU-användning mått är aktiverad][EnabledMetric]
6. Om du vill ta bort ett mått från visa diagrammet och listan, Välj **ta bort måttet** i Aktivitetsfältet. Om du vill lägga till mått tillbaka i listan, Välj **lägga till mätvärden** Kontrollera måttet i Aktivitetsfältet och välj på bockmarkeringen för att återgå till den **övervakaren** fliken. Välj den nedtonad ut cirkel om du vill aktivera mått.

## <a name="Metrics"></a>Tillgängliga mått
Följande räknare/prestandamåtten är tillgängliga:

<table border="1">

<tr>
<td><strong>RountdTrip svarstid</strong></td>
<td>Visar Genomsnittlig tid i millisekunder (ms) för att bearbeta ett meddelande från tidpunkten som meddelandet tas emot förrän meddelandet bearbetas fullständigt av BizTalk Service alla bryggor. Endast meddelanden som har bearbetat räknas.<br/><br/>
När följande händelser inträffar, skapas en tidsstämpel:
<ul>
<li>Meddelandet anger gatewayen</li>
<li>Meddelandet dirigeras till målet</li>
<li>Mål-svar har tagits emot</li>
<li>Mål bekräftelse svar som har skickats till gatewayen</li>
</ul>
<br/>
Det här måttet visar resultatet av följande beräkning:
<br/><br/>
[Mål bekräftelse svar som har skickats till gateway] - [meddelandet anger gatewayen]</td>
</tr>
<tr>
<td><strong>Fel vid källan</strong></td>
<td>Visar det totala antalet meddelanden som inte godkänts av BizTalk Service när hämta meddelanden från käll-slutpunkter.</td>
</tr>
<tr>
<td><strong>CPU-användning</strong></td>
<td>Visar den genomsnittliga % processortid för alla rollinstanser.</td>
</tr>
<tr>
<td><strong>Svarstid för bearbetning</strong></td>
<td>Visar Genomsnittlig tid i millisekunder (ms) för att bearbeta ett meddelande av BizTalk Service alla bryggor, exklusive tid som mål. Endast meddelanden som har bearbetat räknas.<br/><br/>
En tidsstämpel skapas när var och en av följande händelser inträffar:

<ul>
<li>Meddelandet anger gatewayen</li>
<li>Meddelandet dirigeras till målet</li>
<li>Mål-svar har tagits emot</li>
<li>Mål bekräftelse svar som har skickats till gatewayen</li>
</ul>
<br/>Det här måttet visar resultatet av följande beräkning:<br/><br/>
[Mål bekräftelse svar som har skickats till gateway] - [meddelandet anger gatewayen] - [mål svar] + [meddelandet dirigeras till målet]</td>
</tr>
<tr>
<td><strong>Fel i processen</strong></td>
<td>Visar det totala antalet meddelanden som misslyckades under bearbetning av BizTalk Service över alla bryggor inom ett tidsintervall.</td>
</tr>
<tr>
<td><strong>Meddelanden som skickas</strong></td>
<td>Visar det totala antalet meddelanden som skickas av BizTalk Service över alla bryggor inom ett tidsintervall. Det här måttet ökar stegvis när ett meddelande som skickas från en pipeline når väg mål. Det här måttet visar inte att ett meddelande har bearbetat.<br/><br/>
Måttet är i ett scenario med Request-Reply stegvis när väg mål skickar en bekräftelse för inleverans tillbaka till pipelinen.</td>
</tr>
<tr>
<td><strong>Mottagna meddelanden</strong></td>
<td>Visar det totala antalet meddelanden som tagits emot av BizTalk Service över alla bryggor inom ett tidsintervall. Det här måttet ökar stegvis när ett nytt meddelande tas emot av pipeline.</td>
</tr>
<tr>
<td><strong>Meddelanden i processen</strong></td>
<td>Visar det totala antalet meddelanden som för närvarande bearbetas av BizTalk Service inom ett tidsintervall.</td>
</tr>
<tr>
<td><strong>Meddelanden som bearbetas</strong></td>
<td>Visar det totala antalet meddelanden som har behandlats av BizTalk Service över alla bryggor inom ett tidsintervall. Det här måttet ökar stegvis när ett meddelande har tagits emot av pipeline och kunna vidarebefordras till målet.</td>
</tr>
</table>


## <a name="scale"></a>Skala
Du kan lägga till eller subtraheras antalet enheter som används av BizTalk-Service på fliken Skala. Som standard finns en enhet är konfigurerad. Ytterligare enheter kan läggas till skala BizTalk Service. Om du ökar skalan ökar genomströmningen. Mängden resurser som ökar också inklusive distribuerade platslänkbryggor, avtal, LOB-anslutningar och processorkraft. Exempelvis kan du öka skala från 1 enhet till 2 enheter. I så fall kan du distribuera dubbelt så många bryggor, dubbla avtalen, dubbla LOB-anslutningar och dubbla processorkraften.

Vissa versioner av BizTalk ger inte ett alternativ för skala. I så fall kan är en enhet tillåtet. För att avgöra hur många enheter som den här versionen kan skalas avser [BizTalk-tjänst: utgåvor diagram](biztalk-editions-feature-chart.md).

Öka antalet enheter som kan påverka priser. Om du ökar enheterna, välja **spara** visar ett meddelande som talar om fakturering påverkas. Du sedan välja att fortsätta. När du ökar antalet enheter, BizTalk Service status ändras från aktiv till uppdatering. BizTalk Service fortsätter att köras i läget uppdatering.

[BizTalk-tjänst: Utgåvor diagram](biztalk-editions-feature-chart.md) definierar ”enhet”.

## <a name="configure"></a>Konfigurera
Gäller inte för Hybridanslutningar.

Anger Status för säkerhetskopiering till None eller automatisk. När inställd på None, skapas inga säkerhetskopior automatiskt. När inställd på automatisk, konfigurera du platsen för säkerhetskopiering, frekvens för säkerhetskopiering och hur lång tid att behålla de säkerhetskopiera filerna. 

[BizTalk-tjänst: Säkerhetskopiera och återställa](biztalk-backup-restore.md) visar information. 

## <a name="HybridConnections"></a>Hybridanslutningar
Hybridanslutningar ansluter ett Azure-program, t.ex. Webbappar eller Mobilappar i Azure App Service till en lokal resurs som använder en statisk TCP-port, till exempel SQL Server, MySQL, http-webb-API: er och de flesta anpassade webbtjänster. Hybridanslutningar hanteras i BizTalk-tjänst.

Om du vill skapa eller hantera Hybridanslutningar i Azure BizTalk Services, se [Hybridanslutningar](integration-hybrid-connection-overview.md).

## <a name="next"></a>Nästa
Nu när du är bekant med de olika flikarna kan du lära dig mer om Azure BizTalk-Services-funktioner:

* [BizTalk Services: Begränsning](biztalk-throttling-thresholds.md)  
* [BizTalk Services: Utfärdarens namn och nyckel](biztalk-issuer-name-issuer-key.md)  
* [BizTalk Services: Säkerhetskopiering och återställning](biztalk-backup-restore.md)

## <a name="see-also"></a>Se även
* [Hybridanslutningar](integration-hybrid-connection-overview.md)  
* [BizTalk-tjänst: Utvecklare, Basic, Standard och Premium-utgåvor diagram](biztalk-editions-feature-chart.md)  
* [BizTalk-tjänst: etablering](biztalk-provision-services.md)  
* [BizTalk-tjänst: BizTalk-tjänstens tillstånd-diagram](biztalk-service-state-chart.md)  
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

