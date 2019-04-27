---
title: Använda CI/CD med Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: yuvalm
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
ms.openlocfilehash: 983af0dd75e6ae62630c85d04ac3819c7e260439
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60687375"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Använda CI/CD med Azure Dev blanksteg

Den här artikeln hjälper dig att konfigurera kontinuerlig integrering/kontinuerlig distribution (CI/CD) till Azure Kubernetes Service (AKS) Dev blanksteg aktiverat. CI/CD till AKS kan appuppdateringar distribueras automatiskt när allokerade koden skickas till centrallagret. Med CI/CD tillsammans med en Dev-blanksteg är aktiverade klustret användbart eftersom det kan hålla en baslinje för programmet uppdaterad för teamet att arbeta med.

![CI/CD-exempeldiagram](../media/common/ci-cd-simple.png)

Även om den här artikeln hjälper dig med Azure DevOps, skulle samma koncept gäller för CI/CD-system som Jenkins, TeamCity, osv.

## <a name="prerequisites"></a>Nödvändiga komponenter
* [Azure Kubernetes Service (AKS)-kluster med Azure Dev blanksteg aktiverat](../get-started-netcore.md)
* [Azure CLI för utveckling blanksteg installerat](upgrade-tools.md)
* [Azure DevOps-organisation med ett projekt](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Azure Container Registry [administratörskontot](../../container-registry/container-registry-authentication.md#admin-account) information som är tillgängliga
* [Auktorisera AKS-kluster för att hämta från Azure Container Registry](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>Hämta exempelkoden
Nu ska vi skapa en förgrening i vårt exempel kod GitHub-lagringsplatsen för tiden. Gå till https://github.com/Azure/dev-spaces och välj **förgrening**. När Förgrena processen är klar, **klona** din förgrenade lagringsplatsen lokalt-version. Som standard den _master_ gren checkas ut, men vi har inkluderat några tidsbesparande ändringar i den _azds_updates_ grenen, vilket bör också har överförts under din förgrening. Den _azds_updates_ grenen innehåller uppdateringar som vi be dig att göra manuellt i avsnitten Dev blanksteg självstudiekursen, samt vissa fördefinierade YAML och JSON-filer för att förenkla distributionen av CI/CD-system. Du kan använda ett kommando som `git checkout -b azds_updates origin/azds_updates` att checka ut den _azds_updates_ gren i din lokala lagringsplats.

## <a name="dev-spaces-setup"></a>Dev blanksteg installationen
Skapa en ny sida som kallas _dev_ med hjälp av den `azds space select` kommando. Den _dev_ utrymme som ska användas av din CI/CD-pipeline för att skicka dina ändringar i koden. Det kommer också användas för att skapa _underordnade blanksteg_ utifrån _dev_.

```cmd
azds space select -n dev
```

När du uppmanas att välja ett överordnat utvecklingsutrymme väljer du _\<none\>_ (inget).

När du har skapat ditt dev-utrymme som du behöver avgöra suffixet som värd. Använd den `azds show-context` kommando för att visa Azure Dev blanksteg Ingress-kontrollant värd-suffix.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

I exemplet ovan är suffixet som värd _fedcba098.eus.azds.io_. Det här värdet används senare när du skapar din versionsdefinition.

Den _dev_ utrymme kommer alltid att innehålla det senaste tillståndet för databasen, baslinje, så att utvecklare kan skapa _underordnade blanksteg_ från _dev_ att testa ändringarna isolerade inom ramen för större appen. Detta begrepp beskrivs i detalj i Dev blanksteg självstudier.

## <a name="creating-the-build-definition"></a>Skapa build-definition
Öppna projektet Azure DevOps-team i en webbläsare och navigera till den _Pipelines_ avsnittet. Klicka på ditt profilfoto uppe till höger på webbplatsen Azure DevOps, öppna funktioner förhandsgranskningsfönstret, och inaktivera den _upplevelse för nya YAML pipeline när du skapar_:

![Funktioner för att öppna förhandsgranskning](../media/common/preview-feature-open.png)

Du kan inaktivera:

![Skapa en ny YAML pipeline appalternativ](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Azure DevOps _upplevelse för nya YAML pipeline när du skapar_ förhandsversion funktionen står i konflikt med att skapa fördefinierade skapa pipeliner just nu. Du måste inaktivera den för tillfället för att distribuera vår fördefinierade build-pipeline.

I den _azds_updates_ gren som vi har inkluderat en enkel [Azure Pipeline YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) som definierar build steg som krävs för *mywebapi* och *webfrontend* .

Beroende på vilket språk som du har valt har pipelinen YAML incheckade vid en sökväg som liknar: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Skapa en Pipeline från den här filen:
1. På din DevOps-projekt huvudsidan, navigerar du till en pipeline > bygger.
1. Välj alternativet för att skapa en **New** skapa pipeline.
1. Välj **GitHub** som källa, auktorisera dig med ditt GitHub-konto om nödvändigt och väljer den _azds_updates_ gren från din förgrenade version av dev-blanksteg exempelapp för databasen.
1. Välj **konfiguration som kod**, eller **YAML**, som din mall.
1. Nu visas en konfigurationssida för build-pipeline. Som nämnts ovan navigera till språkspecifik sökväg för den **YAML-filsökväg** med hjälp av den **...**  knappen. Till exempel `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Gå till den **variabler** fliken.
1. Lägg till manuellt _dockerId_ som en variabel, vilket är användarnamnet för din [administratörskontot för Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). (Beskrivs i artikeln krav)
1. Lägg till manuellt _dockerPassword_ som en variabel, vilket är lösenordet för din [administratörskontot för Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). Se till att ange _dockerPassword_ som en hemlighet (genom att välja låsikonen) av säkerhetsskäl.
1. Välj **spara och köa**.

Nu har du en CI-lösning som bygger automatiskt *mywebapi* och *webfrontend* för en uppdatering som skickas till den _azds_updates_ gren i din GitHub-förgrening. Du kan kontrollera Docker-avbildningar har överförts genom att gå till Azure-portalen, väljer Azure Container Registry och bläddra i **databaser** fliken. Det kan ta flera minuter för bilder för att skapa och visas i ditt behållarregister.

![Azure Container Registry-databaser](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Skapa versionsdefinitionen

1. På din DevOps-projekt huvudsidan, navigerar du till en pipeline > versioner
1. Om du arbetar i en helt ny DevOps-projekt som ännu inte innehåller en versionsdefinition, måste du först skapa en tom versionsdefinition innan du fortsätter. Import-alternativet visas inte i Användargränssnittet förrän du har en befintlig versionsdefinition.
1. Till vänster, klicka på den **+ ny** knappen och klicka sedan på **importera en pipeline**.
1. Klicka på **Bläddra** och välj `samples/release.json` från ditt projekt.
1. Klicka på **OK**. Observera rutan Pipeline har lästs in med sidan Redigera versionen definition. Observera att det finns även vissa red varning ikoner som anger klusterspecifika information som fortfarande måste konfigureras.
1. Till vänster i fönstret pipelinen klickar du på den **lägga till en artefakt** bubbeldiagram.
1. I den **källa** listrutan Välj bygget pipeline du skapade tidigare.
1. För den **standardversion**, Välj **senaste från build pipeline standardgrenen med taggar**.
1. Lämna **taggar** tom.
1. Ange den **källa alias** till `drop`. Den **källa alias** värdet används av de fördefinierade publiceringsuppgifter så måste anges.
1. Klicka på **Lägg till**.
1. Klicka på blixtikonen på den nyligen skapade `drop` artefakt källa, enligt nedan:

    ![Viktig artefakt kontinuerlig distribution installationen](../media/common/release-artifact-cd-setup.png)
1. Aktivera den **utlösare av kontinuerlig distribution**.
1. Hovra över den **uppgifter** fliken bredvid **Pipeline** och klicka på _dev_ att redigera den _dev_ mellanlagra uppgifter.
1. Kontrollera **Azure Resource Manager** är markerat under **anslutningstyp.** och du ser de tre listrutekontroller markerat i rött: ![Inställning av versionen](../media/common/release-setup-tasks.png)
1. Ange den prenumeration du använder Azure Dev blanksteg. Du kan också behöva klicka på **auktorisera**.
1. Välj resursgrupp och kluster som du använder Azure Dev blanksteg.
1. Klicka på **agentjobbet**.
1. Välj **finns Ubuntu 1604** under **agentpoolen**.
1. Hovra över den **uppgifter** Väljaren överst på sidan klickar du på _prod_ att redigera den _prod_ mellanlagra uppgifter.
1. Kontrollera **Azure Resource Manager** är markerat under **anslutningstyp.** och välj den Azure-prenumeration, resursgrupp och kluster som du använder Azure Dev blanksteg.
1. Klicka på **agentjobbet**.
1. Välj **finns Ubuntu 1604** under **agentpoolen**.
1. Klicka på den **variabler** fliken för att uppdatera variablerna för din version.
1. Uppdatera värdet för **DevSpacesHostSuffix** från **UPDATE_ME** till värd-suffixet. Värd-suffix visas när du körde den `azds show-context` kommandot tidigare.
1. Klicka på **spara** i det övre högra hörnet, och **OK**.
1. Klicka på **+ Release** (bredvid knappen Spara) och **skapa en version**.
1. Under **artefakter**, kontrollera den senaste versionen från build-pipeline har valts.
1. Klicka på **Skapa**.

En automatiserad lanseringsprocessen kommer nu att börja, distribuera den *mywebapi* och *webfrontend* diagram till Kubernetes-kluster i den _dev_ översta utrymme. Du kan övervaka förloppet för din version på webbportalen Azure DevOps:

1. Navigera till den **versioner** avsnittet **Pipelines**.
1. Klicka på releasepipeline för exempelprogrammet.
1. Klicka på namnet på den senaste versionen.
1. Hovra över **dev** rutan **faser** och klicka på **loggar**.

Versionen är klar när alla aktiviteter har slutförts.

> [!TIP]
> Om din version misslyckas med ett felmeddelande som *det gick inte att uppgradera: Tidsgränsen uppnåddes vid väntan villkoret*, försök att kontrollera poddarna i ditt kluster [med hjälp av Kubernetes-instrumentpanelen](../../aks/kubernetes-dashboard.md). Om du ser poddarna misslyckas att börja med felmeddelanden som *det gick inte att hämta avbildningen ”azdsexample.azurecr.io/mywebapi:122”: RPC-fel: kod = okänt desc = felsvar från daemon: Hämta https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: obehörig: autentisering krävs*, det kan bero på att klustret inte har auktoriserats för att hämta från Azure Container Registry. Kontrollera att du har slutfört den [auktorisera AKS-kluster för att hämta från Azure Container Registry](../../container-registry/container-registry-auth-aks.md) krav.

Nu har du en helt automatiserad CI/CD-pipeline för GitHub-förgreningen över exempelappar Dev blanksteg. Varje gång du verkställa och push kod, skapa pipelinen skapar och skicka den *mywebapi* och *webfrontend* bilder till din anpassade ACR-instansen. Sedan versionspipelinen ska distribuera Helm-diagram för varje app till den _dev_ utrymme på klustret Dev blanksteg-aktiverade.

## <a name="accessing-your-dev-services"></a>Åtkomst till din _dev_ tjänster
Efter distributionen kan den _dev_ version av *webfrontend* kan nås med en offentlig URL som: `http://dev.webfrontend.fedcba098.eus.azds.io`. Du hittar Webbadressen genom att köra den `azds list-uri` kommando: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Distribuera det till produktion

Att manuellt flytta upp en viss version till _prod_ med CI/CD-system som skapats i den här självstudien:
1. Navigera till den **versioner** avsnittet **Pipelines**.
1. Klicka på releasepipeline för exempelprogrammet.
1. Klicka på namnet på den senaste versionen.
1. Hovra över den **prod** rutan **faser** och klicka på **distribuera**.
    ![Skicka till produktion](../media/common/prod-promote.png)
1. Hovra över **prod** rutan igen **faser** och klicka på **loggar**.

Versionen är klar när alla aktiviteter har slutförts.

Den _prod_ steg för CI/CD-pipeline använder en belastningsutjämnare i stället för Dev blanksteg Ingress-kontrollanten för att ge åtkomst till _prod_ tjänster. Tjänster som distribueras i den _prod_ scenen är tillgängliga som IP-adresser i stället för DNS-namn. I en produktionsmiljö kan du välja att skapa egna Ingress-kontrollanten ska vara värd för dina tjänster baserat på din egen DNS-konfiguration.

Du kan fastställa IP-Adressen för tjänsten webfrontend genom att klicka på den **skriva ut webfrontend offentlig IP-adress** steg för att expandera loggutdata. Använd IP-adress som visas i loggen för utgående åtkomst till den **webfrontend** program.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Dev blanksteg instrumentation i produktion
Även om utveckling blanksteg instrumentation har utformats för _inte_ för att komma i vägen för normal drift av ditt program, rekommenderar vi att körs dina produktionsarbetsbelastningar i ett Kubernetes-namnområde som inte är aktiverad Dev blanksteg. Med hjälp av den här typen av Kubernetes namnområdes innebär att du ska skapa din produktion namnområde med hjälp av den `kubectl` CLI, eller kan CI/CD-systemet att skapa den under den första Helm-distributionen. _Att välja_ eller annat skapa ett utrymme med utveckling blanksteg verktyg lägger till Dev blanksteg instrumentation i det här namnområdet.

Här är ett exempel namnområdesstruktur som har stöd för funktionen, ”utveckling”-miljön, _och_ produktion, allt i ett Kubernetes-kluster:

![Exempel namnområdesstruktur](../media/common/cicd-namespaces.png)

> [!Tip]
> Om du redan har skapat en `prod` utrymme, och skulle bara som ska undantas från instrumentation Dev blanksteg (utan att ta bort det!), kan du göra det med följande Dev blanksteg CLI-kommando:
>
> `azds space remove -n prod --no-delete`
>
> Du kan behöva ta bort alla poddar i den `prod` namnområde när du har gjort detta så att de kan skapas på nytt utan Dev blanksteg instrumentation.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om utveckling i grupp med Azure Dev blanksteg](../team-development-netcore.md)