---
title: "Installera eller uppdatera Mono på HDInsight - Azure | Microsoft Docs"
description: "Lär dig hur du använder en viss version av Mono med HDInsight-kluster. Mono används för att köra .NET-program på Linux-baserade HDInsight-kluster."
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.custom: hdinsightactive
ms.openlocfilehash: e9e55f05c97fe335a315683294805b1027b53784
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="install-or-update-mono-on-hdinsight"></a>Installera eller uppdatera Mono på HDInsight

Lär dig hur du installerar en specifik version av [Mono](https://www.mono-project.com) på HDInsight 3.4 eller högre.

Mono är installerad på HDInsight 3.4 och högre och används för att köra .NET-program. Mer information om versionen av Mono som ingår i varje version av HDInsight finns [HDInsight component-versioning](hdinsight-component-versioning.md). Om du vill installera en annan version på klustret, Använd skriptåtgärden i det här dokumentet. 

## <a name="how-it-works"></a>Hur det fungerar

Det här skriptet accepterar följande parameter:

* __Monoljud versionsnumret__: version av Mono om du vill installera. Versionen måste vara tillgänglig från [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

Skriptet installerar följande monoljud paket:

* __slutföra Mono__

* __CA-certifikat-mono__

## <a name="the-script"></a>Skriptet

__Script plats__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Krav för__:

* Skriptet måste tillämpas på den __huvudnoder__ och __arbetarnoder__.

## <a name="to-use-the-script"></a>Du använder skriptet

Information om hur du använder det här skriptet med HDInsight finns i [anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentet. Du kan använda skriptet via Azure-portalen, Azure PowerShell eller Azure CLI.

När följande skript åtgärd dokumentet använder du följande URI:

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

Om du vill ange monoljud version som är installerad, använda versionsnumret i den __parametrar__ fältet. Ange till exempel `5.4` installera Mono 5.4.

> [!NOTE]
> När du konfigurerar HDInsight med det här skriptet, markera skriptet som __bevarade__. Den här inställningen HDInsight för att använda skriptet till arbetsnoder som lagts till via skalning åtgärder.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du uppgradera eller installera en viss version av Mono på HDInsight. Mer information om hur du använder .NET-program med Mono på HDInsight finns i följande dokument:

* [Använda .NET för strömning MapReduce på HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Använda .NET med Hive och Pig i HDInsight](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Utveckla C#-lösningar med Storm på HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
* [Migrera .NET-lösningar till Linux-baserat HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Mer information om hur du använder skriptåtgärder finns [anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md)