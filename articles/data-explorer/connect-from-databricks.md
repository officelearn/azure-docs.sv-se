---
title: Ansluta till Azure Data Explorer från Azure Databricks med hjälp av Python
description: Det här avsnittet visas hur du använder Python-bibliotek i Azure Databricks att komma åt data från Azure Data Explorer (ADX) med hjälp av en av två autentiseringsmetoder.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 53f51db8d1b495f9a6faec86450d2b4e08a4fb72
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428524"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-using-python"></a>Ansluta till Azure Data Explorer från Azure Databricks med hjälp av Python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) är en Apache Spark-baserad analysplattform som är optimerad för Microsoft Azures plattform för molntjänster. Den här artikeln visar hur du använder Python-bibliotek i Azure Databricks att komma åt data från Azure Data Explorer (ADX). Det finns flera sätt att autentisera med ADX inklusive inloggning på enhet och Azure Active Directory (Azure AD)-App.

## <a name="prerequisites"></a>Förutsättningar

- [Skapa en Azure Data Explorer-kluster och databas](/azure/data-explorer/create-cluster-database-portal)
- [Skapa en Azure Databricks-arbetsyta](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)

    Under **Azure Databricks-tjänst**i den **prisnivå** listrutan **Premium**. På så sätt kan du använda Azure Databricks-hemligheter för att lagra dina autentiseringsuppgifter och referera till dem i anteckningsböcker och jobb.

- [Skapa ett kluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) i Azure Databricks med följande specifikationer (minsta inställningar som behövs för att köra exemplet anteckningsböcker):

![Skapa kluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-python-library-on-your-azure-databricks-cluster"></a>Installera Python-bibliotek på ditt Azure Databricks-kluster

Installera [Python-bibliotek](/azure/kusto/api/python/kusto-python-client-library) i ditt Azure Databricks-kluster:

1. Gå till Azure Databricks-arbetsytan och [skapa ett bibliotek](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)
2. [Ladda upp ett Python PyPI paketet eller Python ägg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)
    - Ladda upp, installera och bifoga biblioteket i Databricks-klustret.
    - Ange namnet på PyPi: *azure-kusto-data*

## <a name="connect-to-adx-using-device-login"></a>Ansluta till ADX med inloggning på enhet

[Importera en anteckningsbok](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) med hjälp av den [fråga-ADX--inloggning på enhet](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) anteckningsboken för att ansluta till ADX med dina autentiseringsuppgifter.

## <a name="connect-to-adx-using-azure-ad-app"></a>Ansluta till ADX med hjälp av Azure AD-App

1. Skapa Azure AD-App genom att [etablerar en AAD-programmet](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Bevilja åtkomst till din Azure AD-App i Azure Data Explorer databasen på följande sätt:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | Databasnamnet |
    | ```AAD App ID``` | Azure AD App-ID |
    | ```AAD Tenant ID``` | Azure AD-klient-ID |

### <a name="find-your-azure-ad-tenant-id"></a>Hitta din Azure AD-klient-ID

För att autentisera ett program, använder Azure Data Explorer din Azure AD-klient-ID. Du hittar ditt klient-ID genom att använda följande URL. Byt ut *YourDomain* mot din domän.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Om din domän till exempel är *contoso.com* blir URL:en: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Klicka på den här URL:en för att se resultatet. Den första raden ser ut så här. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Din klient-ID är `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Store och skydda din Azure AD-App-ID och nyckel 

Store och skydda din Azure AD-App-ID och nyckel med hjälp av Azure Databricks [hemligheter](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) på följande sätt:
1. [Konfigurera CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)
1. [Installera CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) 
1. [Konfigurera autentisering](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurera den [hemligheter](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) med hjälp av följande exempelkommandon:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importera en anteckningsbok
[Importera en anteckningsbok](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) med hjälp av den [fråga-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) anteckningsboken för att ansluta till ADX. Uppdatera platshållarvärdena med klustrets namn, databasnamnet och Azure AD-klient-ID.