---
title: Azure DevOps-uppgift för Azure Data Explorer
description: I det här avsnittet får du lära dig att skapa en releasepipeline och distribuera
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: a70a887ccb19d9c1cbdb5f8ebf6aa8d4b25a0dfd
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65161077"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure DevOps-uppgift för Azure Data Explorer

[Azure DevOps-tjänster](https://azure.microsoft.com/services/devops/) ger utveckling samarbetsverktyg som högpresterande pipelines, kostnadsfria privata Git-lagringsplatser, konfigurerbara Kanban-kort och omfattande automatiserad och kontinuerlig testfunktionerna. [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) är en Azure DevOps-funktion som hjälper dig att hantera CI/CD för att distribuera din kod med höga prestanda pipelines som fungerar med alla språk, plattformar och molnet.
[Azure Data Explorer - Admin-kommandon](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) är Azure Pipelines uppgiften som hjälper dig att skapa publiceringskanaler och distribuera din databas ändras till Datautforskaren i Azure-databaser. Den är tillgänglig utan kostnad i den [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Det här dokumentet beskrivs ett enkelt exempel på användning av den **Datautforskaren i Azure – administratören kommandon** aktiviteten för att distribuera ditt schema ändras till din databas. Fullständig CI/CD-pipelines finns i [dokumentation för Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure Data Explorer konfiguration:
    * En [Datautforskaren i Azure-kluster och databas](/azure/data-explorer/create-cluster-database-portal)
    * Skapa Azure Active Directory (Azure AD)-app genom att [etablera en Azure AD-program](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Bevilja åtkomst till din Azure AD-App i Azure Data Explorer databasen genom att [hantera databasbehörigheter för Azure Data Explorer](/azure/data-explorer/manage-database-permissions).
* Konfigurationen av Azure DevOps:
    * [Registrera dig för en kostnadsfri organisation](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Skapa en organisation](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Skapa ett projekt i Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Kod med Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Skapa mappar

Skapa följande exempel mappar (*Functions*, *principer*, *tabeller*) på Git-lagringsplatsen. Kopiera filer från [här](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) till respektive mappar som visas nedan och spara ändringarna. Exempelfilerna tillhandahålls för att köra följande arbetsflöde.

![Skapa mappar](media/devops/create-folders.png)

> [!TIP]
> När du skapar ett eget arbetsflöde, rekommenderar vi att din kod idempotenta. Till exempel använda [.create och slå samman tabell](/azure/kusto/management/tables#create-merge-tables) i stället för [.create tabell](/azure/kusto/management/tables#create-table), och använda [.create-eller alter](/azure/kusto/management/functions#create-or-alter-function) i stället för [.create](/azure/kusto/management/functions#create-function) funktionen.

## <a name="create-a-release-pipeline"></a>Skapa en versionspipeline

1. Logga in på din [Azure DevOps-organisation](https://dev.azure.com/).
1. Välj **Pipelines** > **versioner** vänster meny och väljer **ny pipeline**.

    ![Ny pipeline](media/devops/new-pipeline.png)

1. Den **New viktig pipeline** öppnas. I den **Pipelines** fliken den **Välj en mall** fönstret Välj **tom jobbet**.

     ![Välj en mall](media/devops/select-template.png)

1. Välj **scenen** knappen. I **scenen** fönstret Lägg till den **namn på etapp**. Välj **spara** att spara din pipeline.

    ![Namn på scenen](media/devops/stage-name.png)

1. Välj **lägga till en artefakt** knappen. I den **lägga till en artefakt** fönstret markerar du databasen var koden finns, fyller du i relevant information och klicka på **Lägg till**. Välj **spara** att spara din pipeline.

    ![Lägg till en artefakt](media/devops/add-artifact.png)

1. I den **variabler** fliken **+ Lägg till** att skapa en variabel för **slutpunkts-URL** som ska användas i uppgiften. Skriv den **namn** och **värdet** för slutpunkten. Välj **spara** att spara din pipeline. 

    ![Skapa variabel](media/devops/create-variable.png)

    Du hittar din Endpoint_URL översiktssidan för din **Azure Data Explorer klustret** i Azure portal innehåller Azure Data Explorer-kluster-URI. Skapa URI: N i formatet `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Till exempel, https://kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![Azure Data Explorer kluster-URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Skapa uppgifter för att distribuera

1. I den **Pipeline** fliken, klickar du på **1 jobb, 0 uppgift** att lägga till aktiviteter. 

    ![Lägga till aktiviteter](media/devops/add-task.png)

1. Skapa tre uppgifter att distribuera **tabeller**, **Functions**, och **principer**, i den här ordningen. 

1. I den **uppgifter** fliken **+** av **agentjobbet**. Sök efter **Azure-datautforskaren**. I **Marketplace**, installera den **Datautforskaren i Azure – administratören kommandon** tillägget. Välj **Lägg till** i **kör kommandot för Azure Data Explorer**.

     ![Lägg till kommandon för administratör](media/devops/add-admin-commands.png)

1. Klicka på **Kusto-kommandot** till vänster och uppdatera uppgiften med följande information:
    * **Visningsnamn**: Namnet på aktiviteten
    * **Filsökväg**: I den **tabeller** uppgift, ange */Tables/*.csl eftersom tabellen skapas filer finns i den *tabell* mapp.
    * **Slutpunkts-URL**: Ange den `EndPoint URL`variabel har skapats i föregående steg.
    * Välj **Använd tjänstslutpunkt** och välj **+ ny**.

    ![Uppdatera Kusto-kommandouppgiften](media/devops/kusto-command-task.png)

1. Ange följande information i den **lägga till Azure Data Explorer tjänstanslutning** fönster:

    |Inställning  |Föreslaget värde  |
    |---------|---------|
    |**Anslutningsnamn**     |    Ange ett namn som identifierar den här tjänstens slutpunkt     |
    |**Kluster-Url**    |    Värdet finns i översiktsavsnittet för ditt Azure Data Explorer-kluster i Azure portal | 
    |**Id för tjänstens huvudnamn**    |    Ange det AAD-ID (som skapats som krav)     |
    |**App-nyckel för tjänstens huvudnamn**     |    Ange AAD-App-nyckel (som skapats som krav)    |
    |**Id för AAD-klient**    |      Ange din AAD-klient (t.ex microsoft.com contoso.com...)    |

    Välj **Tillåt alla pipelines så att de använder den här anslutningen** kryssrutan. Välj **OK**.

    ![Lägg till tjänstanslutning](media/devops/add-service-connection.png)

1. Upprepa steg 1-5 en annan två gånger för att distribuera filer från den *Functions* och *principer* mappar. Välj **Spara**. I den **uppgifter** fliken kan du läsa de tre aktiviteter som skapats: **Distribuera tabeller**, **distribuera Functions**, och **distribuera principer**.

    ![Distribuera alla mappar](media/devops/deploy-all-folders.png)

1. Välj **+ Release** > **skapa version** att skapa en version.

    ![Skapa en version](media/devops/create-release.png)

1. I den **loggar** och kontrollera distributionens status är lyckad.

    ![Distributionen är klar](media/devops/deployment-successful.png)

Du har nu slutfört skapandet av en releasepipeline för distribution av tre uppgifter till Förproduktion.