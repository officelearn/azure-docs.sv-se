---
title: 'Använda Databricks CLI från Azure Cloud Shell '
description: Lär dig hur du använder Databricks CLI från Azure Cloud Shell för att utföra åtgärder på Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: b327403b29f26f6c1f43f9dfe7aabd5c3c7e0a68
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299903"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Använda Databricks CLI från Azure Cloud Shell

Lär dig hur du använder Databricks CLI från Azure Cloud Shell för att utföra åtgärder på Databricks.

## <a name="prerequisites"></a>Krav

* En Azure Databricks arbets yta och ett kluster. Instruktioner finns i [Kom igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Konfigurera en personlig åtkomsttoken i Databricks. Instruktioner finns i [token Management](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Använd Azure Cloud Shell

1. Logga in på [Azure Portal](https://portal.azure.com).
 
2. Klicka på ikonen **Cloud Shell** i det övre högra hörnet.

   ![Starta Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Starta Azure Cloud Shell")

3. Se till att du väljer **bash** för Cloud Shells miljön. Du kan välja i list rutan som visas på följande skärm bild.

   ![Välj bash för Cloud Shells miljön](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Välj bash") 

4. Skapa en virtuell miljö där du kan installera Databricks CLI. I kodfragmentet nedan skapar du en virtuell miljö som heter `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Växla till den virtuella miljö som du har skapat.

       source databrickscli/bin/activate

6. Installera Databricks CLI.

       pip install databricks-cli

7. Konfigurera autentisering med Databricks med hjälp av den åtkomsttoken som du måste skapa, som visas som en del av förutsättningarna. Ange följande kommando:

       databricks configure --token

    Följande meddelanden visas:

    * Först uppmanas du att ange Databricks-värden. Ange värdet i formatet `https://eastus2.azuredatabricks.net`. Här är **östra USA 2** den Azure-region där du skapade din Azure Databricks-arbetsyta.

    * Sedan uppmanas du att ange en token. Ange den token som du skapade tidigare.

När du har slutfört de här stegen kan du börja använda Databricks CLI från Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Använda Databricks CLI

Nu kan du börja använda Databricks CLI. Kör till exempel följande kommando för att visa en lista över alla Databricks-kluster som du har i din arbets yta.

    databricks clusters list

Du kan också använda följande kommando för att komma åt Databricks-fil systemet (DBFS).

    databricks fs ls


En fullständig referens för kommandon finns i [DATABRICKS CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Nästa steg

* Mer information om Azure CLI finns i [Översikt över Azure CLI](../cloud-shell/overview.md)
* En lista över kommandon för Azure CLI finns i [Azure CLI-referens](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Om du vill se en lista över kommandon för Databricks CLI, se [DATABRICKS CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


