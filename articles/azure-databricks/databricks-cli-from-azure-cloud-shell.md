---
title: Använda Databricks CLI från Azure-molnet Shell | Microsoft Docs
description: Lär dig hur du använder Databricks CLI från Azure Cloud Shell.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: c20ad02f962fbee22bb16653c5eab351d9f3de17
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598733"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Använda Databricks CLI från Azure Cloud Shell

Lär dig hur du använder Databricks CLI från Azure Cloud Shell för att utföra åtgärder på Databricks.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure Databricks arbetsytan och kluster. Instruktioner finns i [Kom igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Ställ in en personlig åtkomsttoken i Databricks. Instruktioner finns i [Token management](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Använd Azure-molnet Shell

1. Logga in på [Azure Portal](https://portal.azure.com).
 
2. I det övre högra hörnet, klickar du på den **moln Shell** ikon.

   ![Starta molnet Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "starta ODBC från Excel")

3. Kontrollera att du väljer **Bash** för molnet Shell-miljön. Du kan välja från listrutan-alternativ som visas i följande skärmbild.

   ![Starta molnet Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "starta ODBC från Excel") 

4. Skapa en virtuell miljö där du kan installera Databtricks CLI. I kodfragmentet nedan skapar du en virtuell miljö som kallas `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Växla till den virtuella miljön som du skapade.

       source databrickscli/bin/activate

6. Installera Databricks CLI.

       pip install databricks-cli

7. Konfigurera autentisering med Databricks med hjälp av den åtkomst-token som du måste ha skapat som en del av nödvändiga komponenter. Ange följande kommando:

       databricks configure --token

    Du får följande frågor:

    * Du uppmanas att ange Databricks värden. Ente värdet i formatet `https://eastus2.azuredatabricks.net`. Här, **östra USA 2** är Azure-regionen där du skapade din Azure Databricks arbetsyta.

    * Du uppmanas att ange ett användarnamn. Ange **token**.

    * Slutligen kan uppmanas du att ange lösenordet. Ange den token som du skapade tidigare.

När du har slutfört de här stegen kan du börja använda Databricks CLI från Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Använda Databricks CLI

Nu kan du starta med hjälp av Databricks CLI. Till exempel köra följande kommando för att visa en lista med alla Databricks-kluster som du har i din arbetsyta.

    databricks clusters list

Du kan också använda följande kommando för att komma åt Databricks filsystem (DBFS).

    databricks fs ls


En fullständig referens om kommandon finns [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Nästa steg

* Mer information om Azure CLI finns [Azure CLI-översikt](../cloud-shell/overview.md)
* Om du vill se en lista med kommandon för Azure CLI finns [Azure CLI-referens](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Om du vill se en lista med kommandon för Databricks CLI, se [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


