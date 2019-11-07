---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: f8b3e541dfd55bbd613af2791329a08402cf8670
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582008"
---
## <a name="configure-the-data-source"></a>Konfigurera data källan

Du utför följande steg för att konfigurera Azure Datautforskaren som en data källa för instrument panels verktyget. Vi kommer att gå igenom de här stegen i detalj i det här avsnittet:

1. Skapa ett tjänst huvud namn för en Azure Active Directory (Azure AD). Tjänstens huvud namn används av instrument panels verktyget för att få åtkomst till Azure Datautforskaren-tjänsten.

1. Lägg till Azure AD-tjänstens huvud namn till *visnings* rollen i Azure datautforskaren-databasen.

1. Ange dina anslutnings egenskaper för instrument panels verktyget baserat på information från Azure AD-tjänstens huvud namn och testa sedan anslutningen.

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa tjänstens huvud namn i [Azure Portal](#azure-portal) eller genom att använda kommando tolken i [Azure CLI](#azure-cli) . Oavsett vilken metod du använder, efter att du har skapat, får du värden för fyra anslutnings egenskaper som du kommer att använda i senare steg.

#### <a name="azure-portal"></a>Azure Portal

1. Följ anvisningarna i [Azure Portal dokumentationen](/azure/active-directory/develop/howto-create-service-principal-portal)för att skapa tjänstens huvud namn.

    1. I avsnittet [tilldela programmet till en roll](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) tilldelar du en roll typ **läsare** till ditt Azure datautforskaren-kluster.

    1. I avsnittet [Hämta värden för signering i](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) kopierar du de tre egenskaps värden som beskrivs i stegen: **katalog-ID** (klient-ID), **program-ID**och **lösen ord**.

1. I Azure Portal väljer du **prenumerationer** och kopierar ID: t för den prenumeration där du skapade tjänstens huvud namn.

    ![Prenumerations-ID – Portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Skapa ett huvudnamn för tjänsten. Ange ett lämpligt omfång och en roll typ för `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Mer information finns i [skapa ett Azure-tjänstens huvud namn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Kommandot returnerar en resultat uppsättning som följande. Kopiera de tre egenskaps värdena: **AppID**, **Password**och **Tenant**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Hämta en lista över dina prenumerationer.

    ```azurecli
    az account list --output table
    ```

    Kopiera rätt prenumerations-ID.

    ![Prenumerations-ID – CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Lägg till tjänstens huvud namn till visnings rollen

Nu när du har ett huvud namn för tjänsten lägger du till det i rollen *visnings program* i Azure datautforskaren-databasen. Du kan utföra den här uppgiften under **behörigheter** i Azure Portal eller under **fråga** med hjälp av ett hanterings kommando.

#### <a name="azure-portal---permissions"></a>Azure Portal-behörigheter

1. I Azure Portal går du till ditt Azure Datautforskaren-kluster.

1. I avsnittet **Översikt** väljer du databasen med StormEvents exempel data.

    ![Välj databas](media/data-explorer-configure-data-source/select-database.png)

1. Välj **behörigheter** och sedan **Lägg till**.

    ![Databas behörigheter](media/data-explorer-configure-data-source/database-permissions.png)

1. Under **Lägg till databas behörigheter**väljer du rollen **visnings program** och **väljer sedan objekt**.

    ![Lägg till databas behörigheter](media/data-explorer-configure-data-source/add-permission.png)

1. Sök efter tjänstens huvud namn som du skapade. Välj huvud kontot och **Välj**sedan.

    ![Hantera behörigheter i Azure Portal](media/data-explorer-configure-data-source/new-principals.png)

1. Välj **Spara**.

    ![Hantera behörigheter i Azure Portal](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Hanterings kommando-fråga

1. I Azure Portal går du till ditt Azure Datautforskaren-kluster och väljer **fråga**.

    ![Fråga](media/data-explorer-configure-data-source/query.png)

1. Kör följande kommando i frågefönstret. Använd program-ID och klient-ID från Azure Portal eller CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Kommandot returnerar en resultat uppsättning som följande. I det här exemplet är den första raden för en befintlig användare i databasen, och den andra raden är för tjänstens huvud namn som nyss lades till.

    ![Resultat uppsättning](media/data-explorer-configure-data-source/result-set.png)
