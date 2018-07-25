---
title: Använda Databricks CLI från Azure Cloudshell | Microsoft Docs
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
ms.openlocfilehash: 3ea4ebbd95237b50054fb0e344f260120d597ab5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225242"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Använda Databricks CLI från Azure Cloud Shell

Lär dig mer om att använda Databricks CLI från Azure Cloud Shell för att utföra åtgärder på Databricks.

## <a name="prerequisites"></a>Förutsättningar

* En Azure Databricks-arbetsytan och kluster. Anvisningar finns i [Kom igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Konfigurera en personlig åtkomsttoken i Databricks. Anvisningar finns i [Token management](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Använda Azure Cloudshell

1. Logga in på [Azure Portal](https://portal.azure.com).
 
2. I det övre högra hörnet, klickar du på den **Cloud Shell** ikon.

   ![Starta Cloudshell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "starta Azure Cloudshell")

3. Kontrollera att du väljer **Bash** för Cloud Shell-miljön. Du kan välja från alternativet listrutan enligt följande skärmbild.

   ![Välj Bash Cloud Shell-miljön](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Välj Bash") 

4. Skapa en virtuell miljö där du kan installera Databricks CLI. I kodavsnittet nedan skapar du en virtuell miljö som kallas `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Växla till den virtuella miljön som du skapade.

       source databrickscli/bin/activate

6. Installera Databricks CLI.

       pip install databricks-cli

7. Konfigurera autentisering med Databricks med hjälp av den åtkomsttoken som du måste ha skapat, visas som en del av förutsättningarna. Ange följande kommando:

       databricks configure --token

    Du får följande frågor:

    * Först uppmanas du att ange värden för Databricks. Ange värdet i formatet `https://eastus2.azuredatabricks.net`. Här kan **östra USA 2** är Azure-regionen där du skapade Azure Databricks-arbetsytan.

    * Du uppmanas sedan att ange en token. Ange den token som du skapade tidigare.

När du har slutfört de här stegen kan du börja använda Databricks CLI från Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Använda Databricks CLI

Du kan nu börja använda Databricks CLI. Till exempel köra följande kommando för att lista alla Databricks-kluster som du har i din arbetsyta.

    databricks clusters list

Du kan också använda följande kommando för att få åtkomst till Databricks filesystem (antingen).

    databricks fs ls


En fullständig referens om kommandon finns i [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure CLI i [översikt över Azure CLI](../cloud-shell/overview.md)
* Om du vill se en lista över kommandon för Azure CLI, se [Azure CLI-referens](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Om du vill se en lista över kommandon för Databricks CLI, se [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


