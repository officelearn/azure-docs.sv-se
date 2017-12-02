---
title: "Hantera domänanslutna HDInsight - kluster i Azure | Microsoft Docs"
description: "Lär dig att hantera domänanslutna HDInsight-kluster"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 0fc32960fc2f1ae69315dbfd6bfb8c34c4adc0fa
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>Hantera domänanslutna HDInsight-kluster
Läs om användarna och roller för domänanslutna HDInsight och hur du hanterar domänanslutna HDInsight-kluster.

## <a name="users-of-domain-joined-hdinsight-clusters"></a>Användare av domänanslutna HDInsight-kluster
Ett HDInsight-kluster som inte är ansluten till domänen har två konton som skapas när klustret skapas:

* **Ambari admin**: det här kontot kallas även *Hadoop användare* eller *HTTP användaren*. Det här kontot kan användas för att logga in på Ambari när https://&lt;klusternamn >. azurehdinsight.net. Det kan också användas för att köra frågor på Ambari-vyer, köra jobb via externa verktyg (d.v.s. PowerShell, Templeton, Visual Studio) och autentisera med ODBC-drivrutinen och BI-verktyg (d.v.s. Excel, PowerBI eller Tableau).
* **SSH-användare**: det här kontot kan användas med SSH och köra sudo-kommandon. Det har rot behörigheter till den virtuella Linux-datorer.

En domänansluten HDInsight-kluster har tre nya användare utöver Ambari Admin och SSH-användare.

* **Ranger admin**: det här kontot är lokalt administratörskonto för Apache Ranger. Det är inte en användare för active directory-domän. Det här kontot kan användas för att konfigurera principer och göra andra användare, Administratörer eller delegerade administratörer (så att användarna kan hantera principer). Som standard är användarnamnet *admin* och lösenordet är detsamma som Ambari administratörslösenord. Lösenordet kan uppdateras från sidan Inställningar i Ranger.
* **Klustret admin domänanvändare**: det här kontot är en active directory-domänanvändare utses Hadoop-kluster administratören inklusive Ambari och Ranger. Du måste ange den här användarens autentiseringsuppgifter när klustret skapas. Den här användaren har följande behörigheter:

  * Ansluta datorer till domänen och placera dem i Organisationsenheten som du anger när klustret skapas.
  * Skapa tjänstens huvudnamn i Organisationsenheten som du anger när klustret skapas.
  * Skapa omvänd DNS-poster.

    Observera de AD-användarna ha dessa privilegier.

    Det finns några slutpunkter i klustret (till exempel Templeton) som inte hanteras av Ranger och därför inte är säker. De här slutpunkterna är låsta för alla användare utom klustret domän administratörsanvändare.
* **Vanliga**: du kan ange flera active directory-grupper när klustret skapas. Användare i dessa grupper kommer att synkroniseras till Ranger och Ambari. Dessa användare har åtkomst till endast hanterade Ranger slutpunkter (till exempel Hiveserver2) är domänanvändare. Alla RBAC principer och granskning kommer att användas på dessa användare.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>Roller för domänanslutna HDInsight-kluster
Domänanslutna HDInsight har följande roller:

* Klusteradministratören
* Kluster-operatorn
* Tjänstadministratör
* Tjänsten Operator
* Kluster-användare

**Se behörigheter för dessa roller**

1. Öppna hanteringsgränssnittet för Ambari.  Se [öppna Ambari hanteringsgränssnittet](#open-the-ambari-management-ui).
2. I den vänstra menyn klickar du på **roller**.
3. Klicka på det blå frågetecknet visas behörigheterna:

    ![Behörigheter för domänanslutna HDInsight-roller](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Öppna hanteringsgränssnittet för Ambari
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Öppna ditt HDInsight-kluster i ett blad. Se [listan och visa](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Klicka på **instrumentpanelen** på den översta menyn för att öppna Ambari.
4. Logga in på Ambari med klustrets administratör domänanvändarnamn och lösenord.
5. Klicka på den **Admin** listrutan från längst upp till höger hörnet och klicka sedan på **hantera Ambari**.

    ![Domänanslutna HDInsight hantera Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    Det ser ut så Användargränssnittet:

    ![Domänanslutna HDInsight Ambari hanteringsgränssnittet](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Visa en lista med domänanvändare som synkroniseras från din Active Directory
1. Öppna hanteringsgränssnittet för Ambari.  Se [öppna Ambari hanteringsgränssnittet](#open-the-ambari-management-ui).
2. I den vänstra menyn klickar du på **användare**. Du bör se alla användare som synkroniseras från din Active Directory till HDInsight-klustret.

    ![Domänanslutna HDInsight Ambari management UI listan användare](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Visa en lista över de domängrupperna som synkroniseras från din Active Directory
1. Öppna hanteringsgränssnittet för Ambari.  Se [öppna Ambari hanteringsgränssnittet](#open-the-ambari-management-ui).
2. I den vänstra menyn klickar du på **grupper**. Du bör se alla grupper som synkroniseras från din Active Directory till HDInsight-klustret.

    ![Domänanslutna HDInsight Ambari UI lista hanteringsgrupper](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Konfigurera behörigheter för Hive-vyer
1. Öppna hanteringsgränssnittet för Ambari.  Se [öppna Ambari hanteringsgränssnittet](#open-the-ambari-management-ui).
2. I den vänstra menyn klickar du på **vyer**.
3. Klicka på **HIVE** att visa detaljerna.

    ![Domänanslutna HDInsight Ambari management UI Hive-vyer](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Klicka på den **Hive-vy** länk för att konfigurera Hive vyer.
5. Rulla ned till den **behörigheter** avsnitt.

    ![Konfigurera behörigheter för domänanslutna HDInsight Ambari management UI Hive-vyer](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Klicka på **Lägg till användare** eller **Lägg till grupp**, och sedan ange användare eller grupper som kan använda Hive vyer.

## <a name="configure-users-for-the-roles"></a>Konfigurera användare för roller
 Om du vill se en lista över roller och deras behörigheter finns [roller för domänanslutna HDInsight-kluster](#roles-of-domain---joined-hdinsight-clusters).

1. Öppna hanteringsgränssnittet för Ambari.  Se [öppna Ambari hanteringsgränssnittet](#open-the-ambari-management-ui).
2. I den vänstra menyn klickar du på **roller**.
3. Klicka på **Lägg till användare** eller **Lägg till grupp** att tilldela användare och grupper för olika roller.

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett domänanslutet HDInsight-kluster kan du läsa i [Konfigurera domänanslutna HDInsight-kluster](apache-domain-joined-configure.md).
* Om du vill konfigurera Hive-principer och köra Hive-frågor kan du läsa i [Konfigurera Hive-principer för domänanslutna HDInsight-kluster](apache-domain-joined-run-hive.md).
* För att köra Hive-frågor med SSH på domänanslutna HDInsight-kluster, se [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
