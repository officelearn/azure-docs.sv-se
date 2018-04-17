---
title: Distribuera och hantera Apache Storm-topologier på HDInsight | Microsoft Docs
description: Lär dig mer om att distribuera, övervaka och hantera Apache Storm-topologier med Storm-instrumentpanelen på HDInsight. Använda Hadoop-verktyg för Visual Studio.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5e542072-f014-42aa-82d6-2694a76df520
ms.service: hdinsight
ms.devlang: java
ms.topic: conceptual
ms.date: 03/01/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 55c92e6408522b8a96a37dbedd99d929af1e49fb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Distribuera och hantera Apache Storm-topologier på Windows-baserade HDInsight

Storm-instrumentpanelen kan du enkelt distribuera och köra Apache Storm-topologier i HDInsight-kluster med hjälp av webbläsaren. Du kan också använda instrumentpanelen för att övervaka och hantera topologier som körs. Om du använder Visual Studio innehåller HDInsight Tools för Visual Studio liknande funktioner i Visual Studio.

Storm-instrumentpanelen och Storm-funktioner i HDInsight Tools beroende Storm REST-API, som kan användas för att skapa en egen övervakning och av hanteringslösningar.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett Storm på HDInsight-kluster som använder Windows som operativsystemet. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Mer information om distribuera och hantera Storm-topologier med ett HDInsight-kluster som använder Linux finns [distribuera och hantera Apache Storm-topologier på Linux-baserat HDInsight](apache-storm-deploy-monitor-topology-linux.md)

## <a name="prerequisites"></a>Förutsättningar

* **Apache Storm på HDInsight** -finns [Kom igång med Apache Storm på HDInsight](apache-storm-tutorial-get-started-linux.md) anvisningar om hur du skapar ett kluster.

* För den **Storm-instrumentpanelen**: en modern webbläsare som stöder HTML5.

* För **Visual Studio** -Azure SDK 2.5.1 eller senare och HDInsight Tools för Visual Studio. Se [komma igång med HDInsight Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) att installera och konfigurera HDInsight tools för Visual Studio.

    En av följande versioner av Visual Studio:

  * Visual Studio 2012 med Update 4

  * Visual Studio 2013 med Update 4 eller Visual Studio Community 2013

  * Visual Studio 2015 (någon utgåva)

  * 2017 för Visual Studio (någon utgåva)

## <a name="storm-dashboard"></a>Storm-instrumentpanelen

Storm-instrumentpanelen är en webbsida som är tillgängliga på Storm-kluster. URL-Adressen är **https://&lt;klusternamn >.azurehdinsight.net/**, där **klusternamn** är namnet på ditt Storm på HDInsight-kluster.

Överkant Storm-instrumentpanelen, Välj **skicka topologi**. Följ anvisningarna på sidan för att köra en exempeltopologi eller för att överföra och köra en topologi som du skapade.

![sidan skicka topologi][storm-dashboard-submit]

### <a name="storm-ui"></a>Storm UI

Storm-instrumentpanelen, Välj den **Storm-Användargränssnittet** länk. Visar information om klustret, förutom alla topologier som körs.

![storm-användargränssnittet][storm-dashboard-ui]

> [!NOTE]
> I vissa versioner av Internet Explorer kan du identifiera Storm-Användargränssnittet inte uppdaterar efter att först har besökt. Det kan till exempel inte visa nya topologier du skickat, eller den kan visa en topologi som aktiv när du tidigare har inaktiverats. Microsoft är medveten om det här problemet och arbetar på en lösning.

#### <a name="main-page"></a>Huvudsida

Huvudsidan för Storm-Användargränssnittet innehåller följande information:

* **Översikt över kluster**: grundläggande information om Storm-kluster.

* **Översikt över topologi**: en lista över topologier som körs. Använd länkarna i det här avsnittet om du vill visa mer information om specifika topologier.

* **Översikt över handledarens**: Information om Storm-administratören.

* **Nimbus configuration**: Nimbus-konfiguration för klustret.

#### <a name="topology-summary"></a>Topologi sammanfattning

Att välja en länk från den **Topology summary** avsnitt visar följande information om topologin:

* **Översikt över topologi**: grundläggande information om topologin.

