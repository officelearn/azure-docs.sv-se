---
title: 'Viktig information: Azure Synapse Analytics (arbets ytor)'
description: Viktig information för Azure Synapse Analytics (arbets ytor)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: add5c89e83f33980803bf571239023859653c4f1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059614"
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