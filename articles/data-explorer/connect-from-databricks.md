---
title: Ansluta till Datautforskaren i Azure från Azure Databricks med hjälp av Python
description: Det här avsnittet visar hur du använder en Python-bibliotek i Azure Databricks för att komma åt data från Azure Data Explorer med hjälp av en av två autentiseringsmetoder.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 7e91aa0707b1ae8ac4b9b6ddd9ee7142a04a0f37
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756933"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Ansluta till Datautforskaren i Azure från Azure Databricks med hjälp av Python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) är en Apache Spark-baserad analysplattform som är optimerad för Microsoft Azure-plattformen. Den här artikeln visar hur du använder ett Python-bibliotek i Azure Databricks för att komma åt data från Azure Data Explorer. Det finns flera sätt att autentisera med Azure Data Explorer, inklusive en inloggning på enhet och en Azure Active Directory (Azure AD)-app.

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett Azure Data Explorer-kluster och databasen](/azure/data-explorer/create-cluster-database-portal).
- [Skapa en Azure Databricks-arbetsyta](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). Under **Azure Databricks-tjänst**i den **prisnivå** listrutan, väljer **Premium**. Det här alternativet kan du använda Azure Databricks-hemligheter för att lagra dina autentiseringsuppgifter och referera till dem i anteckningsböcker och jobb.

- [Skapa ett kluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) i Azure Databricks med följande specifikationer (minsta inställningar som behövs för att köra exemplet anteckningsböcker):

   ![Specifikationer för att skapa ett kluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Installera Python-bibliotek på ditt Azure Databricks-kluster

Installera den [Python-bibliotek](/azure/kusto/api/python/kusto-python-client-library) i ditt Azure Databricks-kluster:

1. Gå till Azure Databricks-arbetsytan och [skapa ett bibliotek](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Ladda upp ett Python PyPI paketet eller Python ägg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Ladda upp, installera och bifoga biblioteket i Databricks-klustret.
   - Ange namnet på PyPi: **azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Ansluta till Datautforskaren i Azure med hjälp av en inloggning på enhet

[Importera en anteckningsbok](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) med hjälp av den [fråga-ADX--inloggning på enhet](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) anteckningsboken. Du kan sedan ansluta till Datautforskaren i Azure med dina autentiseringsuppgifter.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Ansluta till ADX med hjälp av en Azure AD-app

1. Skapa Azure AD-app genom att [etablera en Azure AD-program](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Bevilja åtkomst till din Azure AD-app i Azure Data Explorer databasen på följande sätt:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | Databasnamnet |
    | ```AAD App ID``` | Azure AD app-ID |
    | ```AAD Tenant ID``` | en Azure AD-klient-ID |

### <a name="find-your-azure-ad-tenant-id"></a>Hitta en Azure AD-klient-ID

För att autentisera ett program, använder Azure Data Explorer din Azure AD-klient-ID. Du hittar ditt klient-ID, kan du använda följande URL. Ersätta din domän för *Dindomän*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Om din domän till exempel är *contoso.com* blir URL:en: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Välj den här URL: en för att se resultaten. Den första raden är följande: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Din klient-ID är `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Store och skydda Azure AD app-ID och nyckel 

Store och skydda din Azure AD app-ID och nyckel med hjälp av Azure Databricks [hemligheter](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) på följande sätt:
1. [Konfigurera CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Installera CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Konfigurera autentisering](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurera den [hemligheter](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) med hjälp av följande exempelkommandon:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importera en anteckningsbok
[Importera en anteckningsbok](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) med hjälp av den [fråga-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) anteckningsboken för att ansluta till Datautforskaren i Azure. Uppdatera platshållarvärdena med klustrets namn, databasnamnet och Azure AD-klient-ID.