* **Topologi åtgärder**: hanteringsåtgärder som du kan utföra för topologin.

  * **Aktivera**: återupptar bearbetningen av en inaktiverad topologi.

  * **Inaktivera**: pausar en topologi som körs.

  * **Balansera**: justerar topologins parallellitet. Du bör balansera om topologier som körs när du har ändrat antalet noder i klustret. Detta gör att topologin kan justera parallelliteten och kompensera för ökar eller minskar antalet noder i klustret.

      Mer information finns i [förstå parallellitet i en Storm-topologi (http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

  * **Avsluta**: avslutar en Storm-topologi efter en angiven tidsgräns.

* **Topology stats**: statistik om topologin. Länkarna i den **fönstret** kolumnen för att ange tidsperioden för de återstående posterna på sidan.

* **Spouts**: kanaler som används av topologin. Använd länkarna i det här avsnittet om du vill visa mer information om specifika kanaler.

* **Bolts**: bultar som används av topologin. Använd länkarna i det här avsnittet om du vill visa mer information om specifika bultar.

* **Topologi configuration**: konfigurationen av den valda topologin.

#### <a name="spout-and-bolt-summary"></a>Kanal och bult sammanfattning

Att välja en kanal från den **Spouts** eller **Bolts** avsnitt visar följande information om det markerade objektet:

* **Sammanfattning**: grundläggande information om den kanal eller en bult.

* **Kanal/bult stats**: statistik om den kanal eller en bult. Länkarna i den **fönstret** kolumnen för att ange tidsperioden för de återstående posterna på sidan.

* **Input stats** (endast bultar): Information om de inkommande dataströmmar som används av bulten.

* **Utdata stats**: Information om strömmar orsakat av detta kanal eller bultar.

* **Executors**: Information om instanser av den kanal eller en bult. Välj den **Port** post för en specifik utförare att visa en logg över diagnostikinformation produceras för den här instansen.

* **Fel**: felinformation för den här kanalen eller bultar.

## <a name="hdinsight-tools-for-visual-studio"></a>HDInsight Tools för Visual Studio

HDInsight-verktyg kan användas för att skicka eller hybrid C#-topologier till ditt Storm-kluster. Följande steg använder ett exempelprogram. Information om hur du skapar egna topologier med HDInsight Tools finns [utveckla C#-topologier med HDInsight Tools för Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Använd följande steg när du distribuerar ett exempel på till ditt Storm på HDInsight-kluster och sedan visa och hantera topologin.

1. Om du inte redan har installerat den senaste versionen av HDInsight Tools för Visual Studio finns [komma igång med HDInsight Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Öppna Visual Studio, markera **filen** > **ny** > **projekt**.

3. I den **nytt projekt** dialogrutan Expandera **installerad** > **mallar**, och välj sedan **HDInsight**. Välj i listan över mallar **Storm exempel**. Ange ett namn för programmet längst ned i dialogrutan.

    ![Bild](./media/apache-storm-deploy-monitor-topology/sample.png)

4. I **Solution Explorer**, högerklicka på projektet och välj **skicka till Storm på HDInsight**.

   > [!NOTE]
   > Om du uppmanas ange inloggningsuppgifterna för din Azure-prenumeration. Om du har mer än en prenumeration kan du logga in på den tabell som innehåller ditt Storm på HDInsight-kluster.

5. Välj ditt Storm på HDInsight-kluster från de **Storm-kluster** listrutan och välj sedan **skicka**. Du kan övervaka om överföring har genomförts med den **utdata** fönster.

6. När topologin har skickats, den **Storm-topologier** för klustret ska visas. Välj topologi från listan för att visa information om topologin som körs.

    ![Övervakare för Visual studio](./media/apache-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > Du kan också visa **Storm-topologier** från **Server Explorer** genom att expandera **Azure** > **HDInsight**, och sedan Högerklicka på ett Storm på HDInsight-kluster och välja **visa Storm-topologier**.

    Markera formen för kanaler eller bultar att visa information om dessa komponenter. För varje vald öppnas ett nytt fönster.

   > [!NOTE]
   > Namnet på topologin är klassnamnet för topologin (i det här fallet `HelloWord`,) med en tidstämpel läggas till.

7. Från den **Topology Summary** väljer **Kill** att stoppa topologin.

   > [!NOTE]
   > Storm-topologier fortsätta köras förrän de har stoppats eller ta bort klustret.


## <a name="rest-api"></a>REST-API

Storm-Användargränssnittet är byggt på REST-API, så att du kan göra liknande hantering och övervakning av funktioner med hjälp av REST API. Du kan använda REST API för att skapa anpassade verktyg för att hantera och övervaka Storm-topologier.

Mer information finns i [Storm UI REST API](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). Följande information gäller med hjälp av REST-API med Apache Storm på HDInsight.

### <a name="base-uri"></a>Bas-URI

Bas-URI för REST-API på HDInsight-kluster är **https://&lt;klusternamn >.azurehdinsight.net/stormui/api/v1/**, där **klusternamn** är namnet på ditt Storm på HDInsight kluster.

### <a name="authentication"></a>Autentisering

REST API-begäranden måste använda **grundläggande autentisering**, så att du använder HDInsight-klustrets administratörsnamn och lösenord.

> [!NOTE]
> Eftersom grundläggande autentisering skickas i klartext, bör du **alltid** använder HTTPS för att skydda kommunikationen med klustret.

### <a name="return-values"></a>Returnera värden

Information som returneras från REST-API kan endast användas från i kluster eller virtuella datorer i samma virtuella nätverk med Azure som klustret. Exempelvis det fullständigt kvalificerade domännamnet (FQDN) returneras för Zookeeper-servrar är inte tillgänglig från Internet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar och övervakar topologier med hjälp av Storm-instrumentpanelen, Läs mer om hur du:

* [Utveckla C#-topologier med HDInsight Tools för Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

* [Utveckla Java-baserad topologier med Maven](apache-storm-develop-java-topology.md)

En lista över flera exempeltopologier finns [exempeltopologier för Storm på HDInsight](apache-storm-example-topology.md).

[hdinsight-dashboard]: ./media/apache-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/apache-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/apache-storm-deploy-monitor-topology/storm-ui-summary.png
