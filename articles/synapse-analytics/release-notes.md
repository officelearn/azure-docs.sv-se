---
title: Viktig information
description: Viktig information för Azure Synapse Analytics (arbets ytor)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 514694dc2e3f06db2fb80f6b3ba0106343be11d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658500"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Viktig information om Azure Synapse Analytics (för hands version)

Den här artikeln beskriver begränsningar och problem med Azure Synapse Analytics (arbets ytor). Relaterad information finns i [Vad är Azure Synapse Analytics (arbets ytor)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure-Synapse (arbets ytor) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- Problem och kund påverkan: arbets ytor som skapats av SDK kan inte starta Synapse Studio

- Lösning: utför följande steg: 
  1.    Skapa arbets yta genom att köra `az synapse workspace create` .
  2.    Extrahera ID för hanterad identitet genom att köra `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Lägg till arbets yta som roll till lagrings kontot genom att köra ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` .
  4.    Lägg till brand Väggs regel genom att köra ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Nästa steg

* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda Synapse Studio](quickstart-synapse-studio.md)
* [Skapa en SQL-pool](quickstart-create-sql-pool-portal.md)
* [Använda SQL på begäran](quickstart-sql-on-demand.md)
* [Skapa en Apache Spark pool](quickstart-create-apache-spark-pool-portal.md)