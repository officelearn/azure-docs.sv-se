---
title: Apache Hadoop-komponenter och versioner – Azure HDInsight
description: Läs om Apache Hadoop-komponenter och versioner i HDInsight och servicenivåer som är tillgängliga i den här molndistribution av Hortonworks Data Platform.
keywords: hadoop-versioner, hadoop-ekosystemet komponenter, hadoop-komponenter, hur du kontrollerar hadoop-version
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 1783bf51c33a1dec84572b76149771a9723fe209
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519654"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Vad är Apache Hadoop-komponenter och versioner som är tillgängliga med HDInsight?

Lär dig mer om den [Apache Hadoop](https://hadoop.apache.org/) ekosystem komponenter och versioner i Microsoft Azure HDInsight, samt Enterprise Security Package. Lär dig även att kontrollera komponenten-versioner som Hadoop i HDInsight. 

Varje HDInsight-version är en molndistribution av en version av Hortonworks Data Platform (HDP).

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoop-komponenter som är tillgängliga med olika versioner av HDInsight
Azure HDInsight har stöd för flera Hadoop-klusterversioner som kan distribueras när som helst. Varje version alternativ skapar en specifik version av HDP-distributionen och en uppsättning komponenter som ingår i distributionen. Från och med 4 April 2017, kluster standardversionen används av Azure HDInsight är 3.6 och baseras på HDP 2.6.

Komponent-versioner som är associerade med HDInsight-klusterversioner som listas i följande tabell: 

> [!NOTE]  
> Standardversionen för HDInsight-tjänsten kan ändras utan föregående meddelande. Om du har ett beroende som version, ange HDInsight version när du skapar ditt kluster med .NET SDK med Azure PowerShell och Azure klassiskt CLI.

| Komponent | HDInsight 4.0 (förhandsversion) | HDInsight 3.6 (standard) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 | HDInsight 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |3.0 |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop och YARN |3.1.1 |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.9.1 |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive och HCatalog |-|1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive |3.1.0 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez Hive2 |-| 0.8.4 |-|-|-|-|-|-|
| Apache Ranger |1.1.0 |0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |2.0.1 |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.7 |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.3.1 |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.2.1 |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |-|0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |5 |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.3.1 |2.3.0, 2.2.0, 2.1.0 |1.6.2, 2.0 |1.6.0 |1.5.2 |1.3.1 (endast Windows) |-|-|
| Apache Livy |0,5 |0.4 |0.3 |0.3 |0.2 |-|-|-|
| Apache Kafka | 1.1 |1.1, 1.0 * (Se kommentaren nedan) | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.7.0 |2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.8.0 |0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |4.2.1 |3.2.8 |-|-|-|
| Apache skjutreglaget |-| 0.92.0 |-|-|-|-|-|-|

> [!NOTE]
> På grund av system prestandaöverväganden, stöd för Kafka version 0.10 har upphört att gälla i mars 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Sök efter aktuell versionsinformation för Hadoop-komponent

Hadoop-ekosystemet komponenten versioner som är associerade med HDInsight-kluster-versioner kan ändra med uppdateringar till HDInsight. Att kontrollera Hadoop-komponenter och kontrollera vilka versioner som används för ett kluster, kan du använda Ambari REST API. Den **GetComponentInformation** kommandot hämtar information om tjänstens komponenter. Mer information finns i [Apache Ambari dokumentation][ambari-docs].

> [!IMPORTANT]    
> Linux är det enda operativsystem som används på HDInsight version 3.4 och senare. Mer information finns i [Windows-avveckling på HDInsight](#hdinsight-windows-retirement).

### <a name="release-notes"></a>Viktig information

Se [HDInsight viktig](hdinsight-release-notes.md) för ytterligare viktig information på de senaste versionerna av HDInsight.

## <a name="supported-hdinsight-versions"></a>HDInsight-versioner som stöds
I tabellerna nedan listas versionerna av HDInsight. HDP-versioner som motsvarar varje HDInsight-version visas tillsammans med datum för produkt-versionen. Support upphör att gälla och tillbakadragande datumen tillhandahålls även när de är kända.

### <a name="available-versions"></a>Tillgängliga versioner

I följande tabell visas de versioner av HDInsight som är tillgängliga i Azure portal samt andra distributionsmetoder som PowerShell och .NET SDK.

| HDInsight-version | HDP-version | VM OS | Utgivningsdatum | Support upphör att gälla | Slutdatum | Hög tillgänglighet |  Tillgänglighet på Azure portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 <br> (Förhandsversion) |HDP 3.0 |Ubuntu 16.0.4 LTS |24 september 2018 | | |Ja |Ja |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4 april 2017 | | |Ja |Ja |
| HDInsight 3.5 <br> (Spark)\* |HDP 2.6 |Ubuntu 16.0.4 LTS |30 september 2016 |Den 13 mars 2019 |Den 13 mars 2019 |Ja |Ja |

*&ast; Stöd för HDInsight 3.5 utökades endast för Spark klustertyper*

> [!NOTE]  
> När du har stöd för en version har upphört att gälla, kanske den inte tillgänglig via Microsoft Azure-portalen. Dock klusterversioner fortsätta att vara tillgängliga med den `Version` parameter i Windows PowerShell [New AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) kommando- och .NET SDK tills versionen slutdatum.
>

### <a name="retired-versions"></a>Utgångna versioner

I följande tabell visas de versioner av HDInsight som är **inte** tillgängliga i Azure-portalen.

| HDInsight-version | HDP-version | VM OS | Utgivningsdatum | Support upphör att gälla | Slutdatum | Hög tillgänglighet |  Tillgänglighet på Azure portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 <br> (Icke-Spark) |HDP 2.5 |Ubuntu 16.0.4 LTS |30 september 2016 |Den 5 september 2017 |28 juni 2018 |Ja |Nej |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |Den 29 mars 2016 |Den 29 december 2016 |9 januari 2018 |Ja |Nej |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |Den 2 december 2015 |27 juni 2016 |31 juli 2018 |Ja |Nej |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |Den 2 december 2015 |27 juni 2016 |31 juli 2017 |Ja |Nej |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS eller Windows Server 2012 R2 |18 februari 2015 |Den 1 mars 2016 |Den 1 april 2017 |Ja |Nej |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24 juni 2014 |18 maj 2015 |Den 30 juni 2016 |Ja |Nej |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 februari 2014 |17 september 2014 |Den 30 juni 2015 |Ja |Nej |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |Den 28 oktober 2013 |12 maj 2014 |Den 31 maj 2015 |Ja |Nej |
| HDInsight 1.6 |HDP 1.1 | |Den 28 oktober 2013 |26 april 2014 |Den 31 maj 2015 |Nej |Nej |

> [!NOTE]  
> Högtillgängliga kluster med två huvudnoder distribueras som standard för HDInsight version 2.1 och senare. De är inte tillgängligt för kluster för HDInsight-version 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Enterprise-säkerhetspaketet för HDInsight

Företagssäkerhet är ett valfritt paket som du kan lägga till i ditt HDInsight-kluster som en del av arbetsflödet för skapa klustret. Har stöd för Enterprise Security Package:

- Integrering med Active Directory för autentisering.

    Du kan bara skapa HDInsight-kluster med en lokal administratörsanvändare och en lokal SSH-användare tidigare. Lokal administratör kan komma åt alla filer, mappar, tabeller och kolumner.  Med Enterprise Security Package, kan du aktivera rollbaserad åtkomstkontroll genom att integrera HDInsight-kluster med dina egna Active Directory, som innehåller den lokala Active Directory, Azure Active Directory Domain Services eller Active Directory på IaaS den virtuella datorn. Domänadministratören i klustret kan ge användare använder sina egna företagets (domän)-användarnamn och lösenord för åtkomst till klustret. 

    Mer information finns i:

    - [En introduktion till Apache Hadoop-säkerhet med domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-introduction.md)
    - [Planera Azure-domänanslutna Apache Hadoop-kluster i HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Konfigurera domänansluten testmiljö](./domain-joined/apache-domain-joined-configure.md)
    - [Konfigurera domänanslutna HDInsight-kluster med Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Auktorisering för data

  - Integrering med Apache Ranger för auktorisering för Hive och Spark SQL Yarn köer.
  - Du kan ställa in åtkomstkontroll för filer och mappar.

    Mer information finns i:

  - [Konfigurera Apache Hive-policyer i domänanslutna HDInsight](./domain-joined/apache-domain-joined-run-hive.md)

- Visa granskningsloggar för att övervaka åtkomst och konfigurerade principer. 

### <a name="supported-cluster-types"></a>Klustertyper som stöds

För närvarande stöder endast följande klustertyper Enterprise Security Package:

- Hadoop (endast HDInsight 3.6)
- Spark
- Interaktiv fråga

### <a name="support-for-azure-data-lake-storage"></a>Stöd för Azure Data Lake Storage

Enterprise Security Package-stöder med hjälp av Azure Data Lake Storage som både den primära lagringen och Lägg till mer lagringsutrymme.

### <a name="pricing-and-sla"></a>Priser och SLA
Information om priser och SLA för Enterprise Security Package finns i [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hdinsight-windows-retirement"></a>Dra tillbaka HDInsight Windows
Microsoft Azure HDInsight version 3.3 har den senaste versionen av HDInsight på Windows. Slutdatum för HDInsight på Windows är den 31 juli 2018. Om du har alla HDInsight-kluster på Windows 3.3 eller tidigare, måste du migrera till HDInsight i Linux (HDInsight version 3.5 eller senare) före den 31 juli 2018. Migrera till Linux-operativsystem kan du behålla möjligheten att skapa eller ändra storlek på dina HDInsight-kluster. Stöd för HDInsight version 3.3 på Windows upphörde den 27 juni 2016.

Från och med HDInsight version 3.4 har släppt Microsoft HDInsight endast på Linux-operativsystem. Därför kan är vissa komponenter i HDInsight tillgängliga för Linux endast. Dessa inkluderar [Apache Ranger](https://ranger.apache.org/), [Apache Kafka](https://kafka.apache.org/), interaktiv fråga [Apache Spark](https://spark.apache.org/), HDInsight-program och Azure Data Lake Storage som det primära filsystemet. Framtida versioner av HDInsight är endast tillgängliga på Linux-operativsystem. Det tillkommer ingen framtida versioner av HDInsight på Windows. 

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Hur ser tidslinjen för att ta bort HDInsight på Windows?
Den 31 juli 2018 är slutdatum för HDInsight på Windows. Om planerat slutdatum är olika för din region, meddelas du separat. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Vad är effekten av att ta bort HDInsight på Windows för befintliga kunder?
När HDInsight på Windows har dragits tillbaka, det går inte att du skapar ett nytt HDInsight Windows-kluster eller ändra storlek på ett befintligt HDInsight Windows-kluster. Stöd för HDInsight version 3.3 upphörde den 27 juni 2016. Det finns därför ingen support eller felkorrigeringar för HDInsight 3.3 eller tidigare versioner. Framtida versioner av HDInsight är endast tillgängliga på Linux-operativsystem. Det tillkommer ingen framtida versioner av HDInsight på Windows.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>Vilka versioner av HDInsight på Windows påverkas?
Azure HDInsight version 3.3 är den senaste versionen av HDInsight för Windows. Innan HDInsight på Windows har dragits tillbaka måste alla HDInsight-Windows-kluster version 3.3 eller tidigare migreras till HDInsight på Linux version 3.5 eller senare. Migrera dina kluster till HDInsight i Linux kan du behålla möjligheten att skapa nya kluster eller ändra storlek på befintliga kluster. 

### <a name="what-do-i-need-to-do"></a>Vad måste jag göra?
Migrera dina HDInsight Windows-kluster till ett stöds HDInsight Linux-kluster innan 31 juli 2018. Läs mer i den [HDInsight migrace](hdinsight-migrate-from-windows-to-linux.md). Information om Azure HDInsight-versioner finns i listan över [versioner som stöds](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Var hittar jag kluster-OS-typ?
Gå till sidan Översikt över HDInsight-kluster i Azure-portalen och leta upp **Klustertyp** under **Essentials**. OS-klustertyper visas på sidan. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>Jag kan inte migrera till ett kluster i HDInsight Linux innan 31 juli 2018. Vad är påverkan på mitt HDInsight Windows-kluster?
HDInsight Windows-kluster kör som-är, men du kan inte skapa ett nytt HDInsight Windows-kluster eller ändra storlek på ett befintligt HDInsight Windows-kluster. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>Mitt kluster är beroende av en .NET. Hur löser jag det här beroendet i Linux?
Du kan lösa dina beroende för Linux-kluster med hjälp av den [Mono projekt](https://www.mono-project.com/). Den här öppen källkod-implementeringen av .NET är tillgänglig för HDInsight Linux-kluster. Läs mer i den [HDInsight migrace](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Jag är en ny kund för HDInsight på Windows. Hur kan jag skapa ett HDInsight-Windows-kluster?
Från och med 3 juli 2017 endast befintliga HDInsight Windows kunder kan skapa nya HDInsight Windows kluster. Nya kunder kan inte skapa ett HDInsight-Windows-kluster i Azure-portalen med hjälp av PowerShell eller SDK. Vi rekommenderar att nya kunder skapar ett Linux HDInsight-kluster. Befintliga kunder kan skapa nya HDInsight Windows kluster tills HDInsight på Windows slutdatum. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Finns det prissättningen påverkas som är associerade med att flytta från HDInsight på Windows till HDInsight på Linux?
Nej, priserna är desamma för HDInsight på antingen OS. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Vad är kund fördelarna som är associerade med flytten till enbart med HDInsight på Linux?
* Snabbare tid till marknad för öppen källkod stordatatekniker via tjänsten HDInsight
* En stor community och ekosystem för support
* Möjlighet att utöva aktivt med utveckling av öppen källkod-communityn för Hadoop och andra tekniker för stordata

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>HDInsight i Linux kan ge ytterligare funktioner utöver vad som är tillgängliga i HDInsight på Windows?
Från och med HDInsight version 3.4 har släppt Microsoft HDInsight endast på Linux-operativsystem. Därför kan är vissa komponenter i HDInsight tillgängliga för Linux endast. Dessa inkluderar Apache Ranger, Kafka, Interactive Query, Spark, HDInsight-program, och Azure Data Lake Storage som det primära filsystemet. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Servicenivåavtalet för HDInsight-klusterversioner
Serviceavtal (SLA) har definierats som en _support fönstret_. Fönstret support är tidsperioden som ett HDInsight-kluster av version som stöds av Microsofts kundservice och Support. Om versionen har en _stöd för förfallodatum_ som har klarat, HDInsight-klustret är utanför fönstret support. Mer information om versioner som stöds finns i listan över [HDInsight-kluster-versioner som stöds](hdinsight-migrate-from-windows-to-linux.md). Utgångsdatumet för stöd för en angiven HDInsight version X (när det finns en nyare version X + 1) beräknas som den senare av:  

* Formel 1: Lägg till 180 dagar i datumet när HDInsight-klusterversionen X släpptes.
* Formel 2: Lägg till 90 dagar i datumet när HDInsight-klusterversionen X + 1 är tillgänglig i Azure-portalen.

Den _datumet för tillbakadragandet_ avser det datum då klusterversionen inte kan skapas på HDInsight. Från och med 31 juli 2017, du kan inte ändra storlek på ett HDInsight-kluster efter dess slutdatum. 

> [!NOTE]  
> HDInsight Windows-kluster (även versionerna 2.1, 3.0, 3.1, 3.2 eller 3.3) körs på Azure Guest OS-familj version 4, vilket använder 64-bitars version av Windows Server 2012 R2. Azure Guest OS-familj version 4 stöder .NET Framework version 4.0, 4.5, 4.5.1 och 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Hortonworks viktig information som är associerade med HDInsight-versioner

Avsnittet innehåller länkar till viktig information för Hortonworks Data Platform-distributioner och Apache-komponenter som används med HDInsight.
* HDInsight-kluster av version 4.0 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* HDInsight-kluster av version 3.6 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* HDInsight-kluster av version 3.5 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). HDInsight-kluster av version 3.5 är den _standard_ Hadoop-kluster som skapas i Azure-portalen.
* HDInsight-kluster av version 3.4 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* HDInsight-kluster av version 3.3 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Viktig information om Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) finns på webbplatsen Apache.
  * [Viktig information om Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) finns på webbplatsen Apache.
* HDInsight-kluster av version 3.2 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.2][hdp-2-2].

  * Viktig information för specifika Apache-komponenter finns på följande sätt: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [vanliga](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), och [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* HDInsight-kluster av version 3.1 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. 3.1 för HDInsight-kluster som skapas före November, 7, 2014 baseras på [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* HDInsight-kluster av version 3.0 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 2.0][hdp-2-0-8].
* HDInsight-kluster av version 2.1 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 1.3][hdp-1-3-0].
* HDInsight-kluster av version 1.6 använder en Hadoop-distribution som baseras på [Hortonworks Data Platform 1.1][hdp-1-1-0].



## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Noden konfigurationen och den virtuella datorn standardstorlekar för kluster
I tabellerna nedan listas standard-storlekar för virtuella datorer (VM) för HDInsight-kluster.  Det här diagrammet är nödvändigt att förstå storlekarna som ska användas när du skapar PowerShell eller Azure CLI-skript för att distribuera HDInsight-kluster.

> [!IMPORTANT]  
> Om du behöver mer än 32 arbetarnoder i ett kluster, måste du välja en head nodstorlek med minst 8 kärnor och 14 GB RAM-minne.

* Alla regioner utom södra Brasilien och västra Japan:

|Klustertyp|Hadoop|HBase|Interaktiv fråga|Storm|Spark|ML Server|Kafka|
|---|---|---|---|---|---|---|---|
|Huvud: standardstorleken för virtuella datorer|D12 v2|D12 v2|D13 v2|A3|D12 v2|D12 v2|D3v2|
|Huvud: rekommenderade storlekar för Virtuella datorer|D3 v2|D3 v2|D13|A4 v2|D12 v2|D12 v2|A2M v2|
||D4 v2|D4 v2|D14|A8 v2|D13 v2|D13 v2|D3 v2|
||D12 v2|D12 v2|E16 v3|A2m v2|D14 v2|D14 v2|D4 v2|
||E4 v3|E4 v3|E32 v3|E4 v3|E4 v3|E4 v3|D12 v2|
|Worker: standardstorleken för virtuella datorer|D4 v2|D4 v2|D14 v2|D3 v2|D13 v2|D4 v2|4 D12v2 med 2 S30 diskar per meddelandekö|
|Worker: rekommenderade storlekar för Virtuella datorer|D3 v2|D3 v2|D13|D3 v2|D4 v2|D4 v2|D13 v2|
||D4 v2|D4 v2|D14|D4 v2|D12 v2|D12 v2|DS12 v2|
||D12 v2|D12 v2|E16 v3|D12 v2|D13 v2|D13 v2|DS13 v2|
||E4 v3|E4 v3|E20 v3|E4 v3|D14 v2|D14 v2|E4 v3|
||||E32 v3||E16 v3|E16 v3|ES4 v3|
||||E64 v3||E20 v3|E20 v3|E8 v3|
||||||E32 v3|E32 v3|ES8 v3|
||||||E64 v3|E64 v3||
|ZooKeeper: standardstorleken för virtuella datorer||A4 v2|A4 v2|A4 v2||A2 v2|D3v2|
|ZooKeeper: rekommenderade storlekar för Virtuella datorer||A4 v2||A2 v2|||A2M v2|
|||A8 v2||A4 v2|||D3 v2|
|||A2m v2||A8 v2|||E8 v3|
|Edge: standardstorleken för virtuella datorer||||||D4 v2||
|Edge: Rekommenderad storlek för virtuell dator||||||D4 v2||
|||||||D12 v2||
|||||||D13 v2||
|||||||D14 v2||
|||||||E16 v3||
|||||||E20 v3||
|||||||E32 v3||
|||||||E64 v3||

* Södra Brasilien och västra Japan endast (ingen v2-storlekar):

  | Klustertyp | Hadoop | HBase | Interaktiv fråga |Storm | Spark | ML Services |
  | --- | --- | --- | --- | --- | --- | --- |
  | Huvud: standardstorleken för virtuella datorer |D12 |D12  | D13 |A3 |D12 |D12 |
  | Huvud: rekommenderade storlekar för Virtuella datorer |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13,<br/> D14 |A3,<br/> A4,<br/> A5 |D12,<br/> D13,<br/> D14 |D12,<br/> D13,<br/> D14 |
  | Worker: standardstorleken för virtuella datorer |D4 |D4  |  D14 |D3 |D13 |D4 |
  | Worker: rekommenderade storlekar för Virtuella datorer |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13,<br/> D14 |D3,<br/> D4,<br/> D12 |D4,<br/> D12,<br/> D13,<br/> D14 | D4,<br/> D12,<br/> D13,<br/> D14 |
  | ZooKeeper: standardstorleken för virtuella datorer | |A4 v2 | A4 v2| A4 v2 | | A2 v2|
  | ZooKeeper: rekommenderade storlekar för Virtuella datorer | |A2,<br/> A3,<br/> A4 | |A2,<br/> A3,<br/> A4 | | |
  | Edge: standard VM-storlekar | | | | | |D4 |
  | Edge: rekommenderade storlekar för Virtuella datorer | | | | | |D4,<br/> D12,<br/> D13,<br/> D14 |

> [!NOTE]
> - HEAD kallas *Nimbus* för Storm typ av kluster.
> - Worker kallas *övervakaren* för Storm typ av kluster.
> - Worker kallas *Region* för HBase typ av kluster.

## <a name="next-steps"></a>Nästa steg
- [Installationen av kluster för Apache Hadoop, Spark och mer på HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Arbeta i Apache Hadoop på HDInsight från Windows PC](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: https://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: https://zookeeper.apache.org/
