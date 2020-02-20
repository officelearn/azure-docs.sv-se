---
title: Azure DevOps-aktivitet för Azure Datautforskaren
description: I det här avsnittet lär du dig hur du skapar en versions pipeline och distribuerar
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472051"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure DevOps-aktivitet för Azure Datautforskaren

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) innehåller utvecklings samarbets verktyg som högpresterande pipelines, kostnads fria privata git-lagringsplatser, konfigurerbara kanban-kort och omfattande automatiserade och kontinuerliga testnings funktioner. [Azure-pipeliner](https://azure.microsoft.com/services/devops/pipelines/) är en Azure DevOps-funktion som du kan använda för att hantera CI/CD för att distribuera din kod med högpresterande pipelines som fungerar med alla språk, plattformar och moln.
[Azure datautforskaren-admin-kommandon](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) är en prenumeration på Azure pipelines som gör att du kan skapa lanserings pipeliner och distribuera dina databas ändringar till dina Azure datautforskaren-databaser. Den är tillgänglig kostnads fritt i [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Det här dokumentet beskriver ett enkelt exempel på hur du använder aktiviteten **Azure datautforskaren – admin-kommandon** för att distribuera dina schema ändringar till din databas. För kompletta CI/CD-pipelines, se [Azure DevOps-dokumentationen](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Konfiguration av Azure Datautforskaren-kluster:
    * Ett [Azure datautforskaren-kluster och-databas](/azure/data-explorer/create-cluster-database-portal)
    * Skapa Azure Active Directory-app (Azure AD) genom att [tillhandahålla ett Azure AD-program](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Bevilja åtkomst till din Azure AD App i Azure Datautforskaren-databasen genom att [Hantera behörigheter för azure datautforskaren-databasen](/azure/data-explorer/manage-database-permissions).
* Installation av Azure-DevOps:
    * [Registrera dig för en kostnads fri organisation](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Skapa en organisation](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Skapa ett projekt i Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Kod med git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Skapa mappar

Skapa följande exempel mappar (*Functions*, *policys*, *Tables*) i git-lagringsplatsen. Kopiera filerna [härifrån till respektive](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) mappar som visas nedan och genomför ändringarna. Exempelfilerna tillhandahålls för att köra följande arbets flöde.

![Skapa mappar](media/devops/create-folders.png)

> [!TIP]
> När du skapar ett eget arbets flöde rekommenderar vi att du gör koden idempotenta. Använd till exempel [. skapa-sammanfoga tabell](/azure/kusto/management/create-table-command#create-merge-table) i stället för [. CREATE TABLE](/azure/kusto/management/create-table-command)och use [. Create-eller-Alter](/azure/kusto/management/create-alter-function) -funktionen i stället för [. Create](/azure/kusto/management/create-function) -funktionen.

## <a name="create-a-release-pipeline"></a>Skapa en versionspipeline

1. Logga in på din [Azure DevOps-organisation](https://dev.azure.com/).
1. Välj **pipelines** > **utgåvor** från menyn till vänster och välj **ny pipeline**.

    ![Ny pipeline](media/devops/new-pipeline.png)

1. Fönstret **ny version pipeline** öppnas. Välj **Tom jobb**i fönstret **Välj en mall** på fliken **pipeliner** .

     ![Välj en mall](media/devops/select-template.png)

1. Knappen Välj **steg** . I fönstret **Stage** lägger du till **namnet på scenen**. Välj **Spara** för att spara din pipeline.

    ![Namnge scenen](media/devops/stage-name.png)

1. Välj **Lägg till en artefakt** knapp. I fönstret **Lägg till en artefakt** väljer du den lagrings plats där koden finns, fyller i relevant information och klickar på **Lägg till**. Välj **Spara** för att spara din pipeline.

    ![Lägg till en artefakt](media/devops/add-artifact.png)

1. På fliken **variabler** väljer du **+ Lägg** till för att skapa en variabel för **slut punkts-URL** som ska användas i uppgiften. Skriv **namnet** och **värdet** för slut punkten. Välj **Spara** för att spara din pipeline. 

    ![Skapa variabel](media/devops/create-variable.png)

    För att hitta din Endpoint_URL innehåller sidan Översikt för ditt **Azure datautforskaren-kluster** i Azure Portal Azure datautforskaren-klustrets URI. Skapa URI i följande format `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Till exempel https:\//kustodocs.westus.kusto.Windows.net? DatabaseName = SampleDB

    ![Azure Datautforskaren kluster-URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Skapa uppgifter som ska distribueras

1. På fliken **pipelines** klickar du på **1 jobb, 0 aktivitet** för att lägga till aktiviteter. 

    ![Lägg till aktiviteter](media/devops/add-task.png)

1. Skapa tre uppgifter för att distribuera **tabeller**, **funktioner**och **principer**i den här ordningen. 

1. På fliken **aktiviteter** väljer du **+** efter **Agent jobb**. Sök efter **Azure-datautforskaren**. I **Marketplace**installerar du **Azure-datautforskaren – administratörs kommando** tillägg. Välj sedan **Lägg till** i **Kör Azure datautforskaren kommandot**.

     ![Lägg till administratörs kommandon](media/devops/add-admin-commands.png)

1. Klicka på **kommandot Kusto** till vänster och uppdatera uppgiften med följande information:
    * **Visnings namn**: uppgiftens namn
    * **Fil Sök väg**: i tabellen **tabeller** anger du */Tables/* . CSL eftersom tabellens skapelse-filer finns i mappen *Table* .
    * **Slut punkts-URL**: Ange `EndPoint URL`variabeln som skapades i föregående steg.
    * Välj **Använd tjänst slut punkt** och välj **+ ny**.

    ![Uppdatera Kusto kommando uppgift](media/devops/kusto-command-task.png)

1. Slutför följande information i fönstret **Lägg till Azure datautforskaren service-anslutning** :

    |Inställning  |Föreslaget värde  |
    |---------|---------|
    |**Anslutningsnamn**     |    Ange ett namn för att identifiera den här tjänst slut punkten     |
    |**Kluster-URL**    |    Du hittar värdet i avsnittet Översikt i Azure Datautforskaren-klustret i Azure Portal | 
    |**Tjänstens huvud namns-ID**    |    Ange ID för AAD-app (skapas som krav)     |
    |**App-nyckel för tjänstens huvud namn**     |    Ange app-nyckeln för AAD (skapad som nödvändig)    |
    |**AAD-klient-ID**    |      Ange din AAD-klient (till exempel microsoft.com, contoso.com...)    |

    Markera kryss rutan **Tillåt att alla pipeliner använder den här anslutningen** . Välj **OK**.

    ![Lägg till tjänst anslutning](media/devops/add-service-connection.png)

1. Upprepa steg 1-5 ytterligare två gånger för att distribuera filer från mapparna *funktioner* och *principer* . Välj **Spara**. På fliken **aktiviteter** , se de tre uppgifter som skapats: **distribuera tabeller**, **distribuera funktioner**och **distribuera principer**.

    ![Distribuera alla mappar](media/devops/deploy-all-folders.png)

1. Välj **+ version** > **Skapa version** för att skapa en version.

    ![Skapa en version](media/devops/create-release.png)

1. På fliken **loggar** kontrollerar du att distributions statusen har slutförts.

    ![Distributionen har slutförts](media/devops/deployment-successful.png)

Nu har du slutfört skapandet av en versions pipeline för distribution av tre uppgifter till för produktion.