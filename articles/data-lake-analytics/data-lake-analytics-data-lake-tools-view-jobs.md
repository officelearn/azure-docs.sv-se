---
title: Använda jobbwebbläsare & jobbvy - Azure Data Lake Analytics
description: I den här artikeln beskrivs hur du använder jobbwebbläsare och jobbvy för Azure Data Lake Analytics-jobb.
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 2d33a6ec5ff6b687913914e9433b85765aaa7aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309937"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Använda jobbwebbläsaren och jobbvyn för Azure Data Lake Analytics
Azure Data Lake Analytics-tjänstarkivet skickade jobb i ett frågearkiv. I den här artikeln får du lära dig hur du använder Jobbwebbläsare och jobbvy i Azure Data Lake Tools för Visual Studio för att hitta den historiska jobbinformationen. 

Som standard arkiverar tjänsten Data Lake Analytics jobben i 30 dagar. Utgångsdatumet kan konfigureras från Azure-portalen genom att konfigurera den anpassade förfalloprincipen. Du kommer inte att kunna komma åt jobbinformationen efter utgångsdatum. 

## <a name="prerequisites"></a>Krav
Se [Datasjöverktyg för Visual Studio-förutsättningar](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Öppna jobbbläddraren
Öppna jobbbläddraren via **Server Explorer>Azure>Data Lake Analytics>jobb** i Visual Studio.  Med jobbbläddraren kan du komma åt frågearkivet för ett DataSjöanalyskonto. Jobbbläddraren visar Query Store till vänster, med grundläggande jobbinformation och jobbvy till höger som visar detaljerad jobbinformation.

## <a name="job-view"></a>Jobbvy
Jobbvyn visar detaljerad information för ett jobb. Om du vill öppna ett jobb kan du dubbelklicka på ett jobb i jobbbläddraren eller öppna det på Datasjö-menyn genom att klicka på Jobbvy. Du bör se en dialogruta ifylld med jobb-URL:en.

![Data Lake Tools Visual Studio Jobbbläddrare](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Jobbvyn innehåller:

* Jobbsammanfattning
  
    Uppdatera jobbvyn om du vill se den senaste informationen om jobb som körs.
  
  * Jobbstatus (diagram):
    
      Jobbstatus beskriver jobbfaserna:
    
      ![Status för azure datasjöanalysjobbfaser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Förbereda: Ladda upp skriptet till molnet, kompilera och optimera skriptet med hjälp av kompileringstjänsten.
    * Kö: Jobb köas när de väntar på tillräckligt med resurser eller jobben överskrider max samtidiga jobb per kontobegränsning. Prioritetsinställningen bestämmer sekvensen av köade jobb - ju lägre antal, desto högre prioritet.
    * Kör: Jobbet körs faktiskt i ditt Data Lake Analytics-konto.
    * Slutför: Jobbet slutförs (till exempel slutföra filen).
      
      Jobbet kan misslyckas i varje fas. Kompileringsfel i fasen Förbereda, timeout i fasen Kö och körningsfel i körfasen osv.
  * Grundläggande information
    
      Den grundläggande jobbinformationen visas i den nedre delen av panelen Projektsammanfattning.
    
      ![Status för azure datasjöanalysjobbfaser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Jobbresultat: Lyckades eller misslyckades. Jobbet kan misslyckas i varje fas.
    * Total varaktighet: Väggklockans tid (varaktighet) mellan inlämningstid och sluttid.
    * Total beräkningstid: Summan av varje hörnkörningstid, du kan betrakta det som den tid då jobbet körs i endast ett hörn. Se Totalt antal hörn för att hitta mer information om hörn.
    * Skicka/starta/sluttid: Den tidpunkt då DataSjöanalys-tjänsten tar emot jobböverföring/börjar köra jobbet/avslutar jobbet framgångsrikt eller inte.
    * Kompilering/kö/löpning: Väggklockatid som tillbringats under fasen Förbereda/köad/Kör.
    * Konto: DataSjöanalyskontot som används för att köra jobbet.
    * Författare: Användaren som skickade jobbet, kan det vara en verklig persons konto eller ett systemkonto.
    * Prioritet: Jobbets prioritet. Ju lägre siffra, desto högre prioritet. Det påverkar bara sekvensen av jobben i kön. Att ange en högre prioritet förutsätter inte att jobb körs.
    * Parallellism: Det begärda maximala antalet samtidiga Azure Data Lake Analytics-enheter (ADLAUs), aka vertices. För närvarande är ett hörn lika med en virtuell dator med två virtuella kärnor och sex GB RAM, även om detta kan uppgraderas i framtida Data Lake Analytics-uppdateringar.
    * Byte vänster: Byte som måste bearbetas tills jobbet är klart.
    * Lästa/skriftliga byte: Byte som har lästs/skrivits sedan jobbet började köras.
    * Totalt hörn: Jobbet är uppdelat i många bitar av arbete, varje arbete kallas ett hörn. Det här värdet beskriver hur många arbetsstycken jobbet består av. Du kan betrakta ett hörn som en grundläggande processenhet, aka Azure Data Lake Analytics Unit (ADLAU) och hörn kan köras parallellt. 
    * Slutförd/Körs/misslyckades: Antalet slutförda/körs/misslyckades. Hörn kan misslyckas på grund av både användarkod och systemfel, men systemförsöken misslyckades automatiskt några gånger. Om hörnet fortfarande misslyckas efter ett nytt försök misslyckas hela jobbet.
* Jobbdiagram
  
    Ett U-SQL-skript representerar logiken i att omvandla indata till utdata. Skriptet kompileras och optimeras till en fysisk körningsplan i förberedelsefasen. Jobbdiagram är att visa den fysiska körningsplanen.  Följande diagram illustrerar processen:
  
    ![Status för azure datasjöanalysjobbfaser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Ett jobb är uppdelat i många bitar av arbete. Varje arbete kallas vertex. Hörnen grupperas som Super Vertex (aka-scenen) och visualiseras som jobbdiagram. De gröna scenplakaten i jobbdiagrammet visar stegen.
  
    Varje hörn i ett skede gör samma typ av arbete med olika bitar av samma data. Om du till exempel har en fil med en TB-data och det finns hundratals hörn som läser från den, läser var och en av dem ett segment. Dessa hörn är grupperade i samma skede och gör samma arbete på olika delar av samma indatafil.
  
  * <a name="state-information"></a>Sceninformation
    
      I ett visst skede visas vissa siffror i plakatet.
    
      ![Projektfasen i Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1-utdrag: Namnet på en fas, namngiven efter ett tal och operationsmetoden.
    * 84 hörn: Den totala räkningen av hörn i detta skede. Figuren visar hur många verk som är uppdelade i detta skede.
    * 12.90 s/vertex: Den genomsnittliga hörnkörningstiden för det här steget. Den här siffran beräknas av SUMMA (varje hörnkörningstid) / (totalt antal vertex). Vilket innebär att om du kunde tilldela alla hörn som utförs parallellt, hela scenen är klar i 12,90 s. Det innebär också att om allt arbete i detta skede görs seriellt, skulle kostnaden vara #vertices * AVG tid.
    * 850 895 rader skrivna: Totalt antal rader skrivna i detta skede.
    * R/W: Mängden data som läss/skrivs i det här steget i byte.
    * Färger: Färger används i scenen för att ange olika hörnstatus.
      
      * Grönt anger att hörnet har lyckats.
      * Orange anger att hörnet görs igen. Det senaste försöket misslyckades men görs automatiskt och framgångsrikt av systemet och den övergripande fasen har slutförts. Om hörnet försökte igen men ändå misslyckades blir färgen röd och hela jobbet misslyckades.
      * Rött indikerar misslyckades, vilket innebär att ett visst hörn hade gjorts om några gånger av systemet men ändå misslyckats. Det här scenariot medför att hela jobbet misslyckas.
      * Blått betyder att ett visst hörn körs.
      * Vitt indikerar att hörnet väntar. Hörnet kan vänta på att schemaläggas när en ADLAU blir tillgänglig, eller så väntar det på indata eftersom indata kanske inte är klara.
      
      Du kan hitta mer information för scenen genom att hålla muspekaren med ett tillstånd:
      
      ![Sceninformation om Azure Data Lake Analytics-jobbdiagram](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Hörn: Beskriver vertices-detaljerna, till exempel hur många hörn totalt, hur många hörn som har slutförts, är de misslyckades eller fortfarande körs / väntar, etc.
  * Data läsa cross /intra pod: Filer och data lagras i flera poddar i distribuerade filsystem. Värdet här beskriver hur mycket data som har lästs i samma pod eller korspodd.
  * Total beräkningstid: Summan av varje hörnkörningstid i fasen, du kan betrakta det som den tid det skulle ta om allt arbete i fasen körs i endast ett hörn.
  * Data och rader skrivna/lästa: Anger hur mycket data eller rader som har lästs/skrivits eller behöver läsas.
  * Hörnläsfel: Beskriver hur många hörn som misslyckas när data läses.
  * Vertex duplicera ignoreras: Om ett hörn går för långsamt kan systemet schemalägga flera hörn för att köra samma arbete. Reductant vertices kommer att ignoreras när en av hörnen har slutförts. Vertex duplicering tar bort poster antalet hörn som ignoreras som dupliceringar i scenen.
  * Vertex återkallelser: Vertex lyckades, men få repris senare på grund av vissa skäl. Om till exempel nedströmshörnarten förlorar mellanliggande indata, kommer det att be uppströms vertex att köra igen.
  * Vertex schemakörningar: Den totala tiden som hörnen har schemalagts.
  * Min/Average/Max Vertex-data läs: Lägsta/medelvärde/maximum för varje hörnlätdata.
  * Varaktighet: Väggklockan tid en etapp tar, måste du ladda profilen för att se detta värde.
  * Jobbuppspelning
    
      DataSjöanalys kör jobb och arkiverar vertices som kör information om jobben, till exempel när hörnen startas, stoppas, misslyckades och hur de görs om, etc. All information loggas automatiskt i frågearkivet och lagras i jobbprofilen. Du kan ladda ned jobbprofilen via "Läs in profil" i jobbvyn, och du kan visa jobbuppspelningen när du har hämtat jobbprofilen.
    
      Jobbuppspelning är en symbol visualisering av vad som hände i klustret. Det hjälper dig att titta på prestandakörning av jobb och visuellt upptäcka prestandaavvikelser och flaskhalsar på mycket kort tid (mindre än 30s vanligtvis).
  * Jobb värme karta display 
    
      Jobbvärmekarta kan väljas via listrutan Visa i jobbdiagrammet. 
    
      ![Azure Data Lake Analytics jobbdiagram heap karta display](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Den visar I/O, tids- och dataflödesvärmekartan för ett jobb, genom vilken du kan hitta var jobbet tillbringar större delen av tiden, eller om ditt jobb är ett I/O-gränsjobb och så vidare.
    
      ![Exempel på Azure Data Lake Analytics-jobbdiagram](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Förlopp: Förloppet för jobbkörning, se Information i steginformation.
    * Lästa/skriftliga data: Värmekartan över de totala data som läss/skrivs i varje steg.
    * Beräkningstid: Värmekartan för SUMMA (varje hörnkörningstid), du kan betrakta detta som hur lång tid det skulle ta om allt arbete i fasen körs med endast 1 hörn.
    * Genomsnittlig körningstid per nod: Värmekartan för SUMMA (varje hörnkörningstid) / (Vertex-nummer). Vilket innebär att om du kunde tilldela alla hörn som utförs parallellt, kommer hela scenen att göras inom denna tidsram.
    * In-/utdataflöde: Värmekartan för indata-/utdatagenomströmning för varje steg, du kan bekräfta om ditt jobb är ett I/O-bundet jobb genom detta.
* Metadataåtgärder
  
    Du kan utföra vissa metadataåtgärder i ditt U-SQL-skript, till exempel skapa en databas, släppa en tabell osv. Dessa åtgärder visas i Metadata-åtgärden efter kompilering. Du kan hitta påståenden, skapa entiteter, släppa entiteter här.
  
    ![Azure Data Lake Analytics-jobbvy-metadataåtgärder](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Statens historia
  
    Tillståndshistoriken visualiseras också i Jobbsammanfattning, men du kan få mer information här. Du kan hitta detaljerad information, till exempel när jobbet förbereds, köas, började köras, avslutades. Du kan också hitta hur många gånger jobbet har sammanställts (CcsAttempts: 1), när är jobbet skickas till klustret faktiskt (Detalj: Utskick jobb till kluster), etc.
  
    ![Tillståndshistorik för Azure Data Lake Analytics-jobbvyn](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostik
  
    Verktyget diagnostiserar jobbkörning automatiskt. Du får aviseringar när det finns vissa fel eller prestandaproblem i dina jobb. Observera att du måste ladda ner Profil för att få fullständig information här. 
  
    ![Azure Data Lake Analytics-jobbvy diagnostik](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Varningar: En varning visas här med kompilatorvarning. Du kan klicka på länken "x issue(s)" om du vill ha mer information när aviseringen visas.
  * Vertex köra för länge: Om någon vertex tar på tid (säg 5 timmar), kommer frågor att hittas här.
  * Resursanvändning: Om du har allokerat mer eller inte tillräckligt med parallellitet än behov, hittar du problem här. Du kan också klicka på Resursanvändning om du vill se mer information och utföra vad händer om-scenarier för att hitta en bättre resursallokering (mer information finns i den här guiden).
  * Minneskontroll: Om ett hörn använder mer än 5 GB minne hittas problem här. Jobbkörning kan dödas av systemet om det använder mer minne än systembegränsning.

## <a name="job-detail"></a>Jobbinformation
Jobbinformation visar detaljerad information för jobbet, inklusive skript, resurser och vertex körningsvy.

![Jobbinformation för Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skript
  
    U-SQL-skriptet för jobbet lagras i frågearkivet. Du kan visa det ursprungliga U-SQL-skriptet och skicka det igen om det behövs.
* Resurser
  
    Du hittar jobbkompileringsutdata som lagras i frågearkivet via Resurser. Till exempel kan du hitta "algebra.xml" som används för att visa jobbdiagrammet, de sammansättningar du registrerade, etc. här.
* Körningsvy för Vertex
  
    Den visar vertices körning detaljer. Jobbprofilen arkiverar varje hörnkörningslogg, till exempel total dataläst/skriven, körning, tillstånd osv. Genom den här vyn kan du få mer information om hur ett jobb kördes. Mer information finns [i Använda Vertex Execution View i DataSjöverktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Efterföljande moment
* Information om hur du loggar diagnostikinformation finns i [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Information om hur du använder körningsvyn i vertex finns [i Använda vertexutförandevyn i Datasjöverktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

