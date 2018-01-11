---
title: "Stöd för kombinationer av körningen och data miljöer för Azure Machine Learning Data förberedelser | Microsoft Docs"
description: "Det här dokumentet innehåller en fullständig lista över kombinationer som stöds av olika körningar och datakällor för Data förberedelser för Azure Machine Learning"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 248cbcfe35db646a8bc71c6f825dcaa8a4661e91
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="supported-matrix-for-this-release"></a>Matris som stöds för den här versionen 
När koden läser in data med hjälp av Azure Machine Learning-datakällor eller Azure Machine Learning Data förberedelser, komma antingen Pandas eller Spark dataframe, följande kombinationer av experimentet compute miljöer och data platser stöds:

|     |Lokala filer  |Azure Blob Storage  |SQL Server-databasen ***  |
|---------|---------|---------|---------|---------|
|Lokala Python    |     Stöds    |Stöds inte         | Stöds inte        |         |
|Docker (Linux VM) Python     |Stöds endast projektfiler *         | Stöds inte        |        Stöds inte |         |
|PySpark docker (Linux VM)     |Stöds endast projektfiler *     |Stöds         | Stöds**        |         |
|Azure datavetenskap virtuella Python     |Stöds endast projektfiler *         |Stöds inte         |Stöds inte         |         |
|Azure datavetenskap virtuella PySPark     | Stöds endast projektfiler *        |Stöds inte         |Stöds inte         |         |
|Azure HDInsight PySpark     | Stöds inte        |Stöds         |Stöds**         |         |
|Azure HDInsight Python     | Stöds inte        | Stöds inte        | Stöds inte        |         |

Azure Data Lake Store stöds inte för varje compute-mål.

* När lokala sökvägar används kopieras till compute-miljön och läses sedan det filer i projektet. Kopieras inte filerna utanför projektet och kommer inte längre att lösa sökvägarna i beräknings-miljö. Överväg att använda datakälla ersättning så att din kod kan använda en lokal fil när du kör lokalt. Sedan växla till en Azure Storage blob för en annan kör konfiguration. Du kan också använda provtagning stöd på datakällor för att hantera körs på stora mängder data endast i vissa kör konfigurationer.

** Använder SQL Server för Maven JDBC-drivrutinen 6.2.1. Du måste se till att det här paketet (eller en kompatibel) ingår i filen spark_dependencies.yml för beräknings-miljö.

Har stöd för Azure SQL Database eller SQL Server har angetts i databasen kan nås från beräknings-miljön. 
