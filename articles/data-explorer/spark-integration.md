---
title: Azure Data Explorer-anslutningsapp för Apache Spark
description: Den här artikeln visar hur du använder Datautforskaren i Azure-anslutning för Apache Spark.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 6f115df97d0b790c94d6dc07f3f40feeceb1a7cc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824324"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Azure Data Explorer-anslutningsapp för Apache Spark

Du kan använda Datautforskaren i Azure och Apache Spark, för att skapa snabba, skalbara program som riktar in sig på datadriven scenarier, till exempel machine learning (ML), extrahering, transformering och laddning (ETL) och Log Analytics.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda anslutningstjänsten måste måste ditt program ha:

* Java 1.8 SDK
* Maven 3.x
* Spark-version 2.3.2 eller högre

## <a name="link-to-data-explorer"></a>Länk till Datautforskaren

Projektet definitioner för Scala och Java-program med hjälp av Maven, länka ditt program med följande artefakter:

```java
groupId = com.microsoft.azure
artifactId = spark-kusto-connector
version = 1.0.0-Beta-01 
```

I Maven-länk med följande:

```Maven
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-kusto-connector</artifactId>
    <version>1.0.0-Beta-01</version>
  </dependency>
```

## <a name="build-commands"></a>Skapa kommandon

Att skapa JAR-filen och kör alla test:

```
mvn clean package
```

Kör alla test för att skapa jar och installera JAR-filen till din lokala lagringsplats med Maven:

```
mvn clean install
```