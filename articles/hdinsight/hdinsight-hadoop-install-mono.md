---
title: Installera eller uppdatera Mono på HDInsight - Azure
description: Lär dig hur du använder en specifik version av Mono med HDInsight-kluster. Mono används för att köra .NET-program på Linux-baserade HDInsight-kluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.custom: hdinsightactive
ms.openlocfilehash: 9daf0d45c9bb24192750991e7bf84fa3614c970d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011042"
---
# <a name="install-or-update-mono-on-hdinsight"></a>Installera eller uppdatera Mono på HDInsight

Lär dig hur du installerar en specifik version av [Mono](https://www.mono-project.com) i HDInsight 3.4 eller högre.

Mono installeras på HDInsight 3.4 och senare och används för att köra .NET-program. Information om versionen av Mono som ingår i varje HDInsight-version finns i [versionshantering för HDInsight](hdinsight-component-versioning.md). Installera en annan version på ditt kluster genom att använda instruktionen skriptet i det här dokumentet. 

## <a name="how-it-works"></a>Hur det fungerar

Det här skriptet godkänner följande parameter:

* __Mono versionsnumret__: version av Mono för att installera. Versionen måste vara tillgänglig från [ https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/ ](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

Skriptet installerar följande Mono paket:

* __fullständig Mono__

* __CA-certifikat-mono__

## <a name="the-script"></a>Skriptet

__Skriptplats__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Krav för__:

* Skriptet måste tillämpas på den __huvudnoder__ och __arbetsnoder__.

## <a name="to-use-the-script"></a>Du använder skriptet

Information om hur du använder det här skriptet med HDInsight finns i den [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentet. Du kan använda skript via Azure portal, Azure PowerShell eller den klassiska Azure-CLI.

Använd följande URI enligt skriptet åtgärd dokumentet:

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

Om du vill ange Mono version som är installerad, använder du versionsnumret i den __parametrar__ fält. Ange till exempel `5.4` installera Mono 5.4.

> [!NOTE]
> När du konfigurerar HDInsight med det här skriptet, markera skriptet som __bevarade__. Den här inställningen HDInsight tillämpas skriptet till arbetsnoder som har lagts till via skalning.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du uppgraderar eller installerar en specifik version av Mono på HDInsight. Mer information om hur du använder .NET-program med Mono på HDInsight finns i följande dokument:

* [Använda .NET för strömmande MapReduce på HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Använda .NET med Hive och Pig i HDInsight](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Utveckla C#-lösningar med Storm på HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
* [Migrera .NET-lösningar till Linux-baserat HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Mer information om hur du använder skriptåtgärder finns i [anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md)
