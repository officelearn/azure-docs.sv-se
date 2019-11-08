---
title: Felsöka Azure Data Factory data flöden
description: Lär dig hur du felsöker data flödes problem i Azure Data Factory.
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 1b2309ec71cb3d43f4e5a39b80db593ab201c614
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721346"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Felsöka Azure Data Factory data flöden

Den här artikeln utforskar vanliga fel söknings metoder för data flöden i Azure Data Factory.

## <a name="common-errors-and-messages"></a>Vanliga fel och meddelanden

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Fel meddelande: DF-SYS-01: skuggad. databricks. org. apache. Hadoop. FS. Azure. AzureException: com. Microsoft. Azure. Storage. StorageException: den angivna behållaren finns inte.

- **Symptom**: för hands version av data flöde, fel sökning och pipeline-data flöde Miss lyckas eftersom behållaren inte finns

- **Orsak**: när data uppsättningen innehåller en behållare som inte finns i lagringen

- **Lösning**: kontrol lera att den behållare som du refererar till i din data uppsättning finns

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Fel meddelande: DF-SYS-01: Java. lang. AssertionError: kontroll fel: motstridiga katalog strukturer har identifierats. Misstänkta sökvägar

- **Symptom**: när jokertecken används i käll omvandling med Parquet-filer

- **Orsak**: felaktig eller ogiltig syntax för jokertecken

- **Lösning**: kontrol lera syntaxen för jokertecken som du använder i dina käll omvandlings alternativ

### <a name="error-message-df-src-002-container-container-name-is-required"></a>Fel meddelande: DF-SRC-002: container (container Name) måste anges

- **Symptom**: för hands version av data flöde, fel sökning och pipeline-data flöde Miss lyckas eftersom behållaren inte finns

- **Orsak**: när data uppsättningen innehåller en behållare som inte finns i lagringen

- **Lösning**: kontrol lera att den behållare som du refererar till i din data uppsättning finns

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Fel meddelande: DF-UNI-001: PrimaryKeyValue har inkompatibla typer IntegerType och StringType

- **Symptom**: för hands version av data flöde, fel sökning och pipeline-data flöde Miss lyckas eftersom behållaren inte finns

- **Orsak**: händer vid försök att infoga felaktig primär nyckel typ i databas mottagare

- **Lösning**: Använd en härledd kolumn för att omvandla kolumnen som du använder för den primära nyckeln i ditt data flöde för att matcha data typen för mål databasen

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Fel meddelande: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: TCP/IP-anslutningen till värden xxxxx.database.windows.net port 1433 har misslyckats. Fel: "xxxx.database.windows.net. Verifiera anslutnings egenskaperna. Se till att en instans av SQL Server körs på värden och accepterar TCP/IP-anslutningar på porten. Se till att TCP-anslutningar till porten inte blockeras av en brand vägg. "

- **Symptom**: det går inte att förhandsgranska data eller köra pipeline med databas källa eller mottagare

- **Orsak**: databasen skyddas av brand väggen

- **Lösning**: öppna brand Väggs åtkomst till databasen

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Fel meddelande: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: det finns redan ett objekt med namnet xxxxxx i databasen.

- **Symptom**: mottagaren kan inte skapa tabellen

- **Orsak**: det finns redan ett befintligt tabell namn i mål databasen med samma namn som definierats i din källa eller i data uppsättningen

- **Lösning**: ändra namnet på den tabell som du försöker skapa

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-string-or-binary-data-would-be-truncated"></a>Fel meddelande: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: sträng data eller binära data skulle trunkeras. 

- **Symptom**: när du skriver data till en SQL-mottagare går det inte att köra data flödet i pipeline-körningen med möjliga trunkering-fel.

- **Orsak**: ett fält från ditt data flöde mappar till en kolumn i din SQL-databas är inte tillräckligt brett för att lagra värdet, vilket gör att SQL-drivrutinen kan utlösa det här felet

- **Lösning**: du kan minska längden på data för sträng kolumner med ```left()``` i en härledd kolumn eller implementera [mönstret "fel rad".](how-to-data-flow-error-rows.md)

### <a name="error-message-since-spark-23-the-queries-from-raw-jsoncsv-files-are-disallowed-when-the-referenced-columns-only-include-the-internal-corrupt-record-column"></a>Fel meddelande: sedan Spark 2,3 är frågorna från RAW JSON/CSV-filer inte tillåtna när de refererade kolumnerna bara innehåller kolumnen intern skadad post. 

- **Symptom**: det går inte att läsa från en JSON-källa

- **Orsak**: vid läsning från en JSON-källa med ett enda dokument på många kapslade rader kan inte ADF, via Spark, avgöra var ett nytt dokument börjar och det föregående dokumentet slutar.

- **Lösning**: på käll omvandlingen som använder en JSON-datauppsättning expanderar du "JSON-inställningar" och aktiverar "enstaka dokument".


## <a name="general-troubleshooting-guidance"></a>Allmän fel söknings vägledning

1. Kontrol lera statusen för dina data uppsättnings anslutningar. I varje käll-och mottagar omvandling kan du gå till den länkade tjänsten för varje data uppsättning som du använder och testa anslutningarna.
2. Kontrol lera status för dina fil-och tabell anslutningar från data flödes designern. Växla vid fel sökning och klicka på Förhandsgranska data på käll omvandlingarna för att säkerställa att du kan komma åt dina data.
3. Om allt ser bra ut från data förhands granskningen, går du till pipeline-designern och sätter ditt data flöde i en pipeline-aktivitet. Felsök pipelinen för ett slut punkt till slut punkts test.

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory-bloggen](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestanda guide för ADF-mappning av data flöden](concepts-data-flow-performance.md)
