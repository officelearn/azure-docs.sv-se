---
title: Använda CI/CD med Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Lär dig hur du konfigurerar kontinuerlig integrering/kontinuerlig distribution med Azure DevOps med Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
ms.openlocfilehash: 66ff2080ad44098757a5d9360fd3307e65f7431a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438449"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Använda CI/CD med Azure Dev Spaces

Den här artikeln hjälper dig att konfigurera kontinuerlig integrering/kontinuerlig distribution (CI/CD) till Azure Kubernetes Service (AKS) med Dev Spaces aktiverat. CI/CD till AKS gör att appuppdateringar kan distribueras automatiskt när bekräftad kod skickas till källdatabasen. Det är användbart att använda CI/CD tillsammans med ett Dev Spaces-aktiverat kluster eftersom det kan hålla en baslinje för programmet uppdaterad för teamet att arbeta med.

![CI/CD-exempeldiagram](../media/common/ci-cd-simple.png)

Även om den här artikeln vägleder dig med Azure DevOps, skulle samma begrepp gälla för CI/CD-system som Jenkins, TeamCity osv.

## <a name="prerequisites"></a>Krav
* [Aks-kluster (Azure Kubernetes Service) med Azure Dev Spaces aktiverat](../get-started-netcore.md)
* [Azure Dev Spaces CLI installerat](upgrade-tools.md)
* [Azure DevOps-organisation med ett projekt](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure-behållarregister (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Azure Container Registry [administratörskonto](../../container-registry/container-registry-authentication.md#admin-account) information tillgänglig
* [Auktorisera aks-klustret att hämta från ditt Azure-behållarregister](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Ladda ner exempelkod
För tidsskäl, låt oss skapa en gaffel av vår exempelkod GitHub-lagringsplats. Gå https://github.com/Azure/dev-spaces till och välj **Gaffel**. När gaffelprocessen är klar **klonar du** den klutna versionen av databasen lokalt. Som standard kommer _huvudgrenen_ att checkas ut, men vi har inkluderat några tidsbesparande ändringar i _azds_updates_ grenen, som också borde ha överförts under gaffeln. Den _azds_updates_ grenen innehåller uppdateringar som vi ber dig att göra manuellt i Dev Spaces handledning avsnitt, samt några färdiga YAML och JSON filer för att effektivisera distributionen av CI / CD-systemet. Du kan använda `git checkout -b azds_updates origin/azds_updates` ett kommando som att checka ut _azds_updates_ grenen i den lokala databasen.

## <a name="dev-spaces-setup"></a>Inställningar för Dev Spaces
Skapa ett nytt _dev_ utrymme `azds space select` som kallas dev med kommandot. _Utvecklingsutrymmet_ kommer att användas av CI/CD-pipelinen för att skicka kodändringarna. Det kommer också att användas för att skapa _underordnade utrymmen_ baserat på _dev_.

```cmd
azds space select -n dev
```

När du uppmanas att välja _ \<ett\>_ överordnat utvecklingsutrymme väljer du ingen .

När utvecklingsutrymmet har skapats måste du bestämma värdsuffixet. Använd `azds show-context` kommandot för att visa värdsuffixet för Azure Dev Spaces Ingress Controller.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

I exemplet ovan är värdsuffixet _fedcba098.eus.azds.io_. Det här värdet används senare när du skapar versionsdefinitionen.

_Utvecklingsutrymmet_ innehåller alltid det senaste tillståndet för databasen, en baslinje, så att utvecklare kan skapa _underordnade utrymmen_ från _dev_ för att testa sina isolerade ändringar inom ramen för den större appen. Detta begrepp diskuteras mer i detalj i Dev Spaces tutorials.

## <a name="creating-the-build-definition"></a>Skapa byggdefinitionen
Öppna ditt Azure DevOps-teamprojekt i en webbläsare och navigera till avsnittet _Pipelines._ Klicka först på ditt profilfoto längst upp till höger på Azure DevOps-webbplatsen, öppna fönstret för förhandsgranskningsfunktioner och inaktivera _den nya yaml-pipelinens upplevelse:_

![Öppna fönstret för förhandsgranskningsfunktioner](../media/common/preview-feature-open.png)

Alternativet att inaktivera:

![Nytt alternativ för skapande av YAML-pipeline](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Azure DevOps _New YAML pipeline creation experience_ preview-funktionen står i konflikt med att skapa fördefinierade byggpipelines för tillfället. Du måste inaktivera den för tillfället för att kunna distribuera vår fördefinierade byggpipeline.

I _azds_updates_ grenen har vi inkluderat en enkel [Azure Pipeline YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) som definierar de byggsteg som krävs för *mywebapi* och *webfrontend*.

Beroende på vilket språk du har valt har YAML för pipeline-pipelinen checkats in på en bana som liknar:`samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Så här skapar du en pipeline från den här filen:
1. På huvudsidan för DevOps-projektet navigerar du till Pipelines > Builds.
1. Välj alternativet för att skapa en **pipeline för nybyggnation.**
1. Välj **GitHub** som källa, auktorisera med ditt GitHub-konto om det behövs och välj _den azds_updates_ grenen från den kluvna versionen av exempelprogramdatabasen för _dev-spaces._
1. Välj **Konfiguration som kod**eller **YAML**som mall.
1. Du presenteras nu med en konfigurationssida för din byggpipeline. Som nämnts ovan navigera till den språkspecifika sökvägen för **YAML-filsökvägen** med hjälp av **...-knappen.** Till exempel `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Gå till fliken **Variabler.**
1. Lägg manuellt till _dockerId_ som en variabel, vilket är användarnamnet för ditt [Azure Container Registry-administratörskonto](../../container-registry/container-registry-authentication.md#admin-account). (Nämns i artikeln förutsättningar)
1. Lägg manuellt till _dockerPassword_ som en variabel, vilket är lösenordet för ditt [Azure Container Registry-administratörskonto](../../container-registry/container-registry-authentication.md#admin-account). Var noga med att ange _dockerPassword_ som en hemlighet (genom att välja låsikonen) av säkerhetsskäl.
1. Välj **Spara & kön**.

Du har nu en CI-lösning som automatiskt kommer att bygga *mywebapi* och *webfrontend* för alla uppdateringar som skjuts till _azds_updates_ grenen av din GitHub gaffel. Du kan kontrollera att Docker-avbildningarna har pushats genom att navigera till Azure-portalen, välja ditt Azure-behållarregister och bläddra i fliken **Databaser.** Det kan ta flera minuter för avbildningarna att bygga och visas i behållarregistret.

![Azure-behållareregisterdatabaser](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Skapa versionsdefinitionen

1. På huvudsidan för DevOps-projektet navigerar du till Pipelines >-versioner
1. Om du arbetar i ett helt nytt DevOps-projekt som ännu inte innehåller någon versionsdefinition måste du först skapa en definition av tomma utversioner innan du fortsätter. Alternativet Importera visas inte i användargränssnittet förrän du har en befintlig versionsdefinition.
1. Till vänster klickar du på knappen **+ Ny** och sedan på Importera **en pipeline**.
1. Klicka **Browse** på `samples/release.json` Bläddra och välj från projektet.
1. Klicka på **OK**. Observera att fönstret Pipeline har lästs in med redigeringssidan för versionsdefinition. Observera också att det finns några röda varningsikoner som anger klusterspecifika detaljer som fortfarande måste konfigureras.
1. Klicka på Rutan Lägg till **en artefakt** till vänster i fönstret Pipeline.
1. I listrutan **Källa** väljer du den byggpipeline som du skapade tidigare.
1. För **standardversionen**väljer du **Senaste i standardgrenen för byggpipeline med taggar**.
1. Lämna **taggarna** tomma.
1. Ange **alias** källa `drop`till . Värdet **för källalias** används av de fördefinierade utgivningsuppgifterna så att det måste anges.
1. Klicka på **Lägg till**.
1. Klicka nu på blixtikonen `drop` på den nyskapade artefaktkällan, som visas nedan:

    ![Släpp konfiguration för kontinuerlig distribution av artefakt](../media/common/release-artifact-cd-setup.png)
1. Aktivera **utlösaren för kontinuerlig distribution**.
1. Hovra över fliken **Uppgifter** **bredvid Pipeline** och klicka på _dev-fasaktiviteterna._ _dev_
1. Kontrollera att **Azure Resource Manager** har valts under **Anslutningstyp.** och du ser de tre listrkontrollerna markerade i rött: ![Släpp definitionsinställningar](../media/common/release-setup-tasks.png)
1. Välj den Azure-prenumeration du använder med Azure Dev Spaces. Du kan också behöva klicka på **Auktorisera**.
1. Välj den resursgrupp och det kluster som du använder med Azure Dev Spaces.
1. Klicka på **Agent jobb**.
1. Välj **Hosted Ubuntu 1604** under **Agent pool**.
1. Hovra över **uppgiftsväljaren** högst upp och klicka på _prod_ för att redigera prod-fasaktiviteterna. _prod_
1. Kontrollera att **Azure Resource Manager** har valts under **Anslutningstyp.** och välj den Azure-prenumeration, resursgrupp och kluster som du använder med Azure Dev Spaces.
1. Klicka på **Agent jobb**.
1. Välj **Hosted Ubuntu 1604** under **Agent pool**.
1. Klicka på fliken **Variabler** om du vill uppdatera variablerna för utgivningen.
1. Uppdatera värdet för **DevSpacesHostSuffix** från **UPDATE_ME** till värdsuffixet. Värdsuffixet visas när du `azds show-context` körde kommandot tidigare.
1. Klicka på **Spara** längst upp till höger och **OK**.
1. Klicka på **+ Släpp** (bredvid knappen Spara) och Skapa **en version**.
1. Under **Artefakter**kontrollerar du att den senaste versionen från byggpipelinen har valts.
1. Klicka på **Skapa**.

En automatiserad utgivningsprocess börjar nu, distribuera *mywebapi-* och *webfrontend-diagrammen* till kubernetes-klustret i utrymmet _för utveckling_ på den översta nivån. Du kan övervaka hur din version fortskrider på Azure DevOps webbportal:

1. Navigera till avsnittet **Versioner** under **Pipelines**.
1. Klicka på utgivningspipelinen för exempelprogrammet.
1. Klicka på namnet på den senaste versionen.
1. Hovra över **utvecklingsrutan** under **Faser** och klicka på **Loggar**.

Versionen görs när alla uppgifter är slutförda.

> [!TIP]
> Om din version misslyckas med ett felmeddelande som *UPPGRADERING MISSLYCKADES: timed out väntar på villkoret,* försök att inspektera poddar i klustret [med hjälp av Kubernetes instrumentpanelen](../../aks/kubernetes-dashboard.md). Om poddar misslyckas med att starta med felmeddelanden som *Det gick inte att hämta avbildningen "azdsexample.azurecr.io/mywebapi:122": rpc-fel: https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: kod = Okänt desc = Felsvar från demon: Få obehörig: autentisering krävs,* kan det bero på att klustret inte har auktoriserats att hämta från ditt Azure-behållarregister. Kontrollera att du har slutfört [auktorisera AKS-klustret för att hämta från din Azure Container Registry](../../aks/cluster-container-registry-integration.md) förutsättning.

Du har nu en helt automatiserad CI/CD-pipeline för din GitHub-gaffel av exempelapparna Dev Spaces. Varje gång du genomför och push-kod, kommer bygga pipeline bygga och driva *mywebapi* och *webfrontend* bilder till din anpassade ACR-instans. Sedan distribuerar versionspipelinen Helm-diagrammet för varje app till _utvecklingsutrymmet_ i ditt Dev Spaces-aktiverade kluster.

## <a name="accessing-your-_dev_-services"></a>Komma åt dina _dev-tjänster_
Efter distributionen kan _dev-versionen_ av *webfrontend* nås med `http://dev.webfrontend.fedcba098.eus.azds.io`en offentlig URL som: . Du hittar den här `azds list-uri` URL:en genom att köra kommandot: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Distribuera till produktion

Så här befordrar du en viss version manuellt till _prod_ med hjälp av CI/CD-systemet som skapas i den här självstudien:
1. Navigera till avsnittet **Versioner** under **Pipelines**.
1. Klicka på utgivningspipelinen för exempelprogrammet.
1. Klicka på namnet på den senaste versionen.
1. Hovra över **prod-rutan** under **Faser** och klicka på **Distribuera**.
    ![Flytta upp till produktion](../media/common/prod-promote.png)
1. Hovra över **prod** rutan igen under **Steg** och klicka **på Loggar**.

Versionen görs när alla uppgifter är slutförda.

_Prod-fasen_ för CI/CD-pipelinen använder en belastningsutjämnare i stället för Dev Spaces Ingress-styrenheten för att ge åtkomst till _prod-tjänster._ Tjänster som _distribueras_ i prod-fasen är tillgängliga som IP-adresser i stället för DNS-namn. I en produktionsmiljö kan du välja att skapa en egen Ingress-styrenhet som värd för dina tjänster baserat på din egen DNS-konfiguration.

För att fastställa IP för webfrontend-tjänsten, klicka på **Print webfrontend offentliga IP-steg** för att expandera loggutdata. Använd IP-adressen som visas i loggutdata för att komma åt **webfrontend-programmet.**

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Dev Spaces instrumentering i produktion
Även om Dev Spaces-instrumentering har utformats för att _inte_ komma i vägen för normal drift av ditt program, rekommenderar vi att du kör dina produktionsarbetsbelastningar i ett Kubernetes-namnområde som inte är aktiverat med Dev Spaces. Med den här typen av Kubernetes namnområde innebär att `kubectl` du antingen bör skapa ditt produktionsnamnområde med HJÄLP av CLI eller låta CI/CD-systemet skapa det under den första Helm-distributionen. _Om du väljer_ eller på annat sätt skapar ett utrymme med hjälp av dev spaces-verktyg läggs Dev Spaces-instrumenteringen till det namnområdet.

Här är ett exempel namnområde struktur som stöder funktionsutveckling, "dev" miljö _och_ produktion, allt i en enda Kubernetes kluster:

![Exempel på namnområdesstruktur](../media/common/cicd-namespaces.png)

> [!Tip]
> Om du redan har `prod` skapat ett utrymme och helt enkelt vill utesluta det från Dev Spaces-instrumentering (utan att ta bort det!), kan du göra det med följande Dev Spaces CLI-kommando:
>
> `azds space remove -n prod --no-delete`
>
> Du kan behöva ta bort `prod` alla poddar i namnområdet efter att ha gjort detta så att de kan återskapas utan Dev Spaces-instrumentering.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om grupputveckling med Azure Dev Spaces](../team-development-netcore.md)