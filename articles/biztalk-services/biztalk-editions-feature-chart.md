<properties
    pageTitle="Läs mer om funktioner i BizTalk Services-utgåvorna | Microsoft Azure"
    description="Jämför funktionerna i BizTalk Services-utgåvorna: Free, Developer, Basic, Standard och Premium. MABS, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# BizTalk Services: Diagram över utgåvor

Azure BizTalk Services erbjuder flera olika utgåvor. Läs den här artikeln för att se vilken utgåva som passar dina scenario- och affärsbehov bäst.


## Jämför utgåvorna

**Kostnadsfri (förhandsversion)**

Kan skapa och hantera hybridanslutningar. En hybridanslutning är ett enkelt sätt att ansluta en Azure-webbplats till ett lokalt system, t.ex. SQL Server.

**Developer**

Innehåller hybridanslutningar, EAI- och EDI-meddelandebehandling med en lättanvänd hanteringsportal för handelspartners, samt stöd för vanliga EDI-scheman och omfattande EDI-bearbetning över X12 och AS2. Kan skapa vanliga EAI-scenarier som ansluter tjänster i molnet med valfritt HTTP/S-, REST-, FTP-, WCF- och SFTP-protokoll för att kunna läsa och skriva meddelanden.  Anslut till lokala LOB-system med färdiga SAP-, Oracle eBusiness-, Oracle DB-, Siebel- och SQL Server-nätverkskort. Använd en centrerad miljö för utvecklare med Visual Studio-verktyg för enkel utveckling och distribution. Begränsad till enbart utveckling och testning utan något serviceavtal (SLA).

**Basic**

Innehåller de flesta funktioner som utvecklare behöver med utökade hybridanslutningar, EAI-bryggor, EDI-avtal och BizTalk Adapter Pack-anslutningar. Ger också hög tillgänglighet samt alternativ för skalning med ett serviceavtal (SLA).

**Standard**

Innehåller alla Basic-funktionerna med utökade hybridanslutningar, EAI-bryggor, EDI-avtal och BizTalk Adapter Pack-anslutningar. Ger också hög tillgänglighet samt alternativ för skalning med ett serviceavtal (SLA).

**Premium**

Innehåller alla Standard-funktionerna med utökade hybridanslutningar, EAI-bryggor, EDI-avtal och BizTalk Adapter Pack-anslutningar. Innehåller också arkivering, hög tillgänglighet samt alternativ för skalning med ett serviceavtal (SLA).


## Diagram över utgåvor
I nedanstående tabell visas skillnaderna.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Kostnadsfri (förhandsversion)</th>
        <th>Developer</th>
        <th>Basic</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Startpris</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Priser för Azure BizTalk Services</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full"> Azure priskalkylator</a></td>
