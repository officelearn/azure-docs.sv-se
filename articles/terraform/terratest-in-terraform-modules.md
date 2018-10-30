---
title: Testa Terraform-moduler i Azure med hjälp av Terratest
description: Lär dig hur du använder Terratest för att testa Terraform-modulerna.
services: terraform
ms.service: terraform
keywords: terraform, devops, storage account, azure, terratest, unit test, integration test
author: JunyiYi
manager: jeconnoc
ms.author: junyi
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 7feee063c7b311934f7d157a9dff62d803a041b0
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638727"
---
# <a name="test-terraform-modules-in-azure-using-terratest"></a>Testa Terraform-moduler i Azure med hjälp av Terratest

Terraform-moduler används för att skapa återanvändbara, sammansättningsbara och testningsbara komponenter. De inför inkapsling i begreppet ”infrastruktur som kod”.

Precis som med andra programkomponenter spelar kvalitetskontroll en viktig roll i Terraform-moduler. Tyvärr finns det inte mycket dokumentation som förklarar hur du skapar enhetstester och integreringstester i Terraform-moduler. Den här självstudien innehåller en infrastruktur för testning och metodtips som vi implementerade under skapandet av våra [Azure Terraform-moduler](https://registry.terraform.io/browse?provider=azurerm).

Vi gick igenom alla de mest populära testningsinfrastrukturerna och valde sedan att använda [Terratest](https://github.com/gruntwork-io/terratest). Terratest implementeras som ett Go-bibliotek. Det tillhandahåller en samling hjälpfunktioner och mönster för vanliga infrastrukturtestningsuppgifter, till exempel att göra HTTP-begäranden och SSH till en viss virtuell dator. Några av de stora fördelarna med Terratest är:

- **Det har praktiska hjälpkomponenter för att kontrollera infrastruktur.** Den här funktionen är användbar när du vill verifiera din verkliga infrastruktur i den verkliga miljön.
- **Mappstrukturen är organiserad och tydlig.** Din testfall organiseras på ett tydligt sätt och följer [standardmappstrukturen för Terraform-modulen](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Alla testfall skrivs i Go.** Eftersom de flesta Terraform-utvecklare redan är Go-utvecklare gör användning av Terratest att de inte behöver lära sig ännu ett programmeringsspråk. Dessutom är de enda beroenden som krävs för att köra testfall i Terratest Go och Terraform.
- **Den här infrastrukturen är mycket anpassningsbar.** Det är inte svårt att utöka ytterligare funktioner ovanpå Terratest, till exempel Azure-specifika funktioner.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här praktiska guiden är plattformsoberoende, så den kan köras på Windows, Linux eller MacOS. Innan du fortsätter installerar du följande programvara:

- **Programmeringsspråket Go**: Terraform-testfallen är skrivna i [Go](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) är ett beroendehanteringsverktyg för Go.
- **Azure CLI**: [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) är ett kommandoradsverktyg för att hantera Azure-resurser. (Terraform stöder autentisering till Azure via ett tjänsthuvudnamn eller [via Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage**: Vi kommer att använda den [körbara mage-filen](https://github.com/magefile/mage/releases) till att lära dig hur du förenklar körning av Terratest-fallen. 

## <a name="create-a-static-webpage-module"></a>Skapa en statisk webbplatsmodul

I den här självstudien skapar du en Terraform-modul som etablerar en statisk webbplats genom att ladda upp en enskild HTML-fil till Azure-lagringsblob. Den här modulen gör att användare över hela världen kan komma åt den här webbplatsen via en URL som returneras av modulen.

> [!NOTE]
> Alla filer som beskrivs i det här avsnittet bör skapas under din [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Skapa först en ny mapp med namnet `staticwebpage` under `src`-mappen för din GoPath. Den övergripande mappstrukturen för den här självstudien beskrivs nedan. (Filer som är markerade med en asterisk `(*)` utgör fokus i det här avsnittet.)

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

Modulen för den statiska webbplatsen accepterar tre indata, som deklareras i `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which all resources will be created."
}

variable "website_name" {
  description = "The website name which will be used to create a bunch of related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static homepage HTML in your local filesystem."
  default     = "index.html"
}
```

Som tidigare nämnts matar den här modulen även ut en URL som deklareras i `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

Nu kommer vi till den huvudsakliga logiken i den här modulen. Totalt kommer den att etablera fyra resurser:
- En resursgrupp vars namn är indata för `website_name` som läggs till av `-staging-rg`.
- Ett lagringskonto vars namn är indata för `website_name` som läggs till av `data001`. Men för att kunna följa namnbegränsningarna för lagringskonton tar modulen bort alla specialtecken och gör hela namnet till gemener.
- En container med det fasta namnet `wwwroot` skapas i lagringskontot ovan.
- En enskild HTML-fil som lästs från indata för `html_path` och laddats upp till `wwwroot/index.html`.

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

Terratest är traditionellt ett verktyg för integreringstester – vilket innebär att det etablerar verkliga resurser i en verklig miljö. Ibland blir sådana jobb exceptionellt stora, särskilt när du har stora mängder resurser som ska etableras. Lagringskontots namnkonverteringslogik, som beskrevs i föregående avsnitt, är ett bra exempel: vi behöver egentligen inte etablera några resurser – vi vill bara se till att namnkonverteringslogiken är korrekt.

Tack vare flexibiliteten i Terratest är det lätt att åstadkomma med hjälp av enhetstester. Enhetstester är testfall som körs lokalt (tillgång till Internet krävs dock fortfarande) genom att kommandona `terraform init` och `terraform plan` körs. Enhetstestfallen parsar utdata för `terraform plan` och letar efter de attributvärden som ska jämföras.

Resten av det här avsnittet beskriver hur vi använder Terratest för att implementera ett enhetstest för att kontrollera att lagringskontots namnkonverteringslogik är korrekt. Vi är bara intresserade av de filer som är markerade med en asterisk `(*)`.

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

Till en början är en tom HTML-fil `./test/fixtures/storage-account-name/empty.html` bara är en platshållare.

Filen `./test/fixtures/storage-account-name/main.tf` är testfallets stomme. Den accepterar en inmatning `website_name`, som även är indata för enhetstesterna. Dess logik visas här:

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

Slutligen är implementeringen av enhetstesterna den huvudsakliga komponenten: `./test/storage_account_name_unit_test.go`

Om du är Go-utvecklare ser du att den matchar signaturen för en klassisk Go-testfunktion genom att acceptera ett argument av typen `*testing.T`.

I brödtexten i enhetstestet har vi totalt fem fall som definieras i variabeln `testCases` (nyckeln som indata, medan värdet är förväntade utdata). För varje enhetstestfall kör vi först `terraform init` med inriktning på testfixturmappen (`./test/fixtures/storage-account-name/`). 

Efter det sparar ett `terraform plan`-kommando med specifika testfallsindata (se definitionen av `website_name` i `tfOptions`) resultatet till `./test/fixtures/storage-account-name/terraform.tfplan` (som inte anges i den övergripande mappstrukturen).

Sedan parsas den här resultatfilen till en kodläsbar struktur med hjälp av den officiella Terraform-planparsern.

Nu letar vi efter de attribut som vi är intresserade av (i det här fallet `name` på `azurerm_storage_account`) och jämför dem med förväntade utdata.

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
        // Specify test case folder and "-var" options
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

För att köra enhetstesterna behöver du slutföra följande steg i kommandoraden.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

Du ser det traditionella Go-testresultatet efter ungefär en minut.

### <a name="integration-test"></a>Integreringstest

Till skillnad från enhetstester krävs integreringstester för att etablera resurser till en verklig miljö från slutpunkt till slutpunkt-perspektivet. Terratest presterar väl för sådana uppgifter. Eftersom bästa praxis för Terraform-modulen också rekommenderar att `examples`-mappen ska innehåller några slutpunkt till slutpunkt-exempel borde det väl fungera att testa de exemplen som integreringstester? I det här avsnittet fokuserar vi på tre filer, som var och en markerats med en asterisk `(*)`.

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

Vi tar och börjar med exemplen. En ny exempelmapp med namnet `hello-world/` skapas i mappen `./examples/`. Här har vi en enkel HTML-sida som ska laddas upp `./examples/hello-world/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demostrate Terratest.</p>
</body>
</html>
```

Terraform-exemplet `./examples/hello-world/main.tf` liknar det som visas i enhetstestet, med bara en stor skillnad: det skriver även ut URL:en för den uppladdade HTML:en med namnet `homepage`.

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

Terratest- och den klassisk Go-testfunktionen visas igen i integreringstestfilen `./test/hello_world_example_test.go`.

Till skillnad från enhetstester skapar integreringstester verkliga resurser i Azure, och därför behöver du vara noga med att undvika namnkonflikter. (Var särskilt uppmärksam på vissa globalt unika namn, till exempel lagringskontonamn). Därför är det första steget i testningslogiken att generera en slumpmässig `websiteName` med hjälp av den `UniqueId()`-funktion som tillhandahålls av Terratest. Den här funktionen genererar ett slumpmässigt namn som innehåller gemener, versaler eller siffror. `tfOptions` gör att alla Terraform-kommandon riktar in sig på mappen `./examples/hello-world/` och ser även till att `website_name` anges till slumpmässigt `websiteName`.

Sedan körs `terraform init`, `terraform apply` och `terraform output` en i taget. Vi använde en annan hjälpfunktion, `HttpGetWithCustomValidation()`, som anges av Terratest för att se till att HTML:en överförs till URL:en för utdata-`homepage` som returneras av `terraform output` genom att jämföra HTTP Get-statuskoden med `200` och söka efter vissa nyckelord i HTML-innehållet. Slutligen ”utlovas” det att `terraform destroy` körs genom att `defer`-funktionen i Go används.

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

    // Generate a random website name to prevent naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

För att köra integreringstesterna behöver du slutföra följande steg i kommandoraden.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

Du ser det traditionella Go-testresultatet efter ungefär två minuter. Naturligtvis kan du även köra båda enhetstesterna samt integreringstesterna genom att köra:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Som du märker tar integreringstester mycket längre tid än enhetstester (två minuter för ett integreringsfall jämfört med en minut för fem enhetsfall). Det är dock fortfarande ditt beslut om när du ska använda enhetstester kontra integreringstester. Normalt föredrar vi att använda enhetstester för komplex logik med hjälp av Terraform HCL-funktioner och integreringstester från slutpunkt till slutpunkt-perspektivet för en användare.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Använda mage för att förenkla körning av Terratest-fall 

Som du har sett är det inte helt enkelt att köra testfall i gränssnittet eftersom du behöver navigera till olika kataloger och köra olika kommandon. Det är därför vi introducerar byggsystemet i våra projekt. I det här avsnittet använder vi en Go-byggsystemsmage för att utföra jobbet.

Det enda som krävs av mage är en `magefile.go` i projektets rotkatalog (markerad med `(+)` i följande bild).

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

Här är ett exempel på `./magefile.go`. I det här byggskriptet, som skrivits i Go, implementerade vi fem byggsteg:
- `Clean`: det här steget tar bort alla genererade/tillfälliga filer under testkörningarna.
- `Format`: det här steget körs `terraform fmt` och `go fmt` för att formatera din kodbas.
- `Unit`: det här steget körs alla enhetstester (med hjälp av funktionsnamnkonventionen `TestUT_*`) under mappen `./test/`.
- `Integration`: liknar `Unit`, men i stället för enhetstester kör den integreringstester (`TestIT_*`).
- `Full`: det här steget kör `Clean`, `Format`, `Unit', and `Integration (integrering) i följd.

```go
// +build mage

// Build script to format and run tests of a Terraform module project.
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// Default target when execute `mage` in shell
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

// A build step that removes temporary build/test files
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

På ett liknande sätt som körningen av de tidigare stegen kan du använda följande kommandon för att köra en fullständig testsvit:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage$ mage
```

Du kan ersätta den sista kommandoraden med eventuella mage-steg, till exempel `mage unit` eller `mage clean`. Du antar kanske att det fortfarande finns många kommandorader här, och det är en bra idé att bädda in `dep`-kommandon samt `az login` i mage-filen. Men vi visar inte koden här. Ett ytterligare steg för att använda mage är att stegen skulle kunna delas med hjälp av Go-paketsystemet. Mage-filer i alla dina moduler skulle alltså kunna förenklas genom att en vanlig implementering refereras och beroende deklareras (`mg.Deps()`).

> [!NOTE]
> **Alternativ: Konfigurera miljövariabler för tjänsthuvudnamn att köra acceptanstester**
> 
> I stället för att köra `az login` före tester kan du åstadkomma Azure-autentisering genom att konfigurera miljövariablerna för tjänsthuvudnamn. Terraform publicerar [en lista över miljövariabelnamn](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Endast de första fyra av dessa miljövariabler krävs.) Terraform publicerar även detaljerade instruktioner som förklarar hur du [hämta värdet för dessa miljövariabler.](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)

## <a name="next-steps"></a>Nästa steg

Mer information om Terratest finns på [dess GitHub-sida](https://github.com/gruntwork-io/terratest). Du kan hitta användbar information om mage på i [dess GitHub-sida](https://github.com/magefile/mage) och [startsida](https://magefile.org/).
