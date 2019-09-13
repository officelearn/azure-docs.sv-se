---
title: Konfigurera Hive-principer i HDInsight med Enterprise Security Package – Azure
description: Lär dig hur du konfigurerar Apache Ranger-principer för Hive i en Azure HDInsight-tjänst med Enterprise Security Package.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b0213fc1a96b38b615cbd8b7b6374a6716b9f840
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918190"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Konfigurera Apache Hive-principer i HDInsight med Enterprise Security Package
Lär dig hur du konfigurerar Apache Ranger-principer för Apache Hive. I den här artikeln skapar du två Ranger-principer för att begränsa åtkomsten till hivesampletable. Hivesampletable medföljer HDInsight-kluster. När du har konfigurerat principerna kan du använda Excel och ODBC-drivrutinen för att ansluta till Hive-tabeller i HDInsight.

## <a name="prerequisites"></a>Förutsättningar
* Ett HDInsight-kluster med Enterprise Security Package. Se [Konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure.md).
* En arbetsstation med Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, fristående Excel 2013 eller Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Anslut till Apache Ranger Admin-gränssnittet
**Så här ansluter du till Ranger Admin-gränssnittet**

1. Anslut till Ranger Admin-gränssnittet från en webbläsare. Webbadressen är https://&lt;ClusterName>.azurehdinsight.net/Ranger/.

   > [!NOTE]  
   > Ranger använder andra autentiseringsuppgifter än Apache Hadoop kluster. Om du vill förhindra att webbläsare använder cachelagrade Hadoop-autentiseringsuppgifter använder du nytt InPrivate-webbläsarfönster för att ansluta till användar gränssnittet Ranger.

2. Logga in med klusteradministratörens domänanvändarnamn och lösenord:

    ![Start sida för HDInsight ESP Ranger](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    För närvarande fungerar Ranger bara med Yarn och Hive.

## <a name="create-domain-users"></a>Skapa domänanvändare
Information om hur du skapar hiveruser1 och hiveuser2 finns i [skapa ett HDInsight-kluster med ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp). Du använder de två användar kontona i den här artikeln.

## <a name="create-ranger-policies"></a>Skapa Ranger-principer
I det här avsnittet skapar du två Ranger-principer för att få åtkomst till hivesampletable. Du kan ge select-behörighet för olika uppsättningar med kolumner. Båda användarna skapades med att [skapa ett HDInsight-kluster med ESP](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp). I nästa avsnitt ska du testa två principer i Excel.

**Skapa Ranger-principer**

1. Öppna Ranger Admin-gränssnittet. Se Anslut till Apache Ranger admin-gränssnittet.
2. Klicka på **&lt;Klusternamn>_hive** under **Hive**. Du bör se två förkonfigurerade principer.
3. Klicka på **Lägg till ny princip** och ange sedan följande värden:

   * Principnamn: read-hivesampletable-all
   * Hive-database: standard
   * tabell: hivesampletable
   * Hive-kolumn: *
   * Välj användare: hiveuser1
   * Behörigheter: select

     ![Konfiguration av HDInsight ESP Ranger Hive-princip](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

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
 | Användarnamn | Ange hiveuser1@contoso158.onmicrosoft.com. Uppdatera domän namnet om det skiljer sig. |
 | lösenordsinställning | Ange lösenordet för hiveuser1. |

Se till att klicka på **Test** innan du sparar datakällan.

## <a name="import-data-into-excel-from-hdinsight"></a>Importera data till Excel från HDInsight
I det sista avsnittet konfigurerade du två principer.  hiveuser1 har select-behörighet för alla kolumner och hiveuser2 har select-behörighet på två kolumner. I det här avsnittet personifierar du de två användarna för att importera data till Excel.

1. Öppna en ny eller befintlig arbetsbok i Excel.
2. På fliken **Data** klickar du på **From Other Data Sources** (Från andra datakällor) och sedan klickar du på **Från guiden Dataanslutning** för att starta den **guiden Dataanslutning**.

    ![Öppna guiden Dataanslutning][img-hdi-simbahiveodbc.excel.dataconnection]
3. Välj **ODBC DSN** som datakälla och klicka sedan på **Nästa**.
4. Från ODBC-datakällor väljer du datakällsnamnet som du skapade i det föregående steget och sedan klickar du på **Nästa**.
5. Ange lösen ordet för klustret i guiden igen och klicka sedan på **OK**. Vänta tills dialogrutan **Markera databas och tabell** öppnas. Det kan ta några sekunder.
6. Välj **hivesampletable** och klicka sedan på **Nästa**.
7. Klicka på **Slutför**.
8. I dialogrutan **Importera data** kan du ändra eller specificera frågan. Gör det genom att klicka på **Egenskaper**. Det kan ta några sekunder.
9. Klicka på fliken **Definition**. Kommandotexten är:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Enligt Ranger-principerna du definierade har hiveuser1 select-behörighet för alla kolumner.  Den här frågan fungerar med hiveuser1's-autentiseringsuppgifter, men den här frågan fungerar inte med autentiseringsuppgifterna hiveuser2-autentiseringsuppgifter.

   ![Anslutningsegenskaper][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Klicka på **OK** för att stänga dialogrutan Anslutningsegenskaper.
11. Klicka på **OK** för att stänga dialogrutan **Importera data**.  
12. Ange lösenordet för hiveuser1 igen och klicka sedan på **OK**. Det tar några sekunder innan data har importerats till Excel. När det är klart bör du se 11 kolumner med data.

Om du vill testa den andra principen (Read-hivesampletable-devicemake) skapade du i det sista avsnittet

1. Lägg till ett nytt kalkylblad i Excel.
2. Följ föregående procedur för att importera data.  Den enda ändring du gör är att använda autentiseringsuppgifterna hiveuser2-autentiseringsuppgifter i stället för hiveuser1's. Detta Miss lyckas eftersom hiveuser2 endast har behörighet att visa två kolumner. Du bör se följande fel:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Följ samma procedur för att importera data. Den här gången använder du autentiseringsuppgifterna för hiveuser2 och ändrar också select-uttrycket från:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    till:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    När det är klart bör du se två kolumner med importerade data.

## <a name="next-steps"></a>Nästa steg
* Information om hur du konfigurerar ett HDInsight-kluster med Enterprise Security Package finns i [Konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure.md).
* Information om hur du hanterar HDInsight-kluster med ESP finns i [Hantera HDInsight-kluster med ESP](apache-domain-joined-manage.md).
* Information om hur du kör Hive-frågor med SSH på HDInsight-kluster med ESP finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* För att ansluta Hive med Hive-JDBC, se [Anslut till Apache Hive på Azure HDInsight med HIVE JDBC-drivrutinen](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Information om hur du ansluter Excel till Hadoop med Hive ODBC finns i [ansluta Excel till Apache Hadoop med Microsoft HIVE ODBC-enhet](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Information om hur du ansluter Excel till Hadoop med hjälp av Power Query finns i [ansluta Excel till Apache Hadoop med Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
