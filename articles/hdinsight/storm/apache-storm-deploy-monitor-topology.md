---
title: Distribuera och hantera Apache Storm-topologier i Azure HDInsight
description: Lär dig mer om att distribuera, övervaka och hantera Apache Storm-topologier med hjälp av Storm-instrumentpanelen på Windows-baserade HDInsight. Använda Hadoop-verktyg för Visual Studio.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/01/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: ce39e3ffce0b7721bde84254c7e5a35ec28465dc
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583167"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Distribuera och hantera Apache Storm-topologier i Windows-baserade HDInsight

Den [Apache Storm](http://storm.apache.org/) instrumentpanel kan du enkelt distribuera och köra Apache Storm-topologier till dina HDInsight-kluster med hjälp av webbläsaren. Du kan också använda instrumentpanelen för att övervaka och hantera topologier som körs. Om du använder Visual Studio, tillhandahåller HDInsight Tools för Visual Studio liknande funktioner i Visual Studio.

Storm-instrumentpanelen och funktionerna som Storm i HDInsight-verktyg beroende Storm REST API, som kan användas för att skapa egna övervakning och lösningar för hantering.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett Storm på HDInsight-kluster som använder Windows som operativsystemet. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Information om att distribuera och hantera Storm-topologier med HDInsight-kluster som använder Linux finns i [distribuera och hantera Apache Storm-topologier på Linux-baserat HDInsight](apache-storm-deploy-monitor-topology-linux.md)

## <a name="prerequisites"></a>Förutsättningar

* **Apache Storm på HDInsight** – Se [Kom igång med Apache Storm på HDInsight](apache-storm-tutorial-get-started-linux.md) anvisningar om hur du skapar ett kluster.

* För den **Storm-instrumentpanelen**: en modern webbläsare som stöder HTML5.

* För **Visual Studio** – Azure SDK 2.5.1 eller senare och HDInsight Tools för Visual Studio. Se [kommer igång med HDInsight Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) att installera och konfigurera HDInsight-verktyg för Visual Studio.

    En av följande versioner av Visual Studio:

  * Visual Studio 2012 med uppdatering 4

  * Visual Studio 2013 med Update 4 eller Visual Studio Community 2013

  * Visual Studio 2015 (alla versioner)

  * Visual Studio 2017 (alla versioner)

## <a name="storm-dashboard"></a>Storm-instrumentpanelen

Storm-instrumentpanelen är en webbsida som är tillgängliga i ditt Storm-kluster. URL: en är **https://&lt;klusternamn >.azurehdinsight.net/**, där **clustername** är namnet på ditt Storm på HDInsight-kluster.

Högst upp på Storm-instrumentpanelen, Välj **skicka topologi**. Följ anvisningarna på sidan för att köra en exempeltopologi eller för att ladda upp och köra en topologi som du skapade.

![sidan skicka topologi][storm-dashboard-submit]

### <a name="storm-ui"></a>Storm UI

Storm-instrumentpanelen väljer du den **Storm-Användargränssnittet** länk. Detta visar information om klustret, förutom några topologier som körs.

![storm-användargränssnittet][storm-dashboard-ui]

> [!NOTE]
> Med vissa versioner av Internet Explorer kan du identifiera Storm-Användargränssnittet inte uppdateras när du har besökt det först. Det kan till exempel inte visa nya topologier du skickat, eller den kan visa en topologi som aktiv när du tidigare har inaktiverats. Microsoft är medvetna om problemet och arbetar på en lösning.

#### <a name="main-page"></a>Huvudsida

Huvudsidan för Storm-Användargränssnittet innehåller följande information:

* **Klustersammanfattning**: grundläggande information om Storm-kluster.

* **Sammanfattning av topologi**: en lista över topologier som körs. Använd länkarna i det här avsnittet om du vill visa mer information om specifika topologier.

* **Övervakaren sammanfattning**: Information om Storm-övervakaren.

* **Nimbus configuration**: Nimbus-konfigurationen för klustret.

#### <a name="topology-summary"></a>Sammanfattning av topologi

Att välja en länk från den **Topology summary** avsnittet visar följande information om topologin:

* **Sammanfattning av topologi**: grundläggande information om topologin.

* **Topologi åtgärder**: hanteringsåtgärder som du kan utföra för topologin.

  * **Aktivera**: återupptar bearbetningen av en inaktiverad topologi.

  * **Inaktivera**: pausar en topologi som körs.

  * **Balansera om**: justerar topologins parallellitet. Du bör balansera om topologier som körs när du har ändrat antalet noder i klustret. Detta gör att topologin kan justera parallelliteten och kompensera för ökat eller minskat antalet noder i klustret.

      Mer information finns i [förstå parallellitet i en Apache Storm-topologi](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

  * **Avsluta**: avslutar en Storm-topologi efter en angiven tidsgräns.

* **Topology stats**: statistik om topologin. Använda länkar i den **fönstret** kolumnen för att ange en mer specifik tidsram för de övriga objekten på sidan.

* **Spouts**: kanaler som används av topologin. Använd länkarna i det här avsnittet om du vill visa mer information om specifika kanaler.

* **Bolts**: bultar som används av topologin. Använd länkarna i det här avsnittet om du vill visa mer information om specifika bultar.

* **Topologikonfiguration**: konfigurationen av den valda topologin.

#### <a name="spout-and-bolt-summary"></a>Spout och bult sammanfattning

Att välja en kanal från den **Spouts** eller **Bolts** avsnitt visar följande information om det markerade objektet:

* **Översikt över komponenterna**: grundläggande information om den kanal eller en bult.

* **Spout/bult stats**: statistik om den kanal eller en bult. Använda länkar i den **fönstret** kolumnen för att ange en mer specifik tidsram för de övriga objekten på sidan.

* **Input stats** (endast bultar): Information om indataströmmar som används av bulten.

* **Utdata stats**: Information om de strömmar som av detta kanalen eller bultar.

* **Executors**: Information om instanser av den kanal eller en bult. Välj den **Port** post för en specifik executor att visa en logg över diagnostikinformation produceras för den här instansen.

* **Fel**: någon information om fel för den här kanalen eller bultar.

## <a name="hdinsight-tools-for-visual-studio"></a>HDInsight Tools för Visual Studio

Den [HDInsight Tools](https://azure.microsoft.com/resources/videos/hdinsight-tools-for-visual-studio/) kan användas för att skicka C# eller hybridtopologier till ditt Storm-kluster. Följande steg använder ett exempelprogram. Information om hur du skapar dina egna topologier med hjälp av HDInsight-verktyg finns i [utveckla C#-topologier med HDInsight Tools för Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Använd följande steg för att distribuera ett exempel till ditt Storm på HDInsight-kluster, visa och hantera topologin.

1. Om du inte redan har installerat den senaste versionen av HDInsight-verktyg för Visual Studio finns i [kommer igång med HDInsight Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Öppna Visual Studio, Välj **filen** > **New** > **projekt**.

3. I den **nytt projekt** dialogrutan Expandera **installerad** > **mallar**, och välj sedan **HDInsight**. Välj i listan över mallar **Storm-exempel**. Skriv ett namn för programmet längst ned i dialogrutan.

    ![image](./media/apache-storm-deploy-monitor-topology/sample.png)

4. I **Solution Explorer**, högerklicka på projektet och välj **skicka till Storm på HDInsight**.

   > [!NOTE]
   > Om du uppmanas, anger du inloggningsuppgifterna för din Azure-prenumeration. Om du har mer än en prenumeration kan du logga in på det som innehåller ditt Storm på HDInsight-kluster.

5. Välj ditt Storm på HDInsight-kluster från den **Storm-kluster** listrutan och välj sedan **skicka**. Du kan övervaka om överföringen har genomförts med den **utdata** fönster.

6. När topologin har skickats, den **Storm-topologier** för klustret ska visas. Välj topologi i listan att visa information om topologin som körs.

    ![Övervakare för Visual studio](./media/apache-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > Du kan också visa **Storm-topologier** från **Server Explorer** genom att expandera **Azure** > **HDInsight**, och sedan att högerklicka på ett Storm på HDInsight-kluster och välja **Zobrazit Topologie Stormu**.

    Markera formen för kanaler eller bultar att visa information om dessa komponenter. Ett nytt fönster öppnas för varje objekt har valts.

   > [!NOTE]
   > Namnet på topologin är klassnamnet för topologin (i det här fallet `HelloWord`,) med en tidsstämpel som bifogats.

7. Från den **Topology Summary** väljer **Kill** att stoppa topologin.

   > [!NOTE]
   > Storm-topologier fortsätter att köras tills de stoppas eller klustret tas bort.


## <a name="rest-api"></a>REST-API

Storm-Användargränssnittet är byggt på REST API, så att du kan utföra liknande övervaknings- och funktioner med hjälp av REST-API. Du kan använda REST API för att skapa anpassade verktyg för att hantera och övervaka Storm-topologier.

Mer information finns i [Apache Storm UI REST API](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). Följande information gäller med hjälp av REST-API med Apache Storm på HDInsight.

### <a name="base-uri"></a>Bas-URI

Bas-URI för REST-API i HDInsight-kluster är **https://&lt;klusternamn >.azurehdinsight.net/stormui/api/v1/**, där **clustername** är namnet på ditt Storm på HDInsight kluster.

### <a name="authentication"></a>Autentisering

REST API-begäranden måste använda **grundläggande autentisering**, så att du använder HDInsight-kluster databasadministratörens namn och lösenord.

> [!NOTE]
> Eftersom grundläggande autentisering skickas i klartext, bör du **alltid** använder HTTPS för att skydda kommunikationen med klustret.

### <a name="return-values"></a>Returvärden

Information som returneras från REST-API: et kan bara användas från inom klustret eller virtuella datorer i samma virtuella nätverk med Azure som klustret. Till exempel det fullständigt kvalificerade domännamnet (FQDN) returneras för [Apache ZooKeeper](https://zookeeper.apache.org/) servrar är inte tillgänglig från Internet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att distribuera och övervaka topologier med hjälp av Storm-instrumentpanelen, Lär dig hur du:

* [Utveckla C#-topologier med HDInsight Tools för Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

* [Utveckla Java-baserade topologier med Apache Maven](apache-storm-develop-java-topology.md)

En lista över flera exempeltopologier finns i [exempeltopologier för Apache Storm på HDInsight](apache-storm-example-topology.md).

[hdinsight-dashboard]: ./media/apache-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/apache-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/apache-storm-deploy-monitor-topology/storm-ui-summary.png
