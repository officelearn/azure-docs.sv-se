---
title: Installera publicerade program - Datameer - Azure HDInsight | Microsoft Docs
description: Installera och använda Hadoop-programmet Datameer från tredje part.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 9eef1760b7cee3bbdf33122514669b38b0b4d9db
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400851"
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Installera programmet - Cask Data programmet plattform (CDAP)

Den här artikeln beskriver hur du installerar och kör den [CDAP](http://cask.co/products/cdap/) publicerade Hadoop-program på Azure HDInsight. En översikt över HDInsight-programplattformen och en lista över tillgängliga oberoende leverantör (ISV) publicerade program finns i [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md). Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

## <a name="about-cdap"></a>Om CDAP

Kan vara svårt att utveckla program i Hadoop.  Det finns ett stort och växande antal Hadoop-teknik tillägg som kan ta lite tid att integrera. Övervaka dataflöde och att samla in mått kan kräva att skapa en separat lösning.

### <a name="how-does-cdap-help"></a>Hur hjälper CDAP?

Cask Data programmet plattform (CDAP) är en integrering plattform för Stordata. Du kan fokusera på att bygga program i stället för på den underliggande infrastrukturen CDAP.

CDAP använder avancerade begrepp och abstraktioner som är vanlig för utvecklare. Dessa abstraktioner dölja svårigheter för internt system och uppmuntra återanvändning av lösningar.

Tillägget CDAP kallas [Cask Hydrator](http://cask.co/products/hydrator/) ger ett användargränssnitt för att utveckla och hantera data pipelines. En data-pipeline består av olika * plugin-program som utför uppgifter som datainsamling, transformation, analys och efter kör åtgärder.

Varje CDAP plugin-programmet har en väldefinierad gränssnitt så att utvärdera olika tekniker är bara en fråga för att ersätta ett plugin-program med en annan, utan att behöva touch resten av programmet.

CDAP *pipelines* ger en övergripande grafiska flödet av data i ditt program. Den här visualiseringen visar slutpunkt till slutpunkt-flödet av data från införandet via Datatransformationer och analyser, och slutligen lagra en externa data.

Följande exempel visar en data-pipeline en twitter-data i realtid och filtrerar bort vissa tweets baserat på fördefinierade villkor. Data pipeline omvandlar rådata tweet data och store-projekt att data till ett mer lättläst format sedan grupper tweets enligt en uppsättning värden och skriver resultatet till en HBase.

![CDAP pipeline](./media/hdinsight-apps-install-cask/pipeline.png)

Den här pipelinen för slutpunkt-till-slutpunkt skapas med den **Cask Hydrator UI**, med dess plugin gränssnitt och dra och släpp funktioner till formuläret anslutningar mellan varje steg. Du kan isolera och ändra funktionen för varje plugin-programmet oberoende av varandra. Med CDAP kan liknande pipelines vara inbyggda och godkänts i timmar. I den vanliga Hadoop-världen, kan det ta flera dagar att konstruera sådana lösningar.

CDAP innehåller också ett tillägg som kallas [Cask spåraren](http://cask.co/products/tracker/) att visuellt spårningsdata som flödar genom programmet. Lägger till cask spåraren *datastyrning* i systemet så att datatillgångar hanteras formellt i hela programmet. Du kan spåra varje datapunkt härkomst, samla in relevanta mått och granskningslogg data under hela processen.

Här är en illustration av hur data flödar i pipeline för ovan:

![CDAP spåraren](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>Förutsättningar

Om du vill installera den här appen på en ny HDInsight-kluster eller ett befintligt kluster, måste du ha följande konfiguration:

* Klustret nivå: Standard
* Kluster-typ: HBase
* Kluster av version: 3.4, 3.5

## <a name="install-the-cdap-published-application"></a>Installera CDAP publicerat program

Stegvisa instruktioner om hur du installerar det här och andra tillgängliga ISV-program läsa [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Starta CDAP

1. Efter installationen kan starta CDAP från ditt kluster i Azure-portalen genom att gå till den **inställningar** rutan, sedan välja **program** under den **allmänna** kategori. Fönstret installerade appar visar en lista över alla installerade program.

    ![Installerade CDAP app](./media/hdinsight-apps-install-cask/cdap-app.png)

2. När du väljer CDAP visas en länk till webbsidan och HTTP-slutpunkten och även till sökvägen för SSH-slutpunkten. Välj länken för WEBBSIDAN.

3. När du uppmanas, anger du dina administratörsautentiseringsuppgifter för klustret.

    ![Autentisering](./media/hdinsight-apps-install-cask/auth.png)

4. Efter inloggningen finns Cask CDAP GUI-startsidan.

    ![Cask GUI-startsida](./media/hdinsight-apps-install-cask/gui.png)

5. Om du vill utforska gränssnittet CDAP klickar du på den **Cask marknaden** menyn länk på sidan.

    ![Cask marknaden länk](./media/hdinsight-apps-install-cask/cask-market.png)

6. Välj **Access loggen exempel** från listan.

    ![Access Log-exempel](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Klicka på **belastningen** att bekräfta.

    ![Klicka på Läs in](./media/hdinsight-apps-install-cask/market-load.png)

8. En vy över inkluderade exempeldata visas. Välj **nästa**.

    ![Åtkomst till loggen exempel - visa Data](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Välj **dataströmmen** som mål-typ, ange ett namn på målet och välj sedan **Slutför**.

    ![Åtkomst till loggen exempel - Välj målserver](./media/hdinsight-apps-install-cask/market-destination.png)

10. När datapack har läst in, markera **visa information om strömmen**.

    ![Datapack som laddats upp](./media/hdinsight-apps-install-cask/market-view-details.png)

11. Välj för att aktivera metadata för namnområdet **aktivera** fliken användning på sidan Åtkomstlogg information.

    ![Access loggen exempel - inlästa: Aktivera metadata](./media/hdinsight-apps-install-cask/log-loaded.png)

12. När metadata är aktiverad, visas ett diagram som visar granskningsinformation för meddelandet.

    ![Åtkomst till exempel Log - Metadata som aktiverats](./media/hdinsight-apps-install-cask/log-metadata.png)

13. Om du vill utforska loggdata, Välj den **utforska** ikon på sidan.

    ![Åtkomst till loggen exempel – utforska](./media/hdinsight-apps-install-cask/log-explore.png)

14. Du ser en exempelfråga SQL. Du kan ändra den som önskad och sedan väljer **kör**.

    ![Åtkomst till loggen exempel – utforska datamängd med en fråga](./media/hdinsight-apps-install-cask/log-query.png)

15. När frågan har slutförts, markerar du den **visa** ikon under kolumnen åtgärder.

    ![Åtkomst till loggen exempel - frågan för systemvyer slutförts](./media/hdinsight-apps-install-cask/log-query-view.png)

16. Du kan se resultatet av frågan.

    ![Åtkomst till loggen exempel - frågeresultat](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>Nästa steg

* [Cask dokumentationen](http://cask.co/resources/documentation/).
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): information om hur du definierar HDInsight-program.
* [Anpassa Linux-baserat HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): Lär dig hur du använder skriptåtgärd till att installera fler program.
* [Använd tom edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Lär dig hur du använder en tom kantnod för åtkomst till HDInsight-kluster och för att testa och värd för HDInsight-program.
