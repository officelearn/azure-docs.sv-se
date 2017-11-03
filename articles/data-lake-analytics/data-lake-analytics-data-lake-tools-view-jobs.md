---
title: "Använd jobbet webbläsare och jobbet för Azure Data Lake Analytics-jobb | Microsoft Docs"
description: "Lär dig hur du använder webbläsaren för jobbet och jobbet för Azure Data Lake Analytics-jobb. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2017
ms.author: jgao
ms.openlocfilehash: 8f1729f84a4fde2a56427a41b356d6263818519e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics-jobs"></a>Använd jobbet webbläsare och jobbet för Azure Data lake Analytics-jobb
Azure Data Lake Analytics-tjänsten arkiveras skickade jobb i en [frågearkivet](#query-store). Du lära dig hur du använder webbläsaren för jobbet och jobbet i Azure Data Lake-verktyg för Visual Studio för att hitta historiska Jobbinformationen i den här artikeln. 

Som standard arkiveras Data Lake Analytics-tjänsten jobben i 30 dagar. Förfallotid perioden kan konfigureras från Azure portal genom att konfigurera en anpassad princip. Du kan inte komma åt jobbinformation efter förfallodatumet. 

## <a name="prerequisites"></a>Krav
Se [Data Lake-verktyg för Visual Studio förutsättningar](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Öppna webbläsaren jobb
Åtkomst till jobbet webbläsaren via **Server Explorer > Azure > Data Lake Analytics > jobb** i Visual Studio.  Du kan öppna frågearkivet för Data Lake Analytics-konto med jobb-webbläsaren. Jobbet webbläsaren visar Query Store till vänster, grundläggande jobbinformation och jobbet visa på rätt visar detaljerad information om jobbet.

## <a name="job-view"></a>Visa jobb
Jobbet visas detaljerad information om ett jobb. Om du vill öppna ett jobb du dubbelklicka på ett jobb i jobb webbläsaren eller öppna den från Data Lake-menyn genom att klicka på jobbet. Du bör se en dialogruta med jobb-URL.

![Data Lake-verktyg webbläsare för Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Jobbet innehåller:

* Jobbsammanfattning
  
    Uppdatera vyn jobb för att det finns nyare av jobb som körs.
  
  * Jobbets Status (diagram):
    
      Jobbstatus beskrivs jobbet faser:
    
      ![Azure Data Lake Analytics-jobbstatusen faser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Förbereder: Överföra skriptet till molnet, kompilering och optimera skriptet med hjälp av tjänsten kompilering.
    * I kö: Jobb är köade vassle de väntar på att tillräckligt med resurser eller jobb överskrider de högsta antal samtidiga jobb per konto begränsning. Prioritetsinställningen bestämmer i vilken ordning köade jobb - Ju lägre nummer, desto högre prioritet.
    * Körs: Jobbet faktiskt körs i ditt Data Lake Analytics-konto.
    * Slutför: Jobbet slutförs (till exempel Slutför filen).
      
      Jobbet kan misslyckas i varje fas. Till exempel kompileringsfel i förbereda fas, timeout-fel i fasen i kö och körningsfel i körs fasen osv.
  * Grundläggande Information
    
      Grundläggande jobbinformation visas i den nedre delen av panelen jobbsammanfattning.
    
      ![Azure Data Lake Analytics-jobbstatusen faser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Jobbet resultat: Lyckades eller misslyckades. Jobbet misslyckas i varje fas.
    * Total varaktighet: Väggskåp systemtid (varaktighet) mellan skickar tid och sluttid.
    * Total tid för beräkning: Summan av alla vertex körningstid kan du den som den tid som jobbet körs i en enda nod. Referera till totalt formhörnen du hittar mer information om hörn.
    * Skicka/Starttid/Sluttid: Den tid när Data Lake Analytics-tjänsten tar emot jobbet skicka/startar att köra jobbet/avslutar jobbet har eller inte.
    * Körs-kompilering/i kö: Klocktid använt under fasen körs-förbereda/i kö.
    * Konto: Data Lake Analytics-kontot används för att köra jobbet.
    * Skapad av: Användaren som skickade jobbet, det kan vara en verklig person konto eller ett system.
    * Prioritet: Prioritet för jobbet. Ju lägre nummer, desto högre prioritet. Den påverkar bara sekvensen av jobb i kön. Jobb som körs åsidosätter inte när du anger en högre prioritet.
    * Parallellitet: Det begärda maximala antalet samtidiga Azure Data Lake Analytics-enheter (ADLAUs), även kallat formhörnen. För närvarande ett hörn är lika med en virtuell dator med två virtuella kärnor och sex GB RAM, även om detta kunde uppgraderas i framtiden Data Lake Analytics uppdateras.
    * Byte kvar: Antal byte som måste bearbetas, tills jobbet har slutförts.
    * Läs/skrivna byte: byte som har lästs/skrivits sedan jobbet startades.
    * Totalt antal formhörnen: jobbet har delats upp i många arbeten, varje arbetsuppgifter kallas en nod. Det här värdet beskriver hur många arbeten jobbet består av. Du kan överväga att en nod som en grundläggande process-enhet, även kallat Azure Data Lake Analytics enhet (ADLAU), och formhörnen kan köras i parallellitet. 
    * Slutfört/körs/misslyckades: Antal formhörnen slutförts/körs/misslyckades. Formhörnen kan misslyckas på grund av både användaren kod och system fel, men systemet försök misslyckades formhörnen automatiskt ett fåtal gånger. Om vertex fortfarande misslyckas efter att ha försökt att hela jobbet misslyckas.
* Jobbdiagram
  
    Ett U-SQL-skript representerar logiken för att omvandla inkommande data till utdata. Skriptet kompileras och optimerad till en fysisk Körningsplan på fasen förbereda. Jobbdiagram är att visa den fysiska åtgärdsplan.  Följande diagram illustrerar processen:
  
    ![Azure Data Lake Analytics-jobbstatusen faser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Ett jobb har delats upp i många arbeten. Varje arbetsuppgifter kallas för en nod. Formhörnen grupperas som Super Vertex (aka delen) och visualiseras som Jobbdiagram. Grön steget skyltar i jobbet diagrammet visar stegen.
  
    Varje nod i ett steg gör samma typ av fungerar med olika delar av samma data. Om du har en fil med en TB data och det finns hundratals formhörnen läsning från den, var och en av dem läser ett segment. Dessa formhörnen grupperas i samma steg och göra samma fungerar på olika delar av samma indatafilen.
  
  * <a name="state-information"></a>Steg-information
    
      Tal visas i en viss fas i placard.
    
      ![Azure Data Lake Analytics-jobbet diagrammet fas](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Extrahera: namnet på ett steg som namnges av ett tal och metoden igen.
    * 84 formhörnen: Totalt antal formhörnen i det här steget. Bilden visar hur många arbeten är uppdelad i det här steget.
    * 12.90 s/vertex: brytpunkt Genomsnittlig körningstid för det här steget. Denna bild beräknas genom att SUMMAN (varje vertex körningstid) / (totalt antal Vertex). Vilket innebär att om du kan tilldela alla formhörnen köras i parallellitet hela steget har slutförts i 12.90 s. Det innebär även om allt arbete i det här steget är klart seriellt kostnaden skulle vara #vertices * Genomsnittlig tid.
    * 850,895 rader skrivna: Totalt antal rader som har skrivits i det här skedet.
    * R/b: mängden data Läs/skriftliga i det här steget i byte.
    * Färger: Färger som används i skedet ange olika vertex status.
      
      * Grönt anger vertex lyckades.
      * Orange anger vertex försöks. Göras vertex gick men försöks har och automatiskt av systemet, och det övergripande steget har slutförts. Om vertex igen men fortfarande misslyckades, aktiverar färgen röd och hela jobbet misslyckades.
      * Rött anger misslyckades, vilket innebär att en viss nod hade varit igen några gånger av systemet men fortfarande misslyckades. Detta medför att hela jobbet ska misslyckas.
      * Blå innebär en viss nod körs.
      * Vit anger vertex väntar. Vertex kan väntar på att schemaläggas när en ADLAU blir tillgänglig, eller det kanske väntar på indata eftersom dess indata är inte kanske redo.
      
      Du hittar mer information för steg genom att Hovra pekaren av ett tillstånd:
      
      ![Azure Data Lake Analytics-Jobbdetaljer diagrammet fas](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Formhörnen: Beskriver formhörnen information, till exempel hur många formhörnen totalt, hur många formhörnen har slutförts, är de misslyckades eller fortfarande körs/väntar, osv.
  * Läsa data mellan/inom baljor: filer och data lagras i flera skida i distributed file system. Det här värdet beskriver hur mycket data har lästs i samma baljor eller mellan baljor.
  * Total tid för beräkning: summan av varje vertex körningstid i skedet kan du den som den tid det tar om alla fungerar i steg utförs i en enda nod.
  * Data och rader skrivs/läses: Anger hur mycket data rader har lästs/skrivits eller måste läsas.
  * Vertex läsa fel: Beskriver hur många formhörnen misslyckades vid läsning av data.
  * Ignorerar dubblett Vertex: om en nod är för långsam, systemet kan schemalägga flera formhörnen att köra samma arbetsuppgifter. Reductant formhörnen ignoreras när något av formhörnen slutföras. Vertex dubbla rensningar registrerar antalet formhörnen ignoreras som upprepningar i steget.
  * Återkallelse av Vertex: vertex lyckades, men hämta senare kör igen på grund av olika orsaker. Till exempel om underordnade vertex förlorar mellanliggande indata, uppmanas överordnade vertex igen.
  * Vertex schema körningar: den totala tiden som formhörnen har schemalagts.
  * Max-min/genomsnittlig Vertex data läses: den genomsnittliga/gränsvärden för varje hörn läsa data.
  * Varaktighet: Klocktid ett steg tar genom att läsa in profilen om du vill se det här värdet.
  * Jobbuppspelning
    
      Data Lake Analytics kör jobb och arkiveras formhörnen kör information för jobb, till exempel när formhörnen startas, stoppas, misslyckades och hur de är på nytt, osv. All information är automatiskt inloggad query store och lagras i dess jobbet profil. Du kan hämta jobbprofilen via ”Beläggningsprofil” i jobbet och du kan visa den Jobbuppspelning när du har hämtat jobbprofilen.
    
      Jobbuppspelning är en epitome visualisering av vad som hände i klustret. Det hjälper dig se förloppet för jobbkörningen och identifiera visuella prestandaavvikelser och flaskhalsar i mycket kort tid (mindre än vanligtvis 30s).
  * Jobbet termiska karta visning 
    
      Du kan välja jobbet termisk karta via listrutan visas i Jobbdiagram. 
    
      ![Azure Data Lake Analytics-jobbet diagrammet heap kartbilden](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Den visar i/o, tid och genomströmning termisk karta för ett jobb som du kan hitta där jobbet tillbringar i de flesta fall, eller om ditt jobb är ett jobb för i/o-gräns, och så vidare.
    
      ![Azure Data Lake Analytics-jobbet diagrammet heap kartan exempel](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Förlopp: Jobbkörningen utvecklas, se informationen i [mellanlagra information](#stage-information).
    * Läsa/skriva data: termisk karta av totala data lästa/skrivna i varje steg.
    * Beräkna tid: termisk karta för SUM (varje vertex körningstid), kan du detta som hur lång tid det tar om allt arbete i skedet körs med endast 1 hörn.
    * Genomsnittlig körningstid per nod: termisk karta för SUM (varje vertex körningstid) / (Vertex nummer). Vilket innebär att om du kan tilldela alla formhörnen köras i parallellitet hela steg ska utföras i denna tidsperiod.
    * I/o-genomströmning: termisk karta av i/o-genomflöde i varje fas, du kan bekräfta om jobbet är ett i/o-bundna jobb via.
* Åtgärder för metadata
  
    Du kan utföra vissa åtgärder för metadata i U-SQL-skript, exempelvis skapa en databas, ta bort en tabell, osv. Dessa åtgärder visas i Metadata-åtgärd efter kompilering. Du kan hitta intyg, skapa entiteter, släpp entiteter här.
  
    ![Azure Data Lake Analytics-jobbet visa metadataåtgärder](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Tillståndshistorik
  
    Tillstånd historiken också visualiseras i jobbsammanfattning, men du kan få mer information här. Du hittar detaljerad information t.ex. när jobbet är förberedd i kö, igång körs avslutas. Du kan söka efter hur många gånger som jobbet har kompilerats (CcsAttempts: 1), när jobbet skickas till klustret faktiskt (detaljer: skicka jobb till klustret) osv.
  
    ![Azure Data Lake Analytics-jobbet visa tillståndshistorik](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostik
  
    Verktyget diagnostiserar jobbkörningen automatiskt. Du får aviseringar när det inte finns några fel eller prestandaproblem i dina jobb. Observera att du behöver hämta profil om du vill ha fullständig information här. 
  
    ![Azure Data Lake Analytics-jobbet visa diagnostik](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Varningar: En avisering visas här med kompileringsvarning. Du kan klicka på länken ”x eller flera” för att få mer information när varningen visas.
  * Vertex kör för lång: om alla vertex tar slut tid (Säg 5 timmar), problem hittas här.
  * Resursanvändning: Om du har tilldelat fler eller finns inte tillräckligt med parallellitet än behöver problem hittas här. Du kan också klicka på Resursanvändning om du vill se mer information och utföra olika sätt för att hitta en bättre resursallokering (Mer information finns i den här guiden).
  * Minneskontroll: om alla vertex använder mer än 5 GB minne, problem hittas här. Jobbkörningen kan hämta avslutats av systemet om det använder mer minne än begränsning i filsystemet.

## <a name="job-detail"></a>Jobbdetaljer
Information om jobbet visar detaljerad information för jobbet, inklusive skript, resurser och Vertex vy.

![Azure Data Lake Analytics-Jobbdetaljer](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skript
  
    U-SQL-skript för jobbet lagras i query store. Du kan visa det ursprungliga U-SQL-skriptet och skicka det igen om det behövs.
* Resurser
  
    Du kan hitta jobb kompilering utdata som lagras i frågearkivet genom resurser. Du hittar för instans ”algebra.xml” som används för att visa Jobbdiagram, sammansättningar som du har registrerat, här osv.
* Vertex vy
  
    Den visar formhörnen körning av information. Jobbprofilen arkiveras varje vertex körningsloggen, till exempel totala läsa/skriva, runtime, tillstånd, osv. Den här vyn kan får du mer information om hur en jobbet kördes. Mer information finns i [använda vyn Vertex körning i Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Nästa steg
* Information om hur du loggar diagnostikinformation finns i [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Om du vill använda vertex vy finns [använda vyn Vertex körning i Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

