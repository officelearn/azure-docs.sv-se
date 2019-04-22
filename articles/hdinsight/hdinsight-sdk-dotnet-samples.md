---
title: 'Azure HDInsight: .NET-exempel'
description: Hitta C# .NET-exempel på GitHub för vanliga uppgifter med hjälp av HDInsight-SDK för .NET.
author: hrasheed-msft
ms.service: hdinsight
ms.topic: sample
ms.date: 04/15/2019
ms.author: hrasheed
ms.openlocfilehash: f589c5e23be105086d2908fb7db72e69c781e726
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678266"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight: .NET-exempel

> [!div class="op_single_selector"]
> * [.NET-exempel](hdinsight-sdk-dotnet-samples.md)
> * [Python-exempel](hdinsight-sdk-python-samples.md)
> * [Java-exempel](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

Den här artikeln innehåller:

* Innehåller länkar till exempel för klustret skapas uppgifter.
* Länkar till Referensinnehåll för andra hanteringsåtgärder.

Du kan [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Din Visual Studio-prenumeration ger dig krediter varje månad som du kan använda för Azure-betaltjänster.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [Azure HDInsight SDK för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>Klusterhantering - skapande

* [Skapa ett Kafka-kluster](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [Skapa ett Spark-kluster](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [Skapa ett Spark-kluster med Azure Data Lake Storage Gen2](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [Skapa ett Spark-kluster med Enterprise Security Package (ESP)](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

Du kan hämta de här exemplen för .NET genom att klona den [hdinsight-dotnet-sdk-samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) GitHub-lagringsplatsen.

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

Kodfragment för den här ytterligare SDK-funktioner finns i den [HDInsight SDK för .NET-referensdokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).