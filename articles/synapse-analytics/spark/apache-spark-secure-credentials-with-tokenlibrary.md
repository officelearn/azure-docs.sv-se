---
title: Skydda autentiseringsuppgifter för åtkomst med länkade tjänster i Apache Spark för Azure Synapse Analytics
description: Den här artikeln innehåller begrepp för hur du på ett säkert sätt kan integrera Apache Spark för Azure Synapse Analytics med andra tjänster med hjälp av länkade tjänster och token-bibliotek
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: d542e6ef784d763e406aad28231431cbc382fbfd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450913"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>Skydda dina autentiseringsuppgifter genom länkade tjänster med TokenLibrary
Att komma åt data från externa källor är ett vanligt mönster. Om inte den externa data källan tillåter anonym åtkomst måste du skydda anslutningen med en autentiseringsuppgift, hemlig eller anslutnings sträng.  

Azure Synapse Analytics tillhandahåller länkade tjänster för att förenkla integrerings processen genom att lagra anslutnings informationen i en länkad tjänst eller Azure Key Vault. När du har skapat en länkad tjänst kan Apache Spark referera till den länkade tjänsten för att tillämpa anslutnings informationen i din kod. 

Mer information finns i [länkade tjänster](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
> [!NOTE]
> Att komma åt filer från Azure Data Lake Storage i din arbets yta använder AAD-genomströmning för autentisering. därför behöver du inte använda TokenLibrary. 


## <a name="prerequisite"></a>Förutsättning
* Länkad tjänst – du måste skapa en länkad tjänst till den externa data källan och referera till den länkade tjänsten från token-biblioteket. Läs mer om [länkade tjänster](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>Anslut till ADLS Gen2 utanför Synapse-arbetsytan

Synapse tillhandahåller en integrerad länkad tjänst upplevelse för Azure Data Lake Storage Gen2.

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>Använda token-biblioteket

Om du vill ansluta till andra länkade tjänster kan du göra ett direkt anrop till TokenLibrary.

### <a name="getconnectionstring"></a>GetConnectionString
 Hämta anslutnings strängen med hjälp av <b>getConnectionString</b> -funktionen och skicka den <b>länkade tjänstens namn</b>.

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
Parsa vissa värden från ett <i>nyckel = värde</i> -par i anslutnings strängen, till exempel 

<i>DefaultEndpointsProtocol = https; AccountName = \<AccountName> ; AccountKey =\<AccountKey></i>

Använd funktionen <b>getConnectionStringAsMap</b> och skicka nyckeln för att returnera värdet.
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>Nästa steg

- [Skriv till dedikerad SQL-pool](./synapse-spark-sql-pool-import-export.md)

