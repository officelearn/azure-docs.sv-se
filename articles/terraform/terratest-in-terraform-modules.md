---
title: Testa Terraform-moduler i Azure med hjälp av Terratest
description: Lär dig hur du använder Terratest för att testa Terraform-modulerna.
services: terraform
ms.service: azure
keywords: terraform, devops, storage account, azure, terratest, unit test, integration test
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 03/19/2019
ms.openlocfilehash: 9d621905122ab7bf64432323d7d11cf8f1b50750
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224013"
---
# <a name="test-terraform-modules-in-azure-by-using-terratest"></a>Testa Terraform-moduler i Azure med hjälp av Terratest

> [!NOTE]
> Exempelkoden i den här artikeln fungerar inte med version 0,12 (eller senare).

Du kan använda Azure Terraform-moduler för att skapa återanvändbara, sammansättningsbara och testningsbara komponenter. Terraform-moduler innehåller inkapsling som är användbar för att implementera infrastruktur som kodprocesser.

Det är viktigt att implementera kvalitetskontroll när du skapar Terraform-moduler. Tyvärr finns det endast begränsad dokumentation som förklarar hur du skapar enhetstester och integreringstester i Terraform-moduler. Den här självstudien innehåller en infrastruktur för testning och metodtips som vi implementerade under skapandet av våra [Azure Terraform-moduler](https://registry.terraform.io/browse?provider=azurerm).

Vi tittade på alla de mest populära testningsinfrastrukturerna och valde [Terratest](https://github.com/gruntwork-io/terratest) för att testa våra Terraform-moduler. Terratest implementeras som ett Go-bibliotek. Terratest tillhandahåller en samling hjälpfunktioner och mönster för vanliga infrastrukturtestningsuppgifter, till exempel att göra HTTP-begäranden och använda SSH för att komma åt en viss virtuell dator. I följande lista beskrivs några av de stora fördelarna med att använda Terratest:

- **Det har praktiska hjälpkomponenter för att kontrollera infrastruktur**. Den här funktionen är användbar när du vill verifiera din verkliga infrastruktur i den verkliga miljön.
- **Mappstrukturen är organiserad och tydlig**. Din testfall organiseras på ett tydligt sätt och följer [standardmappstrukturen för Terraform-modulen](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Alla testfall skrivs i Go**. De flesta utvecklare som använder Terraform är Go-utvecklare. Om du är Go-utvecklare behöver du inte lära dig något annat programmeringsspråk för att använda Terratest. Dessutom är Go och Terraform de enda beroenden som krävs för att du ska kunna köra testfall i Terratest.
- **Den här infrastrukturen är mycket utökningsbar**. Du kan utöka ytterligare funktioner ovanpå Terratest, till exempel Azure-specifika funktioner.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här praktiska artikeln är plattformsoberoende. Du kan köra de kodexempel som vi använder i den här artikeln på Windows, Linux eller MacOS. 

Innan du börjar installerar du följande programvara:

- **Programmeringsspråket Go**: Terraform-testfallen är skrivna i [Go](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) är ett beroendehanteringsverktyg för Go.
- **Azure CLI**: [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) är ett kommandoradsverktyg som du kan använda för att hantera Azure-resurser. (Terraform stöder autentisering till Azure via ett tjänsthuvudnamn eller [via Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage**: Vi använder [den körbara mage-filen](https://github.com/magefile/mage/releases) för att visa hur du förenklar körningen av Terratest-fall. 

## <a name="create-a-static-webpage-module"></a>Skapa en statisk webbplatsmodul

I den här självstudien skapar du en Terraform-modul som etablerar en statisk webbplats genom att ladda upp en enskild HTML-fil till en Azure Storage-blob. Den här modulen ger användare från hela världen åtkomst till webbplatsen via en URL som modulen returnerar.

> [!NOTE]
> Skapa alla filer som beskrivs i det här avsnittet under din [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH)-plats.

Skapa först en ny mapp med namnet `staticwebpage` under `src`-mappen för din GoPath. Den övergripande mappstrukturen för den här självstudien visas i följande exempel. Filer som är markerade med en asterisk `(*)` utgör huvudfokus i det här avsnittet.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

Modulen för den statiska webbplatsen accepterar tre indata. Indata deklareras i `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

Som vi nämnde tidigare i artikeln matar den här modulen även ut en URL som deklareras i `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

Den huvudsakliga logiken i modulen etablerar fyra resurser:
- **resursgrupp**: Namnet på resursgruppen är de `website_name`-indata som läggs till av `-staging-rg`.
- **lagringskonto**: Namnet på lagringskontot är de `website_name`-indata som läggs till av `data001`. För att följa namnbegränsningar för lagringskontot tar modulen bort alla specialtecken och använder gemener i hela lagringskontonamnet.
- **container med fast namn**: Containern heter `wwwroot` och skapas i lagringskontot.
- **enskild HTML-fil**: HTML-filen läses in från `html_path`-indata och laddas upp till `wwwroot/index.html`.

Logiken för den statiska webbplatsens modul implementeras i `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Enhetstest

Terratest är utformat för integreringstester. För detta ändamål etablerar Terratest verkliga resurser i en verklig miljö. Ibland kan integreringstestjobb bli exceptionellt stora, särskilt när du har ett stort antal resurser som ska etableras. Den logik som konverterar lagringskontonamn som vi refererar till i föregående avsnitt är ett bra exempel. 

Men vi behöver egentligen inte etablera några resurser. Vi vill bara se till att logiken för namngivningskonvertering är rätt. Tack vare flexibiliteten i Terratest kan vi använda enhetstester. Enhetstester är testfall som körs lokalt (tillgång till Internet krävs dock). Enhetstestfall kör kommandona `terraform init` och `terraform plan` för att parsa utdata för `terraform plan` och leta efter de attributvärden som ska jämföras.

Resten av det här avsnittet beskriver hur vi använder Terratest för att implementera ett enhetstest för att kontrollera att den logik som används för att konvertera lagringskontonamn är korrekt. Vi är bara intresserade av de filer som är markerade med en asterisk `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Först använder vi en tom HTML-fil med namnet `./test/fixtures/storage-account-name/empty.html` som platshållare.

Filen `./test/fixtures/storage-account-name/main.tf` är testfallets ram. Den accepterar en inmatning, `website_name`, som även är indata för enhetstesterna. Logiken visas här:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

Den huvudsakliga komponenten är implementeringen av enhetstesterna i `./test/storage_account_name_unit_test.go`.

Go-utvecklare märker förmodligen att enhetstestet matchar signaturen för en klassisk Go-testfunktion genom att acceptera ett argument av typen `*testing.T`.

I brödtexten i enhetstestet har vi totalt fem fall som definieras i variabeln `testCases` (`key` som indata och `value` som förväntade utdata). För varje enhetstestfall kör vi först `terraform init` med testfixturmappen (`./test/fixtures/storage-account-name/`) som mål. 

Därefter sparar ett `terraform plan`-kommando som använder specifika testfallsindata (se definitionen av `website_name` i `tfOptions`) resultatet till `./test/fixtures/storage-account-name/terraform.tfplan` (som inte anges i den övergripande mappstrukturen).

Den här resultatfilen parsas till en kodläsbar struktur med hjälp av den officiella Terraform-planparsern.

Nu letar vi efter de attribut som vi är intresserade av (i det här fallet `name` på `azurerm_storage_account`) och jämför resultatet med förväntade utdata:

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

För att köra enhetstesterna slutför du följande steg i kommandoraden:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Det traditionella Go-testresultatet returneras efter cirka en minut.

### <a name="integration-test"></a>Integreringstest

Till skillnad från enhetstester måste integreringstester etablera resurser till en verklig miljö för ett slutpunkt till slutpunkt-perspektiv. Terratest presterar väl för den här typen av uppgift. 

Bästa praxis för Terraform-moduler innefattar att installera mappen `examples`. Mappen `examples` innehåller några slutpunkt till slutpunkt-exempel. För att undvika att arbeta med verkliga data kan vi väl testa de exemplen som integreringstester? I det här avsnittet fokuserar vi på de tre filer som är markerade med en asterisk `(*)` i följande mappstruktur:

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Vi tar och börjar med exemplen. En ny exempelmapp med namnet `hello-world/` skapas i mappen `./examples/`. Här har vi en enkel HTML-sida som ska laddas upp: `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

Terraform-exemplet `./examples/hello-world/main.tf` liknar det som visas i enhetstestet. Det finns en viktig skillnad: exemplet skriver även ut URL: en för det uppladdade HTML som en webbplats med namnet `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

Vi använder Terratest och klassiska Go-testfunktioner igen i integreringstestfilen `./test/hello_world_example_test.go`.

Till skillnad från enhetstester skapar integreringstester verkliga resurser i Azure. Därför måste du vara noga med att undvika namnkonflikter. (Var särskilt uppmärksam på vissa globalt unika namn, till exempel lagringskontonamn.) Därför är det första steget i testningslogiken att generera ett slumpmässigt `websiteName` med hjälp av den `UniqueId()`-funktion som tillhandahålls av Terratest. Den här funktionen genererar ett slumpmässigt namn som innehåller gemener, versaler eller siffror. `tfOptions` gör så att alla Terraform-kommandon inriktas på mappen `./examples/hello-world/`. Det ser även till att `website_name` ställs in på det slumpmässiga `websiteName`.

Sedan körs `terraform init`, `terraform apply` och `terraform output` en i taget. Vi använder en annan hjälpfunktion, `HttpGetWithCustomValidation()`, som tillhandahålls av Terratest. Vi använder hjälpfunktionen för att se till att HTML laddas upp till den utdata-URL `homepage` som returneras av `terraform output`. Vi jämför HTTP GET-statuskoden med `200` och letar efter vissa nyckelord i HTML-innehållet. Slutligen ”utlovas” det att `terraform destroy` körs genom att `defer`-funktionen i Go används.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

För att köra integreringstesterna slutför du följande steg i kommandoraden:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Det traditionella Go-testresultatet returneras efter cirka två minuter. Du kan även köra båda enhetstesterna samt integreringstesterna genom att köra de här kommandona:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Integreringstester tar mycket längre tid än enhetstester (två minuter för ett integreringsfall jämfört med en minut för fem enhetsfall). Men det är ditt beslut om du vill använda enhetstester eller integreringstester i ett scenario. Normalt föredrar vi att använda enhetstester för komplex logik med hjälp av Terraform HCL-funktioner. Vi använder vanligtvis integreringstester för slutpunkt till slutpunkt-perspektivet för användare.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Använda mage för att förenkla körning av Terratest-fall 

Att köra testfall i Azure Cloud Shell är inte helt enkelt. Du måste gå till olika kataloger och köra olika kommandon. För att undvika användning av Cloud Shell introducerar vi byggsystemet i vårt projekt. I det här avsnittet använder vi ett Go-byggsystem, mage, för jobbet.

Det enda som krävs av mage är en `magefile.go` i projektets rotkatalog (markerad med `(+)` i följande exempel):

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Här är ett exempel på `./magefile.go`. I det här byggskriptet, som skrivits i Go, implementerar vi fem byggsteg:
- `Clean`: Steget tar bort alla genererade och tillfälliga filer som genereras under testkörningar.
- `Format`: Steget kör `terraform fmt` och `go fmt` för att formatera din kodbas.
- `Unit`: Steget kör alla enhetstester (med hjälp av funktionsnamnskonventionen `TestUT_*`) i mappen `./test/`.
- `Integration`: Steget liknar `Unit`, men i stället för enhetstester kör det integreringstester (`TestIT_*`).
- `Full`: Steget kör `Clean`, `Format`, `Unit` och `Integration` i följd.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

Du kan använda följande kommandon för att köra en fullständig testsvit. Koden liknar de körande stegen som vi använde i ett tidigare avsnitt. 

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only required when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage$ mage
```

Du kan ersätta den sista kommandoraden med ytterligare mage-steg. Du kan till exempel använda `mage unit` eller `mage clean`. Det är en bra idé att bädda in `dep`-kommandon och `az login` i mage-filen. Vi visar inte koden här. 

Med mage kan du även dela stegen med hjälp av Go-paketsystemet. I det fallet kan du förenkla mage-filer över alla dina moduler genom att endast referera en vanlig implementering refereras och deklarera beroenden (`mg.Deps()`).

**Valfritt: Konfigurera miljövariabler för tjänsthuvudnamn att köra acceptanstester**
 
I stället för att köra `az login` före tester kan du slutföra Azure-autentisering genom att konfigurera miljövariablerna för tjänsthuvudnamn. Terraform publicerar en [lista över miljövariabelnamn](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Endast de första fyra av dessa miljövariabler krävs.) Terraform publicerar även detaljerade instruktioner som förklarar hur du [hämtar värdet för dessa miljövariabler](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Nästa steg

* Mer information om Terratest finns på [GitHub-sidan för Terratest](https://github.com/gruntwork-io/terratest).
* Information om mage finns på [GitHub-sidan för mage](https://github.com/magefile/mage) och [mage-webbplatsen](https://magefile.org/).
