---
title: Azure-snabbstart – Skapa ett Azure Automation-konto | Microsoft Docs
description: Lär dig hur du skapar ett Azure Automation-konto och kör en runbook
services: automation
author: csand-msft
ms.author: csand
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: automation
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7f7905a4b09e685ad98a1663333aa32bc1d7ae90
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009520"
---
# <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto

Azure Automation-konton kan skapas via Azure. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa och konfigurera Automation-konton och relaterade resurser. Den här snabbstarten går igenom hur du skapar ett Automation-konto och kör en runbook i kontot.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in i Azure på https://portal.azure.com

## <a name="create-automation-account"></a>Skapa ett Automation-konto

1. Klicka på knappen **Skapa en resurs** längst upp till vänster i Azure.

1. Välj **Hanteringsverktyg** och välj sedan **Automation**.

1. Ange kontoinformation. För **Skapa Kör som-konto i Azure**, väljer du **Ja** så att artefakterna för att förenkla autentisering till Azure aktiveras automatiskt. Observera att du inte kan ändra namnet på ett Automation-konto som du skapar i efterhand. *Namn är unikt för varje region och resursgrupp. Namn för Automation-konton som har tagits bort är kanske inte omedelbart tillgängligt.* Ett Automation-konto kan hantera resurser i alla regioner och prenumerationer för en viss klientorganisation. När du är färdig klickar du på **Skapa** för att starta distributionen av Automation-kontot.

    ![Ange information om ditt Automation-konto på sidan](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > En uppdaterad lista över platser du kan distribuera ett Automation-konto på finns i [Tillgängliga produkter per region](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=automation&regions=all).

1. När distributionen är klar klickar du på **Alla tjänster**, väljer **Automation-konton** och väljer sedan det Automation-konto som du skapade.

    ![Översikt över Automation-konton](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Kör en runbook

Kör en självstudierunbook.

1. Klicka på **Runbooks** under **PROCESSAUTOMATISERING**. En lista med runbooks visas. Som standard är flera självstudierunbooks aktiverade i kontot.

    ![Runbook-lista för Automation-konto](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Välj följande runbook: **AzureAutomationTutorialScript**. Den här åtgärden öppnar sidan för runbook-översikt.

    ![Runbook-översikt](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Klicka på **Starta** och på sidan **Starta Runbook** klickar du på **OK** för att starta en runbook.

    ![Sida för Runbook-jobb](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Efter **Jobbstatus** ändrats till **Körs** klickar du på **Utdata** eller **Alla loggar** för att se utdata för runbook-jobbet. För denna självstudierunbook är utdata en lista över dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett Automation-konto, startat ett runbook-jobb och sett jobbresultaten. Om du vill veta mer om Azure Automation kan du fortsätta att använda snabbstarten för att skapa din första runbook.

> [!div class="nextstepaction"]
> [Automation-Snabbstart – skapa Runbook](./automation-quickstart-create-runbook.md)

