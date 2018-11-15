---
title: Hantera HDInsight-kluster med Enterprise Security Enterprise - Azure
description: Lär dig mer om att hantera HDInsight-kluster med Enterprise Security Package.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: a995f99f7baa8d6f9476f852aa47ce239c921a50
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634472"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Hantera HDInsight-kluster med Enterprise Security Package
Lär dig användarna och roller i HDInsight Enterprise Security Package (ESP) och hur du hanterar ESP-kluster.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Använda VSCode för att länka till domänanslutna kluster

Du kan länka en normal kluster med hjälp av Apache Ambari hanteras användarnamn, också länka ett security Apache Hadoop-kluster med hjälp av domänanvändarnamn (t.ex: user1@contoso.com).
1. Öppna kommandopaletten genom att välja **CTRL + SKIFT + P**, och ange sedan **HDInsight: länka ett kluster**.

   ![länken cluster kommando](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Ange HDInsight-kluster-URL > indata Username -> lösenordet -> Välj typ av kluster -> den visar lyckad info om verifiering.
   
   ![länka kluster dialog](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]
   > Länkade användarnamn och lösenord används om klustret både loggas i Azure-prenumeration och länkad ett kluster. 
   
3. Du kan se ett länkade-kluster med hjälp av kommandot **listan kluster**. Nu kan du skicka ett skript för att den här länkade kluster.

   ![länkade kluster](./media/apache-domain-joined-manage/linked-cluster.png)

4. Du kan också Avlänka ett kluster genom att mata in **HDInsight: ta bort länken till ett kluster** från kommandopaletten.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Använda IntelliJ för att länka till domänanslutna kluster

Du kan länka ett normalt kluster med Ambari hanteras användarnamn, också länka ett hadoop-kluster med säkerhet med hjälp av domänanvändarnamn (t.ex: user1@contoso.com). 
1. Klicka på **länka ett kluster** från **Azure Explorer**.

   ![länken snabbmenyn för kluster](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Ange **klusternamn**, **användarnamn** och **lösenord**. Du måste kontrollera användarnamnet och lösenordet om autentisering misslyckades. Du kan också lägga till Storage-konto, Lagringsnyckeln, och välj sedan en behållare från Storage-behållare. Information om är så att Lagringsutforskaren i det vänstra trädet
   
   ![länka kluster dialog](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]
   > Vi använder länkade lagringsnyckel, användarnamn och lösenord om klustret både loggas i Azure-prenumeration och länkad ett kluster.
   > ![Storage explorer i IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. Du kan se ett länkade kluster i **HDInsight** noden om informationen om indata är rätt. Nu kan du skicka ett program till den här länkade kluster.

   ![länkade kluster](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Du kan också Avlänka ett kluster från **Azure Explorer**.
   
   ![ta bort kopplingen kluster](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Använda Eclipse för att länka till domänanslutna kluster

Du kan länka ett normalt kluster med Ambari hanteras användarnamn, också länka ett hadoop-kluster med säkerhet med hjälp av domänanvändarnamn (t.ex: user1@contoso.com).
1. Klicka på **länka ett kluster** från **Azure Explorer**.

   ![länken snabbmenyn för kluster](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Ange **klusternamnet**, **användarnamn** och **lösenord**, klicka sedan på OK för att länka kluster. Alternativt kan du ange Storage-konto, Lagringsnyckeln och välj sedan storage explorer för att arbeta i den vänstra trädvyn Storage-behållare
   
   ![länka kluster dialog](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]
   > Vi använder länkade lagringsnyckel, användarnamn och lösenord om klustret både loggas i Azure-prenumeration och länkad ett kluster.
   > ![Storage explorer i Eclipse](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Du kan se ett länkade kluster i **HDInsight** nod när du klickar på OK-knapp om informationen om indata är rätt. Nu kan du skicka ett program till den här länkade kluster.

   ![länkade kluster](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. Du kan också Avlänka ett kluster från **Azure Explorer**.
   
   ![ta bort kopplingen kluster](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Åtkomst till kluster med Enterprise Security Package.

Enterprise Security Package (tidigare kallat HDInsight Premium) ger flera användare åtkomst till klustret, där autentisering görs genom att Active Directory och auktorisering av Apache Ranger och Storage-ACL: er (ADLS ACL: er). Auktorisering ger säker gränser mellan flera användare och tillåter enbart behöriga användare ska ha åtkomst till data baserat på auktoriseringsprinciper för.

Säkerhet och användare isolering är viktiga för ett HDInsight-kluster med Enterprise Security Package. SSH-åtkomst till klustret med Enterprise Security Package blockeras som uppfyller dina krav. I följande tabell visas de rekommenderade metoderna för varje typ av kluster:

|Arbetsbelastning|Scenario|Åtkomstmetod|
|--------|--------|-------------|
|Hadoop|Hive – interaktiva jobb/frågor |<ul><li>[Beeline](#beeline)</li><li>[Hive-vyn](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Powerbi](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-verktyg](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Interaktiva jobb/frågor, interaktiv PySpark|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin med Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive-vyn](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Powerbi](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-verktyg](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Batchscenarier – Spark-submit, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interactive Query (LLAP)|Interaktiv|<ul><li>[Beeline](#beeline)</li><li>[Hive-vyn](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Powerbi](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-verktyg](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Alla|Installera anpassade program|<ul><li>[Skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]
   > Jupyter är inte installerad/stöds i Enterprise Security Package.

Med hjälp av standard-API: er hjälper från säkerhetsperspektiv. Dessutom kan få du följande fördelar:

1.  **Hantering av** – du kan hantera din kod och automatisera jobb med hjälp av standard-API: er – Livy HS2 osv.
2.  **Granska** – med SSH, det finns inget sätt att granska vilka användare SSH var tvungen att klustret. Detta skulle vara fallet när du jobb skapas via standard-slutpunkter som de skulle verkställas i kontexten för användaren. 



### <a name="beeline"></a>Använd Beeline 
Installera Beeline på din dator och ansluta via det offentliga internet, använder följande parametrar: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Om du har installerat lokalt Beeline och ansluta via Azure Virtual Network, använder du följande parametrar: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Använd informationen i den hantera HDInsight med hjälp av Ambari REST API-dokumentet för att hitta det fullständigt kvalificerade domännamnet för en huvudnod.














## <a name="users-of-hdinsight-clusters-with-esp"></a>Användare av HDInsight-kluster med hjälp av ESP
Ett icke - ESP HDInsight-kluster har två konton som skapas när klustret skapas:

* **Ambari admin**: det här kontot kallas även *Hadoop-användare* eller *HTTP-användare*. Det här kontot kan användas för att logga in på Ambari på https://&lt;klusternamn >. azurehdinsight.net. Det kan också användas för att köra frågor på Ambari-vyer, köra jobb via externa verktyg (till exempel PowerShell, Templeton, Visual Studio) och autentisera med Hive ODBC-drivrutinen och BI-verktyg (till exempel Excel, PowerBI eller Tableau).

Ett HDInsight-kluster med ESP har tre nya användare förutom Ambari Admin.

* **Ranger admin**: det här kontot är det lokala administratörskontot för Apache Ranger. Det är inte en domänanvändare för active directory. Det här kontot kan användas för att konfigurera principer och göra andra användare, Administratörer eller delegerade administratörer (så att användarna kan hantera principer). Användarnamnet är som standard *admin* och lösenordet är detsamma som administratörslösenordet som Ambari. Lösenordet kan uppdateras från sidan Inställningar i Ranger.
* **Klustret admin domänanvändare**: det här kontot är en active directory-domänanvändare som är utsedd till Hadoop-kluster-administratör, inklusive Ambari och Ranger. Du måste ange den här användarens autentiseringsuppgifter när klustret skapas. Den här användaren har följande behörigheter:

  * Ansluta datorer till domänen och placera dem inom den Organisationsenhet som du anger när klustret skapas.
  * Skapa tjänstens huvudnamn i Organisationsenheten som du anger när klustret skapas.
  * Skapa omvända DNS-poster.

    Observera de AD-användarna också ha dessa privilegier.

    Det finns några slutpunkter i klustret (till exempel Templeton) som inte hanteras av Ranger och därför inte är säker. De här slutpunkterna är låsta för alla användare utom domänanvändare för kluster-administratör.
* **Vanliga**: du kan ange flera active directory-grupper när du skapar klustret. Användare i dessa grupper synkroniseras till Ranger och Ambari. Dessa användare är domänanvändare och har åtkomst till endast Ranger-hanterade slutpunkter (till exempel Hiveserver2). Alla RBAC-principer och granskning kommer att användas på dessa användare.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Rollerna för HDInsight-kluster med hjälp av ESP
Enterprise-säkerhetspaketet för HDInsight har följande roller:

* Klusteradministratören
* Kluster-Operator
* Tjänstadministratör
* Tjänsten Operator
* Klusteranvändaren

**Se behörigheterna för dessa roller**

1. Öppna hanteringsgränssnittet för Ambari.  Se [öppna Ambari hanteringsgränssnittet](#open-the-ambari-management-ui).
2. I den vänstra menyn klickar du på **roller**.
3. Klicka på blå frågetecknet visas behörigheterna:

    ![ESP HDInsight rollbehörigheter](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Öppna hanteringsgränssnittet för Ambari

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna ditt HDInsight-kluster. Se [lista och visa kluster](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Klicka på **instrumentpanelen** på den översta menyn för att öppna Ambari.
4. Logga in på Ambari med klustret domänanvändarnamn och lösenord.
5. Klicka på den **Admin** nedrullningsbara menyn från övre högra hörnet och klicka sedan på **hantera Ambari**.

    ![ESP HDInsight hantera Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Det ser ut som Användargränssnittet:

    ![Hantering av ESP HDInsight Ambari UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Lista de domänanvändare som synkroniseras från din Active Directory
1. Öppna hanteringsgränssnittet för Ambari.  Se [öppna Ambari hanteringsgränssnittet](#open-the-ambari-management-ui).
2. I den vänstra menyn klickar du på **användare**. Du bör se alla användare som synkroniseras från Active Directory till HDInsight-klustret.

    ![ESP HDInsight Ambari UI-användare av lista](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Lista de domängrupperna som synkroniseras från din Active Directory
1. Öppna hanteringsgränssnittet för Ambari.  Se [öppna Ambari hanteringsgränssnittet](#open-the-ambari-management-ui).
2. I den vänstra menyn klickar du på **grupper**. Du bör se alla grupper som synkroniserats från Active Directory till HDInsight-kluster.

    ![ESP HDInsight Ambari-Användargränssnittet lista hanteringsgrupper](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurera behörigheter för Hive-vyer
1. Öppna hanteringsgränssnittet för Ambari.  Se [öppna Ambari hanteringsgränssnittet](#open-the-ambari-management-ui).
2. I den vänstra menyn klickar du på **vyer**.
3. Klicka på **HIVE** visa informationen.

    ![Hantering av ESP HDInsight Ambari UI Hive-vyer](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Klicka på den **Hive-vy** länk för att konfigurera Hive-vyer.
5. Rulla ned till den **behörigheter** avsnittet.

    ![ESP HDInsight Ambari UI Hive-vyer för hantering av konfigurera behörigheter](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Klicka på **Lägg till användare** eller **Lägg till grupp**, och anger sedan de användare eller grupper som kan använda Hive-vyer.

## <a name="configure-users-for-the-roles"></a>Konfigurera användare för roller
 Om du vill se en lista över roller och deras behörigheter, se [roller för HDInsight-kluster med ESP](#roles-of-domain---joined-hdinsight-clusters).

1. Öppna hanteringsgränssnittet för Ambari.  Se [öppna Ambari hanteringsgränssnittet](#open-the-ambari-management-ui).
2. I den vänstra menyn klickar du på **roller**.
3. Klicka på **Lägg till användare** eller **Lägg till grupp** att tilldela användare och grupper för olika roller.

## <a name="next-steps"></a>Nästa steg
* Konfigurera ett HDInsight-kluster med Enterprise Security Package finns i [konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure.md).
* Konfigurera Hive-principer och köra Hive-frågor finns i [konfigurera Hive-principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md).
