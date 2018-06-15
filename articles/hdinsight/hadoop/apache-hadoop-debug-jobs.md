---
title: 'Felsöka Hadoop i HDInsight: visa loggar och tolka felmeddelanden - Azure | Microsoft Docs'
description: Läs mer om de felmeddelanden du kan få när du administrerar HDInsight med hjälp av PowerShell och åtgärder du kan vidta för att återställa.
services: hdinsight
tags: azure-portal
editor: cgronlun
manager: jhubbard
author: ashishthaps
documentationcenter: ''
ms.assetid: 7e6ceb0e-8be8-4911-bc80-20714030a3ad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 8952e1381d81d153a170f35dd3cfd3f60cd4f73a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31404764"
---
# <a name="analyze-hadoop-logs"></a>Analysera Hadoop-loggar

Varje Hadoop-kluster i Azure HDInsight har ett Azure storage-konto som används som standard-filsystem. Lagringskontot kallas standardkontot för lagring. Klustret använder Azure Table storage och Blob-lagring på standardkontot för lagring för att lagra loggar.  Om du vill ta reda på standardkontot för lagring för klustret, se [hantera Hadoop-kluster i HDInsight](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account). Loggarna behåller i Lagringskontot läggs även när klustret har tagits bort.

## <a name="logs-written-to-azure-tables"></a>Loggar som skrivs till Azure-tabeller

Loggarna skrivs till Azure-tabeller ger en inblick i vad som händer med ett HDInsight-kluster.

När du skapar ett HDInsight-kluster skapas automatiskt sex tabeller för Linux-baserade kluster i standard Table storage:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Tabell filnamn **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Dessa tabeller innehåller följande fält:

* ClusterDnsName
* Komponentnamn
* eventTimestamp
* Värd
* MALoggingHash
* Meddelande
* N
* PreciseTimeStamp
* Roll
* RowIndex
* Klientorganisation
* TIDSSTÄMPEL
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Verktyg för att komma åt loggarna
Det finns många verktyg för att komma åt data i dessa tabeller:

* Visual Studio
* Azure Lagringsutforskaren
* Power Query för Excel

