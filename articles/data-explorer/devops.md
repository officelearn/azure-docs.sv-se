---
title: Azure DevOps-uppgift för Azure Data Explorer
description: I det här avsnittet lär du dig att skapa en versionspipeline och distribuera
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472051"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure DevOps-uppgift för Azure Data Explorer

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) tillhandahåller verktyg för utvecklingssamarbete, till exempel högpresterande pipelines, kostnadsfria privata Git-databaser, konfigurerbara Kanban-anslagstavlor och omfattande automatiserade och kontinuerliga testfunktioner. [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) är en Azure DevOps-funktion som gör att du kan hantera CI/CD för att distribuera din kod med högpresterande pipelines som fungerar med alla språk, plattformar och moln.
[Azure Data Explorer - Admin Commands](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) är Azure Pipelines-uppgiften som gör att du kan skapa versionspipelor och distribuera dina databasändringar till dina Azure Data Explorer-databaser. Den är tillgänglig gratis på [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

I det här dokumentet beskrivs ett enkelt exempel på hur **Azure Data Explorer används – adminkommandon** för att distribuera schemaändringarna i databasen. Fullständiga CI/CD-pipelines finns i [Azure DevOps-dokumentationen](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure Data Explorer-klusterinstallation:
    * Ett [Azure Data Explorer-kluster och -databas](/azure/data-explorer/create-cluster-database-portal)
    * Skapa Azure Active Directory(Azure AD)-app genom [att etablera ett Azure AD-program](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Bevilja åtkomst till din Azure AD-app i din Azure Data Explorer-databas genom [att hantera Azure Data Explorer-databasbehörigheter](/azure/data-explorer/manage-database-permissions).
* Installation av Azure DevOps:
    * [Registrera dig för en kostnadsfri organisation](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Skapa en organisation](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Skapa ett projekt i Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Kod med Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Skapa mappar

Skapa följande exempelmappar (*Funktioner*, *Principer*, *Tabeller*) i Git-databasen. Kopiera filerna [härifrån](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) till respektive mappar som ses nedan och genomföra ändringarna. Exempelfilerna tillhandahålls för att köra följande arbetsflöde.

![Skapa mappar](media/devops/create-folders.png)

> [!TIP]
> När du skapar ett eget arbetsflöde rekommenderar vi att du gör din kod idempotent. Använd till exempel [.create-merge-tabell](/azure/kusto/management/create-table-command#create-merge-table) i stället för [.create-tabell](/azure/kusto/management/create-table-command)och använd [.create-or-alter-funktion](/azure/kusto/management/create-alter-function) i stället för [.create-funktionen.](/azure/kusto/management/create-function)

## <a name="create-a-release-pipeline"></a>Skapa en versionspipeline

1. Logga in på din [Azure DevOps-organisation](https://dev.azure.com/).
1. Välj **Pipelines-versioner** > **Releases** från menyn till vänster och välj **Ny pipeline**.

    ![Ny pipeline](media/devops/new-pipeline.png)

1. **Pipeline-fönstret Ny version** öppnas. Välj **Tomt jobb**i fönstret Välj en mall i fönstret Välj **en mall** på fliken **Pipelines** .

     ![Välj en mall](media/devops/select-template.png)

1. Välj **knappen Scen.** Lägg till **scennamnet**i rutan **Scen** . Välj **Spara** om du vill spara pipelinen.

    ![Namnge scenen](media/devops/stage-name.png)

1. Välj **Lägg till en artefaktknapp.** I fönstret **Lägg till en artefakt** markerar du den databas där koden finns, fyller i relevant information och klickar på **Lägg till**. Välj **Spara** om du vill spara pipelinen.

    ![Lägg till en artefakt](media/devops/add-artifact.png)

1. På fliken **Variabler** väljer du **+ Lägg till** om du vill skapa en variabel för **slutpunkts-URL** som ska användas i aktiviteten. Skriv **slutpunktens namn** och **värde.** Välj **Spara** om du vill spara pipelinen. 

    ![Skapa variabel](media/devops/create-variable.png)

    Om du vill hitta din Endpoint_URL innehåller översiktssidan för ditt **Azure Data Explorer-kluster** i Azure-portalen Azure Data Explorer-kluster URI. Konstruera URI i `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`följande format .  Till exempel https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![Azure Data Explorer kluster URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Skapa uppgifter att distribuera

1. Klicka på **ett jobb, 0 aktivitet** på fliken **Pipeline.** 

    ![Lägga till uppgifter](media/devops/add-task.png)

1. Skapa tre uppgifter för att distribuera **tabeller,** **funktioner**och **principer**i den här ordningen. 

1. Välj efter **agentjobb**på **+** fliken **Uppgifter** . Sök efter **Azure-datautforskaren**. Installera azure **data explorer – admin commands-tillägget** på **Marketplace.** Välj sedan **Lägg till** i Kommandot Kör Azure Data **Explorer**.

     ![Lägga till administratörskommandon](media/devops/add-admin-commands.png)

1. Klicka på **Kusto Command** till vänster och uppdatera uppgiften med följande information:
    * **Visningsnamn**: Uppgiftens namn
    * **Sökväg för tabeller**: I aktiviteten **Tabeller** anger du */Tabeller/* CSL eftersom filerna för att skapa tabellen finns i mappen *Tabell.*
    * **Slutpunkts-URL:** ange variabeln `EndPoint URL`som skapats i föregående steg.
    * Välj **Använd tjänstslutpunkt** och välj **+ Ny**.

    ![Kommandot Uppdatera Kusto](media/devops/kusto-command-task.png)

1. Slutför följande information i **anslutningsfönstret Lägg till Azure Data Explorer-tjänst:**

    |Inställning  |Föreslaget värde  |
    |---------|---------|
    |**Anslutningsnamn**     |    Ange ett namn för att identifiera den här tjänstslutpunkten     |
    |**Url till kluster**    |    Värde finns i översiktsavsnittet i Azure Data Explorer Cluster i Azure-portalen | 
    |**Tjänstens huvudmanS-ID**    |    Ange AAD-app-ID (skapad som förutsättning)     |
    |**Appnyckel för tjänsten**     |    Ange AAD-appnyckeln (skapad som förutsättning)    |
    |**AAD-klient-ID**    |      Ange din AAD-klient (till exempel microsoft.com, contoso.com...)    |

    Markera **Tillåt att alla pipelines använder den här anslutningsrutan.** Välj **OK**.

    ![Lägg till tjänstanslutning](media/devops/add-service-connection.png)

1. Upprepa steg 1-5 ytterligare två gånger för att distribuera filer från mapparna *Funktioner* och *principer.* Välj **Spara**. På fliken **Uppgifter** läser du de tre uppgifter som skapats: **Distribuera tabeller,** **Distribuera funktioner**och Distribuera **principer**.

    ![Distribuera alla mappar](media/devops/deploy-all-folders.png)

1. Välj **+ Släpp** > **Skapa release** för att skapa en version.

    ![Skapa en version](media/devops/create-release.png)

1. Kontrollera att **distributionsstatusen** har framgångsrikt.

    ![Distributionen lyckas](media/devops/deployment-successful.png)

Du har nu slutfört skapandet av en versionspipeline för distribution av tre aktiviteter till förproduktion.