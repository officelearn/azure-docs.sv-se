---
title: Visualisera data från Azure Data Explorer i Grafana
description: I den här anvisningen du lära dig hur du konfigurerar Datautforskaren i Azure som en datakälla för Grafana och visualisera data från en exempel-klustret.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 141859d155383b01cfea998c6b7158848517eac2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531960"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualisera data från Azure Data Explorer i Grafana

Grafana är en analysplattform som gör det möjligt att fråga och visualisera data, sedan skapa och dela instrumentpaneler som baseras på dina visualiseringar. Grafana ger en Azure Data Explorer *plugin-programmet*, vilket gör att du kan ansluta till och visualisera data från Azure Data Explorer. Du lär dig hur du konfigurerar Datautforskaren i Azure som en datakälla för Grafana och visualisera data från ett kluster i exemplet i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra den här så här:

* [Grafana version 5.3.0 eller senare](https://docs.grafana.org/installation/) för ditt operativsystem

* Den [Datautforskaren i Azure-plugin-programmet](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) för Grafana

* Ett kluster som innehåller exempeldata StormEvents. Mer information finns i [snabbstarten: Skapa ett Azure Data Explorer-kluster och databasen](create-cluster-database-portal.md) och [mata in exempeldata i Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>Konfigurera datakälla

Du utför följande steg för att konfigurera Datautforskaren i Azure som en datakälla för Grafana. Vi går igenom de här stegen i detalj i det här avsnittet:

1. Skapa en Azure Active Directory (Azure AD) tjänstens huvudnamn. Tjänstens huvudnamn används av Grafana åtkomst till Datautforskaren i Azure-tjänsten.

1. Lägg till Azure AD-tjänstobjekt till den *visningsprogram* roll i Datautforskaren i Azure-databasen.

1. Ange anslutningsegenskaper för Grafana baserat på information från Azure AD-tjänstens huvudnamn och sedan testa anslutningen.

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Du kan skapa tjänsten huvudnamn i den [Azure-portalen](#azure-portal) eller med hjälp av den [Azure CLI](#azure-cli) kommandoradsmiljö. Oavsett vilken metod använder du, när du har skapat du hämta de värden för fyra anslutningsegenskaper som du ska använda i senare steg.

#### <a name="azure-portal"></a>Azure Portal

1. Om du vill skapa tjänstens huvudnamn, följer du anvisningarna i den [dokumentation om Azure portal](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. I den [tilldela programmet till en roll](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) avsnittet, tilldela en roll för **läsare** i Datautforskaren i Azure-klustret.

    1. I den [få värden för att logga in](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) avsnittet, kopiera tre egenskapsvärdena som beskrivs i stegen: **Katalog-ID** (klient-ID), **program-ID**, och **lösenord**.

1. I Azure-portalen väljer du **prenumerationer** sedan kopiera ID: T för den prenumeration där du skapade tjänstens huvudnamn.

    ![Prenumerations-ID – portal](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Skapa ett huvudnamn för tjänsten. Ange en lämplig omfattning och en rolltyp `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Mer information finns i [skapa Azure-tjänstens huvudnamn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Kommandot returnerar en resultatuppsättning som liknar följande. Kopiera tre värden: **appID**, **lösenord**, och **klient**.

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Hämta en lista över dina prenumerationer.

    ```azurecli
    az account list --output table
    ```

    Kopiera rätt prenumerations-ID.

    ![Prenumerations-ID – CLI](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Lägg till tjänstobjektet till rollen visningsprogram

Nu när du har ett huvudnamn för tjänsten kan du lägga till den till den *visningsprogram* roll i Datautforskaren i Azure-databasen. Du kan utföra den här uppgiften under **behörigheter** i Azure-portalen eller under **fråga** genom att använda en management-kommando.

#### <a name="azure-portal---permissions"></a>Azure portal - behörigheter

1. Gå till Datautforskaren i Azure-kluster i Azure-portalen.

1. I den **översikt** väljer du databasen med exempeldata StormEvents.

    ![Välj databas](media/grafana/select-database.png)

1. Välj **behörigheter** sedan **lägga till**.

    ![Databasbehörighet](media/grafana/database-permissions.png)

1. Under **lägga till databasbehörigheter**väljer den **Viewer** sedan rollen **Välj huvudkonton**.

    ![Lägg till databasbehörigheter](media/grafana/add-permission.png)

1. Sök efter tjänstens huvudnamn som du skapade (exemplet visar huvudkontot **mb grafana**). Välj huvudkonto, sedan **Välj**.

    ![Hantera behörigheter i Azure portal](media/grafana/new-principals.png)

1. Välj **Spara**.

    ![Hantera behörigheter i Azure portal](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>Kommandot - fråga

1. Gå till Datautforskaren i Azure-kluster i Azure-portalen och välj **fråga**.

    ![Söka i data](media/grafana/query.png)

1. Kör följande kommando i frågefönstret. Använd det program-ID och klient-ID från Azure-portalen eller CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Kommandot returnerar en resultatuppsättning som liknar följande. I det här exemplet är den första raden är för en befintlig användare i databasen och den andra raden är för tjänstens huvudnamn just har lagt till.

    ![Resultatuppsättningen](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>Ange egenskaper och testa anslutningen

Med tjänstens huvudnamn som tilldelats den *visningsprogram* roll du nu ange egenskaper i din instans av Grafana och testa anslutningen till Datautforskaren i Azure.

1. I Grafana, på den vänstra menyn väljer du kugghjulsikonen sedan **datakällor**.

    ![Datakällor](media/grafana/data-sources.png)

1. Välj **Lägg till datakälla**.

1. På den **datakällor / nya** sidan, anger du ett namn för datakällan och välj sedan typ **Azure Data Explorer Datasource**.

    ![Anslutningens namn och typ](media/grafana/connection-name-type.png)

1. Ange namnet på klustret i formuläret https://{ClusterName}. {Region}. kusto.windows.net. Ange de andra värdena från Azure-portalen eller CLI. Se tabellen nedan på följande bild för en mappning.

    ![Anslutningsegenskaper](media/grafana/connection-properties.png)

    | Grafana UI | Azure Portal | Azure CLI |
    | --- | --- | --- |
    | Prenumeration-ID | PRENUMERATIONS-ID | SubscriptionId |
    | Klient-ID | Katalog-ID | klient |
    | Klient-ID | Program-ID:t | appId |
    | Klienthemlighet | Lösenord | lösenord |
    | | | |

1. Välj **spara och testa**.

    Om testet lyckas kan du gå till nästa avsnitt. Om du får problem, kontrollerar du de värden som du angav i Grafana och granska föregående steg.

## <a name="visualize-data"></a>Visualisera data

Nu du har konfigurerat Datautforskaren i Azure som en datakälla för Grafana, är det dags att visualisera data. Vi visar ett grundläggande exempel, men det är mycket mer du kan göra. Vi rekommenderar att du tittar på [skriva frågor för Azure Data Explorer](write-queries.md) exempel på andra frågor som ska köras mot exempeldata.

1. I Grafana, på den vänstra menyn, Välj plusikonen sedan **instrumentpanelen**.

    ![Skapa instrumentpanel](media/grafana/create-dashboard.png)

1. Under den **Lägg till** fliken **Graph**.

    ![Lägg till diagram](media/grafana/add-graph.png)

1. På panelen graph väljer **panelens rubrik** sedan **redigera**.

    ![Redigera panel](media/grafana/edit-panel.png)

1. Markera längst ned på panelen **datakälla** väljer du datakällan som du har konfigurerat.

    ![Välja datakälla](media/grafana/select-data-source.png)

1. Kopiera i följande fråga i frågefönstret och välj sedan **kör**. Frågan buckets antal händelser per dag för exempeldata.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Kör frågan](media/grafana/run-query.png)

1. Diagrammet visar inte några resultat eftersom den är begränsad som standard till data från de senaste sex timmarna. På menyn högst upp väljer **senaste 6 timmarna**.

    ![Senaste sex timmar](media/grafana/last-six-hours.png)

1. Ange ett anpassat intervall som omfattar 2007 år som ingår i vår StormEvents provdatauppsättning. Välj **Använd**.

    ![Eget datumintervall](media/grafana/custom-date-range.png)

    Diagrammet visar nu data från 2007, bucketas per dag.

    ![Klar graph](media/grafana/finished-graph.png)

1. På den översta menyn väljer du spara ikon: ![Ikonen Spara](media/grafana/save-icon.png).

## <a name="next-steps"></a>Nästa steg

[Skriva frågor för Azure Data Explorer](write-queries.md)

[Självstudier: Visualisera data från Azure Data Explorer i Power BI](visualize-power-bi.md)