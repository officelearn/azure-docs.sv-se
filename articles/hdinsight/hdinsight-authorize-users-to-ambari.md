---
title: Auktorisera användare för Ambari Views - Azure HDInsight
description: Så här hanterar du Ambari användar- och behörigheter för HDInsight-kluster med ESP aktiverat.
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 69ae1bd05b64912b3d53ca88b468a72a90ff5a74
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718307"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Auktorisera användare för Apache Ambari Views

[Enterprise Security Package (ESP) aktiverat HDInsight-kluster](./domain-joined/apache-domain-joined-introduction.md) tillhandahåller funktioner för företagsklass, inklusive Azure Active Directory-baserad autentisering. Du kan [synkronisera nya användare](hdinsight-sync-aad-users-to-cluster.md) lagts till i Azure AD-grupper som har fått åtkomst till klustret, så att dessa specifika användare att utföra vissa åtgärder. Arbeta med användare, grupper och behörigheter i [Apache Ambari](https://ambari.apache.org/) stöds för både ESP HDInsight-kluster och standard HDInsight-kluster.

Active Directory-användare kan logga in på noderna i klustret med hjälp av sina domänautentiseringsuppgifter. De kan också använda sina domänautentiseringsuppgifter för att autentisera kluster interaktioner med andra godkända slutpunkter som [Hue](https://gethue.com/), Ambari Views, ODBC, JDBC, PowerShell och REST API: er.

> [!WARNING]  
> Ändra inte lösenordet för Ambari-watchdog (hdinsightwatchdog) på Linux-baserade HDInsight-klustret. Ändra lösenordet delar möjligheten att använda skriptåtgärder eller utföra skalningsåtgärder med klustret.

Om du inte redan har gjort det, Följ [instruktionerna](./domain-joined/apache-domain-joined-configure.md) att etablera ett nytt ESP-kluster.

## <a name="access-the-ambari-management-page"></a>Komma åt sidan för hantering av Ambari

Att komma till den **Ambari hanteringssida** på den [Apache Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md), bläddra till **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Ange kluster administratörens användarnamn och lösenord som du definierade när klustret skapas. Välj sedan Ambari-instrumentpanelen **hantera Ambari** under den **admin** menyn:

![Hantera Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Bevilja behörigheter till Apache Hive-vyer

Ambari medföljer Visa instanser för [Apache Hive](https://hive.apache.org/) och [Apache TEZ](https://tez.apache.org/), bland annat. Om du vill bevilja åtkomst till en eller flera Hive Visa instanser, går du till den **Ambari hanteringssidan**.

1. Sidan väljer du den **vyer** länka den **vyer** rubrik på menyn till vänster.

    ![Vyer länk](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. På sidan vyer Expandera den **HIVE** raden. Det finns en standard Hive-vyn som skapades när Hive-tjänsten har lagts till i klustret. Du kan också skapa mer Hive Visa instanser efter behov. Välj en Hive-vyn:

    ![Vyer - Hive-vyn](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Rulla längst ned på sidan Visa. Under den *behörigheter* avsnittet har du två alternativ för att bevilja domänanvändare deras behörigheter till vyn:

**Bevilja behörighet till dessa användare** ![bevilja behörighet till dessa användare](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Bevilja behörighet till dessa grupper** ![bevilja behörighet till dessa grupper](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

1. Om du vill lägga till en användare väljer den **Lägg till användare** knappen.

   * Börja skriva användarens namn och du ser en nedrullningsbar lista över tidigare definierade namn.

     ![Användaren autocompletes](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

   * Välj eller har skrivit klart användarnamnet. Om du vill lägga till det här användarnamnet som en ny användare väljer den **New** knappen.

   * För att spara ändringarna, Välj den **blå kryssrutan**.

     ![Användaren angav](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

1. Om du vill lägga till en grupp, Välj den **Lägg till grupp** knappen.

   * Börja skriva namnet på. Processen för att välja ett befintligt gruppnamn eller lägga till en ny grupp är desamma som för att lägga till användare.
   * För att spara ändringarna, Välj den **blå kryssrutan**.

     ![Grupp som anges](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Lägga till användare direkt i en vy är användbart när du vill tilldela behörigheter till en användare kan använda vyn, men inte vill att de ska vara medlem i en grupp som har ytterligare behörighet. Om du vill minska den administrativa kostnader, kan det vara enklare att tilldela behörigheter till grupper.

## <a name="grant-permissions-to-apache-tez-views"></a>Bevilja behörigheter till Apache TEZ vyer

Den [Apache TEZ](https://tez.apache.org/) Visa instanser Tillåt användare att övervaka och felsöka alla Tez-jobb från [Apache Hive](https://hive.apache.org/) frågor och [Apache Pig](https://pig.apache.org/) skript. Det finns en Tez visa standardinstansen som skapas när klustret har etablerats.

Om du vill tilldela användare och grupper till en vy Tez-instans, expandera den **TEZ** raden på sidan vyer som tidigare beskrivits.

![Vyer - Tez-vy](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Lägg till användare eller grupper, Upprepa steg 3 till 5 i föregående avsnitt.

## <a name="assign-users-to-roles"></a>Tilldela användare till roller

Det finns fem säkerhetsroller för användare och grupper som visas i fallande åtkomstbehörigheter:

* Klusteradministratören
* Kluster-Operator
* Tjänstadministratör
* Tjänsten Operator
* Klusteranvändaren

För att hantera roller, går du till den **Ambari hanteringssida**och välj sedan den **roller** länka inom den *kluster* grupp i menyn till vänster.

![Roller menyn länk](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Om du vill se listan över behörigheter för varje roll, klicka på det blå frågetecknet bredvid den **roller** tabellrubrik på sidan roller.

![Roller menyn länk](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Det finns två olika vyer som du kan använda för att hantera roller för användare och grupper på den här sidan: Blockera och lista.

### <a name="block-view"></a>Visa block

Visa Block visar varje roll i en egen rad och ger den **tilldela roller till dessa användare** och **tilldela roller till dessa grupper** alternativ som tidigare beskrivits.

![Roller blockera vyn](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Listvy

Listvyn ger snabb redigering funktioner i två kategorier: Användare och grupper.

* Kategorin användare för listvyn visar en lista över alla användare, där du kan välja en roll för varje användare i listrutan.

    ![Roller listvy - användare](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  Kategorin grupper i listvyn visas alla grupper och rolltilldelningen för varje grupp. I vårt exempel i listan över grupper som ska synkroniseras från Azure AD-grupper som anges i den **ha åtkomst till användargrupper** egenskapen för klustrets Domäninställningar. Se [skapa ett HDInsight-kluster med ESP aktiverat](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Roller listvy - grupper](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    I bilden ovan, gruppen ”hiveusers” tilldelas den *Klusteranvändaren* roll. Det här är en skrivskyddad roll som låter användare av den gruppen för att visa men inte ändra tjänstkonfiguration och klustret mått.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Logga in på Ambari som en skrivskyddad användare

Vi har tilldelat vår Azure AD domain-användare ”hiveuser1” behörigheter till Hive och Tez vyer. När vi starta Ambari-Webbgränssnittet och ange domänautentiseringsuppgifter för den här användaren (Azure AD-användarnamn i postformatet för e-och lösenord) omdirigeras användaren till sidan Ambari-vyer. Här kan kan användaren välja alla tillgängliga vyer. Användaren inte får besöka andra delar av webbplatsen, inklusive sidorna för instrumentpanelen, tjänster, värdar, aviseringar eller administratör.

![Användare med endast vyer](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Logga in på Ambari som en kluster-användare

Vi har tilldelat vår Azure AD-domänanvändare ”hiveuser2” till den *Klusteranvändaren* roll. Den här rollen kan komma åt instrumentpanelen och alla menyalternativ. En kluster-användare har färre tillåtna alternativ än en administratör. Till exempel hiveuser2 kan visa konfigurationer för varje tjänst, men kan inte redigeras.

![Användare med rollen kluster](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera principer för Apache Hive i HDInsight med ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Hantera ESP HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md)
* [Använda Apache Hive-vyn med Apache Hadoop i HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synkronisera Azure AD-användare i klustret](hdinsight-sync-aad-users-to-cluster.md)
