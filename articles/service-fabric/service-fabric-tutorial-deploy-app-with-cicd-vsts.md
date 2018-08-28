---
title: Distribuera en Service Fabric-app med kontinuerlig integrering (Team Services) i Azure | Microsoft Docs
description: I den här självstudiekursen får du lära dig hur du ställer in kontinuerlig integrering och distribution för ett Service Fabric-program med hjälp av Visual Studio Team Services.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 2122b6d9c385e1137d0fc6df5229975359fa20d5
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "41917711"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Självstudie: Distribuera ett program med CI/CD till ett Service Fabric-kluster

Den här självstudien är del fyra i en serie. Du får du lära dig hur du ställer in kontinuerlig integrering och distribution för ett Azure Service Fabric-program med hjälp av Visual Studio Team Services.  En befintlig Service Fabric-tillämpning krävs. Den som skapas i [Bygga ett .NET-program](service-fabric-tutorial-create-dotnet-app.md) används som exempel.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * lägga till källkontroll i projektet
> * Skapa en byggesdefinition i Team Services
> * skapa en versionsdefinition i Team Services
> * distribuera och uppgradera ett program automatiskt.

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett .NET Service Fabric-program](service-fabric-tutorial-create-dotnet-app.md)
> * [Distribuera programmet till ett fjärrkluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Lägga till en HTTPS-slutpunkt i en klienttjänst i ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Konfigurera CI/CD med hjälp av Visual Studio Team Services
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Installera Visual Studio 2017](https://www.visualstudio.com/) och installera **Azure Development** och arbetsbelastningarna **ASP.NET och webbutveckling**.
* [Installera Service Fabric SDK](service-fabric-get-started.md)
* Skapa ett Windows Service Fabric-kluster i Azure, till exempel genom att [följa den här självstudiekursen](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Skapa ett [Team Services-konto](https://docs.microsoft.com/vsts/organizations/accounts/create-organization-msa-or-work-student).

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet Röstning

Om du inte byggde exempelprogrammet Röstning i [del ett av självstudiekursen](service-fabric-tutorial-create-dotnet-app.md) kan du ladda ned det. Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Förbered publiceringsprofil

Nu när du har [skapat ett program](service-fabric-tutorial-create-dotnet-app.md) och [distribuerat programmet till Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md) är du redo att konfigurera kontinuerlig integrering.  Börja med att förbereda en publiceringsprofil i programmet för användning med distributionsprocessen som körs i Team Services.  Publiceringsprofilen ska konfigureras med inriktning på det kluster som du skapade tidigare.  Starta Visual Studio och öppna ett befintligt Service Fabric-programprojekt.  I **Solution Explorer** högerklickar du på programmet och väljer **Publicera**.

Välj en målprofil inom programprojektet att använda för arbetsflödet för den kontinuerliga integreringen, till exempel molnet.  Specificera klusteranslutningsslutpunkten.  Markera kryssrutan **Uppgradera programmet** så att programmet uppgraderas för varje distribution i Team Services.  Klicka på hyperlänken **Spara** och spara inställningarna i profilen. Klicka sedan på **Avbryt** och stäng dialogrutan.

![Push-profil][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Dela Visual Studio-lösningen till en ny Team Services Git-lagringsplats

Dela programkällfilerna till ett teamprojekt i Team Services så att du kan generera byggen.

Skapa en ny lokal Git-lagringsplats för projektet genom att välja alternativet för att **lägga till källkontroll** -> **Git** i statusfältet i det nedre högra hörnet av Visual Studio.

I vyn för **Push-överföring**  i **Team Explorer** väljer du knappen för att **publicera Git-lagringsplatsen** under alternativet för att **push-överföra till Visual Studio Team Services**.

![Push-överföring med Git-lagringsplats][push-git-repo]

Verifiera din e-postadress och välj ditt konto i listrutan **Team Services-domän**. Skriv in lagringsplatsens namn och välj **Publicera lagringsplats**.

![Push-överföring med Git-lagringsplats][publish-code]

När du publicerar lagringsplatsen skapas ett nytt teamprojekt i kontot med samma namn som den lokala lagringsplatsen. Om du vill skapa lagringsplatsen i ett befintlig teamprojekt klickar du på **Avancerat** bredvid namnet på **databasen** och väljer ett teamprojekt. Du kan visa koden på webben genom att välja alternativet för att **visa på webben**.

## <a name="configure-continuous-delivery-with-vsts"></a>Konfigurera kontinuerlig leverans med VSTS

En byggesdefinition för Team Services beskriver ett arbetsflöde som består av en uppsättning byggesåtgärder som utförs i tur och ordning. Skapa en byggesdefinition som producerar ett Service Fabric-programpaket och andra artefakter att distribuera till ett Service Fabric-kluster. Ta reda på mer om [byggesdefinitioner för Team Services](https://www.visualstudio.com/docs/build/define/create). 

En versionsdefinition för Team Services beskriver ett arbetsflöde som distribuerar ett programpaket till ett kluster. När de används tillsammans utför byggesdefinitionen och versionsdefinitionen hela arbetsflödet med början på källfiler för att sluta med ett program som körs i klustret. Lär dig mer om [versionsdefinitioner för Team Services](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-definition"></a>Skapa en byggesdefinition

Öppna en webbläsare och navigera till det nya teamprojektet på: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Välj fliken **Build and release** (Bygge och version), därefter **Builds** (Byggen) och klicka sedan på **New Pipeline** (Ny pipeline).

![Ny pipeline][new-pipeline]

Välj **VSTS Git** som källa, teamprojektet **Voting** (Röstning), lagringsplatsen **Voting** och standardgrenen **master** eller manuella och schemalagda byggen.  Klicka sedan på **Fortsätt**.

I **Välj en mall** väljer du mallen **Azure Service Fabric-program** och klickar på **Använd**.

![Välj byggesmall][select-build-template]

I **Uppgifter** anger du Hosted VS2017 som **Agent queue** (Agentkö).

![Välj uppgifter][save-and-queue]

Under **Utlösare** aktiverar du kontinuerlig integrering genom att markera **Aktivera kontinuerlig integrering**. I **Branch filters** (Grenfilter) klickar du på **+ Lägg till** så får **Branch specification** (Grenspecifikation) standardvärdet **master**. Välj alternativet för att **spara och köa** för att manuellt starta en version.

I **dialogrutan för att spara bygg-pipeline och kö** klickar du på **Save & queue** (Spara och köra).

![Välj utlösare][save-and-queue2]

Byggen utlöser också vid push och incheckning. Om du vill kontrollera förloppet för bygget växlar du till fliken **Builds** (Byggen).  När du har kontrollerat att bygget körs rätt kan du definiera en versionsdefinition som distribuerar programmet till ett kluster.

### <a name="create-a-release-definition"></a>Skapa en versionsdefinition

Välj fliken **Build & Release** (Bygge och version), fliken **Releases** (Versioner) och sedan **+New pipeline** (+ Ny pipeline).  I **Välj en mall** väljer du mallen **Azure Service Fabric Deployment** på listan och sedan **Använd**.

![Välj versionsmall][select-release-template]

Välj **Uppgifter**->**Environment 1** (Miljö 1)och sedan **+New** (+Ny) för att lägga till en ny klusteranslutning.

![Lägga till klusteranslutning][add-cluster-connection]

I vyn för att **lägga till nya Service Fabric-anslutningar** väljer du autentisering av typerna **Certifikatbaserad** eller **Azure Active Directory**.  Ange ett anslutningsnamn för mysftestcluster och en klusterslutpunkt för tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000 (eller slutpunkten i klustret som du distribuerar till).

Om du använder certifikatbaserad autentisering lägger du till **servercertifikatets tumavtryck** för det servercertifikat som används för att skapa klustret.  I **klientcertifikat** lägger du till base-64-kodningen för klientcertifikatfilen. Det finns ett hjälppopupfönster för fältet om du behöver information om hur du hittar den base-64-kodade representationen för certifikatet. Lägg också till **lösenordet** för certifikatet.  Du kan använda certifikatet för klustret eller servern om du inte har något separat klientcertifikat.

För Azure Active Directory-autentiseringsuppgifter lägger du till **servercertifikatets tumavtryck** för det servercertifikatet som användes för att skapa klustret och de autentiseringsuppgifter du vill använda för att ansluta till klustret i fälten **Användarnamn** och **Lösenord**.

Klicka på **Lägg till** och spara klusteranslutningen.

Lägg sedan till en byggesartefakt i pipelinen så versionsdefinitionen kan hitta utdata från bygget. Välj **Pipeline** och **artefakter**->**+ Lägg till**.  I **Source (Build definition)** (Källa (byggesdefinition)) väljer du den byggesdefinition som du skapade tidigare.  Klicka på **Lägg till** för att spara byggesartefakten.

![Lägg till artefakt][add-artifact]

Aktivera en kontinuerlig distributionsutlösare så att versionen automatiskt skapas när bygget har slutförts. Klicka på blixtikonen i artefakten, aktivera utlösaren och klicka på **Spara** för att spara versionsdefinitionen.

![Aktivera utlösare][enable-trigger]

Välj **+Release** -> **Create a Release**  -> **Create** (+Version, Skapa en version, Skapa) för att manuellt skapa en version. Du kan övervaka versionsförloppet på fliken **Releases** (Versioner).

Kontrollera att distributionen har slutförts och programmet körs i klustret.  Öppna en webbläsare och navigera till [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Observera programversionen. I det här exemplet är den 1.0.0.20170616.3.

## <a name="commit-and-push-changes-trigger-a-release"></a>Genomför och push-överföring av ändringar utlöser en släppning

För att kontrollera att pipelinen för den kontinuerliga integreringen fungerar genom att kontrollera kodändringar i Team Services.

När du skriver koden spåras dina ändringar automatiskt av Visual Studio. Genomför ändringar av den lokala Git-lagringsplatsen genom att välja ikonen väntande ändringar (![Väntande åtgärder][pending]) från statusfältet i nederkant högra hörnet.

I vyn **Ändringar** i Team Explorer lägger du till ett meddelande som beskriver uppdateringen. Spara sedan ändringarna.

![Genomför alla][changes]

Välj statusfältikonen för de opublicerade ändringarna (![Opublicerade ändringar][unpublished-changes]) eller vyn Synkronisera i Team Explorer. Välj **Push** (Push-överföring) för att uppdatera koden i Team Services/TFS.

![Push-överföring av ändringar][push]

När du skickar ändringar till Team Services via push-överföring utlöser ett bygge automatiskt.  När byggesdefinition har slutförts skapas en version automatiskt och börjar uppgradera programmet i klustret.

Om du vill kontrollera förloppet för bygget kan du byta till fliken **Builds** (Byggen) i **Team Explorer** i Visual Studio.  När du har kontrollerat att bygget körs rätt kan du definiera en versionsdefinition som distribuerar programmet till ett kluster.

Kontrollera att distributionen har slutförts och programmet körs i klustret.  Öppna en webbläsare och navigera till [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Observera programversionen. I det här exemplet är den 1.0.0.20170815.3.

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Uppdatera programmet

Göra kodändringar i programmet.  Spara och genomföra ändringarna via de här stegen.

När programuppgraderingen påbörjas kan du visa uppgraderingsförloppet i Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

Programuppgraderingen kan ta flera minuter. När uppgraderingen har slutförts kör programmet nästa version.  I det här exemplet 1.0.0.20170815.4.

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * lägga till källkontroll i projektet
> * Skapa en byggesdefinition
> * Skapa en versionsdefinition
> * distribuera och uppgradera ett program automatiskt.

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
