---
title: Felsöka Azure Data Factory data flöden | Microsoft Docs
description: Lär dig hur du felsöker data flödes problem i Azure Data Factory.
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 5eafa0cc6284df5c969f63e2ab3fac4113fab417
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178623"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>Felsöka Azure Data Factory data flöden

Den här artikeln utforskar vanliga fel söknings metoder för data flöden i Azure Data Factory.

## <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>Felmeddelande: DF-SYS-01: skuggad. databricks. org. apache. Hadoop. FS. Azure. AzureException: com. Microsoft. Azure. Storage. StorageException: Den angivna behållaren finns inte.

- **Symptom**: Körningen av data flödet för för hands version, fel sökning och pipeline Miss lyckas eftersom behållaren inte finns

- **Orsak**: När data uppsättningen innehåller en behållare som inte finns i lagringen

- **Lösning**: Kontrol lera att den behållare som du refererar till i din data uppsättning finns

## <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>Felmeddelande: DF-SYS-01: Java. lang. AssertionError: kontroll fel: Motstridiga katalog strukturer har identifierats. Misstänkta sökvägar

- **Symptom**: När jokertecken används i käll omvandling med Parquet-filer

- **Orsak**: Felaktig eller ogiltig syntax för jokertecken

- **Lösning**: Kontrol lera syntaxen för jokertecken som du använder i dina käll omvandlings alternativ

## <a name="error-message-df-src-002-container-container-name-is-required"></a>Felmeddelande: DF-SRC-002: "container" (container namn) krävs

- **Symptom**: Körningen av data flödet för för hands version, fel sökning och pipeline Miss lyckas eftersom behållaren inte finns

- **Orsak**: När data uppsättningen innehåller en behållare som inte finns i lagringen

- **Lösning**: Kontrol lera att den behållare som du refererar till i din data uppsättning finns

## <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>Felmeddelande: DF-UNI-001: PrimaryKeyValue har inkompatibla typer IntegerType och StringType

- **Symptom**: Körningen av data flödet för för hands version, fel sökning och pipeline Miss lyckas eftersom behållaren inte finns

- **Orsak**: Inträffar vid försök att infoga felaktig primär nyckel typ i databas mottagare

- **Lösning**: Använd en härledd kolumn för att omvandla kolumnen som du använder för den primära nyckeln i ditt data flöde för att matcha data typen för mål databasen

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>Felmeddelande: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: TCP/IP-anslutningen till värden xxxxx.database.windows.net port 1433 har misslyckats. Fel: "xxxx.database.windows.net. Verifiera anslutnings egenskaperna. Se till att en instans av SQL Server körs på värden och accepterar TCP/IP-anslutningar på porten. Se till att TCP-anslutningar till porten inte blockeras av en brand vägg. "

- **Symptom**: Det går inte att förhandsgranska data eller köra pipelinen med databas källan eller Sink

- **Orsak**: Databasen skyddas av brand väggen

- **Lösning**: Öppna brand Väggs åtkomst till databasen

## <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>Felmeddelande: DF-SYS-01: com. Microsoft. SqlServer. JDBC. SQLServerException: Det finns redan ett objekt med namnet xxxxxx i databasen.

- **Symptom**: Mottagaren kan inte skapa tabellen

- **Orsak**: Det finns redan ett befintligt tabell namn i mål databasen med samma namn som definierats i din källa eller i data uppsättningen

- **Lösning**: Ändra namnet på den tabell som du försöker skapa



## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
