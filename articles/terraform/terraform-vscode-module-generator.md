---
title: Självstudiekurs - Skapa en Terraform-basmall i Azure med Yeoman
description: Lär dig hur du skapar en grundläggande Terraform-mall i Azure med hjälp av Yeoman.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 82c3f5e640789547abb716b55959e1821a61e6d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472153"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Självstudiekurs: Skapa en Terraform-basmall i Azure med Yeoman

I den här självstudien får du lära dig hur du använder kombinationen av [Terraform](/azure/terraform/) och [Yeoman](https://yeoman.io/). Terraform är ett verktyg för att skapa infrastruktur på Azure. Yeoman gör det enkelt att skapa Terraform-moduler.

I den här artikeln får du lära dig hur du utför följande uppgifter:
> [!div class="checklist"]
> * Skapa en basmall för Terraform med yeomanmodulgeneratorn.
> * Testa Terraform-mallen med två olika metoder.
> * Kör Terraform-modulen med en Docker-fil.
> * Kör Terraform-modulen internt i Azure Cloud Shell.

## <a name="prerequisites"></a>Krav

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- **Visual Studio Code:** [Ladda ner Visual Studio-kod](https://code.visualstudio.com/download) för din plattform.
- **Terraform:** [Installera Terraform](terraform-install-configure.md) för att köra modulen som skapats av Yeoman.
- **Docker:** [Installera Docker](https://www.docker.com/get-started) för att köra modulen som skapats av Yeoman-generatorn.
- **Gå programmeringsspråk:** [Installera Go](https://golang.org/) som Yeoman-genererade testfall är kod med go-språket.

>[!NOTE]
>De flesta av procedurerna i den här självstudien involverar kommandoradsgränssnittet. De steg som beskrivs gäller för alla operativsystem och kommandoradsverktyg. För exemplen valdes PowerShell för den lokala miljön och Git Bash för molnskalmiljön.

## <a name="prepare-your-environment"></a>Förbered din miljö

### <a name="install-nodejs"></a>Installera Node.js

För att använda Terraform i Cloud Shell måste du [installera Node.js](https://nodejs.org/en/download/) 6.0+.

>[!NOTE]
>Kontrollera om Node.js är installerat genom att öppna ett terminalfönster och ange `node --version`.

### <a name="install-yeoman"></a>Installera Yeoman

Kör följande kommando:

```bash
npm install -g yo
```

![Installera Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Installera Yeoman-mallen för Terraform-modulen

Kör följande kommando:

```bash
npm install -g generator-az-terra-module
```

![Installera generator-az-terra-modulen](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

Om du vill kontrollera att Yeoman är installerat kör du följande kommando:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Skapa en katalog för den Yeoman-genererade modulen

Yeoman-mallen genererar filer i den aktuella katalogen. Därför måste du skapa en katalog.

Den tomma katalogen måste placeras under $GOPATH/src. Mer information om den här sökvägen finns i artikeln [Ange GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

1. Navigera till den överordnade katalog som du vill skapa en ny katalog från.

1. Kör följande kommando som ersätter platshållaren. I det här exemplet `GeneratorDocSample` används ett katalognamn på.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Navigera till den nya katalogen:

    ```bash
    cd <new-directory-name>
    ```

    ![Navigera till den nya katalogen](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>Skapa en basmodulsmall

1. Kör följande kommando:

    ```bash
    yo az-terra-module
    ```

1. Följ instruktionerna på skärmen för att tillhandahålla följande information:

    - **Terraform modul projekt** Namn `doc-sample-module` - Ett värde av används för exempel.

        ![Projektnamn](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Vill du inkludera Docker-bildfilen?** - `y`Skriv in . Om du `n`väljer stöder den genererade modulkoden endast att köras i inbyggt läge.

        ![Inkludera Docker-bildfil?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. Lista kataloginnehållet om du vill visa de resulterande filerna som skapas:

    ```bash
    ls
    ```

    ![Lista över skapade filer](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Granska den genererade modulkoden

1. Starta Visual Studio-kod

1. Från menyraden väljer du **Fil > Öppna mapp** och väljer mappen som du skapade.

    ![Visual Studio-koden](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Följande filer skapades av Yeoman modul generator. Mer information om dessa filer och deras användning finns [i Terratest i Terraform-moduler.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster).

- `main.tf`- Definierar en `random-shuffle`modul som heter . Ingången är `string_list`en . Utdata är antalet permutationer.
- `variables.tf`- Definierar in- och utdatavariabler som används av modulen.
- `outputs.tf`- Definierar vad modulen matar ut. Här är det värdet som `random_shuffle`returneras av , som är en inbyggd Terraform-modul.
- `Rakefile`- Definierar byggstegen. Dessa steg omfattar:
    - `build`- Validerar formateringen av main.tf filen.
    - `unit`- Det genererade modulskelettet innehåller inte kod för ett enhetstest. Om du vill ange ett enhetstestscenario, lägger du till den koden här.
    - `e2e`- Kör ett end-to-end test av modulen.
- `test`
    - Testfall skrivs i Go.
    - Alla koder i test är heltäckande tester.
    - End-to-end-tester försöker etablera alla objekt `fixture`som definierats under . Resultaten i `template_output.go` filen jämförs med de fördefinierade förväntade värdena.
    - `Gopkg.lock`och `Gopkg.toml`: Definierar beroenden. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testa din nya Terraform-modul med hjälp av en Docker-fil

Det här avsnittet visar hur du testar en Terraform-modul med hjälp av en Docker-fil.

>[!NOTE]
>I det här exemplet körs modulen lokalt. inte på Azure.

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

    ![Meddelande som anger en lyckad version](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Från kommandotolken `docker image ls` anger du för `terra-mod-example` att se den skapade modulen i listan.

    ![Lista som innehåller den nya modulen](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Ange `docker run -it terra-mod-example /bin/sh`. När du `docker run` har kört kommandot är du i Docker-miljön. Då kan du identifiera filen med `ls` kommandot.

    ![Fillista i Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Skapa modulen

1. Kör följande kommando:

    ```bash
    bundle install
    ```

1. Kör följande kommando:

    ```bash
    rake build
    ```

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Kör slutpunkt till slutpunkt-testet

1. Kör följande kommando:

    ```bash
    rake e2e
    ```

1. Efter en liten stund visas meddelandet **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Ange `exit` för att slutföra testet och avsluta Docker-miljön.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Använda Yeoman-generatorn till att skapa och testa en modul i Cloud Shell

I det här avsnittet används Yeoman-generatorn för att skapa och testa en modul i Cloud Shell. Processen blir betydligt enklare om du använder Cloud Shell istället för att använda en Docker-fil. Med Cloud Shell är följande produkter förinstallerade:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Starta en Cloud Shell-session

1. Starta en Azure Cloud Shell-session via [Azure-portalen](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com) eller [Azure-mobilappen](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Sidan **Välkommen till Azure Cloud Shell** öppnas. Välj **Bash (Linux)**.

    ![Välkommen till Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Om du inte redan har konfigurerat ett Azure Storage-konto visas följande skärm. Välj **Skapa lagring**.

    ![Ingen lagring har monterats](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell startar i det gränssnitt som du valde tidigare och visar information om molnenheten som precis skapats åt dig.

    ![Din molnenhet har skapats](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Förbereda en katalog för att hålla din Terraform-modul

1. Nu har Cloud Shell redan konfigurerat GOPATH i miljövariablerna åt dig. Ange `go env` om du vill se sökvägen.

1. Skapa $GOPATH-katalogen om det inte redan finns `mkdir ~/go`någon katalog: Ange .

1. Skapa en katalog i $GOPATH katalogen. Den här katalogen används för att lagra de olika projektkataloger som skapats i det här exemplet. 

    ```bash
    mkdir ~/go/src
    ```

1. Skapa en katalog som innehåller terraform-modulen som ersätter platshållaren. I det här exemplet `my-module-name` används ett katalognamn på.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Navigera till modulkatalogen: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Skapa och testa Terraform-modulen

1. Kör följande kommando och följ instruktionerna. När du tillfrågas om du vill `N`skapa Docker-filerna anger du .

    ```bash
    yo az-terra-module
    ```

1. Kör följande kommando för att installera beroenden:

    ```bash
    bundle install
    ```

1. Kör följande kommando för att skapa modulen:

    ```bash
    rake build
    ```

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Kör följande kommando för att köra texten:

    ```bash
    rake e2e
    ```

    ![Test-pass resultat](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Installera och använd Azure Terraform Visual Studio Code-tillägget](/azure/terraform/terraform-vscode-extension).