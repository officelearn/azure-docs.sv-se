---
title: Instrumentpanel, övervaka, skala, konfigurera, och Hybridanslutningar i BizTalk Services | Microsoft Docs
description: Lär dig mer om kontrollerna och övervaka prestanda för BizTalk Services
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 7a1815db-0de2-4274-8be0-198c1b077324
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 3f4763b5e15d4b9b84e868262a9e8538b8a407a2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228835"
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Granska flikarna instrumentpanel, övervaka, skala, konfigurera och hybridanslutning

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

När du skapar BizTalk Service och distribuera ditt program, kan du ändra vissa inställningar för BizTalk Service och övervaka programmets prestanda. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Då öppnas ett nytt fönster med följande flikar. Det här avsnittet beskrivs de här flikarna.

## <a name="quickstart-quickstartquickstart"></a>Snabbstart (![Snabbstart][Quickstart])
Alla alternativ som visas kanske inte tillgänglig beroende på BizTalk Services-version. 

<table border="1">
    <tr>
        <td><strong>Hämta verktygen</strong></td>
        <td>Ladda ned BizTalk Services SDK för att installera Visual Studio-projektmallar på din lokala utvecklingsdator. De här mallarna skapar den <strong>BizTalk Services</strong> (brygga) och <strong>BizTalk-tjänstens artefakter</strong> (Transform) Visual Studio-projekt som har distribuerats till din BizTalk Service.
        <br/><br/>
        <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302335"> Hur börjar jag använda Azure BizTalk Services SDK </a> och <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=241589">installerar Azure BizTalk Services SDK</a> innehåller instruktioner om hur du kommer igång.
        </td>
    </tr>
    <tr>
        <td><strong>Skapa partner avtal</strong></td>
        <td>Öppnar Azure BizTalk Services-portalen finns i Azure där du lägger till partners och skapa X12, AS2, EDIFACT EDI-avtal och.
        <br/><br/>
        <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurera komponenter för EDI-meddelanden på BizTalk Services-portalen</a> innehåller instruktioner om hur du kommer igång.
        </td>
    </tr>

<tr>
        <td><strong>Läs mer om BizTalk Services</strong></td>
        <td>Gå till den <a HREF="https://azure.microsoft.com/documentation/services/biztalk-services/">learning center</a> vill veta mer om Azure BizTalk Services.</td>
</tr>
</table>


I Aktivitetsfältet längst ned på sidan kan du:

<table border="1">

<tr>
<td><strong>Hantera</strong> programdistributionen</td>
<td>Öppnar Azure BizTalk Services-portalen. BizTalk Services-portalen är ingången till EDI-konfigurationen, inklusive att lägga till partner och skapa X12, AS2, EDIFACT-avtal och.
<br/><br/>
Det här är samma som <strong>skapa partner avtal</strong> på den <strong>Snabbstart</strong> fliken.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurera komponenter för EDI-meddelanden på BizTalk Services-portalen</a> finns mer information om BizTalk Services-portalen.</td>
</tr>

<tr>
<td><strong>Anslutningsinformationen</strong> av Access Control-Namespace</td>
<td>När du väljer anslutningsinformation, visas den åtkomstkontroll Namespace, standard utfärdare och standard nyckel. Du kan kopiera dessa värden.
<br/><br/>
Du kan också öppna Access Control-Portal. <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285670">Skapa en åtkomstkontroll Namespace</a> finns mer information om Access Control-Portal.</td>
</tr>

<tr>
<td><strong>Synkronisera nycklar</strong> i Storage-konto</td>
<td>När du skapar ett lagringskonto skapas automatiskt en primär nyckel och en sekundär nyckel. Dessa krypteringsnycklar styra åtkomsten till ditt Lagringskonto. BizTalk Service använder automatiskt den primärnyckeln. <strong>Synkronisera nycklar</strong> användarna kan växla mellan primärnyckeln och sekundärnyckeln utan att störa BizTalk Service.
<br/><br/>
Exempelvis kan du BizTalk Service att använda en ny primärnyckel för Lagringskontot. Gör så här:
<br/><br/>
<ol>
<li>Välj BizTalk Service och <strong>synkronisera nycklar</strong>. Välj den sekundära nyckeln. När du gör detta startar BizTalk Service med hjälp av den sekundärnyckeln.</li>
<li>Välj ditt lagringskonto och återskapa den primärnyckeln. Kom ihåg att din BizTalk Service använder den sekundärnyckeln.</li>
<li>Välj BizTalk Service och <strong>synkronisera nycklar</strong>. Välj nu den primärnyckeln. Det här är den nya primärnyckeln som du har återskapats.</li>
<li>Välj ditt lagringskonto och återskapa den sekundärnyckeln.</li>
</ol>
<br/>
Den här processen kallas ”förnya nycklar”. Syftet är att användarna kan växla mellan primärnyckeln och sekundärnyckeln utan att störa BizTalk Service.</td>
</tr>

