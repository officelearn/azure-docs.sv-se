---
title: Azure-snabbstart – Skapa ett Azure Automation-konto | Microsoft Docs
description: Lär dig hur du skapar ett Azure Automation-konto och kör en runbook
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7704f080b7c1878f2fa2b079a1f242c8c2cc87a9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536989"
---
# <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto

Du kan skapa ett Azure Automation-konto via Azure med hjälp av Azure-portalen, ett webbläsarbaserat användargränssnitt som tillåter åtkomst till ett antal resurser. Ett Automation-konto kan hantera resurser i alla regioner och prenumerationer för en viss klient. 

Den här snabbstarten hjälper dig att skapa ett Automation-konto och köra en runbook i kontot. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

[Logga in på Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Skapa ett Automation-konto

1. Välj ett namn för ditt Azure-konto. Automation-kontonamn är unika per region och resursgrupp. Namn för Automation-konton som har tagits bort kanske inte är omedelbart tillgängliga.

    > [!NOTE]
    > Du kan inte ändra kontonamnet när det har angetts i användargränssnittet. 

2. Klicka på knappen **Skapa en resurs** som finns i det övre vänstra hörnet av Azure-portalen.

3. Välj **IT-& hanteringsverktyg**och välj sedan **Automation**.

4. Ange kontoinformationen, inklusive det valda kontonamnet. För **Skapa Kör som-konto i Azure**, väljer du **Ja** så att artefakterna för att förenkla autentisering till Azure aktiveras automatiskt. När informationen är klar klickar du på **Skapa** för att starta distributionen av Automation-konto.

    ![Ange information om ditt Automation-konto på sidan](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > En uppdaterad lista över platser som du kan distribuera ett Automation-konto till finns i [Produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).

5. När distributionen är klar klickar du på **Alla tjänster**.

6. Välj **Automation-konton** och välj sedan det Automation-konto som du har skapat.

    ![Översikt över Automation-konton](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Kör en runbook

Kör en självstudierunbook.

1. Klicka på **Runbooks** under **ProcessAutomation**. En lista med runbooks visas. Som standard är flera självstudieprogram aktiverade i kontot.

    ![Runbook-lista för Automation-konto](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Välj följande runbook: **AzureAutomationTutorialScript**. Den här åtgärden öppnar sidan för runbook-översikt.

    ![Runbook-översikt](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Klicka på **Starta** och på sidan Starta Runbook klickar du på **OK** för att starta en runbook.

    ![Sida för Runbook-jobb](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. När jobbstatusen `Running`har blivit klickar du på **Utdata** eller **Alla loggar** för att visa utdata för runbook-jobb. För denna självstudierunbook är utdata en lista över dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett Automation-konto, startat ett runbook-jobb och sett jobbresultaten. Om du vill veta mer om Azure Automation kan du fortsätta att använda snabbstarten för att skapa din första runbook.

> [!div class="nextstepaction"]
> [Snabbstart för Automation – Skapa Runbook](./automation-quickstart-create-runbook.md)

