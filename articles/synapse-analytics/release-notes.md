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
ms.openlocfilehash: 059e77c063d00ef850a171507ca2e06422ade426
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82191778"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Viktig information om Azure Synapse Analytics (för hands version)

Den här artikeln beskriver begränsningar och problem med Azure Synapse Analytics (arbets ytor). Relaterad information finns i [Vad är Azure Synapse Analytics (arbets ytor)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure-Synapse (arbets ytor) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- Problem och kund påverkan: arbets ytor som skapats av SDK kan inte starta Synapse Studio

- Lösning: utför följande steg: 
  1.    Skapa arbets yta genom `az synapse workspace create`att köra.
  2.    Extrahera ID för hanterad identitet `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`genom att köra.
  3.    Lägg till arbets yta som roll till lagrings ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`kontot genom att köra.
  4.    Lägg till brand Väggs ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `regel genom att köra.

## <a name="next-steps"></a>Nästa steg

* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda Synapse Studio](quickstart-synapse-studio.md)
* [Skapa en SQL-pool](quickstart-create-sql-pool.md)
* [Använda SQL på begäran](quickstart-sql-on-demand.md)
* [Skapa en Apache Spark pool](quickstart-create-apache-spark-pool.md)