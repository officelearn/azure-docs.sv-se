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
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605724"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Använda Databricks CLI från Azure Cloud Shell

Lär dig hur du använder Databricks CLI från Azure Cloud Shell för att utföra åtgärder på Databricks.

## <a name="prerequisites"></a>Krav

* En Azure Databricks-arbetsyta och ett kluster. Instruktioner finns i [Komma igång med Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Skapa en personlig åtkomsttoken i Databricks. Instruktioner finns i [Tokenhantering](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="use-the-azure-cloud-shell"></a>Använda Azure Cloud Shell

1. Logga in på [Azure-portalen](https://portal.azure.com).
 
2. Klicka på ikonen **Cloud Shell** längst upp till höger.

   ![Starta Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Starta Azure Cloud Shell")

3. Se till att du väljer **Bash** för Cloud Shell-miljön. Du kan välja från listrutan alternativ, som visas i följande skärmdump.

   ![Välj Bash för Cloud Shell-miljön](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Välj Bash") 

4. Skapa en virtuell miljö där du kan installera Databricks CLI. I kodavsnittet nedan skapar du en `databrickscli`virtuell miljö som heter .

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Växla till den virtuella miljö som du skapade.

       source databrickscli/bin/activate

6. Installera Databricks CLI.

       pip install databricks-cli

7. Konfigurera autentisering med Databricks med hjälp av åtkomsttoken som du måste ha skapat, som anges som en del av förutsättningarna. Ange följande kommando:

       databricks configure --token

    Följande anvisningar får du:

    * Först uppmanas du att ange Databricks-värden. Ange värdet i `https://eastus2.azuredatabricks.net`formatet . Här är **East US 2** den Azure-region där du skapade din Azure Databricks-arbetsyta.

    * Därefter uppmanas du att ange en token. Ange den token som du skapade tidigare.

När du har slutfört de här stegen kan du börja använda Databricks CLI från Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Använd Databricks CLI

Du kan nu börja använda Databricks CLI. Kör till exempel följande kommando för att lista alla Databricks-kluster som du har på arbetsytan.

    databricks clusters list

Du kan också använda följande kommando för att komma åt Databricks filsystem (DBFS).

    databricks fs ls


En fullständig referens för kommandon finns i [Databricks CLI](/azure/databricks/dev-tools/databricks-cli).


## <a name="next-steps"></a>Nästa steg

* Mer information om Azure CLI finns i [Azure CLI översikt](../cloud-shell/overview.md)
* Information om hur du ser en lista över kommandon för Azure CLI finns i [Azure CLI-referens](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Information om hur du ser en lista över kommandon för Databricks CLI finns i [Databricks CLI](/azure/databricks/dev-tools/databricks-cli)


