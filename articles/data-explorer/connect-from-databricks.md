---
title: Ansluta till Azure Data Explorer från Azure Databricks med Python
description: Det här avsnittet visar hur du använder ett Python-bibliotek i Azure Databricks för att komma åt data från Azure Data Explorer med hjälp av en av två autentiseringsmetoder.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 03dee0570faa863ca411ed91f2a6ec85a1e38380
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76985687"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Ansluta till Azure Data Explorer från Azure Databricks med Python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) är en Apache Spark-baserad analysplattform som är optimerad för Microsoft Azure-plattformen. Den här artikeln visar hur du använder ett Python-bibliotek i Azure Databricks för att komma åt data från Azure Data Explorer. Det finns flera sätt att autentisera med Azure Data Explorer, inklusive en enhetsinloggning och en Azure Active Directory-app (Azure AD).

## <a name="prerequisites"></a>Krav

- [Skapa ett Azure Data Explorer-kluster och -databas](/azure/data-explorer/create-cluster-database-portal).
- [Skapa en Azure Databricks-arbetsyta](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). Välj **Premium**i listrutan **Prisnivå** under **Azure Databricks**Service . Med det här valet kan du använda Azure Databricks hemligheter för att lagra dina autentiseringsuppgifter och referera till dem i anteckningsböcker och jobb.

- [Skapa ett kluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) i Azure Databricks med följande specifikationer (minimiinställningar som behövs för att köra exempeldatorerna):

   ![Specifikationer för att skapa ett kluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Installera Python-biblioteket i Azure Databricks-klustret

Så här installerar du [Python-biblioteket](/azure/kusto/api/python/kusto-python-client-library) i Azure Databricks-klustret:

1. Gå till din Azure Databricks-arbetsyta och [skapa ett bibliotek](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Ladda upp ett Python PyPI-paket eller Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Ladda upp, installera och bifoga biblioteket till ditt Databricks-kluster.
   - Ange **PyPi-namnet: azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Ansluta till Azure Data Explorer med hjälp av en enhetsinloggning

[Importera en anteckningsbok](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) med hjälp av [query-ADX-device-login notebook.](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) Du kan sedan ansluta till Azure Data Explorer med hjälp av dina autentiseringsuppgifter.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Ansluta till ADX med hjälp av en Azure AD-app

1. Skapa Azure AD-app genom [att etablera ett Azure AD-program](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Bevilja åtkomst till din Azure AD-app i din Azure Data Explorer-databas enligt följande:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | ditt databasnamn |
    | ```AAD App ID``` | ditt Azure AD-app-ID |
    | ```AAD Tenant ID``` | ditt Azure AD-klient-ID |

### <a name="find-your-azure-ad-tenant-id"></a>Hitta ditt Azure AD-klient-ID

För att autentisera ett program använder Azure Data Explorer ditt Azure AD-klient-ID. Om du vill hitta ditt klient-ID använder du följande WEBBADRESS. Ersätt din domän med *Din Domän.*

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Om din domän till exempel är *contoso.com* blir URL:en: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Välj den här URL:en för att se resultatet. Den första raden är följande: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Ditt klient-ID är `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Lagra och skydda ditt Azure AD-app-ID och -nyckel 

Lagra och skydda ditt Azure AD-app-ID och -nyckel med hjälp av Azure Databricks [hemligheter](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) enligt följande:
1. [Ställ in CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Installera CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Konfigurera autentisering](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurera [hemligheterna](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) med hjälp av följande exempelkommandon:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importera en anteckningsbok
[Importera en anteckningsbok](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) med hjälp av [query-ADX-AAD-App-anteckningsboken](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) för att ansluta till Azure Data Explorer. Uppdatera platshållarvärdena med ditt klusternamn, databasnamn och Azure AD-klient-ID.
