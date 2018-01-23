---
title: "Synkronisera Azure Active Directory-användare till ett kluster - Azure HDInsight | Microsoft Docs"
description: "Synkronisera autentiserade användare från Azure Active Directory till ett kluster."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: ad1586a6e358dfb1ca2391474ecdd9bee2f6226d
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synkronisera Azure Active Directory-användare till ett HDInsight-kluster

[Domänanslutna HDInsight-kluster](hdinsight-domain-joined-introduction.md) kan använda stark autentisering med Azure Active Directory (Azure AD)-användare, samt använda *rollbaserad åtkomstkontroll* (RBAC)-principer. När du lägger till användare och grupper till Azure AD kan du synkronisera de användare som behöver åtkomst till klustret.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har gjort det, [skapa HDInsight-kluster domänanslutna](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Lägga till nya Azure AD-användare

Öppna Ambari-Webbgränssnittet för att visa dina värdar. Varje nod uppdateras med nya inställningar för obevakad uppgradering.

1. I den [Azure-portalen](https://portal.azure.com), gå till Azure AD-katalog som är associerade med klustret ingår i domänen.

2. Välj **alla användare** i den vänstra menyn, Välj **ny användare**.

    ![Rutan för alla användare](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. Fyll i användarformuläret för nya. Välj grupper som du skapade för att tilldela behörigheter för kluster-baserade. I det här exemplet skapar du en grupp med namnet ”HiveUsers”, som du kan tilldela nya användare. Den [exempel instruktioner](hdinsight-domain-joined-configure.md) omfattar att lägga till två grupper för att skapa kluster domänanslutna `HiveUsers` och `AAD DC Administrators`.

    ![Den nya användaren rutan](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. Välj **Skapa**.

## <a name="use-the-ambari-rest-api-to-synchronize-users"></a>Använda Ambari REST API för att synkronisera användare

Användargrupper som anges när klustret skapas är synkroniserade vid den tiden. Användarsynkronisering sker automatiskt en gång i timmen. Synkronisera användare direkt eller använda Ambari REST API för att synkronisera en annan grupp än de grupper som anges när klustret skapas.

Följande metod använder POST med Ambari REST API. Mer information finns i [hantera HDInsight-kluster med Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Ansluta till klustret med SSH](hdinsight-hadoop-linux-use-ssh-unix.md). I översiktsfönstret för klustret i Azure portal väljer du den **SSH (Secure Shell)** knappen.

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/ssh.png)

2. Kopiera de visade `ssh` kommandot och klistra in den i SSH-klienten. Ange den ssh lösenord när du uppmanas.

3. Efter autentisering, anger du följande kommando:

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

4. Om du vill visa synkroniseringsstatus för att köra en ny `curl` kommando med den `href` värdet som returneras från det föregående kommandot:

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

5. Det här resultatet visar att statusen är **Slutför**, en ny användare har skapats och att användaren har tilldelats ett medlemskap. I det här exemplet tilldelas användaren till ”HiveUsers”-synkroniserade LDAP-gruppen, eftersom användaren har lagts till samma grupp i Azure AD.

> [!NOTE]
> Metoden föregående synkroniserar endast Azure AD-grupper som anges i den **åtkomst användargrupp** -egenskapen för Domäninställningar när klustret skapas. Mer information finns i [skapar ett HDInsight-kluster](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Kontrollera den nytillagda Azure AD-användare

Öppna den [Ambari-Webbgränssnittet](hdinsight-hadoop-manage-ambari.md) att kontrollera att den nya Azure AD-användare har lagts till. Komma åt Ambari-Webbgränssnittet genom att bläddra till  **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Ange klustrets administratörsanvändarnamn och lösenord.

1. Ambari-instrumentpanelen, Välj **hantera Ambari** under den **admin** menyn.

    ![Hantera Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. Välj **användare** under den **användar- + grupphantering** menyn grupp till vänster på sidan.

    ![Menyalternativet för användare](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. Den nya användaren ska visas i tabellen användare. Typen har angetts till `LDAP` snarare än `Local`.

    ![Användarsidan](./media/hdinsight-sync-aad-users-to-cluster/users.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Logga in på Ambari som den nya användaren

När den nya användaren (eller någon annan domänanvändare) loggar du in på Ambari, kan de använda sina kompletta Azure AD användarautentiseringsuppgifter namn och domän.  Ambari visar ett användaralias är visningsnamnet för användaren i Azure AD. Den nya användaren i exemplet har namnet `hiveuser3@contoso.com`. Ambari, den nya användaren visas i som `hiveuser3` men en användarloggar in Ambari som `hiveuser3@contoso.com`.

## <a name="see-also"></a>Se också

* [Konfigurera principer för Hive i HDInsight-domänansluten](hdinsight-domain-joined-run-hive.md)
* [Hantera domänanslutna HDInsight-kluster](hdinsight-domain-joined-manage.md)
* [Auktorisera användare till Ambari](hdinsight-authorize-users-to-ambari.md)
