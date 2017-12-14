---
title: "Auktorisera användare för Ambari - vyer i Azure HDInsight | Microsoft Docs"
description: "Hur du hanterar Ambari användar- och behörigheter för domänanslutna HDInsight-kluster."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 02761b0e4a25e9aa2312d66bb8c6ab861fb0c3cc
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="authorize-users-for-ambari-views"></a>Auktorisera användare för Ambari Views

[Domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-introduction.md) innehåller företagsklass funktioner, inklusive Azure Active Directory-baserad autentisering. Du kan synkronisera nya användare
<!-- [synchronize new users](hdinsight-sync-aad-users-to-cluster.md) --> added to Azure AD groups that have been provided access to the cluster, allowing those specific users to perform certain actions. Working with users, groups, and permissions in Ambari is supported for both domain-joined HDInsight cluster and standard HDInsight cluster.

Active Directory-användare kan logga in på noderna i med sina domänautentiseringsuppgifter. De kan också använda sina domänautentiseringsuppgifter för att autentisera klustret interaktioner med andra godkända slutpunkter som Hue, Ambari-vyer, ODBC, JDBC, PowerShell och REST API: er.

> [!WARNING]
> Ändra inte lösenordet för Ambari watchdog (hdinsightwatchdog) på Linux-baserade HDInsight-kluster. Ändra lösenord bryts möjligheten att använda script actions eller utföra skalning åtgärder med ditt kluster.

Om du inte redan har gjort det, Följ [instruktionerna](./domain-joined/apache-domain-joined-configure.md) att etablera ett nytt kluster som ingår i domänen.

## <a name="access-the-ambari-management-page"></a>Komma åt sidan för hantering av Ambari

För att nå den **sidan för hantering av Ambari** på den [Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md), bläddra till  **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Ange klustrets administratörsanvändarnamn och lösenord som du definierade när klustret skapas. Välj därefter Ambari-instrumentpanelen **hantera Ambari** under den **admin** menyn:

