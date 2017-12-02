---
title: "Konfigurera principer för Hive i HDInsight med domänanslutna - Azure | Microsoft Docs"
description: "Läs mer ..."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3fade1e5-c2e1-4ad5-b371-f95caea23f6d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 812acea414096880c2b80958cb7c6f410f0d9c98
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="configure-hive-policies-in-domain-joined-hdinsight"></a>Konfigurera principer för Hive i HDInsight-domänansluten
Ta reda på mer om hur du konfigurerar Apache Ranger-principer för Hive. I den här artikeln skapar du två Ranger-principer för att begränsa åtkomsten till hivesampletable. Hivesampletable medföljer HDInsight-kluster. När du har konfigurerat principerna kan du använda Excel och ODBC-drivrutinen för att ansluta till Hive-tabeller i HDInsight.

## <a name="prerequisites"></a>Krav
* Ett domänanslutet HDInsight-kluster. Se [Konfigurera domänanslutna HDInsight-kluster](apache-domain-joined-configure.md).
* En arbetsstation med Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, fristående Excel 2013 eller Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Anslut till Apache Ranger Admin-gränssnittet
**Så här ansluter du till Ranger Admin-gränssnittet**

1. Anslut till Ranger Admin-gränssnittet från en webbläsare. Webbadressen är https://&lt;ClusterName>.azurehdinsight.net/Ranger/.

   > [!NOTE]
   > Ranger använder andra autentiseringsuppgifter än Hadoop-kluster. Om du vill förhindra att webbläsare använder cachade Hadoop-autentiseringsuppgifter använder du ett nytt InPrivate-fönster för att ansluta till Ranger Admin-gränssnittet.
   >
   >
