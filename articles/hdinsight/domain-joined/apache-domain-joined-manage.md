---
title: Hantera kluster för enterprise security-paket – Azure HDInsight
description: Lär dig hur du hanterar Azure HDInsight-kluster med Enterprise Security Package.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 93880269edd72477f3aa85b2dbdc9d9f3ec8ef25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435880"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Hantera HDInsight-kluster med Enterprise Security Package

Lär dig användarna och rollerna i HDInsight Enterprise Security Package (ESP) och hur du hanterar ESP-kluster.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Använda VSCode för att länka till domänanslutna kluster

Du kan länka ett normalt kluster med hjälp av Apache Ambari hanterat användarnamn, länka `user1@contoso.com`också ett apache hadoop-kluster med hjälp av domänanvändarnamn (till exempel: ).

1. Öppna [Visual Studio-kod](https://code.visualstudio.com/). Se till att tillägget [Spark & Hive Tools](../hdinsight-for-vscode.md) är installerat.

1. Följ stegen från [Länka ett kluster](../hdinsight-for-vscode.md#link-a-cluster) för Visual Studio-kod.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Använda IntelliJ för att länka till domänanslutna kluster

Du kan länka ett normalt kluster med hjälp av Ambari hanterat användarnamn, länka `user1@contoso.com`också ett säkerhetssamopkluster med hjälp av domänanvändarnamn (till exempel: ).

1. Öppna IntelliJ IDEA. Se till att alla [förutsättningar](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) är uppfyllda.

1. Följ stegen från [Länka ett kluster](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) för IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Använda Eclipse för att länka till domänanslutna kluster

Du kan länka ett normalt kluster med hjälp av Ambari hanterat användarnamn, länka `user1@contoso.com`också ett säkerhetssamopkluster med hjälp av domänanvändarnamn (till exempel: ).

1. Öppna Eclipse. Se till att alla [förutsättningar](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) är uppfyllda.

1. Följ stegen från [Länka ett kluster](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) för Eclipse.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Få tillgång till kluster med Enterprise Security Package

Enterprise Security Package (tidigare kallat HDInsight Premium) ger åtkomst till klustret med flera användare, där autentisering görs av Active Directory och auktorisering av Apache Ranger och Adls-ACL(Storage). Auktorisering ger säkra gränser mellan flera användare och tillåter endast privilegierade användare att få åtkomst till data baserat på auktoriseringsprinciperna.

Säkerhet och användarisolering är viktiga för ett HDInsight-kluster med Enterprise Security Package. För att uppfylla dessa krav blockeras SSH-åtkomst till klustret med Enterprise Security Package. I följande tabell visas de rekommenderade åtkomstmetoderna för varje klustertyp:

|Arbetsbelastning|Scenario|Åtkomstmetod|
|--------|--------|-------------|
|Apache Hadoop|Hive – interaktiva jobb/frågor  |<ul><li>[Beeline](#beeline)</li><li>[Hive-vy](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-verktyg](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interaktiva jobb/frågor, PySpark interaktiv|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin med Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive-vy](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-verktyg](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Batchscenarier – Spark-skicka, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interaktiv fråga (LLAP)|Interaktiv|<ul><li>[Beeline](#beeline)</li><li>[Hive-vy](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-verktyg](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Alla|Installera anpassat program|<ul><li>[Skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter är inte installerat/stöds inte i Enterprise Security Package.

Att använda standard-API:er hjälper ur säkerhetsperspektiv. Du får också följande fördelar:

- **Hantering** – Du kan hantera din kod och automatisera jobb med standard-API: er – Livy, HS2 etc.
- **Granskning** – Med SSH finns det inget sätt att granska, vilka användare SSH'd till klustret. Detta skulle inte vara fallet när jobb konstrueras via standardslutpunkter som de skulle utföras i samband med användaren.

### <a name="use-beeline"></a><a name="beeline"></a>Använda Beeline

Installera Beeline på din dator och anslut via det offentliga internet, använd följande parametrar:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Om du har Beeline installerat lokalt och ansluter via ett Virtuellt Azure-nätverk använder du följande parametrar:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Om du vill hitta det fullständigt kvalificerade domännamnet för en headnode använder du informationen i Hantera HDInsight med hjälp av Ambari REST API-dokumentet.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Användare av HDInsight-kluster med ESP

Ett icke-ESP HDInsight-kluster har två användarkonton som skapas när klustret skapas:

- **Ambari admin:** Detta konto är också känd som *Hadoop användare* eller *HTTP-användare*. Det här kontot kan användas för att `https://CLUSTERNAME.azurehdinsight.net`logga in på Ambari på . Den kan också användas för att köra frågor på Ambari-vyer, utföra jobb via externa verktyg (till exempel PowerShell, Templeton, Visual Studio) och autentisera med Hive ODBC-drivrutinen och BI-verktygen (till exempel Excel, Power BI eller Tableau).

En HDInsight kluster med ESP har tre nya användare utöver Ambari Admin.

- **Ranger admin:** Det här kontot är den lokala Apache Ranger admin-konto. Det är inte en active directory domänanvändare. Det här kontot kan användas för att ställa in principer och göra andra användare till administratörer eller delegerade administratörer (så att dessa användare kan hantera principer). Som standard är användarnamnet *admin* och lösenordet är detsamma som Ambari admin lösenord. Lösenordet kan uppdateras från sidan Inställningar i Ranger.

- **Klusteradministratörsdomänanvändare:** Det här kontot är en active directory-domänanvändare som betecknas som Hadoop-klusteradministratör, inklusive Ambari och Ranger. Du måste ange den här användarens autentiseringsuppgifter när klustret skapas. Den här användaren har följande behörigheter:
    - Anslut datorer till domänen och placera dem inom den organisationsenhet som du anger när klustret skapas.
    - Skapa tjänsthuvudnamn inom den organisationsenhet som du anger när klustret skapas.
    - Skapa omvända DNS-poster.

    Observera att de andra AD-användarna också har dessa privilegier.

    Det finns några slutpunkter i klustret (till exempel Templeton) som inte hanteras av Ranger och därför inte är säkra. Dessa slutpunkter är låsta för alla användare utom klusteradministratörsdomänanvändaren.

- **Normal**: När klustret skapas kan du tillhandahålla flera active directory-grupper. Användarna i dessa grupper synkroniseras med Ranger och Ambari. Dessa användare är domänanvändare och har endast åtkomst till Ranger-hanterade slutpunkter (till exempel Hiveserver2). Alla RBAC-principer och granskning kommer att gälla för dessa användare.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Roller för HDInsight-kluster med ESP

HDInsight Enterprise Security Package har följande roller:

- Klusteradministratör
- Klusteroperator
- Tjänstadministratör
- Serviceoperatör
- Klusteranvändare

**Så här visar du behörigheterna för dessa roller**

1. Öppna användargränssnittet för Ambari-hantering.  Se [Öppna Ambari Management UI](#open-the-ambari-management-ui).
2. Välj **Roller**på den vänstra menyn .
3. Välj det blå frågetecknet om du vill visa behörigheterna:

    ![BEHÖRIGHETER FÖR ESP HDInsight-roller](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Öppna användargränssnittet för Ambari-hantering

1. Navigera `https://CLUSTERNAME.azurehdinsight.net/` till den plats där CLUSTERNAME är namnet på klustret.
1. Logga in på Ambari med hjälp av klusteradministratörens användarnamn och lösenord.
1. Välj listrutan **admin** i det övre högra hörnet och välj sedan **Hantera Ambari**.

    ![ESP HDInsight hantera Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Användargränssnittet ser ut som:

    ![ESP HDInsight Apache Ambari management UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Lista de domänanvändare som är synkroniserade från Active Directory

1. Öppna användargränssnittet för Ambari-hantering.  Se [Öppna Ambari Management UI](#open-the-ambari-management-ui).
2. Välj **Användare**på den vänstra menyn . Du ska se alla användare synkroniserade från Active Directory till HDInsight-klustret.

    ![ESP HDInsight Ambari management UI lista användare](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Lista de domängrupper som synkroniserats från Active Directory

1. Öppna användargränssnittet för Ambari-hantering.  Se [Öppna Ambari Management UI](#open-the-ambari-management-ui).
2. Välj **Grupper**på den vänstra menyn . Du ska se alla grupper synkroniserade från Active Directory till HDInsight-klustret.

    ![ESP HDInsight Ambari hantering användargränssnitt lista grupper](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurera behörigheter för hivs

1. Öppna användargränssnittet för Ambari-hantering.  Se [Öppna Ambari Management UI](#open-the-ambari-management-ui).
2. Välj **Vyer**på den vänstra menyn .
3. Välj **HIVE** för att visa informationen.

    ![ESP HDInsight Ambari hantering UI Hive Visningar](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Välj länken **Hive View** för att konfigurera Hive-vyer.
5. Bläddra ned till avsnittet **Behörigheter.**

    ![ESP HDInsight Ambari-hantering UI Hive-vyer konfigurerar behörigheter](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Välj **Lägg till användare** eller Lägg till **grupp**och ange sedan de användare eller grupper som kan använda Hive-vyer.

## <a name="configure-users-for-the-roles"></a>Konfigurera användare för rollerna

 Information om hur du ser en lista över roller och deras behörigheter finns i Roller för HDInsight-kluster med ESP.

1. Öppna användargränssnittet för Ambari-hantering.  Se [Öppna Ambari Management UI](#open-the-ambari-management-ui).
2. Välj **Roller**på den vänstra menyn .
3. Välj **Lägg till användare** eller Lägg till **grupp** om du vill tilldela användare och grupper till olika roller.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar ett HDInsight-kluster med Enterprise Security Package finns i [Konfigurera HDInsight-kluster med ESP](apache-domain-joined-configure.md).
- För att konfigurera Hive-principer och köra Hive-frågor finns i [Konfigurera Apache Hive-principer för HDInsight-kluster med ESP](apache-domain-joined-run-hive.md).
