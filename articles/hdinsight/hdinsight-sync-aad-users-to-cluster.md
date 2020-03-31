---
title: Synkronisera Azure Active Directory-användare med HDInsight-kluster
description: Synkronisera autentiserade användare från Azure Active Directory till ett HDInsight-kluster.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 299d242c38152db6a471159d1f3d2803598c1832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744859"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synkronisera Azure Active Directory-användare med ett HDInsight-kluster

[HDInsight-kluster med ESP (Enterprise Security Package)](hdinsight-domain-joined-introduction.md) kan använda stark autentisering med Azure Active Directory-användare (Azure AD) samt använda *RBAC-principer (Role-Based Access Control).* När du lägger till användare och grupper i Azure AD kan du synkronisera de användare som behöver åtkomst till klustret.

## <a name="prerequisites"></a>Krav

Om du inte redan har gjort det [skapar du ett HDInsight-kluster med Enterprise Security Package](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Lägga till nya Azure AD-användare

Öppna webbgränssnittet för Ambari om du vill visa dina värdar. Varje nod uppdateras med nya obevakade uppgraderingsinställningar.

1. Från [Azure-portalen](https://portal.azure.com)navigerar du till Azure AD-katalogen som är associerad med ESP-klustret.

2. Välj **Alla användare** på menyn till vänster och välj sedan Ny **användare**.

    ![Azure portal användare och grupper alla](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Fyll i det nya användarformuläret. Välj grupper som du har skapat för att tilldela klusterbaserade behörigheter. Skapa i det här exemplet en grupp med namnet "HiveUsers", som du kan tilldela nya användare till. [Exempelinstruktionerna](hdinsight-domain-joined-configure.md) för att skapa ett `HiveUsers` ESP-kluster är att lägga till två grupper och `AAD DC Administrators`.

    ![Välj grupper för användarfönstret i Azure Portal](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Välj **Skapa**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Använda Apache Ambari REST API för att synkronisera användare

Användargrupper som angetts när klustret skapades synkroniseras vid den tidpunkten. Användarsynkronisering sker automatiskt en gång i timmen. Om du vill synkronisera användarna omedelbart eller synkronisera en annan grupp än de grupper som anges när klustret skapades använder du Ambari REST API.

Följande metod använder POST med Ambari REST API. Mer information finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Använd kommandot ssh för att ansluta till [klustret.](hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan `CLUSTERNAME` genom att ersätta med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. När du har autentiserat anger du följande kommando:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    Svaret ska se ut så här:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. Kör ett nytt `curl` kommando om du vill visa synkroniseringsstatus:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    Svaret ska se ut så här:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Det här resultatet visar att statusen är **SLUTFÖR**, en ny användare skapades och användaren tilldelades ett medlemskap. I det här exemplet tilldelas användaren till den synkroniserade LDAP-gruppen "HiveUsers", eftersom användaren lades till i samma grupp i Azure AD.

    > [!NOTE]  
    > Den tidigare metoden synkroniserar bara Azure AD-grupper som anges i egenskapen **Access-användargrupp** för domäninställningarna när klustret skapas. Mer information finns i [skapa ett HDInsight-kluster](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Verifiera den nyligen tillagda Azure AD-användaren

Öppna [webbgränssnittet Apache Ambari](hdinsight-hadoop-manage-ambari.md) för att kontrollera att den nya Azure AD-användaren har lagts till. Öppna Ambari Web UI genom **`https://CLUSTERNAME.azurehdinsight.net`** att bläddra till . Ange användarnamn och lösenord för klusteradministratören.

1. På Ambari-instrumentpanelen väljer du **Hantera Ambari** under **admin-menyn.**

    ![Apache Ambari instrumentpanel Hantera Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Välj **Användare** under menygruppen **Användare + Grupphantering** till vänster på sidan.

    ![Menyn ANVÄNDARE och grupper i HDInsight](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Den nya användaren ska visas i tabellen Användare. Typen är inställd `LDAP` på `Local`i stället för .

    ![Översikt över hdinsight aad-användare](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Logga in på Ambari som ny användare

När den nya användaren (eller någon annan domänanvändare) loggar in på Ambari använder de sitt fullständiga Azure AD-användarnamn och domänautentiseringsuppgifter.  Ambari visar ett användaralias, som är visningsnamnet för användaren i Azure AD.
Den nya exempelanvändaren `hiveuser3@contoso.com`har användarnamnet . I Ambari, denna nya `hiveuser3` användare dyker upp som men `hiveuser3@contoso.com`användaren loggar in Ambari som .

## <a name="see-also"></a>Se även

* [Konfigurera Apache Hive-principer i HDInsight med ESP](hdinsight-domain-joined-run-hive.md)
* [Hantera HDInsight-kluster med ESP](hdinsight-domain-joined-manage.md)
* [Auktorisera användare till Apache Ambari](hdinsight-authorize-users-to-ambari.md)