2. Logga in med klusteradministratörens domänanvändarnamn och lösenord:

    ![Startsida för HDInsight domänanslutet Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    För närvarande fungerar Ranger bara med Yarn och Hive.

## <a name="create-domain-users"></a>Skapa domänanvändare
I [Konfigurera domänanslutna HDInsight-kluster](apache-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad) har du skapat hiveruser1 och hiveuser2. Du använder dessa två användarkonton i den här lektionen.

## <a name="create-ranger-policies"></a>Skapa Ranger-principer
I det här avsnittet skapar du två Ranger-principer för att komma åt hivesampletable. Du kan ge select-behörighet för olika uppsättningar med kolumner. Båda användarna skapades i [Konfigurera domänanslutna HDInsight-kluster](apache-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad).  I nästa avsnitt ska du testa två principer i Excel.

**Skapa Ranger-principer**

1. Öppna Ranger Admin-gränssnittet. Se [Anslut till Apache Ranger Admin-gränssnittet](#connect-to-apache-ranager-admin-ui).
2. Klicka på **&lt;Klusternamn>_hive** under **Hive**. Du bör se två förkonfigurerade principer.
3. Klicka på **Lägg till ny princip** och ange sedan följande värden:

   * Principnamn: read-hivesampletable-all
   * Hive-database: standard
   * tabell: hivesampletable
   * Hive-kolumn: *
   * Välj användare: hiveuser1
   * Behörigheter: select

     ![Konfigurera Ranger Hive-princip för domänansluten HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]
     > Om en domänanvändare inte är ifylld i Välj användare, vänta en stund för att Ranger ska synkronisera med AAD.
     >
     >
4. Klicka på **Lägg till** för att spara principen.
5. Upprepa de två sista stegen för att skapa en annan princip med följande egenskaper:

   * Principnamn: read-hivesampletable-devicemake
   * Hive-database: standard
   * tabell: hivesampletable
   * Hive-kolumn: clientid, devicemake
   * Välj användare: hiveuser2
   * Behörigheter: select

## <a name="create-hive-odbc-data-source"></a>Skapa Hive ODBC-datakälla
Du hittar anvisningarna i [Skapa Hive ODBC-datakällan](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Egenskap  |Beskrivning |
 | --- | --- |
 | Namn på datakälla | Namnge din datakälla |
 | Värd | Ange &lt;HDInsightClusterName>.azurehdinsight.net. Till exempel myHDICluster.azurehdinsight.net |
 | Port | Använd **443**. (Den här porten har ändrats från 563 till 443.) |
 | Databas | Använd **Standard**. |
 | Hive-servertyp | Välj **Hive Server 2** |
 | Mekanism | Välj **Azure HDInsight-tjänst** |
 | HTTP-sökväg | Lämna tomt. |
 | Användarnamn | Ange hiveuser1@contoso158.onmicrosoft.com. Uppdatera domännamnet om det är olika. |
 | Lösenord | Ange lösenordet för hiveuser1. |

Se till att klicka på **Test** innan du sparar datakällan.

## <a name="import-data-into-excel-from-hdinsight"></a>Importera data till Excel från HDInsight
I det sista avsnittet konfigurerade du två principer.  hiveuser1 har select-behörighet för alla kolumner och hiveuser2 har select-behörighet på två kolumner. I det här avsnittet personifierar du de två användarna för att importera data till Excel.

1. Öppna en ny eller befintlig arbetsbok i Excel.
2. På fliken **Data** klickar du på **From Other Data Sources** (Från andra datakällor) och sedan klickar du på **Från guiden Dataanslutning** för att starta den **guiden Dataanslutning**.

    ![Öppna guiden Dataanslutning][img-hdi-simbahiveodbc.excel.dataconnection]
3. Välj **ODBC DSN** som datakälla och klicka sedan på **Nästa**.
4. Från ODBC-datakällor väljer du datakällsnamnet som du skapade i det föregående steget och sedan klickar du på **Nästa**.
5. Ange lösenordet för klustret i guiden igen och klicka sedan på **OK**. Vänta tills dialogrutan **Markera databas och tabell** öppnas. Det kan ta några sekunder.
6. Välj **hivesampletable** och klicka sedan på **Nästa**.
7. Klicka på **Slutför**.
8. I dialogrutan **Importera data** kan du ändra eller specificera frågan. Gör det genom att klicka på **Egenskaper**. Det kan ta några sekunder.
9. Klicka på fliken **Definition**. Kommandotexten är:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Enligt Ranger-principerna du definierade har hiveuser1 select-behörighet för alla kolumner.  Så den här frågan fungerar med autentiseringsuppgifterna för hiveuser1, men frågan fungerar inte med autentiseringsuppgifterna för hiveuser2.

   ![Anslutningsegenskaper][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Klicka på **OK** för att stänga dialogrutan Anslutningsegenskaper.
11. Klicka på **OK** för att stänga dialogrutan **Importera data**.  
12. Ange lösenordet för hiveuser1 igen och klicka sedan på **OK**. Det tar några sekunder innan data har importerats till Excel. När det är klart bör du se 11 kolumner med data.

Om du vill testa den andra principen (read-hivesampletable-devicemake) du skapade i det sista avsnittet

1. Lägg till ett nytt kalkylblad i Excel.
2. Följ föregående procedur för att importera data.  Den enda ändringen du gör är att använda autentiseringsuppgifterna för hiveuser2 i stället för autentiseringsuppgifterna för hiveuser1. Detta fungerar inte eftersom hiveuser2 endast har behörighet att visa två kolumner. Du bör se följande fel:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Följ samma procedur för att importera data. Den här gången använder du autentiseringsuppgifterna för hiveuser2 och ändrar också select-uttrycket från:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    till:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    När det är klart bör du se två kolumner med importerade data.

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett domänanslutet HDInsight-kluster kan du läsa i [Konfigurera domänanslutna HDInsight-kluster](apache-domain-joined-configure.md).
* Om du vill hantera ett domänanslutet HDInsight-kluster kan du läsa i [Hantera domänanslutna HDInsight-kluster](apache-domain-joined-manage.md).
* För att köra Hive-frågor med SSH på domänanslutna HDInsight-kluster, se [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Om du vill ansluta Hive med Hive JDBC kan du läsa i [Anslut till Hive på Azure HDInsight med Hive JDBC-drivrutin](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Om du vill ansluta Excel till Hadoop med Hive ODBC kan du läsa i [Anslut Excel till Hadoop med Microsoft Hive ODBC-drivrutin](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Om du vill ansluta Excel till Hadoop med Power Query kan du läsa i [Anslut Excel till Hadoop med hjälp av Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