</tr>
<tr>
<td><strong>Minsta standardkonfiguration</strong></td>
<td>1 Free-enhet</td>
<td>1 Developer-enhet</td>
<td>1 Basic-enhet</td>
<td>1 Standard-enhet</td>
<td>1 Premium-enhet</td>
</tr>
<tr>
<td><strong>Skalning</strong></td>
<td>Ingen skalning</td>
<td>Ingen skalning</td>
<td>Ja, i steg om 1 Basic-enhet</td>
<td>Ja, i steg om 1 Standard-enhet</td>
<td>Ja, i steg om 1 Premium-enhet</td>
</tr>
<tr>
<td><strong>Högsta tillåtna skalning ut</strong></td>
<td>Ingen skalning</td>
<td>Ingen skalning</td>
<td>Upp till 8 enheter</td>
<td>Upp till 8 enheter</td>
<td>Upp till 8 enheter</td>
</tr>
<tr>
<td><strong>EAI-bryggor per enhet</strong></td>
<td>Ingår inte</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Innefattar TPM-avtal</td>
<td>Ingår inte</td>
<td>Ingår. 10 avtal per enhet.</td>
<td>Ingår. 50 avtal per enhet.</td>
<td>Ingår. 250 avtal per enhet.</td>
<td>Ingår. 1 000 avtal per enhet.</td>
</tr>
<tr>
<td><strong>Hybridanslutningar per enhet</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Hybridanslutningens dataöverföring (GB) per enhet</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>BizTalk Adapter-tjänstanslutningar till lokala LOB-system</strong></td>
<td>Ingår inte</td>
<td>1 anslutning</td>
<td>2 anslutningar</td>
<td>5 anslutningar</td>
<td>25 anslutningar</td>
</tr>
<tr>
<td align="left"><strong>Protokoll/system som stöds:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Azure-blobb</li>
<li>REST API:er</li>
</ul>
</td>
<td>Ingår inte</td>
<td>Ingår</td>
<td>Ingår</td>
<td>Ingår</td>
<td>Ingår</td>
</tr>
<tr>
<td><strong>Hög tillgänglighet</strong>
<br/><br/>
För serviceavtal (SLA), läs mer i <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Prissättning för Azure BizTalk Services</a>.
</td>
<td>Ingår inte</td>
<td>Ingår inte</td>
<td>Ingår</td>
<td>Ingår</td>
<td>Ingår</td>
</tr>
<tr>
<td><strong>Säkerhetskopiering och återställning</strong></td>
<td>Ingår inte</td>
<td>Ingår</td>
<td>Ingår</td>
<td>Ingår</td>
<td>Ingår</td>
</tr>
<tr>
<td><strong>Spårning</strong></td>
<td>Ingår inte</td>
<td>Ingår</td>
<td>Ingår</td>
<td>Ingår</td>
<td>Ingår</td>
</tr>
<tr>
<td><strong>Arkivering</strong><br/><br/>
Innehåller NRR (Non-Repudiation of Receipt) och hämtning av spårade meddelanden</td>
<td>Ingår inte</td>
<td>Ingår</td>
<td>Ingår inte</td>
<td>Ingår inte</td>
<td>Ingår</td>
</tr>
<tr>
<td><strong>Användning av anpassad kod</strong></td>
<td>Ingår inte</td>
<td>Ingår</td>
<td>Ingår</td>
<td>Ingår</td>
<td>Ingår</td>
</tr>
<tr>
<td><strong>Användning av transformeringar, inklusive anpassad XSLT</strong></td>
<td>Ingår inte</td>
<td>Ingår</td>
<td>Ingår</td>
<td>Ingår</td>
<td>Ingår</td>
</tr>
</table>

> [AZURE.NOTE] För återhämtning vid maskinvarufel innebär hög tillgänglighet att ha flera virtuella datorer i en enda BizTalk-enhet.


## Vanliga frågor och svar

#### Vad är en BizTalk-enhet?
En ”enhet” är den atomiska nivån i en distribution av Azure BizTalk Services. Varje utgåva levereras med en enhet som har annan beräkningskapacitet och minne. Till exempel har en Basic-enhet mer databearbetning än Developer, och Standard har mer databearbetning än Basic och så vidare. En BizTalk-tjänst skalas i enheter.

#### Vad är skillnaden mellan BizTalk Services och virtuella Azure BizTalk-datorer?
BizTalk Services innehåller en verklig ”plattform som en tjänst”-arkitektur (PaaS) som kan skapa integrationslösningar i molnet. Med PaaS-modellen kan du helt fokusera på programlogiken och lämna all infrastrukturhantering till Microsoft, inklusive:

- Inget behov av att hantera eller korrigera virtuella datorer.
- Microsoft garanterar tillgängligheten.
- Du kan styra skalningen på begäran genom att helt enkelt begära mer eller mindre kapacitet via Azure-portalen.

BizTalk Server i Azure Virtual Machines ger en ”infrastruktur som en tjänst”-arkitektur (IaaS). Du skapar virtuella datorer och konfigurerar dem likadant som din lokala miljö, vilket gör det lättare att köra befintliga program i molnet utan kodändringar. Med IaaS är du fortfarande ansvarig för att konfigurera virtuella datorer, hantera virtuella datorer (till exempel installera programvara och korrigeringsfiler för operativsystem), samt för att anpassa programmet för hög tillgänglighet.

Om du funderar på att skapa nya integrationslösningar som minimerar din infrastrukturhantering, bör du använda BizTalk Services. Om du vill migrera dina befintliga BizTalk-lösningar snabbt, eller om du söker efter en ”på begäran”-miljö för att utveckla och testa BizTalk Server-program, använder du BizTalk Server på en virtuell Azure-dator.

