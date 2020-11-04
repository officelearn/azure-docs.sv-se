---
title: Kom igång med Cognitive Services för Big data
description: Konfigurera din MMLSpark-pipeline med Cognitive Services i Azure Databricks och kör ett exempel.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 444b76a594e768face892462da12a1cbb35a5106
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324677"
---
# <a name="getting-started"></a>Komma igång

Att konfigurera din miljö är det första steget för att skapa en pipeline för dina data. När din miljö är klar går det snabbt och enkelt att köra ett exempel.

I den här artikeln utför vi de här stegen för att komma igång:

1. [Skapa en -resurs för Cognitive Services](#create-a-cognitive-services-resource)
1. [Skapa ett Apache Spark-kluster](#create-an-apache-spark-cluster)
1. [Prova ett exempel](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Skapa en -resurs för Cognitive Services

Om du vill använda Big data Cognitive Services måste du först skapa en kognitiv tjänst för vårt arbets flöde. Det finns två huvud typer av Cognitive Services: moln tjänster som finns i Azure och behållar tjänster som hanteras av användarna. Vi rekommenderar att du börjar med den enklare molnbaserade Cognitive Services.

### <a name="cloud-services"></a>Molntjänster

Molnbaserad Cognitive Services är intelligenta algoritmer som finns i Azure. Dessa tjänster är redo att användas utan utbildning. du behöver bara en Internet anslutning. Du kan [skapa en kognitiv tjänst i Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows) eller med [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli?tabs=windows).

### <a name="containerized-services-optional"></a>Behållar tjänster (valfritt)

Om ditt program eller din arbets belastning använder stora data uppsättningar, kräver privat nätverk eller inte kan kontakta molnet kan det vara omöjligt att kommunicera med moln tjänster. I den här situationen har container Cognitive Services följande fördelar:

* **Låg anslutning** : du kan distribuera behållare Cognitive Services i valfri dator miljö, både på molnet och av. Om programmet inte kan kontakta molnet kan du överväga att distribuera container Cognitive Services i ditt program.

* **Låg latens** : eftersom behållar tjänster inte kräver kommunikation med tur och retur till/från molnet, returneras svar med mycket lägre fördröjning.

* **Sekretess-och data säkerhet** : du kan distribuera behållar tjänster i privata nätverk så att känsliga data inte lämnar nätverket.

* **Hög skalbarhet** : behållarens tjänster har inte "hastighets begränsningar" och körs på användar hanterade datorer. Det innebär att du kan skala Cognitive Services utan slut för att hantera stora arbets belastningar.

Följ [den här guiden](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support?tabs=luis) för att skapa en behållar kognitiv tjänst.

## <a name="create-an-apache-spark-cluster"></a>Skapa ett Apache Spark-kluster

[Apache Spark &trade; ](http://spark.apache.org/) är ett ramverk för distribuerad data behandling som har utformats för bearbetning av stora data data. Användare kan arbeta med Apache Spark i Azure med tjänster som Azure Databricks, Azure Synapse Analytics, HDInsight och Azure Kubernetes Services. Om du vill använda Big data Cognitive Services måste du först skapa ett kluster. Om du redan har ett Spark-kluster kan du prova med ett exempel.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks är en Apache Spark-baserad analys plattform med en enkel klicknings installation, strömlinjeformade arbets flöden och en interaktiv arbets yta. Den används ofta för att samar beta mellan data experter, tekniker och affärsanalytiker. Följ dessa steg om du vill använda Big data Cognitive Services på Azure Databricks:

1. [Skapa en Azure Databricks-arbetsyta](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Skapa ett Spark-kluster i Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. Installera Big data Cognitive Services
    * Skapa ett nytt bibliotek i din databricks-arbetsyta  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * Mata in följande maven samordnar koordinater:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3` databas: `https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * Installera biblioteket på ett kluster  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="synapse-analytics-optional"></a>Synapse-analys (valfritt)

Du kan också använda Synapse Analytics för att skapa ett Spark-kluster. Azure Synapse Analytics samlar ihop företags data lager hantering och stor data analys. Det ger dig friheten att fråga efter data på dina villkor, med hjälp av antingen server utan på begäran eller etablerade resurser i stor skala. Följ dessa steg för att komma igång med Synapse Analytics:

1. [Skapa en Synapse-arbetsyta (för hands version)](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace).
1. [Skapa en ny server lös Apache Spark pool (för hands version) med hjälp av Azure Portal](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-portal).

I Synapse Analytics installeras Big data för Cognitive Services som standard.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Om du använder container Cognitive Services, är ett populärt alternativ för att distribuera Spark tillsammans med behållare en Azure Kubernetes-tjänst.

Följ dessa steg för att komma igång med Azure Kubernetes-tjänsten:

1. [Distribuera ett kluster med Azure Kubernetes Service (AKS) med hjälp av Azure Portal](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
1. [Installera Apache Spark 2.4.0 Helm-diagrammet](https://hub.helm.sh/charts/microsoft/spark)
1. [Installera en kognitiv tjänst behållare med Helm](https://docs.microsoft.com/azure/cognitive-services/computer-vision/deploy-computer-vision-on-premises)

## <a name="try-a-sample"></a>Prova ett exempel

När du har konfigurerat ditt Spark-kluster och-miljö kan vi köra ett kort exempel. Det här avsnittet visar hur du använder Big data för Cognitive Services i Azure Databricks.

Först kan vi skapa en antecknings bok i Azure Databricks. För andra Spark-kluster leverantörer använder du sina antecknings böcker eller Spark-överföring.

1. Skapa en ny Databricks-anteckningsbok genom att välja **ny antecknings** bok på **Azure Databricks** -menyn.

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. I dialog rutan **skapa antecknings bok** anger du ett namn, väljer **python** som språk och väljer det Spark-kluster som du skapade tidigare.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    Välj **Skapa**.

1. Klistra in det här kodfragmentet i din nya antecknings bok.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Hämta din prenumerations nyckel från menyn **nycklar och slut punkt** från textanalys instrument panelen i Azure Portal.
1. Ersätt plats hållaren för prenumerations nyckeln i din Databricks Notebook-kod med din prenumerations nyckel.
1. Välj Play-eller triangel-symbolen i det övre högra hörnet i din Notebook-cell för att köra exemplet. Du kan också välja **Kör allt** längst upp i antecknings boken för att köra alla celler. Svaren visas under cellen i en tabell.

### <a name="expected-results"></a>Förväntat resultat

| text                                      |   sentiment |
|:------------------------------------------|------------:|
| Jag är så glad idag, dess solig!           |   0,978959  |
| Jag är frustrerad genom den här trafik som skynda på trafik |   0,0237956 |
| Kognitiva tjänster på Spark aint-dåliga  |   0,888896  |

## <a name="next-steps"></a>Nästa steg

- [Korta python-exempel](samples-python.md)
- [Korta Scala-exempel](samples-scala.md)
- [Recept: förutsägande underhåll](recipes/anomaly-detection.md)
- [Recept: intelligent konst utforskning](recipes/art-explorer.md)
