---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78305005"
---
## <a name="configure-the-data-source"></a>Konfigurera datakällan

Du utför följande steg för att konfigurera Azure Data Explorer som en datakälla för instrumentpanelsverktyget. Vi kommer att ta upp dessa steg mer i detalj i det här avsnittet:

1. Skapa ett huvudnamn för Azure Active Directory (Azure AD). Tjänstens huvudnamn används av instrumentpanelsverktyget för att komma åt Azure Data Explorer-tjänsten.

1. Lägg till Azure AD-tjänstens huvudnamn i *visningsrollen* i Azure Data Explorer-databasen.

1. Ange anslutningsegenskaper för instrumentpanelsverktyg baserat på information från Azure AD-tjänstens huvudnamn och testa sedan anslutningen.

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa tjänstens huvudnamn i [Azure-portalen](#azure-portal) eller använda azure CLI-kommandoradsupplevelsen. [Azure CLI](#azure-cli) Oavsett vilken metod du använder får du värden för fyra anslutningsegenskaper som du ska använda i senare steg när du har skapat dem.

#### <a name="azure-portal"></a>Azure Portal

1. Om du vill skapa tjänstens huvudnamn följer du instruktionerna i [Azure-portaldokumentationen](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. I avsnittet [Tilldela programmet till en roll](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) tilldelar du en rolltyp av **Läsare** till ditt Azure Data Explorer-kluster.

    1. I avsnittet [Hämta värden för inloggning](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) kopierar du de tre egenskapsvärden som omfattas av stegen: **Katalog-ID** (klient-ID), **Program-ID**och **Lösenord**.

1. I Azure-portalen väljer du Prenumerationer och **kopierar** sedan ID:et för prenumerationen där du skapade tjänstens huvudnamn.

    ![Prenumerations-ID - portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Skapa ett huvudnamn för tjänsten. Ange ett lämpligt scope och `reader`en rolltyp av .

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Mer information finns i [Skapa ett Azure-tjänsthuvudnamn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Kommandot returnerar en resultatuppsättning som följande. Kopiera de tre egenskapsvärdena: **appID**, **lösenord**och **klient .**


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Få en lista över dina prenumerationer.

    ```azurecli
    az account list --output table
    ```

    Kopiera lämpligt prenumerations-ID.

    ![Prenumerations-ID - CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Lägg till tjänstens huvudnamn i visningsrollen

Nu när du har ett tjänsthuvudnamn lägger du till det i *visningsrollen* i Azure Data Explorer-databasen. Du kan utföra den här uppgiften under **Behörigheter** i Azure-portalen eller under **Fråga** med hjälp av ett hanteringskommando.

#### <a name="azure-portal---permissions"></a>Azure-portal - Behörigheter

1. Gå till Azure Data Explorer-klustret i Azure-portalen.

1. I avsnittet **Översikt** väljer du databasen med exempeldata för StormEvents.

    ![Välj databas](media/data-explorer-configure-data-source/select-database.png)

1. Välj **Behörigheter** och lägg sedan **till**.

    ![Behörigheter för databaser](media/data-explorer-configure-data-source/database-permissions.png)

1. Under **Lägg till databasbehörigheter**väljer du rollen **Visningsprogram** och välj sedan **huvudnamn**.

    ![Lägga till databasbehörigheter](media/data-explorer-configure-data-source/add-permission.png)

1. Sök efter tjänstens huvudnamn som du skapade. Markera huvudbeloppet och **välj**sedan .

    ![Hantera behörigheter i Azure-portalen](media/data-explorer-configure-data-source/new-principals.png)

1. Välj **Spara**.

    ![Hantera behörigheter i Azure-portalen](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Kommandot Hantering - Fråga

1. Gå till Azure Data Explorer-klustret i Azure Data Explorer och välj **Fråga**.

    ![Söka i data](media/data-explorer-configure-data-source/query.png)

1. Kör följande kommando i frågefönstret. Använd program-ID och klient-ID från Azure-portalen eller CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Kommandot returnerar en resultatuppsättning som följande. I det här exemplet är den första raden för en befintlig användare i databasen och den andra raden är för tjänstens huvudnamn som just har lagts till.

    ![Resultatuppsättning](media/data-explorer-configure-data-source/result-set.png)
