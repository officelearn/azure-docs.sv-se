---
title: Snabbstart - Skapa en Terraform-konfiguration för Azure
description: Kom igång med Terraform på Azure genom att distribuera en Azure Cosmos DB och Azure Container Instances.
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 92f66fcfeac69b66eb8cdb0dc58b7a3171d45e10
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77470029"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Snabbstart: Skapa en Terraform-konfiguration för Azure

I den här snabbstarten skapar du Azure-resurser med Terraform. Stegen i den här artikeln går igenom att skapa följande resurser:

> [!div class="checklist"]
> * Azure Cosmos DB-instans
> * Azure Container-instans
> * App som fungerar över dessa två resurser

## <a name="create-first-configuration"></a>Skapa den första konfigurationen

I det här avsnittet skapar du konfigurationen för en Azure Cosmos DB-instans.

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna Azure Cloud Shell.

1. Starta Cloud Shell-redigeraren:

    ```bash
    code main.tf
    ```

1. Konfigurationen i det här steget modellerar ett par Azure-resurser. Dessa resurser inkluderar en Azure-resursgrupp och en Azure Cosmos DB-instans. Ett slumpmässigt heltal används för att skapa ett unikt Cosmos DB-förekomstnamn. Dessutom konfigureras flera Cosmos DB-inställningar. Mer information finns i [cosmos DB Terraform-referensen](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html). Kopiera och klistra in följande Terraform-konfiguration i redigeraren:

    ```hcl
    resource "azurerm_resource_group" "vote-resource-group" {
      name     = "vote-resource-group"
      location = "westus"
    }

    resource "random_integer" "ri" {
      min = 10000
      max = 99999
    }

    resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
      name                = "tfex-cosmos-db-${random_integer.ri.result}"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      offer_type          = "Standard"
      kind                = "GlobalDocumentDB"

      consistency_policy {
        consistency_level       = "BoundedStaleness"
        max_interval_in_seconds = 10
        max_staleness_prefix    = 200
      }

      geo_location {
        location          = "westus"
        failover_priority = 0
      }
    }
    ```

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

## <a name="run-the-configuration"></a>Kör konfigurationen

I det här avsnittet använder du flera Terraform-kommandon för att köra konfigurationen.

1. Kommandot [terraform init](https://www.terraform.io/docs/commands/init.html) initierar arbetskatalogen. Kör följande kommando i Cloud Shell:

    ```bash
    terraform init
    ```

1. Kommandot [terraform plan](https://www.terraform.io/docs/commands/plan.html) kan användas för att validera konfigurationssyntaxen. Parametern `-out` dirigerar resultaten till en fil. Utdatafilen kan användas senare för att tillämpa konfigurationen. Kör följande kommando i Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. Kommandot [terraform apply](https://www.terraform.io/docs/commands/apply.html) används för att tillämpa konfigurationen. Utdatafilen från föregående steg har angetts. Det här kommandot gör att Azure-resurserna skapas. Kör följande kommando i Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Om du vill verifiera resultaten i Azure-portalen bläddrar du till den nya resursgruppen. Den nya Azure Cosmos DB-instansen finns i den nya resursgruppen.

## <a name="update-configuration"></a>Uppdatera konfiguration

Det här avsnittet visar hur du uppdaterar konfigurationen så att den innehåller en Azure Container Instance. Containern kör ett program som läser och skriver data till Cosmos DB.

1. Starta Cloud Shell-redigeraren:

    ```bash
    code main.tf
    ```

1. Konfigurationen i det här steget `COSMOS_DB_ENDPOINT` anger `COSMOS_DB_MASTERKEY`två miljövariabler: och . Dessa variabler innehåller platsen och nyckeln för åtkomst till databasen. Värdena för dessa variabler hämtas från databasinstansen som skapades i föregående steg. Den här processen kallas interpolering. Läs mer om Terraform-interpolering i [interpoleringssyntax](https://www.terraform.io/docs/configuration/interpolation.html). Konfigurationen innehåller även ett utdatablock som returnerar det fullständiga domännamnet (FQDN) för containerinstansen. Kopiera och klistra in följande kod i redigeraren:

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      ip_address_type     = "public"
      dns_name_label      = "vote-aci"
      os_type             = "linux"

      container {
        name   = "vote-aci"
        image  = "microsoft/azure-vote-front:cosmosdb"
        cpu    = "0.5"
        memory = "1.5"
        ports {
          port     = 80
          protocol = "TCP"
        }

        secure_environment_variables = {
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

1. Som du gjorde i föregående avsnitt kör du följande kommando för att visuella de ändringar som ska göras:

    ```bash
    terraform plan --out plan.out
    ```

1. Kör `terraform apply` kommandot för att tillämpa konfigurationen.

    ```bash
    terraform apply plan.out
    ```

1. Anteckna behållarinstansen FQDN.

## <a name="test-application"></a>Testa programmet

Testa programmet genom att navigera till FQDN för behållarinstansen. Du bör se resultat som liknar följande utdata:

![Azure Vote-program](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort de resurser som skapas i den här artikeln.

Kör kommandot [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) för att ta bort De Azure-resurser som skapas i den här självstudien:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Installera och konfigurera Terraform för att etablera Azure-resurser](terraform-install-configure.md).