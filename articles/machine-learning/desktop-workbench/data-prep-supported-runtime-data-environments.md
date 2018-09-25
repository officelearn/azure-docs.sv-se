---
title: Kombinationer av körning och miljöer som stöds för Azure Machine Learning-Dataförberedelser | Microsoft Docs
description: Det här dokumentet innehåller en fullständig lista över kombinationer som stöds av olika körningar och datakällor för förberedelser för Azure Machine Learning Data
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 9168ac1d26432ca3eee5a59b63aa0cec3ae72856
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989064"
---
# <a name="supported-matrix-for-this-release"></a>Matris som stöds för den här versionen 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


När din kod läser in data med hjälp av Azure Machine Learning-datakällor eller Azure Machine Learning Dataförberedelser, komma antingen Pandas eller Spark dataframe följande kombinationer av experimentet compute miljöer och platser stöds:

|     |Lokala filer  |Azure Blob Storage  |SQL Server-databasen ***  |
|---------|---------|---------|---------|---------|
|Lokal Python    |     Stöds    |Stöds inte         | Stöds inte        |         |
|Docker (Linux-VM) Python     |Stöds i projektfiler endast *         | Stöds inte        |        Stöds inte |         |
|PySpark för docker (Linux-VM)     |Stöds i projektfiler endast *     |Stöds         | Stöds**        |         |
|Azure Data Science VM Python     |Stöds i projektfiler endast *         |Stöds inte         |Stöds inte         |         |
|Azure Data Science VM PySPark     | Stöds i projektfiler endast *        |Stöds inte         |Stöds inte         |         |
|Azure HDInsight PySpark     | Stöds inte        |Stöds         |Stöds**         |         |
|Azure Python i HDInsight     | Stöds inte        | Stöds inte        | Stöds inte        |         |

Azure Data Lake Store stöds inte för alla beräkning.

* När lokala sökvägar används kopieras till compute-miljö och läs sedan det filer i projektet. Kopieras inte filerna utanför projektet och sökvägarna löser inte längre i compute-miljö. Överväg att använda Data källan ersättningen så att din kod kan använda en lokal fil när du kör lokalt. Sedan växla till en Azure Storage-blob för en annan kör konfiguration. Du kan också använda sampling support på datakällor för att hantera körs på stora mängder data endast i vissa kör konfigurationer.

** Använder Maven JDBC SQL Server-drivrutinen 6.2.1. Du måste se till att det här paketet (eller en kompatibel) ingår i filen spark_dependencies.yml för compute-miljö.

Har stöd för Azure SQL Database eller SQL Server kan du tillhandahålla databasen kan nås från beräkningsmiljön. 
