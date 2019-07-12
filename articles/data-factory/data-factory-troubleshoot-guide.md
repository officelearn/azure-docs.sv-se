---
title: Felsökning av Azure Data Factory | Microsoft Docs
description: Felsökning av Azure Data Factory. Vanliga dokument för alla externa, kontrollaktiviteter.
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: d220730bb2e93e32d00e56ed98f4962ad89eda5a
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626314"
---
# <a name="troubleshooting-azure-data-factory"></a>Felsökning av Azure Data Factory
Den här artikeln innehåller vanliga felsökningsfrågor.

- [Azure Databricks (anteckningsboken, JAR-filer, python)](#azure-databricks)
- [Azure Data Lake Analytics (U-SQL)](#azure-data-lake-analytics-u-sql)
- [Azure Functions](#azure-functions)
- [Anpassad (Azure Batch)](#custom-azure-batch)
- [HDInsight (Spark, Hive, MapReduce, Pig, Hadoop Streaming)](#hdinsight-spark-hive-mapreduce-pig-hadoop-streaming)
- [Webbaktivitet](#web-activity)



## <a name="azure-databricks"></a>Azure Databricks
| Felkod | Felmeddelande                                          | Problembeskrivning                             | Möjliga korrigering / rekommenderad åtgärd                            |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Fel 403                                                    | Databricks-åtkomsttoken har upphört att gälla.                         | Som standard är Databricks åtkomst-token giltig i 90 dagar.  Skapa en ny token och uppdatera länkade tjänsten. |
| 3201           | Obligatoriskt fält saknas: settings.task.notebook_task.notebook_path | Felaktig redigering: Notebook-sökvägen är inte korrekt angivna. | Ange notebook-sökvägen i Databricks-aktivitet. |
| 3201           | Kluster... finns inte                                 | Redigera fel: Databricks-klustret finns inte eller har tagits bort. | Kontrollera att Databricks-klustret finns. |
| 3201           | Ogiltig python filen URI:... Besök Databricks användarhandboken för URI-scheman som stöds. | Felaktig redigering                                                | Ange antingen absoluta sökvägar för arbetsytan adressering som eller ”dbfs:/folder/subfolder/foo.py” för filer som lagras i antingen. |
| 3201           | {0}   LinkedService bör ha domän och accessToken som nödvändiga egenskaper | Felaktig redigering                                                | Kontrollera [tjänstdefinition](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}   LinkedService bör ange befintligt kluster Id eller nya klusterinformationen för att skapa | Felaktig redigering                                                | Kontrollera [tjänstdefinition](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Nodtyp Standard_D16S_v3 stöds inte. Nodtyper som stöds:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2 | Felaktig redigering                                                | Se felmeddelande                                          |
| 3201           | Invalid notebook_path: .... Endast absoluta sökvägar stöds för närvarande. Sökvägar måste börja med ”/”. | Felaktig redigering                                                | Se felmeddelande                                          |
| 3202           | Det fanns redan 1000 jobb som skapats i de senaste 3 600 sekunder som frekvens överskrids:   1000 jobb som skapats per 3 600 sekunder. | För många Databricks körs i en timme.                         | Kontrollera alla pipelines som använder den här Databricks-arbetsytan för jobbet skapas.  Om pipelines startas för många Databricks körs i aggregering, migrera vissa pipelines till en ny arbetsyta. |
| 3202           | Det gick inte att parsa objekt: Förväntade 'key' och 'value' måste anges för JSON kartan fältet base_parameters fick ”nyckel:”... ”” i stället. | Redigera fel: Inget värde angavs för parametern         | Kontrollera om pipeline-json och se till att alla parametrar i anteckningsboken baseParameters har en icke-tomt värde som angetts. |
| 3202           | Användare: SimpleUserContext{userId=..., name=user@company.com, orgId=…} har inte behörighet att få åtkomst till klustret | Användaren som skapade åtkomsttoken tillåts inte för åtkomst till Databricks-klustret som anges i den länkade tjänsten. | Se till att användaren har behörigheterna som krävs på arbetsytan.   |
| 3203           | Klustret har statusen Uppsagd, finns inte för att ta emot jobb. Lös klustret eller försök igen senare. | Klustret har avslutats.    Det kan vara ett konkurrenstillstånd för interaktiva kluster. | Bästa sättet att undvika detta är att använda jobbet kluster.             |
| 3204           | Det gick inte att köra i jobbet. Det kan vara valfritt antal felmeddelanden från oväntat klustertillstånd verksamhetsspecifika meddelandet.  De flesta vanliga är inget felmeddelande. | Gäller inte                                                          | Gäller inte                                                          |



## <a name="azure-data-lake-analytics-u-sql"></a>Azure Data Lake Analytics (U-SQL)

| Felkod         | Felmeddelande                                                | Problembeskrivning                                          | Möjliga korrigering / rekommenderad åtgärd                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | Åtkomsttoken är från fel klient                    | Felaktig AAD-klient                                         | Tjänstens huvudnamn som används för att komma åt ADLA tillhör en annan AAD-klient. Skapa nytt huvudnamn för tjänsten i samma klientorganisation som ADLA-kontot. |
| 2711,   2705,   2704 | Forbidden. ACL-verifieringen misslyckades. Resursen finns inte eller användaren har inte behörighet att utföra den begärda åtgärden<br/><br/>Användaren har inte möjlighet att få åtkomst till datalake-store  <br/><br/>Användaren har inte behörighet till data lake analytics | Tjänstens huvudnamn eller certifikat som angetts har inte åtkomst till filen i lagring | Kontrollera att tjänstens huvudnamn eller certifikat som de tillhandahåller för ADLA-jobb har åtkomst till både ADLA-konto och ADLS standardlagring för den från rotmappen. |
| 2711                 | Det går inte att hitta ”Azure Data Lake Store-filen eller mappen       | Sökvägen till USQL-filen är felaktiga eller den länkade tjänsten autentiseringsuppgifter har inte åtkomst | Kontrollera sökvägen och autentiseringsuppgifterna i den länkade tjänsten |
| 2707                 | Det går inte att matcha kontot AzureDataLakeAnalytics. Kontrollera ”AccountName' och 'DataLakeAnalyticsUri'. | ADLA-konto i den länkade tjänsten är fel                  | Kontrollera att rätt typ av konto har angetts             |
| 2703                 | Fel-Id: E_CQO_SYSTEM_INTERNAL_ERROR eller eventuella fel börjar med ”fel-Id”: | Fel kommer från ADLA                                    | Eventuella fel som ser ut som exemplet innebär att jobbet har skickats till ADLA och skriptet det misslyckade. Undersökningen måste göras på ADLA. Om du öppnar portalen och gå till ADLA-konto, kan du leta efter jobbet genom att använda ADF aktivitet körnings-Id (inte pipelinekörningens Id). Det jobbet kommer att ha mer information om felet och hjälper till att felsöka. Om lösningen inte är tom. Kontakta supportteamet för ADLA och ange jobb-URL som innehåller namnet på ditt konto och jobb-ID. |
| 2709                 | Vi kan inte acceptera jobbet just nu. Det maximala antalet jobb i kön för ditt konto är 200. | Begränsning på ADLA                                           | Minska antalet skickade jobb till ADLA genom att ändra ADF utlösare och samtidighetsinställningar på aktiviteter eller öka begränsningar i ADLA |
| 2709                 | Det här jobbet avvisades eftersom den kräver 24 AUs och det här kontot har en administratörsdefinierad princip som förhindrar att ett jobb med hjälp av mer än 5 AU: er. | Begränsning på ADLA                                           | Minska antalet skickade jobb till ADLA genom att ändra ADF utlösare och samtidighetsinställningar på aktiviteter eller öka begränsningar i ADLA |



## <a name="azure-functions"></a>Azure Functions

| Felkod | Felmeddelande                           | Beskrivning                                                  | Möjliga korrigering / rekommenderad åtgärd                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Svarsinnehållet är inte en giltig JObject | Det innebär att Azure-funktion som har namnet inte returnerade en JSON-nyttolast i svaret. ADF Azure funktionsaktiviteten har endast stöd för JSON-svar-innehåll. | Uppdatera Azure-funktion för att returnera en giltig JSON-nyttolast t.ex. en C# funktion kan returnera (ActionResult) nya < OkObjectResult (”{`\"Id\":\"123\"`}”); |
| 3600         | Ogiltig HttpMethod ”:..”.               | Det innebär att Http-metod som angetts i nyttolasten aktivitet inte stöds av aktivitet för Azure-funktion. | Http-metoder som stöds är:  <br/>PLACERA, POST, HÄMTA, TA BORT, OPTIONS, HEAD, SPÅRNING |



## <a name="custom-azure-batch"></a>Anpassad (Azure Batch)
| Felkod | Felmeddelande                                                | Beskrivning                                                  | Möjliga korrigering / rekommenderad åtgärd                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Tryck på ett oväntat undantag och körningen misslyckades.             | Det går inte att starta kommandot eller programmet returnerade en felkod. | Kontrollera att den körbara filen finns. Om programmet startas, kontrollera stdout.txt och stderr.txt som överförts till lagringskontot. Det är en bra idé att genererar ymnig loggar i din kod för felsökning. |
| 2501         | Kan inte komma åt användaren batch-konto, kontrollera inställningarna för batch-konto. | Felaktig Batch åtkomst till nyckeln eller pool namn har tillhandahållits.            | Om du behöver verifiera poolnamn och nyckel för Batch-åtkomst i den länkade tjänsten. |
| 2502         | Inte komma åt användaren lagringsutrymme kan kontot,. Kontrollera inställningarna för lagringskontot. | Felaktigt namn eller åtkomst lagringskontonyckel tillhandahålls.       | Om du behöver verifiera lagringskontots namn och åtkomstnyckel i länkade tjänsten. |
| 2504         | Åtgärden returnerade en ogiltig statuskod 'BadRequest'     | För många filer i folderPath om den anpassade aktiviteten.  (Total storlek på resourceFiles får inte vara mer än 32768 tecken). | Ta bort onödiga filer eller zip dem och lägga till ett unzip-kommando för att extrahera, till exempel: powershell.exe - nologo - noprofile-kommandot ”& {Add-Type - A” System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;  $folder\yourProgram.exe |
| 2505         | Det går inte att skapa signatur för delad åtkomst om inte nyckeln för autentiseringsuppgifter som ska användas. | Anpassade aktiviteter har endast stöd för lagringskonton som använder en åtkomstnyckel. | Se beskrivning                                            |
| 2507         | Sökvägen till mappen finns inte eller är tomt:...            | Inga filer i lagringskontot i den angivna sökvägen.       | FolderPath måste innehålla den körbara filer som du vill köra. |
| 2508         | Det är dubblettfiler resursmapp.               | Det finns flera filer med samma namn i olika undermappar för folderPath. | Anpassade aktiviteter platta ut mappstruktur under folderPath.  Om strukturen för snabbkorrigeringar ska bevaras, komprimerar filerna och extrahera dem på Azure Batch med en unzip-kommando, till exempel: powershell.exe - nologo - noprofile-kommandot ”& {Add-Type - A” System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;   $folder\yourProgram.exe |
| 2509         | Batch url... är ogiltigt, det måste vara i Uri-format.         | Batch-URL måste vara liknar https:\//mybatchaccount.eastus.batch.azure.com | Se beskrivning                                            |
| 2510         | Ett fel uppstod när begäran skickades.               | Batch-Webbadressen är ogiltig                                         | Kontrollera URL: en för batch.                                            |

## <a name="hdinsight-spark-hive-mapreduce-pig-hadoop-streaming"></a>HDInsight (Spark, Hive, MapReduce, Pig, Hadoop Streaming)

| Felkod | Felmeddelande                                                | Beskrivning                                                  | Möjliga korrigering / rekommenderad åtgärd                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Det gick inte att skicka för Hadoop-jobb. Fel: Fjärrnamnet kunde inte lösas. <br/><br/>Klustret hittades inte. | Den angivna kluster-URI är ogiltig                              | Kontrollera att klustret inte har tagits bort och den angivna URI: N är korrekt. Du kan öppna URI: N i alla webbläsare och du bör se Ambari UI. Om klustret är i ett virtuellt nätverk, sedan URI: N ska vara privat URI: N och försök att öppna den från en virtuell dator som ingår i samma virtuella nätverk. Mer information för [virtuellt nätverk i HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2 300         | Det gick inte att skicka för Hadoop-jobb. Job: …, Cluster: …/. Fel: En uppgift avbröts. | Tidsgränsen nåddes för överföring av jobbet.                         | Detta kan vara antingen allmänna anslutningsproblem för HDInsight eller ett nätverksproblem. Först bekräfta att HDInsight Ambari UI är tillgängligt via valfri webbläsare och dina autentiseringsuppgifter är fortfarande giltiga. Se till att göra detta från den virtuella datorn/datorn där lokal IR installeras om du använder lokal IR. Försök att skicka jobbet från ADF igen. Om det fortfarande inte, kontakta ADF-teamet för support. |
| 2 300         | Ej behörig:   Ambari användarnamnet eller lösenordet är felaktigt  <br/><br/>Ej behörig:   Användaradministration är låst i Ambari   <br/><br/>403 - ej tillåtet: Åtkomst nekas | Angivna autentiseringsuppgifterna för HDInsight är felaktiga eller har upphört att gälla | Åtgärda dem och distribuera om den länkade tjänsten. Se till att autentiseringsuppgifterna som fungerar på HDInsight först genom att öppna kluster-URI i valfri webbläsare och försök att logga in. Om de inte fungerar, kan du återställa dem från Azure-portalen. |
| 2300,   2310 | 502 – webbservern tog emot ett ogiltigt svar när den fungerade som gateway eller proxy       <br/>Felaktig gateway | Fel kommer från HDInsight                               | Det här felet kommer från HDInsight-kluster. Se [HDInsight felsökare](https://hdinsight.github.io/ambari/ambari-ui-502-error.html) med vanliga fel.    <br/>För Spark-kluster det även bero på grund av [detta](https://hdinsight.github.io/spark/spark-thriftserver-errors.html). <br/><br/>[Ytterligare länk](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502) |
| 2 300         | Det gick inte att skicka för Hadoop-jobb. Jobb:..., kluster:... Fel: {\"fel\":\"det går inte att skicka jobbet begäran-tjänst som templeton tjänst är upptagen med för många begäranden för skicka jobbet. Vänta en stund innan du försöker igen. Se config templeton.parallellism.job.submit konfigurerar samtidiga begäranden. \  <br/><br/>Det gick inte att skicka för Hadoop-jobb. Jobb: 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, Cluster: https:\//abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/.   Fel: {\"fel\":\"java.io.IOException: org.apache.hadoop.yarn.exceptions.YarnException: Det gick inte att skicka application_1561147195099_3730 till YARN: org.apache.hadoop.security.AccessControlException: Kön root.joblauncher redan har 500 program, kan inte acceptera att ansökan: application_1561147195099_3730\ | För många jobb som skickas till HDInsight på samma gång | Överväg att begränsa antalet samtidiga jobb som skickas till HDI. Se ADF aktivitet samtidighet om de som skickas av samma aktivitet. Ändra utlösare så att samtidiga pipelinekörningar spridda över tiden. Även gå till HDInsight-dokumentation för att justera ”templeton.parallellism.job.submit” som antyder fel. |
| 2303,   2347 | Hadoop-jobbet misslyckades med slutkoden '5'. Se ”wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr” för mer information.  <br/><br/>Hive-körning misslyckades med felkoden ”UserErrorHiveOdbcCommandExecutionFailure”.   Se ”wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out” för mer information | Jobbet har skickats till HDInsight och det gick inte att på HDInsight | Jobbet har skickats till HDInsight. Det gick inte på klustret. Du antingen öppna jobb på HDInsight Ambari UI, och öppna loggar det eller öppna filen från storage som fel meddelande pekar ut. Information om felet ska finnas i filen. |
| 2328         | Internt serverfel uppstod när begäran bearbetades. Försök begäran eller kontakta supporten. | Sker på HDInsight på begäran.                              | Det här felet kommer från HDInsight-tjänsten när HDInsight etableringen misslyckas. HDInsight-teamet och ge dem på begäran klustrets namn. |
| 2310         | java.lang.NullPointerException                               | Ett fel uppstod när du skickar jobbet till Spark-kluster      | Det här undantaget kommer från HDInsight och döljer sig tjänsteomfattande.   Kontakta HDInsight-teamet för support och ge dem klusternamnet och tidsintervall för aktivitetskörning. |
|              | Alla andra fel                                             |                                                              | Se [HDInsight felsökare](../hdinsight/hdinsight-troubleshoot-guide.md) och [HDInsight vanliga frågor och svar](https://hdinsight.github.io/) |



## <a name="web-activity"></a>Webbaktivitet

| Felkod | Felmeddelande                                                | Beskrivning                                                  | Möjliga korrigering / rekommenderad åtgärd                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Ogiltig HttpMethod ”:..”.                                    | Det innebär att Http-metod som beskrivs i nyttolasten för aktiviteten inte stöds av Web-aktivitet. | Http-metoder som stöds är: <br/>PUT, POST, HÄMTA, TA BORT |
| 2108         | Ogiltig serverfel 500                                     | Internt fel vid slutpunkten                               | Kontrollera funktioner på en URL (med Fiddler/Postman): [Hur du skapar en HTTP-session med hjälp av Fiddler](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Obehörig 401                                             | Saknar giltig autentisering på begäran                      | Ange giltig autentiseringsmetod (token kan ha upphört att gälla).   <br/><br/>Kontrollera funktioner på en URL (med Fiddler/Postman): [Hur du skapar en HTTP-session med hjälp av Fiddler](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Forbidden 403                                                | Behörigheter som krävs för saknas                                 | Kontrollera att användarbehörighet på resursen används.   <br/><br/>Kontrollera funktioner på en URL (med Fiddler/Postman): [Hur du skapar en HTTP-session med hjälp av Fiddler](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 400 (felaktig förfrågan)                                              | Ogiltig Http-begäran                                         | Kontrollera URL: en, verb och brödtexten i begäran.   <br/><br/>Använd Fiddler/Postman för att verifiera begäran: [Hur du skapar en HTTP-session med hjälp av Fiddler](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 404 hittades inte                                                | Resursen hittades inte                                       | Använd Fiddler/Postman för att verifiera begäran: [Hur du skapar en HTTP-session med hjälp av Fiddler](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Tjänsten är inte tillgänglig                                          | Tjänsten är inte tillgänglig                                       | Använd Fiddler/Postman för att verifiera begäran: [Hur du skapar en HTTP-session med hjälp av Fiddler](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Medietypen stöds inte                                       | Felaktigt Content-Type med brödtext för Web-aktivitet           | Ange rätt innehållstypen som matchar nyttolastformatet Använd Fiddler/Postman för att verifiera begäran: [Hur du skapar en HTTP-session med hjälp av Fiddler](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Den resurs du söker för har tagits bort, bytt namn eller är tillfälligt otillgänglig. | Resursen är inte tillgänglig                                | Kontrollera slutpunkten med hjälp av Fiddler/Postman: [Hur du skapar en HTTP-session med hjälp av Fiddler](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Sidan du söker efter kan inte visas eftersom en ogiltig metod (HTTP-verb) som används. | Felaktig webbmetod aktivitet har angetts i begäran   | Kontrollera slutpunkten med hjälp av Fiddler/Postman: [Hur du skapar en HTTP-session med hjälp av Fiddler](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | invalid_payload                                              | Brödtexten för Web-aktivitet är felaktig                       | Kontrollera slutpunkten med hjälp av Fiddler/Postman: [Hur du skapar en HTTP-session med hjälp av Fiddler](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |

#### <a name="how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application"></a>Hur du skapar en HTTP-session för det övervakade webbprogrammet med hjälp av Fiddler

1. Ladda ned och installera [Fiddler](https://www.telerik.com/download/fiddler)

2. Om ditt webbprogram använder HTTPS:

   1. Öppna Fiddler

   2. Gå till **Verktyg > Fiddler alternativ** och välj som i skärmbilden nedan. 

      ![fiddler-options](media/data-factory-troubleshoot-guide/fiddler-options.png)

3. Om ditt program använder SSL-certifikat, måste du också lägga till Fiddler-certifikatet till din enhet.

4. Om du vill lägga till Fiddler-certifikatet till din enhet, gå till **verktyg** > **Fiddler alternativ** > **HTTPS**  >   **Åtgärder** > **exportera rotcertifikatet för Desktop** du skaffar certifikatet Fiddler.

5. Inaktivera samla in så att webbläsarens cache kan tas bort för att starta en ny spårning.

6. 1. Gå till **filen** > **Capture Traffic** eller tryck på **F12**.
   2. Rensa webbläsarens cacheminne så att alla cachelagrade objekt tas bort och måste vara igen hämtade.

7. Skapa begäran: 

8. 1. Klicka på fliken Composer
   2. Ange Http-metoden och URL: en
   3. Lägga till rubriker och brödtext i förfrågan om det behövs
   4. Klicka på Kör

9. Börja fånga in trafik igen och slutföra problematiska transaktionen på sidan.

10. När du slutfört, går du till **filen** > **spara** > **alla sessioner**.

Mer information om Fiddler [här](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="next-steps"></a>Nästa steg

För mer hjälp med att hitta lösning på problemet, är här några resurser som du kan prova.

*  [Bloggar](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Funktionsbegäranden](https://feedback.azure.com/forums/270578-data-factory)
*  [Videoklipp](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter](https://twitter.com/hashtag/DataFactory)



