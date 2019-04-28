---
title: Skapa en grundläggande Terraform-mall i Azure med hjälp av Yeoman
description: Lär dig hur du skapar en grundläggande Terraform-mall i Azure med hjälp av Yeoman.
services: terraform
ms.service: azure
keywords: terraform, utvecklar, virtuell dator, azure, yeoman
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/08/2018
ms.openlocfilehash: 7e66f374a1f5f4fb050f366fdad0e787292101f8
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128192"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Skapa en grundläggande Terraform-mall i Azure med hjälp av Yeoman

[Terraform](https://docs.microsoft.com/azure/terraform/
) gör det möjligt att enkelt skapa infrastruktur på Azure. [Yeoman](https://yeoman.io/) förenklar modulutvecklarens jobb avsevärt vid skapandet av Terraform-moduler samtidigt som det utgör ett utmärkt ramverk för *bästa praxis*.

I den här artikeln får du lära dig hur du använder Yeoman-modulgeneratorn för att skapa en grundläggande Terraform-mall. Sedan lär du dig hur du testar din nya Terraform-mall med hjälp av två olika metoder:

- Kör Terraform-modulen med hjälp av en Docker-fil som du skapar i den här artikeln.
- Kör Terraform-modulen internt i Azure Cloud Shell.

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration**: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- **Visual Studio Code**: Vi kommer att använda [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) för att undersöka filer som skapats av Yeoman-generatorn. Du kan dock använda valfri kodredigerare.
- **Terraform**: Du behöver en [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure )-installation för att köra den modul som skapas av Yeoman.
- **Docker**: Vi kommer att använda [Docker](https://www.docker.com/get-started) för att köra den modul som skapas av Yeoman-generatorn. (Om du vill kan du kan använda Ruby i stället för Docker för att köra exempelmodulen.)
- **Programmeringsspråket Go**: Du behöver en [Go](https://golang.org/)-installation eftersom de testfall som genereras av Yeoman är skrivna i Go.

>[!NOTE]
>De flesta av procedurerna i den här självstudien omfattar kommandoradsposter. Stegen som beskrivs här gäller för alla operativsystem och kommandoradsverktyg. I våra exempel har vi valt att använda PowerShell för lokal miljö och Git Bash för Cloud Shell-miljö.

## <a name="prepare-your-environment"></a>Förbered din miljö

### <a name="install-nodejs"></a>Installera Node.js

För att använda Terraform i Cloud Shell måste du [installera Node.js](https://nodejs.org/en/download/) 6.0+.

>[!NOTE]
>Kontrollera om Node.js är installerat genom att öppna ett terminalfönster och ange `node --version`.

### <a name="install-yeoman"></a>Installera Yeoman

Från en kommandotolk anger du `npm install -g yo`.

![Installera Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Installera Yeoman-mallen för Terraform-modulen

Från en kommandotolk anger du `npm install -g generator-az-terra-module`.

![Installera generator-az-terra-modulen](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>Om du vill kontrollera att Yeoman har installerats, anger du `yo --version` från ett terminalfönster.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Skapa en tom mapp för att lagra den Yeoman-genererade modulen

Yeoman-mallen genererar filer i den **aktuella katalogen**. Därför måste du skapa en katalog.

>[!Note]
>Den tomma katalogen måste placeras under $GOPATH/src. Du hittar anvisningar [här](https://github.com/golang/go/wiki/SettingGOPATH) för att åstadkomma detta.

Från en kommandotolk:

1. Navigera till den överordnade katalogen där du vill spara den nya tomma katalog som vi håller på att skapa.
1. Ange `mkdir <new-directory-name>`.

    > [!NOTE]
    > Ersätt `<new-directory-name>` med namnet på den nya katalogen. I det här exemplet döpte vi den nya katalogen till `GeneratorDocSample`.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Navigera till den nya katalogen genom att skriva `cd <new directory's name>` och sedan trycka på **Enter**.

    ![Navigera till den nya katalogen](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >Om du vill kontrollera att den här katalogen är tom, anger du `ls`. Det bör inte finnas några filer i den lista som blir resultatet av det här kommandot.

## <a name="create-a-base-module-template"></a>Skapa en basmodulsmall

Från en kommandotolk:

1. Ange `yo az-terra-module`.

1. Följ instruktionerna på skärmen för att tillhandahålla följande information:

    - *Terraform-modulens projektnamn*

        ![Projektnamn](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >I det här exemplet har vi angett `doc-sample-module`.

    - *Vill du inkludera Docker-bildfilen?*

        ![Inkludera Docker-bildfil?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Ange `y`. Om du väljer **n** så stöder den genererade modulkoden bara körning i enhetligt läge.

3. Ange `ls` för att visa de resulterande filer som skapas.

    ![Lista över skapade filer](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Granska den genererade modulkoden

1. Starta Visual Studio-kod

1. Från menyraden väljer du **Fil > Öppna mapp** och väljer mappen som du skapade.

    ![Visual Studio-koden](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Låt oss ta en titt på några av de filer som har skapats av Yeoman-modulgeneratorn.

>[!Note]
>I den här artikeln kommer vi att använda filerna main.tf, variables.tf och outputs.tf som de har skapats av Yeoman-modulgeneratorn. Men när du skapar dina egna moduler redigerar du dessa filer för att passa din Terraform-moduls funktioner. En mer detaljerad beskrivning av de här filerna och deras användning finns i [Terratest i Terraform-moduler.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

### <a name="maintf"></a>main.tf

Definierar en modul som heter *random-shuffle*. Dessa indata utgörs av en *string_list*. Utdata är antalet permutationer.

### <a name="variablestf"></a>variables.tf

Definierar de indata- och utdatavariabler som används av modulen.

### <a name="outputstf"></a>outputs.tf

Definierar vad modulen matar ut. Här är det värdet som returneras av **random_shuffle** som är en inbyggd Terraform-modul.

### <a name="rakefile"></a>Rakefile

Definierar stegen för att skapa versionen. Dessa steg omfattar:

- **build**: Verifierar formateringen av main.tf-filen.
- **enhet**: Den genererade modulstommen innehåller inte kod för ett enhetstest. Om du vill ange ett enhetstestscenario, lägger du till den koden här.
- **e2e**: Kör ett heltäckande test av modulen.

### <a name="test"></a>test

- Testfall skrivs i Go.
- Alla koder i test är heltäckande tester.
- Heltäckande tester försöker använda Terraform för att tillhandahålla alla objekt som definierats under **fixture** och jämför sedan resultatet i **template_output.go**-koden med de fördefinierade förväntade värdena.
- **Gopkg.lock** och **Gopkg.toml**: Definiera beroendena. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testa din nya Terraform-modul med hjälp av en Docker-fil

>[!NOTE]
>I vårt exempel kör vi modulen som en lokal modul utan faktisk beröring med Azure.

### <a name="confirm-docker-is-installed-and-running"></a>Bekräfta att Docker har installerats och körs

Från en kommandotolk anger du `docker version`.

![Dockerversion](media/terraform-vscode-module-generator/ymg-docker-version.png)

Resultatet bekräftar att Docker är installerat.

Tryck på `docker info` för att bekräfta att Docker faktiskt körs.

![Docker info](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Konfigurera en Docker-container

1. Från en kommandotolk anger du följande:

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    Meddelandet **Successfully built (har skapats)** visas.

    ![Successfully built](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Skriv `docker image ls` vid kommandotolken.

    Du kommer att se din nyligen skapade modul *terra-mod-example* i listan.

    ![Lagringsplatsresultat](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >Modulnamnet, *terra-mod-example*, specificerades i kommandot som du angav i steg 1 ovan.

1. Ange `docker run -it terra-mod-example /bin/sh`.

    Du kör nu i Docker och kan placera filen i listan genom att ange `ls`.

    ![Ange Docker-fil i lista](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Skapa modulen

1. Ange `bundle install`.

    Vänta på meddelandet **Bundle complete (paket slutfört)** och fortsätt sedan med nästa steg.

1. Ange `rake build`.

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Kör slutpunkt till slutpunkt-testet

1. Ange `rake e2e`.

1. Efter en liten stund visas meddelandet **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Ange `exit` för att slutföra slutpunkt till slutpunkt-testet och avsluta Docker-miljön.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Använda Yeoman-generatorn till att skapa och testa en modul i Cloud Shell

I det föregående avsnittet har du lärt dig hur du testar en Terraform-modul med hjälp av en Docker-fil. I det här avsnittet använder du Yeoman-generatorn till att skapa och testa en modul i Cloud Shell.

Processen blir betydligt enklare om du använder Cloud Shell istället för att använda en Docker-fil. Om du använder Cloud Shell:

- behöver du inte installera Node.js
- behöver du inte installera Yeoman
- behöver du inte installera Terraform

är alla dessa objekt förinstallerade i Cloud Shell.

### <a name="start-a-cloud-shell-session"></a>Starta en Cloud Shell-session

1. Starta en Azure Cloud Shell-session via [Azure-portalen](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com) eller [Azure-mobilappen](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Sidan **Välkommen till Azure Cloud Shell** öppnas. Välj **Bash (Linux)**. (Power Shell stöds inte.)

    ![Välkommen till Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >I det här exemplet valdes Bash (Linux).

1. Om du inte redan har konfigurerat ett Azure Storage-konto visas följande skärm. Välj **Skapa lagring**.

    ![Ingen lagring har monterats](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell startar i det gränssnitt som du valde tidigare och visar information om molnenheten som precis skapats åt dig.

    ![Din molnenhet har skapats](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Förbereda en mapp som ska innehålla Terraform-modulen

1. Nu har Cloud Shell redan konfigurerat GOPATH i miljövariablerna åt dig. Ange `go env` om du vill se sökvägen.

1. Skapa $GOPATH-mappen om det inte redan finns en sådan: Ange `mkdir ~/go`.

1. Skapa en mapp i $GOPATH-mappen: Ange `mkdir ~/go/src`. Den här mappen som används för att lagra och organisera olika projekt mappar som du kan skapa, till exempel den `<your-module-name>` mapp som vi skapar i nästa steg.

1. Skapa en mapp som ska innehålla Terraform-modulen: Ange `mkdir ~/go/src/<your-module-name>`.

    >[!NOTE]
    >I det här exemplet väljer vi `my-module-name` som mappnamn.

1. Navigera till modulmappen: Ange `cd ~/go/src/<your-module-name>`

### <a name="create-and-test-your-terraform-module"></a>Skapa och testa Terraform-modulen

1. Ange `yo az-terra-module` och följ anvisningarna i guiden.

    >[!NOTE]
    >När du tillfrågas om du vill skapa Docker-filerna kan du ange `N`.

1. Ange `bundle install` för att installera beroenden.

    Vänta på meddelandet **Bundle complete (paket slutfört)** och fortsätt sedan med nästa steg.

1. Ange `rake build` för att skapa modulen.

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Ange `rake e2e` för att köra slutpunkt till slutpunkt-testet.

1. Efter en liten stund visas meddelandet **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Installera och använda Azure Terraform-tillägget för Visual Studio Code.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