![Hantera Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-hive-views"></a>Bevilja behörighet till Hive-vyer

Ambari levereras med Visa instanser för Hive och Tez, bland annat. Om du vill bevilja åtkomst till en eller flera Hive Visa instanser, gå till den **Ambari hanteringssidan**.

1. Sidan Välj den **vyer** länken under den **vyer** menyrubrik till vänster.

    ![Vyer länk](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. På sidan vyer Expandera den **HIVE** rad. Det finns en standard Hive-vy som skapades när tjänsten Hive har lagts till i klustret. Du kan också skapa flera Hive Visa instanser efter behov. Välj en Hive-vy:

    ![Vyer - Hive-vyn](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Bläddra till längst ned på sidan Visa. Under den *behörigheter* avsnittet har du två alternativ för att bevilja domänanvändare deras behörigheter till vyn:

**Bevilja behörighet till dessa användare** ![bevilja behörighet till dessa användare](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Bevilja behörighet till dessa grupper** ![bevilja behörighet till dessa grupper](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

4. Om du vill lägga till en användare, Välj den **Lägg till användare** knappen.

    * Börjar skriva användarnamnet och du ser en listruta över tidigare definierade namn.

    ![Användaren autocompletes](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

    * Välj eller har skrivit användarnamnet. Om du vill lägga till det här användarnamnet som en ny användare väljer den **ny** knappen.

    * Välj för att spara ändringarna i **blå kryssruta**.

    ![Användaren angav](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

5. Om du vill lägga till en grupp, Välj den **Lägg till grupp** knappen.

    * Börja skriva gruppnamnet. Processen för att välja en befintliga gruppnamnet eller lägga till en ny grupp är desamma som för att lägga till användare.
    * Välj för att spara ändringarna i **blå kryssruta**.

    ![Grupp som anges](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Lägga till användare direkt i en vy är användbart när du vill tilldela behörigheter till en användare kan använda för att visa men inte vill att de ska vara medlem i en grupp som har ytterligare behörighet. Det kan vara enklare att tilldela behörigheter till grupper för att minska den administrativa kostnader.

## <a name="grant-permissions-to-tez-views"></a>Bevilja behörighet till Tez vyer

Tez Visa instanser Tillåt användare att övervaka och felsöka alla Tez-jobb, som Hive-frågor och Pig-skript. Det finns en Tez visa standardinstansen som skapas när klustret har tillhandahållits.

Om du vill tilldela användare och grupper till en vy Tez-instans, expandera den **TEZ** raden på sidan vyer som beskrivits ovan.

![Vyer - Tez-vyn](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Lägg till användare eller grupper, Upprepa steg 3-5 i föregående avsnitt.

## <a name="assign-users-to-roles"></a>Tilldela användare till roller

Det finns fem säkerhetsroller för användare och grupper i listan i fallande behörigheter för åtkomst:

* Klusteradministratören
* Kluster-operatorn
* Tjänstadministratör
* Tjänsten Operator
* Kluster-användare

För att hantera roller, gå till den **sidan för hantering av Ambari**och välj den **roller** länka inom den *kluster* grupp till vänster.

![Roller menyn länk](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Om du vill se en lista över behörigheter som tilldelats varje roll, klickar du på det blå frågetecknet bredvid den **roller** huvud på sidan roller.

![Roller menyn länk](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Det finns två olika vyer som du kan använda för att hantera roller för användare och grupper på den här sidan: Block och lista.

### <a name="block-view"></a>Visa block

Visa Block visas varje roll i en egen rad, och ger den **tilldela roller till dessa användare** och **tilldela roller till dessa grupper** alternativ som beskrivs ovan.

![Roller blockera vy](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Listvy

Listvyn ger snabb redigering funktioner i två kategorier: användare och grupper.

* Kategorin användare i listan visar en lista över alla användare, så att du kan välja en roll för varje användare i listrutan.

    ![Roller listvy - användare](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* Kategorin grupper i listan visas alla grupper och den roll som tilldelats till varje grupp. I vårt exempel listan över grupper som ska synkroniseras från Azure AD-grupper som anges i den **åtkomst användargrupp** -egenskapen för klustrets Domäninställningar. Se [skapa HDInsight-kluster](./domain-joined/apache-domain-joined-configure.md#create-an-hdinsight-cluster-in-the-vnet).

    ![Roller listvy - grupper](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    I bilden ovan gruppen ”hiveusers” tilldelas den *klustret användaren* roll. Det här är en skrivskyddad roll som användarna av gruppen att visa men inte ändra tjänstkonfiguration och klustret mått.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Logga in på Ambari som en användare med endast visning

Vi har tilldelats vårt Azure AD-domänanvändare ”hiveuser1” behörigheter Hive och Tez vyer. Användaren omdirigeras till sidan Ambari-vyer när vi starta Ambari-Webbgränssnittet och ange den här användarens autentiseringsuppgifter för domänen (Azure AD-användarnamn i postformatet för e-och lösenord). Härifrån kan välja användaren alla tillgängliga vyer. Användaren kan inte finns någon annan del av platsen, inklusive instrumentpanelen, tjänster, värdar, aviseringar eller admin-sidor.

![Användare med endast vyer](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Logga in på Ambari som en användare i klustret

Vi har tilldelats vårt Azure AD-domänanvändare ”hiveuser2” den *klustret användaren* roll. Den här rollen kan komma åt instrumentpanelen och alla menyalternativ. En användare i klustret har färre tillåtna alternativ än en administratör. Till exempel hiveuser2 kan visa konfigurationer för varje tjänst, men redigeras inte.

![Användare med klustret användarroll](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Nästa steg

* [Konfigurera principer för Hive i HDInsight-domänansluten](./domain-joined/apache-domain-joined-run-hive.md)
* [Hantera domänanslutna HDInsight-kluster](./domain-joined/apache-domain-joined-manage.md)
* [Använd vyn Hive med Hadoop i HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)

<!-- * [Synchronize Azure AD users to the cluster](hdinsight-sync-aad-users-to-cluster.md) -->
