---
title: Felsöka Azure Data Factory | Microsoft Docs
description: Lär dig hur du felsöker externa kontroll aktiviteter i Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: f35a3567ae4ae7c3e2d59f776d3a3bc00ec2be3e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142388"
---
# <a name="troubleshoot-azure-data-factory"></a>Felsöka Azure Data Factory

Den här artikeln utforskar vanliga fel söknings metoder för externa kontroll aktiviteter i Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Anslutnings-och kopierings aktivitet

För anslutnings problem, t. ex. fel vid kopiering av aktivitet, se [fel sökning Azure Data Factory anslutningar](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

| Felkod | Felmeddelande                                          | Beskrivning                             | Rekommendation                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | Fel 403.                                                    | Databricks-åtkomsttoken har upphört att gälla.                         | Som standard är Databricks-åtkomsttoken giltig i 90 dagar.  Skapa en ny token och uppdatera den länkade tjänsten. |
| 3201           | Obligatoriskt fält saknas: Settings. Task. notebook_task. notebook_path | Felaktig redigering: Antecknings bokens sökväg har inte angetts korrekt. | Ange antecknings bokens sökväg i Databricks-aktiviteten. |
| 3201           | Kluster... finns inte.                                 | Redigerings fel: Databricks-klustret finns inte eller har tagits bort. | Kontrol lera att Databricks-klustret finns. |
| 3201           | Ogiltig python-fil-URI... Besök Databricks user guide for Supported URI-scheman. | Felaktig redigering.                                                | Ange antingen absoluta sökvägar för arbets ytans adresserings scheman eller `dbfs:/folder/subfolder/foo.py` för filer som lagras i Databricks-filsystem. |
| 3201           | {0}LinkedService ska ha domän-och accessToken som obligatoriska egenskaper. | Felaktig redigering.                                                | Verifiera den [länkade tjänst definitionen](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}LinkedService ska ange antingen befintligt kluster-ID eller ny kluster information som ska skapas. | Felaktig redigering.                                                | Verifiera den [länkade tjänst definitionen](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Nodtypen Standard_D16S_v3 stöds inte. Nodtyper som stöds:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2. | Felaktig redigering.                                                | Se fel meddelandet.                                          |
| 3201           | Ogiltig notebook_path:... Endast absoluta sökvägar stöds för närvarande. Sökvägar måste börja med "/". | Felaktig redigering.                                                | Referera till fel meddelande.                                          |
| 3202           | Det fanns redan 1000 jobb skapade under de senaste 3600 sekunderna, vilket överskrider hastighets gränsen:   1000 jobb skapande per 3600 sekunder. | För många Databricks körs i en timme.                         | Kontrol lera alla pipeliner som använder den här Databricks-arbetsytan för att skapa takten för jobbet.  Om pipelines startade för många Databricks-körningar i mängd, migrerar du några pipelines till en ny arbets yta. |
| 3202           | Det gick inte att parsa Request-objektet: Värdet ' key ' och ' value ' förväntades för JSON-mappnings fältet base_parameters ' Key: "..." i stället. | Redigerings fel: Inget värde har angetts för parametern.         | Kontrol lera pipeline-JSON och se till att alla parametrar i baseParameters Notebook anger ett värde som inte är tomt. |
| 3202           | Användare: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` har inte behörighet att komma åt klustret. | Användaren som genererade åtkomsttoken får inte åtkomst till det Databricks-kluster som anges i den länkade tjänsten. | Se till att användaren har de behörigheter som krävs på arbets ytan.   |
| 3203           | Klustret är i ett avslutat tillstånd, inte tillgängligt för att ta emot jobb. Åtgärda klustret eller försök igen senare. | Klustret avslutades.    För interaktiva kluster kan detta vara ett tävlings tillstånd. | Det bästa sättet att undvika det här felet är att använda jobb kluster.             |
| 3204           | Jobb körningen misslyckades.  | Fel meddelanden indikerar olika problem, till exempel ett oväntat kluster tillstånd eller en speciell aktivitet. Oftast visas inget fel meddelande alls.                                                          | Gäller inte                                                          |



## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Följande tabell gäller för U-SQL.

| Felkod         | Felmeddelande                                                | Beskrivning                                          | Rekommendation                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | Åtkomsttoken är från fel klient.                    | Felaktig Azure Active Directory-klient (Azure AD).                                         | Tjänstens huvud namn som används för att komma åt Azure Data Lake Analytics tillhör en annan Azure AD-klient. Skapa ett nytt huvud namn för tjänsten i samma klient organisation som Data Lake Analytics kontot. |
| 2711, 2705, 2704 | Ej tillåtet. ACL-verifieringen misslyckades. Antingen finns inte resursen eller så har användaren inte behörighet att utföra den begärda åtgärden.<br/><br/>Användaren kan inte komma åt Data Lake Store.  <br/><br/>Användaren har inte behörighet att använda Data Lake Analytics. | Tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet. | Kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för Data Lake Analytics jobb har åtkomst till Data Lake Analytics-kontot och standard-Data Lake Storage-instansen från rotmappen. |
| 2711                 | Det går inte att hitta Azure Data Lake Store-filen eller mappen.       | Sökvägen till U-SQL-filen är felaktig eller också har autentiseringsuppgifterna för den länkade tjänsten inte åtkomst. | Verifiera sökvägen och autentiseringsuppgifterna som anges i den länkade tjänsten. |
| 2707                 | Det går inte att matcha kontot för AzureDataLakeAnalytics. Kontrol lera AccountName och DataLakeAnalyticsUri. | Det Data Lake Analytics kontot i den länkade tjänsten är felaktigt.                  | Kontrol lera att rätt konto har angetts.             |
| 2703                 | Fel-ID: E_CQO_SYSTEM_INTERNAL_ERROR (eller fel som börjar med "fel-ID:"). | Felet är från Data Lake Analytics.                                    | Ett fel som liknar exemplet innebär att jobbet skickades till Data Lake Analytics och skriptet det misslyckades. Undersök i Data Lake Analytics. I portalen går du till Data Lake Analytics kontot och letar efter jobbet med hjälp av Data Factory aktivitetens körnings-ID (inte pipelinens körnings-ID). Jobbet innehåller mer information om felet och hjälper dig att felsöka. Om upplösningen inte är klar kontaktar du Data Lake Analytics support-teamet och anger jobb-URL: en, som innehåller ditt konto namn och jobb-ID. |
| 2709                 | Vi kan inte acceptera ditt jobb just nu. Det maximala antalet köade jobb för ditt konto är 200. | Felet orsakas av begränsning av Data Lake Analytics.                                           | Minska antalet jobb som skickats till Data Lake Analytics genom att ändra Data Factory utlösare och concurrency-inställningar för aktiviteter. Eller öka gränserna för Data Lake Analytics. |
| 2709                 | Jobbet avvisades eftersom det kräver 24 Australien. Det här kontots administratörs princip förhindrar att ett jobb använder fler än 5 Australien. | Felet orsakas av begränsning av Data Lake Analytics.                                           | Minska antalet jobb som skickats till Data Lake Analytics genom att ändra Data Factory utlösare och concurrency-inställningar för aktiviteter. Eller öka gränserna för Data Lake Analytics. |



## <a name="azure-functions"></a>Azure functions

| Felkod | Felmeddelande                           | Beskrivning                                                  | Rekommendation                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Svars innehållet är inte ett giltigt JObject. | Azure-funktionen som anropades returnerade inte en JSON-nyttolast i svaret. Azure Function-aktivitet i Data Factory stöder endast JSON-svars innehåll. | Uppdatera Azure-funktionen för att returnera en giltig JSON-nyttolast. C# En funktion kan `(ActionResult)new<OkObjectResult("{`till exempel returnera \"ID\":\"123.\"`}");` |
| 3600         | Ogiltigt HttpMethod: "...".               | HTTP-metoden som anges i aktivitetens nytto Last stöds inte av Azure Function-aktiviteten. | Använd en HTTP-metod som stöds, till exempel placera, skicka, Hämta, ta bort, alternativ, huvud eller spåra. |



## <a name="custom"></a>Anpassat

Följande tabell gäller för Azure Batch.

| Felkod | Felmeddelande                                                | Beskrivning                                                  | Rekommendation                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Det gick inte att köra ett oväntat undantag och körningen misslyckades.             | Det går inte att starta kommandot eller så returnerade programmet en felkod. | Se till att den körbara filen finns. Om programmet har startats kontrollerar du att *STDOUT. txt* och *stderr. txt* överfördes till lagrings kontot. Det är en bra idé att generera Copious-loggar i koden för fel sökning. |
| 2501         | Det går inte att komma åt användarens batch-konto. kontrol lera inställningarna för batch-kontot. | Felaktig nyckel för batch-åtkomst eller poolnamn.            | Verifiera poolens namn och åtkomst nyckeln för batch i den länkade tjänsten. |
| 2502         | Det går inte att komma åt användar lagrings kontot. kontrol lera inställningarna för lagrings kontot. | Felaktigt lagrings konto namn eller åtkomst nyckel.       | Kontrol lera lagrings kontots namn och åtkomst nyckeln i den länkade tjänsten. |
| 2504         | Åtgärden returnerade en ogiltig status kod ' BadRequest '.     | För många filer i folderPath för den anpassade aktiviteten. Den totala storleken på resourceFiles får inte vara mer än 32 768 tecken. | Ta bort onödiga filer. Eller Lägg till ett zippa-kommando för att extrahera dem. Använd till exempel`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` |
| 2505         | Det går inte att skapa signaturen för delad åtkomst om inte konto nyckel autentiseringsuppgifter används. | Anpassade aktiviteter stöder bara lagrings konton som använder en åtkomst nyckel. | Se fel beskrivningen.                                            |
| 2507         | Mappsökvägen finns inte eller är tom:...            | Det finns inga filer i lagrings kontot på den angivna sökvägen.       | Mappsökvägen måste innehålla de körbara filer som du vill köra. |
| 2508         | Det finns duplicerade filer i mappen resurs.               | Flera filer med samma namn finns i olika undermappar i folderPath. | Mappstruktur för anpassade aktiviteter under folderPath.  Om du behöver bevara mappstrukturen zip-filerna och extrahera dem i Azure Batch med hjälp av ett zippa-kommando. Använd till exempel`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` |
| 2509         | Batch-URL... är ogiltig. Det måste vara i URI-format.         | Batch-URL: er måste likna`https://mybatchaccount.eastus.batch.azure.com` | Se fel beskrivningen.                                            |
| 2510         | Ett fel uppstod när begäran skickades.               | Batch-URL: en är ogiltig.                                         | Verifiera batch-URL: en.                                            |

## <a name="hdinsight"></a>HDInsight

Följande tabell gäller Spark, Hive, MapReduce, gris och Hadoop streaming.

| Felkod | Felmeddelande                                                | Beskrivning                                                  | Rekommendation                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300, 2310 | Det gick inte att skicka Hadoop-jobbet. Fel: Det gick inte att matcha fjärrnamnet. <br/><br/>Det gick inte att hitta klustret. | Den angivna kluster-URI: n är ogiltig.                              | Kontrol lera att klustret inte har tagits bort och att angiven URI är korrekt. När du öppnar URI i en webbläsare bör du se Ambari-ANVÄNDARGRÄNSSNITTET. Om klustret finns i ett virtuellt nätverk ska URI: n vara den privata URI: n. Öppna den genom att använda en virtuell dator som är en del av samma virtuella nätverk. Mer information finns i [direkt ansluta till Apache Hadoop Services](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2 300         | Det gick inte att skicka Hadoop-jobbet. Jobb:..., kluster:.../. Fel: En uppgift avbröts. | Tids gränsen för jobb överföringen uppnåddes.                         | Problemet kan vara antingen allmän HDInsight-anslutning eller nätverks anslutning. Bekräfta först att HDInsight Ambari-ANVÄNDARGRÄNSSNITTET är tillgängligt från valfri webbläsare. Bekräfta att autentiseringsuppgifterna fortfarande är giltiga. Om du använder en lokal integrerad Runtime (IR), se till att göra detta från den virtuella datorn eller datorn där IR för egen värd är installerad. Försök sedan att skicka jobbet från Data Factory igen. Om det fortfarande inte går att kontakta Data Factory-teamet för support. |
| 2 300         | Tillstånd   Ambari användar namn eller lösen ord är felaktigt  <br/><br/>Tillstånd   Användar administratören är utelåst i Ambari.   <br/><br/>403 – ej tillåtet: Åtkomst nekad. | Autentiseringsuppgifterna för HDInsight är felaktiga eller har upphört att gälla. | Korrigera autentiseringsuppgifterna och distribuera om den länkade tjänsten. Kontrol lera först att autentiseringsuppgifterna fungerar på HDInsight genom att öppna kluster-URI: n i valfri webbläsare och försök logga in. Om autentiseringsuppgifterna inte fungerar kan du återställa dem från Azure Portal. |
| 2300, 2310 | 502 – Webbservern tog emot ett ogiltigt svar när den fungerade som gateway eller proxyserver.       <br/>Felaktig gateway. | Felet är från HDInsight.                               | Det här felet är från HDInsight-klustret. Mer information finns i [AMBARI UI 502-fel](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502-fel vid anslutning till Spark Thrift-Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), 502-fel vid [anslutning till Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)och [fel sökning av felaktiga Gateway-fel i Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502). |
| 2 300         | Det gick inte att skicka Hadoop-jobbet. Jobb:..., kluster:... Fel: {\"Error\":\"det går inte att betjäna begäran om att skicka jobb eftersom Templeton-tjänsten är upptagen med för många begäran om att skicka jobb. Vänta en stund innan du försöker igen. Mer information finns i config Templeton. parallellism. job. Submit för att konfigurera samtidiga förfrågningar.  <br/><br/>Det gick inte att skicka Hadoop-jobbet. Uppgift 161da5d4-6fa8-4EF4-A240-6b6428c5ae2f, kluster: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`.   Fel: {\"Error\":\"Java. io. IOException: org. apache. Hadoop. garn. Exceptions. YarnException: Det gick inte att skicka application_1561147195099_3730 till garn: org. apache. Hadoop. Security. AccessControlException: Queue root. joblauncher har redan 500 program och kan inte acceptera sändning av program: application_1561147195099_3730 \ | För många jobb skickas till HDInsight på samma tid. | Överväg att begränsa antalet samtidiga jobb som skickats till HDInsight. Referera till Data Factory aktivitets samtidighet om jobben skickas av samma aktivitet. Ändra utlösare så att samtidiga pipelines körs sprids över tid. Se HDInsight-dokumentationen för att `templeton.parallellism.job.submit` justera som ett fel förslag. |
| 2303, 2347 | Hadoop-jobbet misslyckades med slut koden ' 5 '. wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderrMer information finns i.  <br/><br/>Hive-körningen misslyckades med felkoden ' UserErrorHiveOdbcCommandExecutionFailure '.   wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.outMer information finns i. | Jobbet skickades till HDInsight och det misslyckades i HDInsight. | Jobbet skickades till HDInsight. Det misslyckades i klustret. Öppna jobbet och loggarna i HDInsight Ambari-ANVÄNDARGRÄNSSNITTET eller öppna filen från Storage som i fel meddelandet. Filen visar fel informationen. |
| 2328         | Ett internt Server fel uppstod när begäran bearbetades. Gör om begäran eller kontakta supporten igen. | Det här felet uppstår i HDInsight på begäran.                              | Felet kommer från HDInsight-tjänsten när HDInsight-etableringen Miss lyckas. Kontakta HDInsight-teamet och ange kluster namnet på begäran. |
| 2310         | java.lang.NullPointerException                               | Det här felet inträffar när jobbet skickas till ett Spark-kluster.      | Detta undantag kommer från HDInsight. Det döljer det faktiska problemet. Kontakta HDInsight-teamet för support. Ange kluster namnet och aktivitetens kör tids intervall. |
|              | Alla andra fel                                             |                                                              | Se [fel sökning med](../hdinsight/hdinsight-troubleshoot-guide.md) [vanliga frågor och svar om](https://hdinsight.github.io/)HDInsight och HDInsight. |



## <a name="web-activity"></a>Webbaktivitet

| Felkod | Felmeddelande                                                | Beskrivning                                                  | Rekommendation                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Ogiltigt HttpMethod: "...".                                    | Webb aktiviteten stöder inte den HTTP-metod som anges i aktivitetens nytto Last. | De HTTP-metoder som stöds är placera, anslå, hämta och ta bort. |
| 2108         | Ogiltigt Server fel 500.                                     | Internt fel på slut punkten.                               | Använd Fiddler eller Postman för att kontrol lera funktionen på URL: en. |
| 2108         | Obehörig 401.                                             | Giltig autentisering saknas för begäran.                      | Token kan ha upphört att gälla. Ange en giltig autentiseringsmetod. Använd Fiddler eller Postman för att kontrol lera funktionen på URL: en. |
| 2108         | Förbjudet 403.                                                | Nödvändiga behörigheter saknas.                                 | Kontrol lera användar behörigheter för den öppna resursen. Använd Fiddler eller Postman för att kontrol lera funktionen på URL: en.  |
| 2108         | Felaktig förfrågan 400.                                              | Ogiltig HTTP-begäran.                                         | Kontrol lera URL, verb och brödtext i begäran. Använd Fiddler eller Postman för att verifiera begäran.  |
| 2108         | Hittade inte 404.                                                | Det gick inte att hitta resursen.                                       | Använd Fiddler eller Postman för att verifiera begäran.  |
| 2108         | Tjänsten är inte tillgänglig.                                          | Tjänsten är inte tillgänglig.                                       | Använd Fiddler eller Postman för att verifiera begäran.  |
| 2108         | Medie typen stöds inte.                                       | Innehålls typen matchar inte webb aktivitets texten.           | Ange den innehålls typ som matchar nytto Last formatet. Använd Fiddler eller Postman för att verifiera begäran. |
| 2108         | Den resurs du söker efter har tagits bort, har fått ett nytt namn eller är tillfälligt otillgänglig. | Resursen är inte tillgänglig.                                | Använd Fiddler eller Postman för att kontrol lera slut punkten. |
| 2108         | Sidan du letar efter kan inte visas eftersom en ogiltig metod (HTTP-verb) används. | En felaktig webb aktivitets metod angavs i begäran.   | Använd Fiddler eller Postman för att kontrol lera slut punkten. |
| 2108         | invalid_payload                                              | Webb aktivitets texten är felaktig.                       | Använd Fiddler eller Postman för att kontrol lera slut punkten. |

Så här använder du Fiddler för att skapa en HTTP-session för det övervakade webb programmet:

1. Ladda ned, installera och öppna [Fiddler](https://www.telerik.com/download/fiddler).

1. Om ditt webb program använder https går du till **verktyg** > **Fiddler alternativ** > **https**. Välj **avbilda https ansluter** och **dekryptera HTTPS-trafik**. 
   
   ![Fiddler-alternativ](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Om programmet använder SSL-certifikat lägger du till Fiddler-certifikatet på enheten. Gå till **verktyg** > **Fiddler alternativ** > **https** **åtgärder** **Exportera rot certifikat till Skriv bordet.**  >  > 

1. Inaktivera hämtning genom att gå till **fil** > **hämtnings trafik**. Eller tryck på **F12**.

1. Rensa webbläsarens cacheminne så att alla cachelagrade objekt tas bort och måste laddas ned igen.

1. Skapa en begäran: 

   a. Välj fliken **Composer** .

   b. Ange HTTP-metod och URL.

   c. Lägg till rubriker och en begär ande text om du behöver.

   d. Välj **Kör**.

9. Aktivera trafik insamlingen igen och slutför den felande transaktionen på sidan.

10. Gå till **filen** > **Spara** > **alla sessioner**.

Mer information finns i [komma igång med Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)



