---
title: Synkronisera Azure Active Directory-användare med ett kluster – Azure HDInsight
description: Synkronisera autentiserade användare från Azure Active Directory till ett kluster.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 08ae8bb1f1ac9b718996d1d4715f28d025aeebcb
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591616"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synkronisera Azure Active Directory-användare till ett HDInsight-kluster

[Domänanslutna HDInsight-kluster](hdinsight-domain-joined-introduction.md) kan använda stark autentisering med Azure Active Directory (Azure AD)-användare, samt använda *rollbaserad åtkomstkontroll* (RBAC)-principer. När du lägger till användare och grupper till Azure AD kan du synkronisera de användare som behöver åtkomst till ditt kluster.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har gjort det, [skapa ett domänanslutet HDInsight-kluster](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Lägg till nya Azure AD-användare

Öppna Ambari-Webbgränssnittet för att visa dina värdar. Varje nod uppdateras med nya inställningar för obevakad uppgradering.

1. I den [Azure-portalen](https://portal.azure.com), gå till Azure AD-katalog som är associerade med din domänanslutet kluster.

2. Välj **alla användare** i den vänstra menyn, Välj **ny användare**.

    ![Fönstret för alla användare](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. Fyll i användarformuläret för nya. Välj grupper som du skapade för att tilldela kluster-baserade behörigheter. I det här exemplet skapar du en grupp med namnet ”HiveUsers”, som du kan tilldela nya användare. Den [exempel instruktioner](hdinsight-domain-joined-configure.md) för att skapa ett domänanslutet kluster omfattar att lägga till två grupper `HiveUsers` och `AAD DC Administrators`.

    ![Den nya användaren rutan](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. Välj **Skapa**.

## <a name="use-the-ambari-rest-api-to-synchronize-users"></a>Använda Ambari REST API för att synkronisera användare

Användargrupper som anges när klustret skapas synkroniseras just då. Användarsynkronisering sker automatiskt en gång i timmen. Synkronisera användare direkt eller använda Ambari REST API för att synkronisera en annan grupp än de grupper som anges när klustret skapas.

Följande metod använder INLÄGG med Ambari REST API. Mer information finns i [hantera HDInsight-kluster med hjälp av Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Ansluta till klustret med SSH](hdinsight-hadoop-linux-use-ssh-unix.md). Översiktsfönstret för ditt kluster i Azure-portalen, väljer du den **Secure Shell (SSH)** knappen.

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/ssh.png)

2. Kopiera de visade `ssh` kommandot och klistra in den i SSH-klienten. Ange den ssh lösenord när du tillfrågas.

3. När de har autentiserat, anger du följande kommando:

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    Svaret ska se ut så här:

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. Om du vill se synkroniseringsstatus för, kör du en ny `curl` kommando med den `href` värdet som returneras från föregående kommando:

    ```bash
    curl -u admin:<YOUR PASSWORD> http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1
    ```
    
    Svaret ska se ut så här:
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
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

5. Det här resultatet visar att statusen är **Slutför**, en ny användare har skapats och att användaren har tilldelats ett medlemskap. I det här exemplet tilldelas användaren till ”HiveUsers”-synkroniseras LDAP-gruppen, eftersom användaren har lagts till samma grupp i Azure AD.

> [!NOTE]
> Den tidigare metoden att endast synkronisera Azure AD-grupper som anges i den **ha åtkomst till användargrupper** egenskapen för domäninställningarna när klustret skapas. Mer information finns i [skapar ett HDInsight-kluster](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Kontrollera det nyligen tillagda Azure AD-användare

Öppna den [Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md) att kontrollera att den nya Azure AD-användare har lagts till. Komma åt Ambari-Webbgränssnittet genom att bläddra till **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Ange kluster administratörens användarnamn och lösenord.

1. Ambari-instrumentpanelen väljer du **hantera Ambari** under den **admin** menyn.

    ![Hantera Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. Välj **användare** under den **användare + grupphantering** grupp i menyn till vänster på sidan.

    ![Menyalternativet för användare](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. Den nya användaren bör visas i tabellen användare. Typen har angetts till `LDAP` snarare än `Local`.

    ![Användarsidan](./media/hdinsight-sync-aad-users-to-cluster/users.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Logga in på Ambari som den nya användaren

När den nya användaren (eller domänanvändare) loggar du in på Ambari, använder de sina fullständiga Azure AD användarautentiseringsuppgifter namn och domän.  Ambari visar ett användaralias, vilket är visningsnamnet för användaren i Azure AD. Den nya användaren i exemplet har namnet `hiveuser3@contoso.com`. Ambari, den nya användaren visas i som `hiveuser3` men en användarloggar in Ambari som `hiveuser3@contoso.com`.

## <a name="see-also"></a>Se också

* [Konfigurera Hive-policyer i domänanslutna HDInsight](hdinsight-domain-joined-run-hive.md)
* [Hantera domänanslutna HDInsight-kluster](hdinsight-domain-joined-manage.md)
* [Auktorisera användare för Ambari](hdinsight-authorize-users-to-ambari.md)
