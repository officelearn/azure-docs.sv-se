---
title: Använda Tez-Användargränssnittet med Windows-baserade HDInsight - Azure
description: Lär dig hur du använder Tez UI för att felsöka Tez-jobb på Windows-baserade HDInsight HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: f54cc60f9490b8a5ca1872a290c3895ea8b0c5e4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590909"
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Använda Tez UI för att felsöka Tez-jobb på Windows-baserade HDInsight
Tez UI kan användas för att felsöka Hive-jobb som använder Tez som motorn för körning. Tez UI visualisera jobbet som ett diagram över anslutna objekt kan öka detaljnivån i varje objekt och hämta statistik och loggningsinformation.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Windows. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Förutsättningar
* Ett Windows-baserade HDInsight-kluster. Anvisningar om hur du skapar ett nytt kluster finns i [komma igång med Windows-baserade HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > Tez UI är endast tillgänglig på Windows-baserade HDInsight-kluster som skapats efter den 8 februari 2016.
  >
  >
* En Windows-baserade fjärrskrivbord-klienten.

## <a name="understanding-tez"></a>Förstå Tez
Tez är ett utökningsbart ramverk för att bearbeta i Hadoop och ger högre hastigheter än med traditionella MapReduce-bearbetning. Du kan aktivera Tez genom att inkludera följande text som en del av en Hive-fråga:

    set hive.execution.engine=tez;

Tez skapar en dirigeras Acyklisk graf (DAG) som beskriver ordningen för körningen av åtgärder som krävs för jobbet. Enskilda åtgärder kallas hörn och köra en del av det övergripande jobbet. Den faktiska körningen av arbetet som beskrivs av en brytpunkt kallas en uppgift och kan distribueras över flera noder i klustret.

### <a name="understanding-the-tez-ui"></a>Förstå Tez-Användargränssnittet
Tez UI är en webbsida innehåller information om processer som använder Tez. Det kan erbjuda användbar information i följande scenarier:

* Övervaka tidskrävande bearbetar, visa förloppet för kartan och minska uppgifter.
* Analys av historiska data för lyckade eller misslyckade processer för att lära dig hur vi kan förbättra bearbetning eller varför den misslyckades.

## <a name="generate-a-dag"></a>Skapa en grupp för Databastillgänglighet
Tez-UI innehåller data om ett jobb som använder Tez-motorn körs eller har körts tidigare. Enkel Hive-frågor kan vanligtvis lösas utan att använda Tez. Mer komplexa frågor som gör filtrering, gruppering, sortering, kopplingar och annat kräver Tez.

Använd följande steg för att köra en Hive-fråga som använder Tez.

1. I en webbläsare, navigerar du till https://CLUSTERNAME.azurehdinsight.net, där **CLUSTERNAME** är namnet på ditt HDInsight-kluster.
2. På menyn överst på sidan, väljer den **Hive-redigeraren**. Då visas en sida med följande exempelfråga.

        Select * from hivesampletable

    Radera exempelfrågan och Ersätt den med följande.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Välj den **skicka** knappen. Den **jobbet Session** avsnittet längst ned på sidan visar status för frågan. När status ändras till **slutförd**väljer den **visa information om** länk för att se resultaten. Den **Jobbutdata** bör likna följande:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Använda Tez-Användargränssnittet
> [!NOTE]
> Tez UI är endast tillgänglig från skrivbordet för klustrets huvudnoder, så du måste använda Fjärrskrivbord för att ansluta till huvudnoderna.
>
>

1. Från den [Azure-portalen](https://portal.azure.com), Välj ditt HDInsight-kluster. Högst upp på bladet HDInsight, Välj den **fjärrskrivbord** ikon. Den här länken visar bladet för remote desktop

    ![Remote skrivbordsikonen](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. Remote Desktop-bladet väljer du **Connect** att ansluta till klustrets huvudnod. När du uppmanas, använder du kluster Remote Desktop-användarnamn och lösenord för att autentisera anslutningen.

    ![Anslutningsikon för fjärrskrivbord](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Om du inte har aktiverat Fjärrskrivbord-anslutningen, ange ett användarnamn, lösenord och upphör att gälla och välj sedan **aktivera** att aktivera fjärrskrivbordet. När den har aktiverats, kan du använda de här stegen för att ansluta.
   >
   >
3. När du är ansluten, öppna Internet Explorer på fjärrskrivbordet, väljer du kugghjulsikonen uppe till höger i webbläsaren och välj sedan **Kompatibilitetsvyinställningarna**.
4. Längst ned i **Kompatibilitetsvyinställningarna**, avmarkerar du kryssrutan för **visa intranätplatser i Kompatibilitetsvy** och **Använd Microsoft-kompatibilitetslista**, och Välj sedan **Stäng**.
5. I Internet Explorer bläddrar du till http://headnodehost:8188/tezui/#/. Detta visar Tez UI

    ![Tez-Användargränssnittet](./media/hdinsight-debug-tez-ui/tezui.png)

    När Tez UI har lästs in visas en lista över dag-enheter som kör eller har körts på klustret. Standardvyn innehåller DAG Name, Id, runtimenamn, Skickat, Status, starttid, sluttid, varaktighet, program-ID och kö. Fler kolumner läggas använder kugghjulsikonen längst till höger på sidan.

    Om du har bara en post, är det för den fråga som du körde i föregående avsnitt. Om du har flera poster kan du söka genom att ange sökvillkor i fälten i dag och sedan trycker på **RETUR**.
6. Välj den **Dag Name** för den senaste posten i DAG. Den här länken visar information om gruppen för Databastillgänglighet, samt alternativet för att ladda ned en zipfil av JSON-filer som innehåller information om gruppen för Databastillgänglighet.

    ![Information om DAG](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Ovanför den **DAG information** finns flera länkar som kan användas för att visa information om gruppen för Databastillgänglighet.

   * **DAG räknare** visar informationen från prestandaräknarna för denna DAG.
   * **Grafisk vy** visar en grafisk representation av den här DAG.
   * **Alla hörn** visar en lista över hörn i den här DAG.
   * **Alla aktiviteter** visar en lista över aktiviteter för alla hörn i den här DAG.
   * **Alla TaskAttempts** visar information om försöker att köra uppgifter för denna DAG.

     > [!NOTE]
     > Om du rullar åt kolumnen visa för hörn, uppgifter och TaskAttempts Observera att det finns länkar för att visa **räknare** och **visa eller ladda ned loggar** för varje rad.
     >
     >

     Om det uppstod ett fel med jobbet, visas statusen misslyckades, samt länkar till information om aktiviteten i DAG-information. Diagnostikinformation visas under DAG-information.
8. Välj **grafisk vy**. Då visas en grafisk representation av gruppen för Databastillgänglighet. Du kan placera markören över varje brytpunkt som vy för att visa information om den.

    ![Grafisk vy](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. När du klickar på ett hörn läser in den **hörn information** för objektet. Klicka på den **kartan 1** hörn att visa information för det här objektet. Välj **Bekräfta** att bekräfta navigeringen.

    ![Information om hörn](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Observera att du nu har länkar överst på sidan som är relaterade till hörn och uppgifter.

    > [!NOTE]
    > Du kan också komma fram till den här sidan genom att gå tillbaka till **DAG information**, välja **hörn information**, och sedan välja den **kartan 1** hörn.
    >
    >

    * **Hörn räknare** visar informationen för den här brytpunkt.
    * **Uppgifter** visar uppgifter för den här brytpunkt.
    * **Uppgift försök** visar information om försök att köra uppgifter för den här brytpunkt.
    * **Datakällor & egenskaperna** visar datakällor och egenskaperna för den här brytpunkt.

      > [!NOTE]
      > Med föregående meny rulla som kolumnen visa för uppgifter, uppgift försök och källor och Sinks__ att visa länkar till mer information för varje objekt.
      >
      >
11. Välj **uppgifter**, och välj sedan den objekt med namnet **00_000000**. Visar den här länken **aktivitetsinformation** för den här uppgiften. Den här skärmen, du kan se **uppgift räknare** och **uppgift försök**.

    ![Uppgiftsinformation](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du använder vyn Tez, Läs mer om [med hjälp av Hive på HDInsight](hadoop/hdinsight-use-hive.md).

Mer detaljerad teknisk information om Tez finns i den [Tez-sidan på Hortonworks](http://hortonworks.com/hadoop/tez/).