#### <a name="use-power-query-for-excel"></a>Använda Power Query för Excel
Power Query kan installeras från [Microsoft Power Query för Excel](http://www.microsoft.com/en-us/download/details.aspx?id=39379). Läs mer på hämtningssidan för systemkraven.

**Använd Power Query för att öppna och analysera service-loggen**

1. Öppna **Microsoft Excel**.
2. Från den **Power Query** -menyn klickar du på **från Azure**, och klicka sedan på **från Microsoft Azure Table storage**.
   
    ![HDInsight Hadoop Excel PowerQuery öppna Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Ange lagringskontonamnet kort namn eller FQDN.
4. Ange lagringskontots åtkomstnyckel. Du bör se en lista över tabeller:
   
    ![HDInsight Hadoop-loggar som lagras i Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Högerklicka på tabellen hadoopservicelog i den **Navigator** fönstret och välj **redigera**. Fyra kolumner visas. Du kan också ta bort den **partitionsnyckel**, **Radnyckel**, och **tidsstämpel** kolumner genom att markera dem och sedan på **ta bort kolumner** från alternativ i menyfliksområdet.
6. Klicka på ikonen i kolumnen innehåll för att välja de kolumner som du vill importera till Excel-kalkylblad. För den här demonstrationen du har valt TraceLevel och komponentnamn: den kan ge mig grundläggande information som komponenter hade problem.
   
    ![HDInsight Hadoop-loggar Välj kolumner](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Klicka på **OK** för import av data.
8. Välj den **TraceLevel**, roll, och **komponentnamn** kolumner och klicka sedan på **Group By** kontroll i menyfliksområdet.
9. Klicka på **OK** i dialogrutan Gruppera efter
10. Klicka på ** Tillämpa & Stäng **.

Du kan nu använda Excel för att filtrera och sortera efter behov. Du kanske vill inkludera andra kolumner (t.ex meddelande) för att öka detaljnivån problem när de inträffar, men att markera och gruppera de kolumner som beskrivs ovan ger en hyfsad bild av vad som händer med Hadoop-tjänster. Samma idé kan tillämpas på tabellerna setuplog och hadoopinstalllog.

#### <a name="use-visual-studio"></a>Använd Visual Studio
**Använd Visual Studio**

1. Öppna Visual Studio.
2. Från den **visa** -menyn klickar du på **Cloud Explorer**. Eller klicka bara på **CTRL +\, CTRL + X**.
3. Från **Cloud Explorer**väljer **resurstyper**.  Det andra alternativet är **resursgrupper**.
4. Expandera **Lagringskonton**, standardkontot för lagring för klustret, och sedan **tabeller**.
5. Dubbelklicka på **hadoopservicelog**.
6. Lägga till ett filter. Exempel:
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop-loggar Välj kolumner](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Mer information om hur du skapar filter finns [filtret konstruerar strängar för tabelldesign](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Loggar som skrivs till Azure Blob Storage
[Loggarna skrivs till Azure-tabeller](#log-written-to-azure-tables) ger en inblick i vad som händer med ett HDInsight-kluster. Dessa tabeller ger dock inte aktivitetsnivå loggarna, vilket kan vara till hjälp för att gå vidare till problem när de inträffar. HDInsight-kluster är konfigurerade för att skriva uppgiften loggar till ditt Blob Storage-konto för alla jobb som skickas via Templeton för att tillhandahålla den här nästa detaljnivå. Praktiskt taget, innebär detta jobb som skickas med Microsoft Azure PowerShell-cmdlets eller .NET jobbet skicka API: erna, inte de jobb som skickats via RDP/Kommandotolken-Command-Line åtkomst till klustret. 

Om du vill visa loggfilerna kan se [åtkomst YARN programloggar på Linux-baserade HDInsight](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Läs mer om programloggarna [förenklad hantering av användare loggar och åtkomst i YARN](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).

## <a name="view-cluster-health-and-job-logs"></a>Visa hälso- och loggar för kluster
### <a name="access-the-ambari-ui"></a>Åtkomst till Ambari-Gränssnittet
Klicka på ett HDInsight-klustrets namn att öppna fönstret kluster från Azure-portalen. Kluster-fönstret klickar du på **instrumentpanelen**.

![Starta instrumentpanelen klustret](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Åtkomst till Yarn-Användargränssnittet
Klicka på ett HDInsight-klustrets namn att öppna fönstret kluster från Azure-portalen. Kluster-fönstret klickar du på **instrumentpanelen**. När du uppmanas ange administratörsautentiseringsuppgifter för klustret. Välj i Ambari, **YARN** från listan över tjänster till vänster. På sidan som visas, väljer **snabblänkar**, Välj aktiva huvudnod posten och Resurshanterargränssnittet.

Du kan använda YARN-Användargränssnittet för att göra följande:

* **Hämta status för klustret**. I den vänstra rutan Expandera **klustret**, och klicka på **om**. Den här finns kluster statusinformation som totalt allokerat minne, kärnor används, status för hanteraren för filserverresurser, kluster av version och så vidare.
  
    ![Starta instrumentpanelen klustret](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Hämta status för noden**. I den vänstra rutan Expandera **klustret**, och klicka på **noder**. Detta visar alla noder i klustret, HTTP-adressen för varje nod och resurser som är allokerade till varje nod osv.
* **Övervaka jobbstatus**. I den vänstra rutan Expandera **klustret**, och klicka sedan på **program** att lista alla jobb i klustret. Om du vill titta på jobb i ett specifikt tillstånd (till exempel nya, Skickat, körs osv.), klickar du på länken under **program**. Du kan ytterligare Klicka på jobbnamnet för att ta reda på mer om jobbet sådana inklusive utdata, loggar, osv.

### <a name="access-the-hbase-ui"></a>Åtkomst till HBase-Gränssnittet
Klicka på ett HDInsight HBase klusternamnet öppna klustret från Azure-portalen. Kluster-fönstret klickar du på **instrumentpanelen**. När du uppmanas ange administratörsautentiseringsuppgifter för klustret. Välj HBase i Ambari, från listan över tjänster. Välj **snabblänkar** pekar på den aktiva Zookeeper nod länken överst på sidan och klicka sedan på HBase Master UI.

## <a name="hdinsight-error-codes"></a>Felkoder för HDInsight
Felmeddelanden i det här avsnittet tillhandahålls så att användare av Hadoop i Azure HDInsight förstå olika feltillstånden som de kan stöta på när du administrerar tjänsten med hjälp av Azure PowerShell och meddela dem om de åtgärder som kan vidtas återställa från fel.

Vissa av dessa felmeddelanden kan också visas i Azure-portalen när den används för att hantera HDInsight-kluster. Andra felmeddelanden som kan uppstå, men det finns mindre detaljerad på grund av begränsningarna som de vidtar åtgärderna som är möjliga i den här kontexten. Andra felmeddelanden tillhandahålls i sammanhang där minskning är uppenbara. 

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
* **Beskrivning**: Ange Azure SQL database-information för minst en komponent för att kunna använda anpassade inställningar för Hive och Oozie metastores.
* **Minskning**: användaren måste ange en giltig SQL Azure-metastore och försök utföra begäran.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Beskrivning**: gick inte att skapa kluster i regionen *nameOfYourRegion*. Använd ett giltigt HDInsight-regionen och försök igen med begäran.
* **Minskning**: kunden ska skapa kluster-region som för närvarande stöder dem: Sydostasien, västra Europa, Norra Europa, östra USA eller västra USA.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Beskrivning**: servern kunde inte hitta den begärda kluster-posten.  
* **Minskning**: försök igen.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Beskrivning**: klustret DNS-namnet *yourDnsName* är ogiltig. Kontrollera att namnet börjar och slutar med alfanumeriska och kan endast innehålla '-' specialtecken  
* **Minskning**: Kontrollera att du har använt ett giltigt DNS-namn för klustret som börjar och slutar med alfanumeriska och innehåller ingen särskild tecken än ett streck '-' och försök sedan igen.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Beskrivning**: klusternamnet *yourClusterName* är inte tillgänglig. Välj ett annat namn.  
* **Minskning**: användaren bör ange ett klusternamn som är unikt och inte finns och försök igen. Om användaren använder portalen meddelar Användargränssnittet dem om en klusternamnet används redan under Skapa stegen.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Beskrivning**: klustret lösenordet är ogiltigt. Lösenordet måste vara minst 10 tecken och måste innehålla minst en siffra, en versal bokstav, gemen bokstav och specialtecken utan blanksteg och får inte innehålla användarnamnet som en del av den.  
* **Minskning**: Ange ett giltigt kluster lösenord och försök igen.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Beskrivning**: klustret användarnamnet är ogiltigt. Kontrollera att användarnamnet inte innehålla specialtecken eller blanksteg.  
* **Minskning**: Ange ett giltigt kluster-användarnamn och försök igen.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Beskrivning**: klustret DNS-namnet *yourDnsClusterName* är ogiltig. Kontrollera att namnet börjar och slutar med alfanumeriska och kan endast innehålla '-' specialtecken  
* **Minskning**: Ange ett giltigt användarnamn för DNS-kluster och försök igen.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Beskrivning**: behållarnamn i URI: N *yourcontainerURI* och DNS-namnet *yourDnsName* i begäran innehållet måste vara samma.  
* **Minskning**: Kontrollera att din behållaren namn och DNS-namn är identiska och försök igen.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Beskrivning**: Ogiltig klusterkonfigurationen. Det gick inte att hitta någon nod datadefinitioner i noden storlek.  
* **Minskning**: försök igen.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Beskrivning**: borttagningen av distributionen misslyckades för klustret  
* **Minskning**: försök igen.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Beskrivning**: tjänsten konfigurationsfel. Information om nödvändiga DNS-mappning inte hittades.  
* **Minskning**: ta bort klustret och skapa ett nytt kluster.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Beskrivning**: duplicera klustret behållaren skapa försök. Det finns en post för *nameOfYourContainer* men Etags matchar inte.
* **Minskning**: Ange ett unikt namn för behållaren och försök skapa-åtgärden.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Beskrivning**: värdtjänsten *nameOfYourHostedService* innehåller redan ett kluster. En värdbaserad tjänst får inte innehålla flera kluster  
* **Minskning**: värd i klustret i en annan värdbaserad tjänst.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Beskrivning**: servern kunde inte uppdatera tillståndet för kluster-distributionen.  
* **Minskning**: försök igen. Om det händer flera gånger, kontakta Kundsupporten.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Beskrivning**: klustret *yourClusterName* har tagits bort som en del av underhåll. Du måste skapa klustret.
* **Minskning**: återskapa klustret.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Beskrivning**: Ogiltig klusterkonfigurationen. Konfiguration av nödvändiga huvudnod hittades inte i noden storlekar.
* **Minskning**: försök igen.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Beskrivning**: Det gick inte att skapa värdbaserade tjänsten *nameOfYourHostedService*. Försök att utföra begäran.  
* **Minskning**: försök begäran.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Beskrivning**: värdtjänsten *nameOfYourHostedService* har redan en Produktionsdistribution. En värdbaserad tjänst får inte innehålla flera Produktionsdistribution. Försök igen med ett annat klusternamn.
* **Minskning**: Använd ett annat klusternamn och försök begäran.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Beskrivning**: värdtjänsten *nameOfYourHostedService* för klustret inte kunde hittas.  
* **Minskning**: om klustret är i feltillstånd, ta bort den och försök sedan igen.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Beskrivning**: värdtjänsten *nameOfYourHostedService* har ingen associerad distribution.  
* **Minskning**: om klustret är i feltillstånd, ta bort den och försök sedan igen.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Beskrivning**: det prenumerations-ID *yourSubscriptionId* saknar kärnor kvar för att skapa kluster *yourClusterName*. Obligatoriskt: *resourcesRequired*, tillgänglig: *resourcesAvailable*.  
* **Minskning**: Frigör resurser i din prenumeration eller öka resurser tillgängliga för prenumerationen och försök att skapa klustret igen.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Beskrivning**: prenumerations-ID *yourSubscriptionId* saknar kvoten för en ny HostedService att skapa kluster *yourClusterName*.  
* **Minskning**: Frigör resurser i din prenumeration eller öka resurser tillgängliga för prenumerationen och försök att skapa klustret igen.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Beskrivning**: servern påträffade ett internt fel. Försök att utföra begäran.  
* **Minskning**: försök begäran.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Beskrivning**: Azure-lagringsplats *dataRegionName* är inte en giltig plats. Kontrollera att regionen är rätt och försök igen med begäran.
* **Minskning**: Välj en lagringsplats som stöder HDInsight, kontrollera att klustret är samplacerade och försök igen.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Beskrivning**: Ogiltig VM-storlek för datanoder. Endast 'stora VM-storleken stöds för alla datanoder.  
* **Minskning**: Ange storleken för datanod nod som stöds och försök igen.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Beskrivning**: Ogiltig VM-storlek för huvudnod. Endast 'Extrastora VM-storlek stöds för huvudnod.  
* **Minskning**: Ange storleken för huvudnoden nod som stöds och försök igen

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Beskrivning**: prenumerations-ID *yourSubscriptionId* används inte har tillräcklig behörighet för att köra delete-åtgärden för klustret *yourClusterName*.  
* **Minskning**: om klustret är i feltillstånd, släpp den och försök sedan igen.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Beskrivning**: externa blob-behållaren lagringskontonamnet *yourContainerName* är ogiltig. Kontrollera att namnet börjar med en bokstav och innehåller bara gemena bokstäver, siffror och bindestreck.  
* **Minskning**: Ange en giltig blob-behållaren lagringskontonamnet och försök igen.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Beskrivning**: konfiguration för externa lagringskontot *yourStorageAccountName* måste ha information om hemlig nyckel anges.  
* **Minskning**: Ange en giltig hemlig nyckel för lagringskontot och försök igen.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Beskrivning**: versionshuvudet *yourVersionHeader* är inte giltigt format för åååå-mm-dd.  
* **Minskning**: Ange ett giltigt format för version-huvudet och försök sedan begäran.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Beskrivning**: Ogiltig klusterkonfigurationen. Hitta mer än en huvudnod konfiguration.  
* **Minskning**: redigera konfigurationen så att bara en huvudnod har angetts.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Beskrivning**: åtgärden slutfördes inte inom den tilldelade tiden eller maximalt antal försök möjligt. Försök att utföra begäran.  
* **Minskning**: försök begäran.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Beskrivning**: parametern *yourParameterName* får inte vara null eller tomt.  
* **Minskning**: Ange ett giltigt värde för parametern.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Beskrivning**: en eller flera kluster skapa begäran indata är inte giltig. Kontrollera att indatavärdena är korrekta och försök igen med begäran.  
* **Minskning**: Kontrollera att indatavärdena är korrekta och försök igen med begäran.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Beskrivning**: Region-funktioner som är inte tillgängliga för region *yourRegionName* prenumerations-ID och *yourSubscriptionId*.  
* **Minskning**: Ange en region som har stöd för HDInsight-kluster. Offentliga områden är: Sydostasien, västra Europa, Norra Europa, östra USA eller västra USA.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Beskrivning**: lagringskonto *yourStorageAccountName* finns i regionen *currentRegionName*. Det ska vara samma som regionen som klustret *yourClusterRegionName*.  
* **Minskning**: Ange ett lagringskonto i samma region som klustret är i eller om data finns redan i lagringskontot kan du skapa ett nytt kluster i samma region som det befintliga lagringskontot. Om du använder portalen meddelar Användargränssnittet dem om det här problemet i förväg.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Beskrivning**: anges prenumerations-ID *yourSubscriptionId* är inte aktiv.  
* **Minskning**: återaktivera din prenumeration eller skaffa en giltig prenumeration.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Beskrivning**: prenumerations-ID *yourSubscriptionId* kunde inte hittas.  
* **Minskning**: Kontrollera att ditt prenumerations-ID är giltigt och försök igen.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Beskrivning**: Det gick inte att matcha DNS *yourDnsUrl*. Kontrollera att den fullständiga URL-Adressen för blob-slutpunkten har angetts.  
* **Minskning**: Ange en giltig blob-URL. URL måste vara fullständigt giltiga, inklusive från och med *http://* och slutar på *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Beskrivning**: Det gick inte att kontrollera platsen för resurs *yourDnsUrl*. Kontrollera att den fullständiga URL-Adressen för blob-slutpunkten har angetts.  
* **Minskning**: Ange en giltig blob-URL. URL måste vara fullständigt giltiga, inklusive från och med *http://* och slutar på *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Beskrivning**: Version-funktioner som är inte tillgängliga för version *specifiedVersion* prenumerations-ID och *yourSubscriptionId*.  
* **Minskning**: välja en version som är tillgänglig och försök igen.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Beskrivning**: Version *specifiedVersion* stöds inte.
* **Minskning**: välja en version som stöds och försök igen.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Beskrivning**: Version *specifiedVersion* är inte tillgänglig i Azure-region *specifiedRegion*.  
* **Minskning**: välja en version som stöds i den region som har angetts och försök igen.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Beskrivning**: Ogiltig klusterkonfigurationen. Nödvändiga WASB kontokonfigurationen hittades inte i externa konton.  
* **Minskning**: Verifiera att kontot finns och är korrekt angivna i konfigurationen och försök igen.

## <a name="next-steps"></a>Nästa steg

* [Använda Ambari-vyer för att felsöka Tez-jobb i HDInsight](../hdinsight-debug-ambari-tez-view.md)
* [Aktivera heap Dumpar för Hadoop-tjänster på Linux-baserat HDInsight](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Hantera HDInsight-kluster med Ambari-Webbgränssnittet](../hdinsight-hadoop-manage-ambari.md)