#### Vad är skillnaden mellan BizTalk Adapter-tjänsten och hybridanslutningar?
BizTalk Adapter-tjänsten används av en Azure BizTalk-tjänst. BizTalk Adapter-tjänsten använder BizTalk Adapter Pack för att ansluta till ett lokalt LOB-system (Line of Business). En hybridanslutning är ett enkelt och praktiskt sätt att ansluta Azure-program, som t.ex. Web Apps-funktionen i Azure Apptjänst och Azure Mobile Services, till en lokal resurs.

#### Vad innebär ”Hybridanslutningens dataöverföring (GB) per enhet”? Är detta per minut/timme/dag/vecka/månad? Vad händer när gränsen har nåtts?

Hybridanslutningens kostnad per enhet beror på vilken BizTalk Services-utgåva du har. Kostnaderna beror helt enkelt på hur mycket data du överför. Att exempelvis överföra 10 GB data per dag kostar mindre än att överföra 100 GB per dag. Använd [Priskalkylatorn](https://azure.microsoft.com/pricing/calculator/?scenario=full) för BizTalk Services när du fastställer kostnader. Vanligtvis tillämpas gränserna per dag. Om du överskrider gränsen debiteras eventuell överanvändning med $1 per GB.

#### Varför går antalet bryggor upp med två i stället för bara en när jag skapar ett avtal i BizTalk Services?

Varje avtal består av två olika bryggor: en kommunikationsbrygga på sändningssidan och en kommunikationsbrygga på mottagningssidan.

####  Vad händer när jag uppnår kvotgränsen för antalet bryggor eller avtal?

Du kommer inte att kunna distribuera några nya bryggor eller skapa några nya avtal. Om du vill distribuera flera måste du skala upp till fler enheter av BizTalk-tjänsten, eller uppgradera till en högre utgåva.

#### Hur migrerar jag från en nivå av BizTalk Services till en annan?

Free-utgåvan kan inte migreras eller ”skalas upp” till en annan nivå och kan inte heller säkerhetskopieras och återställas till en annan nivå. Om du behöver en annan nivå skapar du en ny BizTalk-tjänst med den nya nivån. Alla artefakter som skapats med Free-utgåvan, inklusive hybridanslutningar, måste återskapas i den nya BizTalk-tjänsten. 

För de återstående utgåvorna använder du säkerhetskopiering och återställning för att migrera artefakterna från en nivå till en annan. Du kan till exempel säkerhetskopiera dina artefakter på Standard-nivån och återställa dem till Premium-nivån. [BizTalk Services: Säkerhetskopiering och återställning](biztalk-backup-restore.md) beskriver de migreringsvägar som stöds och visar en lista över vilka artefakter som säkerhetskopieras. Observera att hybridanslutningar inte säkerhetskopieras. När du har säkerhetskopierat och återställt till en ny nivå återskapar du sedan hybridanslutningarna.  


#### Ingår BizTalk Adapter i tjänsten? Hur får jag programvaran?

Ja, BizTalk Adapter-tjänsten med BizTalk Adapter Pack ingår i Azure BizTalk Services SDK-[hämtning](http://www.microsoft.com/download/details.aspx?id=39087).

## Nästa steg

Om du vill skapa Azure BizTalk Services i Azure-portalen går du till [BizTalk Services: Etablering med hjälp av Azure-portalen](biztalk-provision-services.md). Om du vill börja skapa program går du till [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Ytterligare resurser
- [BizTalk Services: Etablering med hjälp av Azure-portalen](biztalk-provision-services.md)<br/>
- [BizTalk Services: Etablering av statusdiagram](biztalk-service-state-chart.md)<br/>
- [BizTalk Services: Flikarna Instrumentpanel, Övervakare och Skalning](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [BizTalk Services: Säkerhetskopiering och återställning](biztalk-backup-restore.md)<br/>
- [BizTalk Services: Begränsning](biztalk-throttling-thresholds.md)<br/>
- [BizTalk Services: Utfärdarens namn och nyckel](biztalk-issuer-name-issuer-key.md)<br/>
- [Hur gör jag för att börja använda Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>



<!--HONumber=sep16_HO1-->


