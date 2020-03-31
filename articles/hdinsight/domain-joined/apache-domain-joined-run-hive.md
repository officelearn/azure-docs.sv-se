---
title: Apache Hive-principer i Apache Ranger - Azure HDInsight
description: Lär dig hur du konfigurerar Apache Ranger-principer för Hive i en Azure HDInsight-tjänst med Enterprise Security Package.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 90d7da9c8ddd8c9c595f2209dcc34e2f595acfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196934"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Konfigurera Apache Hive-principer i HDInsight med Enterprise Security Package

Lär dig hur du konfigurerar Apache Ranger-principer för Apache Hive. I den här artikeln skapar du två Ranger-principer för att begränsa åtkomsten till hivesampletable. Hivesampletable medföljer HDInsight-kluster. När du har konfigurerat principerna använder du Excel- och ODBC-drivrutinen för att ansluta till Hive-tabeller i HDInsight.

## <a name="prerequisites"></a>Krav

* Ett HDInsight-kluster med Enterprise Security Package. Se [Konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure.md).
* En arbetsstation med Office 2016, Office 2013 Professional Plus, Office 365 Pro Plus, fristående Excel 2013 eller Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Anslut till Apache Ranger Admin-gränssnittet
**Så här ansluter du till Ranger Admin-gränssnittet**

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/Ranger/` Ranger Admin-användargränssnittet där CLUSTERNAME är namnet på klustret.

   > [!NOTE]  
   > Ranger använder andra autentiseringsuppgifter än Apache Hadoop-kluster. Om du vill förhindra att webbläsare använder cachelagrade Hadoop-autentiseringsuppgifter använder du det nya webbläsarfönstret InPrivate för att ansluta till Ranger Admin-användargränssnittet.

2. Logga in med klusteradministratörens domänanvändarnamn och lösenord:

    ![HDInsight ESP Ranger hemsida](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    För närvarande fungerar Ranger bara med Yarn och Hive.

## <a name="create-domain-users"></a>Skapa domänanvändare

Se [Skapa ett HDInsight-kluster med ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp), för information om hur du skapar hiveruser1 och hiveuser2. Du använder de två användarkontona i den här artikeln.

## <a name="create-ranger-policies"></a>Skapa Ranger-principer

I det här avsnittet skapar du två Ranger-principer för åtkomst till hivesampletable. Du kan ge select-behörighet för olika uppsättningar med kolumner. Båda användarna skapades med [Skapa ett HDInsight-kluster med ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). I nästa avsnitt testar du de två principerna i Excel.

**Skapa Ranger-principer**

1. Öppna Ranger Admin-gränssnittet. Se Anslut till Apache Ranger Admin-gränssnittet.
2. Välj **CLUSTERNAME_Hive**under **Hive**. Du bör se två förkonfigurerade principer.
3. Välj **Lägg till ny princip**och ange sedan följande värden:

    |Egenskap |Värde |
    |---|---|
    |Principnamn|läs-hivesampletable-all|
    |Hive-databas|standard|
    |tabell|hivesampletable|
    |Hive-kolumn|*|
    |Välj användare|hiveuser1 (hiveuser1)|
    |Behörigheter|välj|

    ![HDInsight ESP Ranger Hive-principer konfigurerar](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Om en domänanvändare inte är ifylld i Välj användare, vänta en stund för att Ranger ska synkronisera med AAD.

4. Välj **Lägg till** om du vill spara principen.

5. Upprepa de två sista stegen för att skapa en annan princip med följande egenskaper:

    |Egenskap |Värde |
    |---|---|
    |Principnamn|läs-hivesampletable-devicemake|
    |Hive-databas|standard|
    |tabell|hivesampletable|
    |Hive-kolumn|clientid, devicemake|
    |Välj användare|hiveuser2 (hiveuser2)|
    |Behörigheter|välj|

## <a name="create-hive-odbc-data-source"></a>Skapa Hive ODBC-datakälla

Du hittar anvisningarna i [Skapa Hive ODBC-datakällan](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Egenskap  |Beskrivning |
 | --- | --- |
 | Namn på datakälla | Namnge din datakälla |
 | Värd | Ange CLUSTERNAME.azurehdinsight.net. Till exempel myHDICluster.azurehdinsight.net |
 | Port | Använd **443**. (Den här porten har ändrats från 563 till 443.) |
 | Databas | Använd **Standard**. |
 | Hive-servertyp | Välj **Hive Server 2** |
 | Mekanism | Välj **Azure HDInsight-tjänst** |
 | HTTP-sökväg | Lämna tomt. |
 | Användarnamn | Ange hiveuser1@contoso158.onmicrosoft.com. Uppdatera domännamnet om det är annorlunda. |
 | lösenord | Ange lösenordet för hiveuser1. |

Se till att klicka på **Test** innan du sparar datakällan.

## <a name="import-data-into-excel-from-hdinsight"></a>Importera data till Excel från HDInsight

I det sista avsnittet har du konfigurerat två principer.  hiveuser1 har select-behörighet för alla kolumner och hiveuser2 har select-behörighet på två kolumner. I det här avsnittet personifierar du de två användarna för att importera data till Excel.

1. Öppna en ny eller befintlig arbetsbok i Excel.

1. Från fliken **Data** navigerar du till **Hämta data** > **från andra källor** > **från ODBC** för att starta **fönstret Från ODBC.**

    ![Guiden Öppna dataanslutning](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. Välj det datakällnamn som du skapade i det sista avsnittet i listrutan och välj sedan **OK**.

1. För första användningen öppnas en **ODBC-drivrutinsdialogruta.** Välj **Windows** på den vänstra menyn. Välj sedan **Anslut** för att öppna **navigatorfönstret.**

1. Vänta tills dialogrutan **Markera databas och tabell** öppnas. Det kan ta några sekunder.

1. Välj **hivesampletable**och välj sedan **Nästa**.

1. Välj **Slutför**.

1. I dialogrutan **Importera data** kan du ändra eller specificera frågan. Om du vill göra det väljer du **Egenskaper**. Det kan ta några sekunder.

1. Välj fliken **Definition.** Kommandotexten är:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Enligt Ranger-principerna du definierade har hiveuser1 select-behörighet för alla kolumner.  Så den här frågan fungerar med hiveuser1 autentiseringsuppgifter, men den här frågan fungerar inte med hiveuser2 autentiseringsuppgifter.

1. Stäng dialogrutan Anslutningsegenskaper genom att välja **OK.**

1. Stäng dialogrutan **Importera data** genom att välja **OK.**  

1. Ange lösenordet för hiveuser1 igen och klicka sedan på **OK**. Det tar några sekunder innan data har importerats till Excel. När det är klart, ska du se 11 kolumner med data.

Om du vill testa den andra principen (läs-hivesampletable-devicemake) skapade du i det sista avsnittet

1. Lägg till ett nytt kalkylblad i Excel.
2. Följ föregående procedur för att importera data.  Den enda förändringen du gör är att använda hiveuser2 s referenser istället för hiveuser1's. Detta misslyckas eftersom hiveuser2 bara har behörighet att se två kolumner. Du bör se följande fel:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
        
3. Följ samma procedur för att importera data. Den här gången använder du autentiseringsuppgifterna för hiveuser2 och ändrar också select-uttrycket från:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    till:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    När det är klart ska du se två kolumner med data som importeras.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar ett HDInsight-kluster med Enterprise Security Package finns i [Konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure.md).
* För hantering av ett HDInsight-kluster med ESP finns i [Hantera HDInsight-kluster med ESP](apache-domain-joined-manage.md).
* För att köra Hive-frågor med SSH på HDInsight-kluster med ESP finns i [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
* För anslutning av Hive med Hive JDBC finns i [Ansluta till Apache Hive på Azure HDInsight med Hive JDBC-drivrutinen](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* För anslutning till Excel till Hadoop med Hive ODBC finns i [Ansluta Excel till Apache Hadoop med Microsoft Hive ODBC-enheten](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* För att ansluta Excel till Hadoop med Power Query finns i [Ansluta Excel till Apache Hadoop med power query](../hadoop/apache-hadoop-connect-excel-power-query.md)
