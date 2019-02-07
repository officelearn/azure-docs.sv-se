---
title: Använd Jobbwebbläsare och Jobbvy för Azure Data Lake Analytics-jobb
description: Den här artikeln beskriver hur du använder Jobbwebbläsare och Jobbvy för Azure Data Lake Analytics-jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonwhowell
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 905100f8a1444f6f6ee18d3bf9e9eab2ede8c805
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815208"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Använd Jobbwebbläsare och Jobbvy för Azure Data Lake Analytics
Azure Data Lake Analytics-tjänsten arkiverar skickade jobb i en frågearkivet. I den här artikeln får du lära dig hur du använder Jobbwebbläsare och Jobbvy i Azure Data Lake Tools för Visual Studio för att hitta den historiska Jobbinformationen. 

Som standard arkiverar Data Lake Analytics-tjänsten jobben i 30 dagar. Giltighetsperiod kan konfigureras från Azure portal genom att konfigurera en anpassad princip. Du kommer inte att kunna komma åt jobbinformation efter förfallodatumet. 

## <a name="prerequisites"></a>Förutsättningar
Se [Data Lake Tools för Visual Studio krav](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Öppna webbläsaren jobb
Komma åt Jobbwebbläsare via **Server Explorer > Azure > Data Lake Analytics > jobb** i Visual Studio.  Webbläsaren jobb, kan du komma åt frågearkivet för ett Data Lake Analytics-konto. Jobbwebbläsare Query Store visas till vänster som visar grundläggande jobbinformation och Jobbvy på rätt visar detaljerad jobbinformation.

## <a name="job-view"></a>Jobbvy
Jobbet visas detaljerad information för ett jobb. För att öppna ett jobb, kan du dubbelklicka på ett jobb i jobb webbläsaren eller öppna det Data Lake-menyn genom att klicka på jobbet. Du bör se en dialogruta som fyllts med jobb-URL.

![Data Lake-verktyg Jobbwebbläsare för Visual Studio](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Jobbet innehåller:

* Jobbsammanfattning
  
    Uppdatera visningen av jobbet om du vill se den nyare informationen för jobb som körs.
  
  * Jobbstatus (graf):
    
      Jobbet har statusen beskrivs jobbet faser:
    
      ![Azure Data Lake Analytics-jobbstatusen faser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Förbereder: Ladda upp skriptet till molnet, när du kompilerar och optimera skriptet med hjälp av tjänsten kompilera.
    * I kö: Jobb ställs i kö när de väntar på tillräckligt med resurser, eller jobb överskrider de maximala samtidiga jobb per konto begränsning. Prioritetsinställningen avgör vilken ordning köade jobb – Ju lägre nummer, desto högre prioritet.
    * Körs: Jobbet körs faktiskt i Data Lake Analytics-kontot.
    * Slutför: Jobbet slutförs (exempelvis Slutför filen).
      
      Jobbet kan misslyckas i varje fas. Till exempel kompileringsfel i förbereda fas, timeout-fel i fasen i kö och körningsfel i körs fas osv.
  * Grundläggande information
    
      Grundläggande Jobbinformationen visar i den nedre delen av panelen jobbsammanfattning.
    
      ![Azure Data Lake Analytics-jobbstatusen faser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Resultatet av jobbet: Lyckades eller misslyckades. Jobbet kan misslyckas i varje fas.
    * Total varaktighet: Klocktid (varaktighet) mellan föreslår tid och sluttid.
    * Total bearbetningstid: Summan av alla hörn körningstid, kan du den som den tid som jobbet körs i endast ett hörn. Se Totalt antal hörn finns mer information om hörn.
    * Skicka/Starttid/Sluttid: Den tid när Data Lake Analytics-tjänsten tar emot bidrag/startar jobbet/avslutas jobbet körs eller inte.
    * Kompilering/i kö/körs: Klocktid ägnat åt under fasen för förbereda/i kö/körs.
    * Konto: Data Lake Analytics-kontot som används för att köra jobbet.
    * Författare: Den användare som skickade jobb, det kan vara en verklig person konto eller ett system.
    * Prioritet: Prioriteten för jobbet. Ju lägre nummer, desto högre prioritet. Den påverkar bara serie jobb i kön. Inte åsidosätter jobb som körs genom att ställa in en högre prioritet.
    * Parallellitet: Det begärda maximala antalet samtidiga Azure Data Lake Analytics Units (ADLAUs), även kallat hörn. För närvarande ett hörn är lika med en virtuell dator med två virtuella kärnor och sex GB RAM, även om detta kunde uppgraderas i framtiden Data Lake Analytics uppdateras.
    * Byte till vänster: Byte som måste bearbetas tills jobbet har slutförts.
    * Byte som lästs/skrivits: Byte som har varit lästa/skrivna sedan jobbet började köras.
    * Totalt antal hörn: Jobbet har delats upp i många arbeten, varje arbetsuppgifter kallas för ett hörn. Det här värdet beskrivs hur många arbeten jobbet består av. Du kan överväga ett hörn som en grundläggande process-enhet, även kallat Azure Data Lake Analytics Unit (ADLAU), och hörn kan köras i parallellitet. 
    * Slutfört/körs/misslyckades: Totalt antal slutförts/körs/misslyckade hörn. Hörn kan misslyckas på grund av båda användarfel för kod och system, men systemet återförsök misslyckades hörn automatiskt några gånger. Om hörnet är fortfarande misslyckas efter ett nytt försök kan misslyckas hela projektet.
* Jobbdiagram
  
    Ett U-SQL-skript representerar logiken för att transformera indata till utdata. Skriptet kompileras och som optimerats för en fysisk Körningsplan i förbereda fas. Jobbdiagram är att visa den fysiska åtgärdsplan.  Följande diagram illustrerar processen:
  
    ![Azure Data Lake Analytics-jobbstatusen faser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Ett jobb har delats upp i många arbeten. Varje mängd arbete kallas för ett hörn. Hörn grupperas som Super hörn (även kallat steg), och visualiseras som Jobbdiagram. Grön scenen skyltar i jobbet diagrammet visar stegen.
  
    Varje brytpunkt som ett steg gör samma typ av arbete med olika typer av samma data. Om du har en fil med en TB data och det finns hundratals hörn läsning från den, läser var och en av dem till exempel ett segment. Dessa hörn är grupperade i samma steg och göra samma fungerar på olika typer av samma indatafilen.
  
  * <a name="state-information"></a>Steg-information
    
      I en viss fas visas tal i placard.
    
      ![Azure Data Lake Analytics-jobbet graph steg](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 extrahera: Namnet på ett steg med namnet som ett tal och Åtgärdsmetoden.
    * 84 hörn: Det totala antalet hörn i det här steget. Bilden visar hur många arbeten delas upp i det här steget.
    * 12.90 s/hörn: Den genomsnittliga hörn körningstiden för det här skedet. Den här bilden beräknas av SAMMANLAGDA (varje hörn körningstid) / (totalt antal hörn). Vilket innebär att om du kan tilldela alla hörn som körs i parallellitet, det hela steget har slutförts i 12.90 s. Det innebär också om allt arbete i det här steget är klart seriellt kostnaden skulle bli #vertices * Genomsnittlig tid.
    * 850,895 rader skrivs: Totalt antal rader som har skrivits i det här steget.
    * R/W: Mängden data som Läs/skriftliga i det här steget i byte.
    * Färger: Färger används i scenen för att ange olika hörn status.
      
      * Grönt anger hörnet är slutfört.
      * Orange anger hörnet görs. Gjorts hörnet kunde inte men görs automatiskt och har ett av systemet, och det övergripande steget har slutförts. Om hörnet göras fortfarande misslyckades, aktiverar färgen röd och hela jobbet misslyckades.
      * Rött anger misslyckades, vilket innebär att ett visst hörn hade varit göras några gånger i systemet men fortfarande misslyckades. Det här scenariot kan det hela jobbet misslyckas.
      * Blå innebär ett vissa hörn körs.
      * White anger hörnet väntar. Hörnet kan väntar på att schemaläggas när en ADLAU blir tillgänglig eller det kan väntar på indata eftersom dess indata inte kan vara klar.
      
      Du hittar mer information för steg genom att Hovra pekaren genom ett tillstånd:
      
      ![Azure Data Lake Analytics-Jobbdetaljer graph steg](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Hörn: Beskriver vad hörn, till exempel hur många hörn sammanlagt, hur många hörn har slutförts, är de misslyckades eller fortfarande körs/väntar osv.
  * Data läses mellan/intra pod: Filer och data lagras i flera poddar i distributed file system. Värdet här beskrivs hur mycket data har lästs i samma pod eller mellan pod.
  * Total bearbetningstid: Summan av alla hörn körningstid i skedet kan du den som den tid det tar om alla fungerar i steget körs i endast ett hörn.
  * Data och rader skrivs/läses: Anger hur mycket data eller rader har tagits lästa/skrivna eller måste läsas.
  * Hörn Läs fel: Beskriver hur många hörn är det gick inte att läsa data.
  * Dubbla hörn ignorerar: Om ett hörn är för långsam, kan systemet schemalägga flera hörn ska köras i samma mängd arbete. Reductant hörn ignoreras när något av hörn slutförs. Hörn duplicerade rensningar registrerar antalet hörn som har förkastats som upprepningar i steget.
  * Återkallelse av hörn: Hörnet lyckades, men får köra senare på grund av orsaker. Exempel: om underordnade hörn förlorar mellanliggande indata, får du frågan överordnade hörnet att köra.
  * Hörn schema körningar: Den totala tiden som hörn har schemalagts.
  * Lästa medelvärde/min/Max-Vertex-data: I genomsnitt/gränsvärden för varje hörn läsa data.
  * Varaktighet: Wall-clock-tid ett steg tar emot och du behöver läsa in profilen om du vill se det här värdet.
  * Jobbuppspelning
    
      Data Lake Analytics kör jobb och arkiverar de hörn som kör information för jobb, till exempel när hörn startas, stoppas, misslyckades och hur de är nytt, osv. All information som är automatiskt loggas i frågearkivet och lagras i dess jobbprofilen. Du kan ladda ned jobbprofilen via ”Beläggningsprofil” i jobbet och du kan visa den Jobbuppspelning när du hämtat jobbprofilen.
    
      Jobbuppspelning är en epitome visualisering av vad som hände i klustret. Det hjälper dig att se förloppet för jobbkörningen och identifiera visuella prestandaavvikelser och flaskhalsar i mycket kort tid (mindre än 30 sekunder vanligtvis).
  * Jobbet termisk karta visning 
    
      Termisk karta för jobbet kan väljas via listrutan visas i Jobbdiagram. 
    
      ![Azure Data Lake Analytics-jobbet graph heap kartbilden](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Den visar i/o, tid och dataflöde termisk karta för ett jobb, genom vilken du hitta där jobbet tillbringar i de flesta fall, eller om ditt jobb är ett jobb för i/o-gräns och så vidare.
    
      ![Exempel på Azure Data Lake Analytics-jobb graph heap Webbplatskarta](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Förlopp: Jobbkörningen förloppet så kan du se Information i steg-information.
    * Lästa/skrivna data: Den termiska kartan den totala mängden data som lästa/skrivna i varje steg.
    * Beräkna tid: Termisk karta för SUM (varje hörn körningstid), kan du detta hur lång tid det tar om allt arbete i steget körs med endast 1 hörn.
    * Genomsnittlig körningstid per nod: Termisk karta för SUM (varje hörn körningstid) / (hörn nummer). Vilket innebär att du kan tilldela alla hörn som körs i parallellitet hela scenen kommer göra det i det här tidsintervallet.
    * I/o-dataflöde: Den termiska kartan i/o-genomflöde i varje fas, du kan kontrollera om jobbet är ett i/o-bundna jobbet via detta.
* Åtgärder för metadata
  
    Du kan utföra vissa åtgärder för metadata i U-SQL-skript, till exempel skapa en databas, ta bort en tabell osv. Dessa åtgärder visas i Metadata-åtgärd efter kompilering. Du kan hitta intyg, skapa entiteter, släpp entiteter här.
  
    ![Åtgärder för metadata för Azure Data Lake Analytics-Jobbvy](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Statushistorik
  
    Den Tillståndshistorik visualiseras också i jobbsammanfattning, men du kan få mer information här. Du hittar detaljerad information t.ex. när jobbet är förberedd i kö, igång körs avslutas. Du kan också hitta hur många gånger som jobbet har kompilerats (CcsAttempts: 1) när jobbet skickas till klustret faktiskt (i detalj: Skicka jobb till kluster), osv.
  
    ![Azure Data Lake Analytics-Jobbvy tillståndshistorik](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostik
  
    Verktyget diagnostisera jobbkörningen automatiskt. Du får aviseringar när det finns några fel eller prestandaproblem i dina jobb. Observera att du behöver hämta profil för ytterligare information här. 
  
    ![Azure Data Lake Analytics-Jobbvy-diagnostik](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Varningar: En avisering visas här med kompileringsvarning. Du kan klicka på länken ”x stödhändelserna” om du vill ha mer information när varningen visas.
  * Hörn som körs för lång: Om alla hörn slut tid (exempelvis 5 timmar), finns problem här.
  * Användning: Om du har allokerat fler eller finns inte tillräckligt med parallellitet än behöver finns problem här. Du kan också klicka på Resursanvändning för att se mer information och utföra olika sätt för att hitta en bättre resursallokering (Mer information finns i den här guiden).
  * Kontroll av minne: Om alla hörn använder mer än 5 GB minne, finns problem här. Jobbkörning kan hämta avslutades av systemet om används mer minne än begränsning i filsystemet.

## <a name="job-detail"></a>Jobbinformation
Jobbinformation visar detaljerad information om jobbet, inklusive skript, resurser och Körningsvy.

![Azure Data Lake Analytics-Jobbdetaljer](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skript
  
    U-SQL-skript för jobbet lagras i query store. Du kan visa det ursprungliga U-SQL-skriptet och skicka den igen om det behövs.
* Resurser
  
    Du hittar jobbet kompilering utdata som lagras i frågearkivet via resurser. Du hittar exempel ”algebra.xml” som används för att visa den Jobbdiagram, sammansättningar som du har registrerat, här osv.
* Körningsvy
  
    Hörn visas information om körningen. Jobbprofilen arkiverar alla hörn körningsloggen, till exempel total lästa/skrivna data, runtime, tillstånd, osv. Den här vyn kan får du mer information om hur ett jobb kördes. Mer information finns i [använda den Körningsvy i Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Nästa steg
* Information om hur du loggar diagnostikinformation finns i [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Om du vill använda Körningsvy Se [använda den Körningsvy i Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

