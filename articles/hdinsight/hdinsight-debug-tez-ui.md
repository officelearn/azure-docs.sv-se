---
title: Använda Tez-Gränssnittet med Windows-baserade HDInsight - Azure | Microsoft Docs
description: Lär dig hur du använder Tez-UI för att felsöka Tez-jobb på Windows-baserade HDInsight HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: a55bccb9-7c32-4ff2-b654-213a2354bd5c
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 4201fb76ef9b0e711fd48972db86c356d72e6671
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406515"
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Använd Tez-UI för att felsöka Tez-jobb på Windows-baserade HDInsight
Tez UI kan användas för att felsöka Hive-jobb som använder Tez som motorn för körning. Tez UI visualizes jobbet som ett diagram över anslutna objekt kan detaljer om varje objekt och hämta statistik och loggningsinformation.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Windows. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Förutsättningar
* Ett Windows-baserade HDInsight-kluster. Anvisningar om hur du skapar ett nytt kluster finns [komma igång med Windows-baserade HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > Tez UI är bara tillgänglig på Windows-baserade HDInsight-kluster som skapas efter den 8 februari 2016.
  >
  >
* En Windows-baserade fjärrskrivbordsklienten.

## <a name="understanding-tez"></a>Förstå Tez
Tez är ett utökningsbart ramverk för databearbetning i Hadoop och ger högre hastigheter än traditionella MapReduce-bearbetning. Du kan aktivera Tez genom att inkludera följande text som en del av en Hive-fråga:

    set hive.execution.engine=tez;

Tez skapar en dirigeras acykliska diagram (DAG) som beskriver ordningen för körningen av åtgärder som krävs av jobbet. Enskilda åtgärder kallas formhörnen och köra en del av en övergripande jobbet. Faktiska körningen av det arbete som beskrivs av en nod kallas för en aktivitet och kan distribueras över flera noder i klustret.

### <a name="understanding-the-tez-ui"></a>Förstå Tez-Gränssnittet
Tez UI är en webbsida innehåller information om processer som använder Tez. Den kan erbjuda användbar information i följande scenarier:

* Övervakning tidskrävande processer, visa förloppet för kartan och minska uppgifter.
* Analysera historiska data för lyckade eller misslyckade processer att lära dig hur bearbetning kan förbättras eller orsaken till felet.

## <a name="generate-a-dag"></a>Generera en DAG
Tez UI innehåller data om ett jobb som använder Tez-motorn körs eller har körts tidigare. Enkel Hive-frågor kan vanligtvis lösas utan att använda Tez. Mer komplexa frågor som gör filtrering, gruppering, sortering, kopplingar, etc. kräver Tez.

Använd följande steg för att köra en Hive-fråga som använder Tez.

1. I en webbläsare, navigerar du till https://CLUSTERNAME.azurehdinsight.net, där **KLUSTERNAMN** är namnet på ditt HDInsight-kluster.
2. På menyn överst på sidan, Välj den **Hive-redigeraren**. En sida med följande exempelfråga visas.

        Select * from hivesampletable

    Radera exempelfråga och Ersätt den med följande.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Välj den **skicka** knappen. Den **jobbet Session** avsnittet längst ned på sidan visar status för frågan. När statusen ändras till **slutförd**, Välj den **visa information** länken om du vill visa resultatet. Den **Jobbutdata** bör likna följande:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Använda Tez-Gränssnittet
> [!NOTE]
> Tez UI är endast tillgängligt från skrivbordet head klusternoder, så du måste använda Fjärrskrivbord för att ansluta till huvudnoderna.
>
>

1. Från den [Azure-portalen](https://portal.azure.com), Välj ditt HDInsight-kluster. Upp i bladet HDInsight, Välj den **fjärrskrivbord** ikon. Den här länken visar bladet remote desktop

    ![Ikon för Remote desktop](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. Remote Desktop-bladet välj **Anslut** att ansluta till klustrets huvudnod. Vid uppmaning används klustret Remote Desktop-användarnamn och lösenord för att autentisera anslutningen.

    ![Anslutningen till fjärrskrivbord ikon](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Om du inte har aktiverat Fjärrskrivbord-anslutningen, ange ett användarnamn, lösenord och upphör att gälla och välj sedan **aktivera** att aktivera Fjärrskrivbord. När den har aktiverats, kan du använda de här stegen för att ansluta.
   >
   >
3. När du är ansluten, öppna Internet Explorer på fjärrskrivbordet, väljer du kugghjulsikonen i övre högra hörnet i webbläsaren och välj sedan **Kompatibilitetsvyinställningarna**.
4. Längst ned i **Kompatibilitetsvyinställningarna**, avmarkerar du kryssrutan för **visa intranätplatser i Kompatibilitetsvy** och **Använd Microsoft kompatibilitetslista**, och välj sedan **Stäng**.
5. I Internet Explorer, bläddra till http://headnodehost:8188/tezui/#/. Detta visar Tez UI

    ![Tez-Gränssnittet](./media/hdinsight-debug-tez-ui/tezui.png)

    När Tez UI läses in, visas en lista över dag som för närvarande körs eller har körts på klustret. Standardvyn innehåller DAG Name, -Id, runtimenamn, Skickat, Status, starttid, sluttid, varaktighet, program-ID och kön. Fler kolumner läggas till med hjälp av växeln-ikonen längst till höger på sidan.

    Om du har en enda post, gäller den fråga som du körde i föregående avsnitt. Om du har flera poster kan du söka genom att ange sökvillkor i fälten i dag och sedan klicka på **RETUR**.
6. Välj den **Dag Name** för den senaste DAG-posten. Den här länken visar information om DAG och alternativet för att hämta en zip JSON-filer som innehåller information om DAG.

    ![DAG-information](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Ovanför den **DAG information** är flera länkar som kan användas för att visa information om DAG.

   * **DAG räknare** visar informationen från prestandaräknarna för denna DAG.
   * **Grafisk vy** visar en grafisk representation av den här DAG.
   * **Alla formhörnen** visar en lista över formhörnen i denna DAG.
   * **Alla aktiviteter** visar en lista över aktiviteter för alla hörn i denna DAG.
   * **Alla TaskAttempts** visar information om försöker att köra uppgifter för denna DAG.

     > [!NOTE]
     > Om du bläddrar i kolumnen visa för formhörnen, uppgifter och TaskAttempts, Observera att det finns länkar för att visa **räknare** och **visa eller hämta loggar** för varje rad.
     >
     >

     Om det uppstod ett fel med jobbet, visas DAG information statusen misslyckades, tillsammans med länkar till information om om aktiviteten. Diagnostikinformationen visas under DAG-information.
8. Välj **grafisk vy**. Visar en grafisk representation av gruppen för Databastillgänglighet. Du kan placera muspekaren över varje nod i vyn för att visa information om den.

    ![Grafisk vy](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Klicka på en brytpunkt läser in den **Vertex information** för objektet. Klicka på den **kartan 1** vertex att visa detaljer för det här objektet. Välj **Bekräfta** bekräfta navigeringen.

    ![Vertex information](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Observera att du nu har länkarna överst på sidan som är relaterade till formhörnen och uppgifter.

    > [!NOTE]
    > Du kan också komma fram till den här sidan genom att gå tillbaka till **DAG information**, välja **Vertex information**, och sedan välja den **kartan 1** hörn.
    >
    >

    * **Vertex räknare** visar räknarinformation för den här hörn.
    * **Uppgifter** uppgifter för den här vertex visas.
    * **Uppgift försök** visar information om försök att köra uppgifter för den här hörn.
    * **Datakällor & egenskaperna** visar datakällor och egenskaperna för den här hörn.

      > [!NOTE]
      > Med den föregående menyn rulla som Visa kolumnen för aktiviteter, aktivitet försök källor och Sinks__ visa länkar till mer information för varje objekt.
      >
      >
11. Välj **uppgifter**, och välj sedan de objekt med namnet **00_000000**. Den här länken visar **aktivitetsinformation** för den här uppgiften. Du kan visa från den här skärmen **aktivitet räknare** och **aktivitet försök**.

    ![Uppgiftsinformation](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du använder vyn Tez, lär du dig mer om [med hjälp av Hive i HDInsight](hadoop/hdinsight-use-hive.md).

Mer teknisk information om Tez finns i [Tez sidan vid Hortonworks](http://hortonworks.com/hadoop/tez/).
