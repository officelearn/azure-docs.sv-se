---
title: Installera publicerad program – Datameer - Azure HDInsight
description: Installera och använda Hadoop-program Datameer från tredje part.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 715e536d7356a4e37f512027a23236b1fd37cbac
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651307"
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Installera publicerad program – Cask Data Application Platform (CDAP)

Den här artikeln beskrivs hur du installerar och kör den [CDAP](https://cask.co/products/cdap/) publicerade [Apache Hadoop](https://hadoop.apache.org/) på Azure HDInsight. En översikt över HDInsight-programplattform och en lista över tillgängliga oberoende programvaruleverantör (ISV) publicerade program finns i [installera Apache Hadoop-program för tredje parts](hdinsight-apps-install-applications.md). Anvisningar om hur du installerar ett eget program finns i [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).

## <a name="about-cdap"></a>Om CDAP

Kan vara svårt att utveckla program i Hadoop.  Det finns ett stort och växande antal Hadoop-teknik tillägg, vilket kan ta lite tid att integrera. Övervakning av dataflöde och samla in mått kan kräva att skapa en separat lösning.

### <a name="how-does-cdap-help"></a>Hur hjälper CDAP?

Cask Data Application Platform (CDAP) är en integreringsplattform för Stordata. CDAP kan du fokusera på att bygga program i stället för den underliggande infrastrukturen.

CDAP använder avancerade begrepp och abstraktioner som är vanlig för utvecklare. Dessa abstraktioner dölja komplexiteten i interna system och uppmuntra återanvändning av lösningar.

Filtillägget CDAP kallas [Cask Hydrator](https://cask.co/products/hydrator/) innehåller ett användargränssnitt för att utveckla och hantera datapipelines. En datapipeline består av olika * plugin-program som utför uppgifter som datainsamling, transformation, analys och efter kör åtgärder.

Varje CDAP plugin-programmet har en väldefinierad gränssnitt så att utvärdera olika tekniker är bara ersätta ett plugin-program med en annan, utan att behöva röra resten av programmet.

CDAP *pipelines* ge ett övergripande grafiska flöde av data i ditt program. Den här visualiseringen visar slutpunkt till slutpunkt-flödet av data från inmatning via dataomvandlingar och analyser, och slutligen lagra en externa data.

Följande exempel visar en datapipeline matar in twitter-data i realtid och filtrerar ut några tweets som baseras på fördefinierade villkor. Datapipelinen omvandlar rådata tweet data och lagra projekt att data till ett mer lättläst format sedan grupperar tweets enligt en uppsättning värden och skriver resultatet till en HBase.

![CDAP pipeline](./media/hdinsight-apps-install-cask/pipeline.png)

Denna slutpunkt till slutpunkt-pipeline har skapats med den **Cask Hydrator UI**, med hjälp av dess plugin-programmet gränssnitt och dra och släpp funktioner till formuläret anslutningar mellan varje steg. Du kan isolera och ändra funktionen för varje plugin-programmet oberoende av varandra. Med hjälp av CDAP, kan liknande pipelines byggas och verifieras i timmar. I vanliga Hadoop-världen, kan det ta flera dagar att konstruera sådana lösningar.

CDAP innehåller också ett tillägg som kallas [Cask spåraren](https://cask.co/products/tracker/) att visuellt spårningsdata som den förs vidare via programmet. Cask spåraren lägger till *datastyrning* i systemet så att datatillgångar hanteras formellt i hela programmet. Du kan spåra varje datapunkt härkomst, samla in relevanta mått och granskningslogg data under hela processen.

Här är en illustration av hur data flödar i ovanstående pipelinen:

![CDAP spåraren](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>Förutsättningar

Om du vill installera den här appen på ett nytt HDInsight-kluster eller ett befintligt kluster, måste du ha följande konfiguration:

* Kluster-nivå: Standard
* Typ av kluster: HBase
* Klusterversion: 3.4, 3.5

## <a name="install-the-cdap-published-application"></a>Installera CDAP publicerad program

Stegvisa instruktioner om hur du installerar det här och andra tillgängliga ISV-program finns [installera Hadoop-program från tredje part](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Starta CDAP

1. Efter installationen kan starta CDAP från ditt kluster i Azure-portalen genom att gå till den **inställningar** och sedan välja **program** under den **Allmänt** kategori. Fönstret installerade appar visas alla installerade program.

    ![Installerade CDAP app](./media/hdinsight-apps-install-cask/cdap-app.png)

2. När du väljer CDAP, visas en länk till sidan och HTTP-slutpunkt, och även till sökvägen för SSH-slutpunkten. Välj länken för WEBBSIDAN.

3. När du uppmanas, anger du dina autentiseringsuppgifter som klustret.

    ![Autentisering](./media/hdinsight-apps-install-cask/auth.png)

4. När du har loggat in kan se du Cask CDAP GUI-startsidan.

    ![Cask GUI-startsida](./media/hdinsight-apps-install-cask/gui.png)

5. Om du vill utforska gränssnittet CDAP, klickar du på den **Cask marknaden** menyn länk på sidan.

    ![Cask marknaden länk](./media/hdinsight-apps-install-cask/cask-market.png)

6. Välj **åtkomst Loggexemplet** i listan.

    ![Åtkomst Loggexemplet](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Klicka på **belastningen** att bekräfta.

    ![Klicka på Läs in](./media/hdinsight-apps-install-cask/market-load.png)

8. En vy över inkluderade exempeldata visas. Välj **nästa**.

    ![Komma åt Log-exempel – visa Data](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Välj **Stream** som mål-typ, ange ett namn på målet och välj sedan **Slutför**.

    ![Komma åt Log-exempel – Välj målserver](./media/hdinsight-apps-install-cask/market-destination.png)

10. När datapack har läst in, väljer **Stream finns i detaljuppgifterna**.

    ![Datapack har laddats upp](./media/hdinsight-apps-install-cask/market-view-details.png)

11. Aktivera metadata för namnområdet **aktivera** på fliken användning på sidan med Åtkomstlogg.

    ![Åtkomst Loggexemplet - inlästa: Aktivera metadata](./media/hdinsight-apps-install-cask/log-loaded.png)

12. När metadata har aktiverats kan se du ett diagram som visar granskningsinformation för meddelandet.

    ![Få åtkomst till exempel Log - Metadata som aktiverats](./media/hdinsight-apps-install-cask/log-metadata.png)

13. Om du vill utforska loggdata, Välj den **utforska** ikonen på sidan.

    ![Komma åt Log exempel – utforska](./media/hdinsight-apps-install-cask/log-explore.png)

14. Du ser en exempelfråga SQL. Ändra det som önskad och sedan väljer **kör**.

    ![Komma åt Loggexemplet – utforska datauppsättningen med en fråga](./media/hdinsight-apps-install-cask/log-query.png)

15. När frågan har slutförts, väljer du den **visa** ikonen under kolumnen åtgärder.

    ![Åtkomst Loggexemplet - frågan för systemvyer har slutförts](./media/hdinsight-apps-install-cask/log-query-view.png)

16. Du ser resultatet av frågan.

    ![Åtkomst Loggexemplet - frågeresultat](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>Nästa steg

* [Cask dokumentation](https://cask.co/resources/documentation/).
* [Installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md): Lär dig hur du distribuerar ett Opublicerat HDInsight-program till HDInsight.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): Lär dig hur du publicerar dina anpassade HDInsight-program på Azure Marketplace.
* [MSDN: Installera ett HDInsight-program](https://msdn.microsoft.com/library/mt706515.aspx): Lär dig hur du definierar HDInsight-program.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md): Lär dig hur du använder skriptåtgärd för att installera fler program.
* [Använda tomma kantnoder i HDInsight](hdinsight-apps-use-edge-node.md): Lär dig hur du använder en tom edge-nod för att komma åt HDInsight-kluster och för testning och som är värd för HDInsight-program.
