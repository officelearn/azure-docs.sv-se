---
title: Auktorisera användare för Ambari-vyer – Azure HDInsight
description: Hantera Ambari-användare och grupp behörigheter för HDInsight-kluster med ESP aktiverat.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: hrasheed
ms.openlocfilehash: 28f30270ab0a6c057ee583ccebc2a8540980c6cc
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442172"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Auktorisera användare för Apache Ambari Views

[Enterprise Security Package (ESP) aktiverade HDInsight-kluster](./domain-joined/hdinsight-security-overview.md) ger funktioner i företags klass, inklusive Azure Active Directory-baserad autentisering. Du kan [synkronisera nya användare](hdinsight-sync-aad-users-to-cluster.md) som har lagts till i Azure AD-grupper som har tilldelats åtkomst till klustret, så att specifika användare kan utföra vissa åtgärder. Att arbeta med användare, grupper och behörigheter i [Apache Ambari](https://ambari.apache.org/) stöds för både ESP HDInsight-kluster och standard HDInsight-kluster.

Active Directory användare kan logga in på klusternoderna med sina domänautentiseringsuppgifter. De kan också använda sina domänautentiseringsuppgifter för att autentisera kluster interaktioner med andra godkända slut punkter som [nyans](https://gethue.com/), Ambari vyer, ODBC, JDBC, POWERSHELL och REST-API: er.

> [!WARNING]  
> Ändra inte lösen ordet för Ambari-hdinsightwatchdog () på ditt Linux-baserade HDInsight-kluster. Om du ändrar lösen ordet bryts möjligheten att använda skript åtgärder eller utföra skalnings åtgärder med klustret.

Om du inte redan har gjort det följer du [de här anvisningarna](./domain-joined/apache-domain-joined-configure.md) för att etablera ett nytt ESP-kluster.

## <a name="access-the-ambari-management-page"></a>Öppna hanterings sidan för Ambari

Öppna **hanterings sidan för Ambari** i [webb gränssnittet Apache Ambari](hdinsight-hadoop-manage-ambari.md)genom att bläddra till **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Ange det användar namn och lösen ord för kluster administratör som du definierade när du skapade klustret. Gå sedan till Ambari-instrumentpanelen och välj **Hantera Ambari** under **Administratörs** menyn:

![Hantera Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Bevilja behörighet att Apache Hive vyer

Ambari levereras med View-instanser för [Apache Hive](https://hive.apache.org/) och [Apache TEZ](https://tez.apache.org/), bland annat. Om du vill bevilja åtkomst till en eller flera instanser av Hive-vyn går du till **hanterings sidan för Ambari**.

1. På sidan hantering väljer du länken **vyer** under **rubrik menyn till** vänster.

    ![Länken vyer](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. På sidan vyer expanderar du **HIVE** -raden. Det finns en standard-Hive-vy som skapas när Hive-tjänsten läggs till i klustret. Du kan också skapa fler instanser av Hive-vyer efter behov. Välj en Hive-vy:

    ![Vyer – Hive-vy](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Rulla nedåt längst ned på sidan Visa. Under avsnittet *behörigheter* har du två alternativ för att ge domän användare behörighet till vyn:

**Bevilja behörighet till dessa användare** ![Bevilja behörighet till dessa användare](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Bevilja behörighet till dessa grupper** ![Bevilja behörighet till dessa grupper](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

1. Om du vill lägga till en användare väljer du knappen **Lägg till användare** .

   * Börja skriva användar namnet så visas en listruta med tidigare definierade namn.

     ![Användaren har slutförts](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

   * Välj eller Skriv klart, användar namnet. Välj knappen **nytt** om du vill lägga till det här användar namnet som en ny användare.

   * Markera **kryss rutan blå**om du vill spara ändringarna.

     ![Angiven användare](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

1. Välj knappen **Lägg till grupp** om du vill lägga till en grupp.

   * Börja skriva grupp namnet. Processen att välja ett befintligt grupp namn eller lägga till en ny grupp är samma som för att lägga till användare.
   * Markera **kryss rutan blå**om du vill spara ändringarna.

     ![Angiven grupp](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Att lägga till användare direkt i en vy är användbart när du vill tilldela behörigheter till en användare som ska använda vyn, men vill inte att de ska vara medlemmar i en grupp som har ytterligare behörigheter. För att minska den administrativa belastningen kan det vara enklare att tilldela grupper behörigheter.

## <a name="grant-permissions-to-apache-tez-views"></a>Bevilja behörighet till Apache TEZ views

Med [Apache TEZ](https://tez.apache.org/) View-instanserna kan användarna övervaka och felsöka alla TEZ-jobb, som skickas av [Apache Hive](https://hive.apache.org/) frågor och [Apache gris](https://pig.apache.org/) -skript. Det finns en instans av standard-Tez som skapas när klustret har allokerats.

Om du vill tilldela användare och grupper till en instans av Tez, expanderar du raden **Tez** på sidan vyer, enligt beskrivningen ovan.

![Vyer – Tez vy](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Om du vill lägga till användare eller grupper upprepar du steg 3-5 i föregående avsnitt.

## <a name="assign-users-to-roles"></a>Tilldela användare till roller

Det finns fem säkerhets roller för användare och grupper, som visas i ordning för minskning av åtkomst behörigheter:

* Kluster administratör
* Kluster operatör
* Tjänstadministratör
* Tjänst operatör
* Kluster användare

Om du vill hantera roller går du till **hanterings sidan för Ambari**och väljer sedan länken **roller** i meny gruppen *kluster* till vänster.

![Meny länken roller](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Om du vill se en lista över behörigheter för varje roll, klickar du på det blå frågetecknet bredvid tabell rubriken **roller** på sidan roller.

![Meny länken roller](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

På den här sidan finns det två olika vyer som du kan använda för att hantera roller för användare och grupper: Blockera och lista.

### <a name="block-view"></a>Blockera vy

I vyn blockera visas varje roll på en egen rad, och den **tilldelas roller till dessa användare** och **tilldelar roller till de här grupp** alternativen enligt beskrivningen ovan.

![Vyn roll block](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Listvy

Listvyn innehåller snabb redigerings funktioner i två kategorier: Användare och grupper.

* Kategorin användare i vyn lista visar en lista över alla användare, så att du kan välja en roll för varje användare i list rutan.

    ![Visa lista över roller – användare](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  Kategorin grupper i listvyn visar alla grupper och rollen som tilldelats till varje grupp. I vårt exempel synkroniseras listan med grupper från de Azure AD-grupper som anges i **användar grupp egenskapen åtkomst** för klustrets domän inställningar. Se [skapa ett HDInsight-kluster med ESP aktiverat](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Vyn roll lista – grupper](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    I bilden ovan tilldelas gruppen "hiveusers" rollen som *kluster användar* roll. Det här är en skrivskyddad roll som gör att användare av gruppen kan visa men inte ändra tjänst konfiguration och kluster mått.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Logga in på Ambari som en användare med endast visning

Vi har tilldelat våra Azure AD-domän användare "hiveuser1"-behörigheter till Hive-och Tez-vyer. När vi startar Ambari-webbgränssnittet och anger användarens domän uppgifter (Azure AD-användarnamnet i e-postformat och lösen ord) omdirigeras användaren till sidan Ambari views. Härifrån kan användaren välja vilken tillgänglig vy som helst. Användaren kan inte besöka någon annan del av webbplatsen, inklusive instrument panelen, tjänster, värdar, aviseringar eller administratörs sidor.

![Användare med endast vyer](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Logga in på Ambari som en kluster användare

Vi har tilldelat vår Azure AD-domän användare "hiveuser2" till *kluster användar* rollen. Den här rollen har åtkomst till instrument panelen och alla meny alternativ. En kluster användare har färre tillåtna alternativ än en administratör. Hiveuser2 kan till exempel Visa konfigurationer för var och en av tjänsterna, men kan inte redigera dem.

![Användare med användar rollen kluster](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Apache Hive principer i HDInsight med ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Hantera ESP HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md)
* [Använd vyn Apache Hive med Apache Hadoop i HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synkronisera Azure AD-användare till klustret](hdinsight-sync-aad-users-to-cluster.md)
