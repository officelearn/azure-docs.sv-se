---
title: 'Självstudiekurs: Konfigurera Apache Ambari-e-postmeddelanden i Azure HDInsight'
description: I den här artikeln beskrivs hur du använder SendGrid med Apache Ambari för e-postmeddelanden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 21376eb40fb40abe67f7e03d15aabd7d89ea62f8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082316"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Självstudiekurs: Konfigurera Apache Ambari-e-postmeddelanden i Azure HDInsight

I den här självstudien konfigurerar du Apache Ambari-e-postmeddelanden med SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) förenklar hanteringen och övervakningen av ett HDInsight-kluster genom att tillhandahålla ett lättanvänt webbgränssnitt och REST API. Ambari ingår i HDInsight-kluster och används för att övervaka klustret och göra konfigurationsändringar. [SendGrid](https://sendgrid.com/solutions/) är en kostnadsfri molnbaserad e-posttjänst som ger tillförlitlig transaktionsbaserad e-postleverans, skalbarhet och realtidsanalys tillsammans med flexibla API:er som gör anpassad integrering enkel. Azure-kunder kan låsa upp 25 000 kostnadsfria e-postmeddelanden varje månad.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skaffa Användarnamn på Sendgrid
> * Konfigurera Apache Ambari-e-postmeddelanden

## <a name="prerequisites"></a>Krav

* Ett SendGrid-e-postkonto. Se [Hur du skickar e-post med SendGrid med Azure](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email) för instruktioner.

* Ett HDInsight-kluster. Se [Skapa Apache Hadoop-kluster med Azure-portalen](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Skaffa SendGrid-användarnamn

1. Från [Azure-portalen](https://portal.azure.com)navigerar du till din SendGrid-resurs.

1. På sidan Översikt väljer du **Hantera**för att gå till SendGrid-webbsidan för ditt konto.

    ![SendGrid översikt i Azure Portal](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. Från den vänstra menyn navigerar du till ditt kontonamn och sedan **kontoinformation**.

    ![Navigering på Instrumentpanelen i SendGrid](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. På sidan **Kontoinformation** registrerar du **användarnamnet**.

    ![SendGrid-kontouppgifter](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Konfigurera E-postmeddelande för Ambari

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`, var `CLUSTERNAME` är namnet på klustret.

1. Välj **Hantera meddelanden**i listrutan **Åtgärder** .

1. Välj **+** ikonen i fönstret **Hantera aviseringar.**

    ![Ambari skapa varningsmeddelande](./media/apache-ambari-email/azure-portal-create-notification.png)

1. Ange följande information i dialogrutan **Skapa varningsmeddelande:**

    |Egenskap |Beskrivning |
    |---|---|
    |Namn|Ange ett namn för meddelandet.|
    |Grupper|Konfigurera efter behov.|
    |Severity|Konfigurera efter behov.|
    |Beskrivning|Valfri.|
    |Metod|Lämna på **E-post**.|
    |E-post till|Ange e-post för att ta emot meddelanden, avgränsade med ett kommatecken.|
    |SMTP-Server|`smtp.sendgrid.net`|
    |SMTP-port|25 eller 587 (för okrypterade/TLS-anslutningar).|
    |E-post från|Ange en e-postadress. Adressen behöver inte vara äkta.|
    |Använd autentisering|Markera den här kryssrutan.|
    |Användarnamn|Ange användarnamnet SendGrid.|
    |lösenord|Ange det lösenord som du använde när du skapade SendGrid-resursen i Azure.|
    |Lösenordsbekräftelse|Ange lösenordet igen.|
    |Starta TLS|Markera den här kryssrutan|

    ![Ambari skapa varningsmeddelande](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Välj **Spara**. Du kommer tillbaka till fönstret **Hantera aviseringar.**

1. Välj **Stäng**i fönstret **Hantera aviseringar.**

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att konfigurera Apache Ambari e-postmeddelanden med SendGrid. Använd följande om du vill veta mer om Apache Ambari:

* [Hantera HDInsight-kluster med hjälp av Apache Ambari-webbgränssnittet](./hdinsight-hadoop-manage-ambari.md)

* [Skapa ett varningsmeddelande](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)