<tr>
<td><strong>Ta bort</strong> ditt program</td>
<td>När du väljer Ta bort din BizTalk Service och alla objekt som har distribuerats till den tas bort.</td>
</tr>
</table>


## <a name="dashboard"></a>Instrumentpanel
Alla alternativ som visas kanske inte tillgänglig beroende på BizTalk Services-version. 

När du väljer namnet på din BizTalk Service visas fliken instrumentpanel. I instrumentpanelen kan du:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Användningsöversikten: Visar antalet använda Hybridanslutningar
Visar även hur data i GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Metrisk diagrammet: Visar en fast lista med prestandamått
Dessa mätvärden är realtidsvärden hälsotillståndet för BizTalk Service. Du kan också välja den **relativa** eller **absolut** värden och tidsintervallet **intervall** över de mått som visas i diagrammet. 

Gå till en beskrivning av de här prestandavärdena [tillgängliga mått](#Metrics) i det här avsnittet.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Snabböversikten: Visar en lista över dina BizTalk Service-egenskaper
<table border="1">

<tr>
<td><strong>Uppdatera autentiseringsuppgifter för spårning av databasen</strong></td>
<td>Ändringar av användarnamn och lösenord som används för att logga in i spårning av databasen.</td>
</tr>
<tr>
<td><strong>Uppdatera SSL-certifikatet</strong></td>
<td>Uppdatera BizTalk Service om du vill använda ett annat SSL-certifikat. Ett självsignerat SSL-certifikat skapas automatiskt när du <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">skapa BizTalk Service</a>.</td>
</tr>
<tr>
<td><strong>Hämta certifikat</strong></td>
<td>Du kan hämta SSL-certifikatet som används av din BizTalk Service till en lokal dator.</td>
</tr>
<tr>
<td><strong>Status</strong></td>
<td>Visar aktuell status för BizTalk Service. Se <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk Services: statusdiagram</a>. </td>
</tr>
<tr>
<td><strong>Tjänst-URL</strong></td>
<td>URL-Adressen för din BizTalk-tjänst. Det här är samma som den <strong>Domänwebbadress</strong> anges när din BizTalk Service skapas.</td>
</tr>
<tr>
<td><strong>Offentliga virtuella IP (VIP)-adressen</strong></td>
<td>IP-adress som tilldelats din BizTalk Service. Den används för alla ingångsslutpunkter och är källadressen för utgående trafik. Den här IP-adressen tillhör BizTalk Service så länge den har skapats. Om du tar bort BizTalk Service tilldelas IP-adressen till en annan BizTalk Service.</td>
</tr>
<tr>
<td><strong>ACS-Namespace</strong></td>
<td>Autentiserar med BizTalk-tjänsten.</td>
</tr>
<tr>
<td><strong>Utgåva</strong></td>
<td>Visar versionen anges när BizTalk Service skapas.</td>
</tr>
<tr>
<td><strong>Plats</strong></td>
<td>Visar den geografiska region som är värd för BizTalk Service.</td>
</tr>
<tr>
<td><strong>Skapat</strong></td>
<td>Visar datum och tid som BizTalk Service har skapats.</td>
</tr>
<tr>
<td><strong>Spårning av databasen</strong></td>
<td>Azure SQL Database-namnet som lagrar spårningstabeller som används av din BizTalk Service. 
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">Krav Explained</a> innehåller information om spårning av databasen.</td>
</tr>
<tr>
<td><strong>Övervakning/arkivering lagring</strong></td>
<td>Namnet för Azure Storage-konto som lagrar övervakningens utdata för BizTalk Service.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=302280">Krav Explained</a> innehåller information om Storage-konto.</td>
</tr>
<tr>
<td><strong>Prenumerationsnamn</strong></td>
<td>Visar den prenumeration som är värd för BizTalk Service. Prenumerationen styr åtkomst.</td>
</tr>
<tr>
<td><strong>prenumerations-ID</strong></td>
<td>När en prenumeration skapas, skapas automatiskt ett prenumerations-ID. När du använder REST API: er kan behöva du ange prenumerations-ID.</td>
</tr>
</table>

[BizTalk Services: Etablering](https://go.microsoft.com/fwlink/p/?LinkID=302280) visar hur du skapar en BizTalk Service.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Hantera anslutningsinformation, synkronisera nycklar och ta bort i Aktivitetsfältet:
<table border="1">

<tr>
<td><strong>Hantera</strong> programdistributionen</td>
<td>Öppnar Azure BizTalk Services-portalen. BizTalk Services-portalen är ingången till EDI-konfigurationen, inklusive att lägga till partner och skapa X12, AS2, EDIFACT-avtal och.
<br/><br/>
Det här är samma som <strong>skapa partner avtal</strong> på den <strong>Snabbstart</strong> fliken.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=303653">Konfigurera komponenter för EDI-meddelanden på BizTalk Services-portalen</a> finns mer information om BizTalk Services-portalen.</td>
</tr>
<tr>
<td><strong>Anslutningsinformationen</strong> av Access Control-Namespace</td>
<td>Visar åtkomstkontroll Namespace, standard utfärdare och värden för standard-nyckel. som kan kopieras.
<br/><br/>
Du kan också öppna Access Control-Portal. Den här portalen för kontroll av åtkomst är detsamma som att använda Active Directory-alternativet i det vänstra navigeringsfönstret.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285670">Hantera ditt ACS-Namespace</a> finns mer information om Access Control-Portal.</td>
</tr>
<tr>
<td><strong>Synkronisera nycklar</strong> i Storage-konto</td>
<td>När du skapar ett lagringskonto skapas automatiskt en primär nyckel och en sekundär nyckel. Dessa krypteringsnycklar styra åtkomsten till ditt Lagringskonto. BizTalk Service använder automatiskt den primärnyckeln. <strong>Synkronisera nycklar</strong> användarna kan växla mellan primärnyckeln och sekundärnyckeln utan att störa BizTalk Service.
<br/><br/>
Exempelvis kan du BizTalk Service att använda en ny primärnyckel för Lagringskontot. Gör så här:
<br/><br/>
<ol>
<li>Välj BizTalk Service och <strong>synkronisera nycklar</strong>. Välj den sekundära nyckeln. När du gör detta startar BizTalk Service med hjälp av den sekundärnyckeln.</li>
<li>Välj ditt lagringskonto och återskapa den primärnyckeln. Kom ihåg att din BizTalk Service använder den sekundärnyckeln.</li>
<li>Välj BizTalk Service och <strong>synkronisera nycklar</strong>. Välj nu den primärnyckeln. Det här är den nya primärnyckeln som du har återskapats.</li>
<li>Välj ditt lagringskonto och återskapa den sekundärnyckeln.</li>
</ol>
<br/>
Den här processen kallas ”förnya nycklar”. Syftet är att användarna kan växla mellan primärnyckeln och sekundärnyckeln utan att störa BizTalk Service.</td>
</tr>

<tr>
<td><strong>Ta bort</strong> ditt program</td>
<td>BizTalk Service och alla objekt som har distribuerats till den tas bort.</td>
</tr>
</table>


## <a name="monitor"></a>Övervaka
Gäller inte för den kostnadsfria versionen.

När du väljer namnet på din BizTalk Service övervakningsfliken är tillgängligt och visar följande:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>: Mått visas valda prestandamått
Dessa mätvärden är realtidsvärden hälsotillståndet för BizTalk Service. Du kan välja vilka prestandamått visas. Upp till sex prestandamått kan användas samtidigt. 

Du kan också välja den **relativa** eller **absolut** värden och tidsintervallet **intervall** över de mått som visas. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Att ta bort eller visa mått i diagrammet:
1. Välj den **övervakaren** fliken.
2. Välj **lägga till mått** i Aktivitetsfältet:  
   ![Välj Lägg till mått][AddMetrics]
3. Kontrollera prestandamått som du vill visa.
4. Välj bockmarkeringen för att återgå till den **övervakaren** fliken.
5. Välj på cirkeln bredvid ett mått som ska visa värdet för den mått i diagrammet.  
   
    Till exempel den **CPU-användning** mått är nedtonat; dess utdata visas inte i diagrammet:  
   ![CPU-användningsmått är nedtonat][GrayedMetric]  
   
    Välj den nedtonad ut cirkel om du vill aktivera den **CPU-användning** mått att visa dess utdata i diagrammet:  
   ![CPU-användningsmått är aktiverat][EnabledMetric]
6. Om du vill ta bort ett mått från visa diagrammet och listan, Välj **ta bort måttet** i Aktivitetsfältet. Om du vill lägga till mått tillbaka i listan, Välj **lägga till mått** Kontrollera måttet i Aktivitetsfältet och välj bockmarkeringen för att återgå till den **övervakaren** fliken. Välj den nedtonad ut cirkel om du vill aktivera måttet.

## <a name="Metrics"></a>Tillgängliga mått
Följande räknare/prestandavärden är tillgängliga:

<table border="1">

<tr>
<td><strong>RountdTrip svarstid</strong></td>
<td>Visar Genomsnittlig tid i millisekunder (ms) att bearbeta ett meddelande från tidpunkten som meddelandet tas emot tills meddelandet bearbetas fullständigt av BizTalk Service över alla bryggor. Endast meddelanden som har bearbetat räknas.<br/><br/>
När följande händelser inträffar, skapas en tidsstämpel:
<ul>
<li>Meddelandet anger gatewayen</li>
<li>Dirigeras meddelandet till målet</li>
<li>Mål-svar tas emot</li>
<li>Mål bekräftelse svar som skickas till gatewayen</li>
</ul>
<br/>
Det här måttet visar resultatet av följande beräkning:
<br/><br/>
[Mål bekräftelse svar som skickas till gatewayen] - [meddelandet anger gatewayen]</td>
</tr>
<tr>
<td><strong>Fel vid källa</strong></td>
<td>Visar det totala antalet meddelanden som inte av BizTalk Service när du hämtar meddelanden från käll-slutpunkter.</td>
</tr>
<tr>
<td><strong>CPU-användning</strong></td>
<td>Visar den genomsnittliga % processortid för alla rollinstanser.</td>
</tr>
<tr>
<td><strong>Bearbetning av svarstid</strong></td>
<td>Visar Genomsnittlig tid i millisekunder (ms) att bearbeta ett meddelande av BizTalk Service över alla bryggor, exklusive tid som mål. Endast meddelanden som har bearbetat räknas.<br/><br/>
När var och en av följande händelser inträffar, skapas en tidsstämpel:

<ul>
<li>Meddelandet anger gatewayen</li>
<li>Dirigeras meddelandet till målet</li>
<li>Mål-svar tas emot</li>
<li>Mål bekräftelse svar som skickas till gatewayen</li>
</ul>
<br/>Det här måttet visar resultatet av följande beräkning:<br/><br/>
[Mål bekräftelse svar som skickas till gatewayen] - [meddelandet anger gatewayen] - [mål-svar tas emot] + [dirigeras meddelandet till målet]</td>
</tr>
<tr>
<td><strong>Fel i processen</strong></td>
<td>Visar det totala antalet meddelanden som misslyckades under bearbetning av BizTalk Service över alla bryggor inom ett tidsintervall.</td>
</tr>
<tr>
<td><strong>Meddelanden som skickas</strong></td>
<td>Visar det totala antalet meddelanden som skickas av BizTalk Service över alla bryggor inom ett tidsintervall. Det här måttet ökar stegvis när ett meddelande som skickas från en pipeline når målet väg. Det här måttet anger inte att meddelandet har bearbetats.<br/><br/>
I ett scenario med begäran / svar-ökar måttet stegvis när målet vägen skickar ett kvitto bekräftelse till pipelinen.</td>
</tr>
<tr>
<td><strong>Mottagna meddelanden</strong></td>
<td>Visar det totala antalet meddelanden som tas emot av BizTalk Service över alla bryggor inom ett tidsintervall. Det här måttet ökar stegvis när ett nytt meddelande tas emot av pipelinen.</td>
</tr>
<tr>
<td><strong>Meddelanden i processen</strong></td>
<td>Visar det totala antalet meddelanden som för närvarande bearbetas av BizTalk Service inom ett tidsintervall.</td>
</tr>
<tr>
<td><strong>Behandlade meddelanden</strong></td>
<td>Visar det totala antalet meddelanden som har behandlats av BizTalk Service över alla bryggor inom ett tidsintervall. Det här måttet ökar stegvis när ett meddelande har tagits emot av pipelinen och kunna vidarebefordras till målet.</td>
</tr>
</table>


## <a name="scale"></a>Skala
På fliken Skala, kan du lägga till eller ta bort antalet enheter som används av din BizTalk Service. Som standard är det en enhet har konfigurerats. Ytterligare enheter kan läggas till att skala din BizTalk Service. Om du ökar skalan ökar du dataflödet. Mängden resurser ökar också, inklusive distribuerade bryggor, avtal, LOB-anslutningar och processorkraft. Exempelvis kan öka du skala från 1 enhet till 2 enheter. I så fall kan du distribuera dubbelt så många bryggor, dubbelklicka avtalen, dubbelklicka LOB-anslutningar och dubbelklicka bearbetningen.

Vissa BizTalk-versioner erbjuder inte ett alternativ för skala. I så fall kan är en enhet tillåtet. För att avgöra hur många enheter som din utgåva kan skalas, som avser [BizTalk Services: diagram över utgåvor](biztalk-editions-feature-chart.md).

Öka antalet enheter kan påverka priser. Om du ökar enheterna, välja **spara** visar ett meddelande som talar om fakturering påverkas. Du sedan välja att fortsätta. När du ökar antalet enheter, BizTalk Service status ändras från aktiv till uppdatering. Tillståndet uppdaterar fortsätter BizTalk Service att köras.

[BizTalk Services: Diagram över utgåvor](biztalk-editions-feature-chart.md) definierar en ”enhet”.

## <a name="configure"></a>Konfigurera
Gäller inte för Hybridanslutningar.

Anger Status för säkerhetskopiering som None eller automatiskt. När värdet None, skapas inga säkerhetskopieringar automatiskt. När inställd på automatisk, konfigurerar du säkerhetskopieringsplatsen-frekvensen för säkerhetskopierings-, och hur lång tid att behålla de säkerhetskopiera filerna. 

[BizTalk Services: Säkerhetskopiering och återställning](biztalk-backup-restore.md) ger detaljerad information. 

## <a name="HybridConnections"></a>Hybridanslutningar
Hybridanslutningar ansluta Azure-program, t.ex. Web Apps eller Mobile Apps i Azure App Service till en lokal resurs som använder en statisk TCP-port, till exempel SQL Server, MySQL, HTTP-webb-API: er och de flesta anpassade webbtjänster. Hybridanslutningar hanteras i BizTalk Services.

Om du vill skapa och hantera Hybridanslutningar i Azure BizTalk Services, se [Hybridanslutningar](integration-hybrid-connection-overview.md).

## <a name="next"></a>Nästa
Nu när du är bekant med de olika flikarna kan läsa du mer om Azure BizTalk Services-funktioner:

* [BizTalk Services: Begränsning](biztalk-throttling-thresholds.md)  
* [BizTalk Services: Utfärdarens namn och nyckel](biztalk-issuer-name-issuer-key.md)  
* [BizTalk Services: Säkerhetskopiering och återställning](biztalk-backup-restore.md)

## <a name="see-also"></a>Se även
* [Hybridanslutningar](integration-hybrid-connection-overview.md)  
* [BizTalk Services: Developer, Basic, Standard och Premium diagram över utgåvor](biztalk-editions-feature-chart.md)  
* [BizTalk Services: etablering](biztalk-provision-services.md)  
* [BizTalk Services: BizTalk-tjänsten statusdiagram](biztalk-service-state-chart.md)  
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

