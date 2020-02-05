---
title: Ansluta till Azure Datautforskaren från Azure Databricks med python
description: Det här avsnittet visar hur du använder ett Python-bibliotek i Azure Databricks för att få åtkomst till data från Azure Datautforskaren genom att använda en av två autentiseringsmetoder.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 03dee0570faa863ca411ed91f2a6ec85a1e38380
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985687"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Ansluta till Azure Datautforskaren från Azure Databricks med python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) är en Apache Spark-baserad analys plattform som är optimerad för Microsoft Azures plattformen. Den här artikeln visar hur du använder ett Python-bibliotek i Azure Databricks för att komma åt data från Azure Datautforskaren. Det finns flera sätt att autentisera med Azure Datautforskaren, inklusive en enhets inloggning och en Azure Active Directory Azure AD-App.

## <a name="prerequisites"></a>Krav

- [Skapa ett Azure datautforskaren-kluster och-databas](/azure/data-explorer/create-cluster-database-portal).
- [Skapa en Azure Databricks-arbetsyta](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). Välj **Premium**i list rutan **pris nivå** under **Azure Databricks tjänst**. Med det här alternativet kan du använda Azure Databricks hemligheter för att lagra dina autentiseringsuppgifter och referera dem i antecknings böcker och jobb.

- [Skapa ett kluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) i Azure Databricks med följande specifikationer (minst de inställningar som krävs för att köra exempel antecknings böckerna):

   ![Specifikationer för att skapa ett kluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Installera python-biblioteket på ditt Azure Databricks-kluster

Så här installerar du [python-biblioteket](/azure/kusto/api/python/kusto-python-client-library) på Azure Databricks-klustret:

1. Gå till arbets ytan Azure Databricks och [skapa ett bibliotek](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Ladda upp ett python pypi-paket eller python-ägg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Ladda upp, installera och koppla biblioteket till ditt Databricks-kluster.
   - Ange namnet på PyPi: **Azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Ansluta till Azure Datautforskaren med en enhets inloggning

[Importera en bärbar dator](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) med hjälp av antecknings boken [query-ADX-Device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) . Sedan kan du ansluta till Azure Datautforskaren med hjälp av dina autentiseringsuppgifter.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Ansluta till ADX med hjälp av en Azure AD-App

1. Skapa en Azure AD-app genom att [tillhandahålla ett Azure AD-program](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Ge åtkomst till din Azure AD-App i Azure Datautforskaren-databasen på följande sätt:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | ditt databas namn |
    | ```AAD App ID``` | ditt Azure AD App-ID |
    | ```AAD Tenant ID``` | ditt Azure AD-klient-ID |

### <a name="find-your-azure-ad-tenant-id"></a>Hitta ditt Azure AD-klient-ID

För att autentisera ett program använder Azure-Datautforskaren ditt Azure AD-klient-ID. Använd följande URL för att hitta klient-ID: t. Ersätt din domän för *dindomän*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Om din domän till exempel är *contoso.com* blir URL:en: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Välj den här URL: en för att se resultatet. Den första raden är följande: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Klient-ID: t är `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Lagra och skydda ditt Azure AD App-ID och nyckel 

Lagra och skydda ditt Azure AD-App-ID och nyckel genom att använda Azure Databricks [hemligheter](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) på följande sätt:
1. [Konfigurera CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Installera CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Konfigurera autentisering](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurera [hemligheterna](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) med hjälp av följande exempel kommandon:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importera en bärbar dator
[Importera en bärbar dator](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) med hjälp av [ADX-AAD-app](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) -anteckningsboken för att ansluta till Azure datautforskaren. Uppdatera plats hållarnas värden med kluster namnet, databas namnet och klient-ID: t för Azure AD.
