---
title: Självstudie – Skapa en terraform Base-mall i Azure med Yeoman
description: Lär dig hur du skapar en grundläggande Terraform-mall i Azure med hjälp av Yeoman.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: ba81d0ee797fd879fdadc3a6b25ca8f310383f61
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159163"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Självstudie: skapa en terraform Base-mall i Azure med Yeoman

I den här självstudien får du lära dig hur du använder kombinationen av [terraform](/azure/terraform/) och [Yeoman](https://yeoman.io/). Terraform är ett verktyg för att skapa en infrastruktur i Azure. Yeoman gör det enkelt att skapa terraform-moduler.

I den här artikeln får du lära dig hur du utför följande uppgifter:
> [!div class="checklist"]
> * Skapa en bas terraform-mall med hjälp av Yeoman-modulens Generator.
> * Testa terraform-mallen med två olika metoder.
> * Kör terraform-modulen med hjälp av en Docker-fil.
> * Kör terraform-modulen internt i Azure Cloud Shell.

## <a name="prerequisites"></a>Krav

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- **Visual Studio Code**: [Ladda ned Visual Studio Code](https://code.visualstudio.com/download) för din plattform.
- **Terraform**: [Installera terraform](/azure/virtual-machines/linux/terraform-install-configure ) för att köra modulen som skapats av Yeoman.
- **Docker**: [Installera Docker](https://www.docker.com/get-started) för att köra modulen som skapats av Yeoman Generator.
- **Go-programmeringsspråk**: [Installera go](https://golang.org/) som Yeoman-genererade test väskor är kod med hjälp av Go-språket.

>[!NOTE]
>De flesta av procedurerna i den här självstudien omfattar kommando rads gränssnittet. De steg som beskrivs gäller för alla operativ system och kommando rads verktyg. I exemplen valdes PowerShell för den lokala miljön och git-bash för Cloud Shell-miljön.

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

Kontrol lera att Yeoman har installerats genom att köra följande kommando:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Skapa en katalog för den Yeoman modulen

Yeoman-mallen genererar filer i den aktuella katalogen. Därför måste du skapa en katalog.

Den tomma katalogen måste placeras under $GOPATH/src. Mer information om den här sökvägen finns i artikel [inställningen GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

1. Navigera till den överordnade katalogen som du vill skapa en ny katalog från.

1. Kör följande kommando för att ersätta plats hållaren. I det här exemplet används ett katalog namn för `GeneratorDocSample`.

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

    - **Terraform module projekt namn** – ett värde för `doc-sample-module` används för exemplet.

        ![Projektnamn](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Vill du inkludera Docker-bildfilen?** -Ange `y`. Om du väljer `n`stöder den genererade modulens kod endast körning i enhetligt läge.

        ![Inkludera Docker-bildfil?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. Lista katalog innehållet för att visa de resulterande filerna som skapas:

    ```bash
    ls
    ```

    ![Lista över skapade filer](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Granska den genererade modulkoden

1. Starta Visual Studio-kod

1. Från menyraden väljer du **Fil > Öppna mapp** och väljer mappen som du skapade.

    ![Visual Studio-koden](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Följande filer skapades av generatorn för Yeoman-modulen. Mer information om dessa filer och deras användning finns i [Terratest i terraform-moduler.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster).

- `main.tf`-definierar en modul som kallas `random-shuffle`. Indatamängden är en `string_list`. Utdata är antalet permutationer.
- `variables.tf` – definierar de in-och utdata som används av modulen.
- `outputs.tf` – definierar modulens utdata. Här är det värdet som returneras av `random_shuffle`, som är en inbyggd, terraform-modul.
- `Rakefile` – definierar Bygg stegen. Dessa steg omfattar:
    - `build` – validerar formateringen för main.tf-filen.
    - `unit`-den genererade modulen Skeleton innehåller inte kod för ett enhets test. Om du vill ange ett enhetstestscenario, lägger du till den koden här.
    - `e2e` – kör en test från slut punkt till slut punkt för modulen.
- `test`
    - Testfall skrivs i Go.
    - Alla koder i test är heltäckande tester.
    - Från slut punkt till slut punkt görs ett försök att etablera alla objekt som definierats under `fixture`. Resultaten i `template_output.go`-filen jämförs med fördefinierade förväntade värden.
    - `Gopkg.lock` och `Gopkg.toml`: definierar beroenden. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testa din nya Terraform-modul med hjälp av en Docker-fil

Det här avsnittet visar hur du testar en terraform-modul med en Docker-fil.

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

1. I kommando tolken anger du `docker image ls` för att se din skapade modul `terra-mod-example` listan.

    ![Lista som innehåller den nya modulen](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Ange `docker run -it terra-mod-example /bin/sh`. När du har kört kommandot `docker run` är du i Docker-miljön. Nu kan du identifiera filen med hjälp av kommandot `ls`.

    ![Fil lista i Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

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

1. Slutför testet genom att ange `exit` och avsluta Docker-miljön.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Använda Yeoman-generatorn till att skapa och testa en modul i Cloud Shell

I det här avsnittet används Yeoman-generatorn för att skapa och testa en modul i Cloud Shell. Processen blir betydligt enklare om du använder Cloud Shell istället för att använda en Docker-fil. Med hjälp av Cloud Shell är följande produkter förinstallerade:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Starta en Cloud Shell-session

1. Starta en Azure Cloud Shell-session via [Azure-portalen](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com) eller [Azure-mobilappen](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Sidan **Välkommen till Azure Cloud Shell** öppnas. Välj **Bash (Linux)** .

    ![Välkommen till Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Om du inte redan har konfigurerat ett Azure Storage-konto visas följande skärm. Välj **Skapa lagring**.

    ![Ingen lagring har monterats](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell startar i det gränssnitt som du valde tidigare och visar information om molnenheten som precis skapats åt dig.

    ![Din molnenhet har skapats](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Förbereda en katalog för att lagra din terraform-modul

1. Nu har Cloud Shell redan konfigurerat GOPATH i miljövariablerna åt dig. Ange `go env` om du vill se sökvägen.

1. Skapa $GOPATH katalog om den inte redan finns: Ange `mkdir ~/go`.

1. Skapa en katalog i $GOPATHs katalogen. Den här katalogen används för att lagra olika projekt kataloger som skapats i det här exemplet. 

    ```bash
    mkdir ~/go/src
    ```

1. Skapa en katalog som ska innehålla din terraform-modul som ersätter plats hållaren. I det här exemplet används ett katalog namn för `my-module-name`.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Navigera till din modul katalog: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Skapa och testa Terraform-modulen

1. Kör följande kommando och följ anvisningarna. När du tillfrågas om du vill skapa Docker-filerna anger du `N`.

    ```bash
    yo az-terra-module
    ```

1. Kör följande kommando för att installera beroendena:

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

    ![Test-pass-resultat](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Installera och Använd kod tillägget för Azure-terraform Visual Studio](/azure/terraform/terraform-vscode-extension).