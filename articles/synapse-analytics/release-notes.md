---
title: Viktig information
description: Viktig information för Azure Synapse Analytics (arbetsytor)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c1b5b9ac5d7c3f04dd3ae2e843425a5ead0d4c07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423861"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Versionsanteckningar för Azure Synapse Analytics (förhandsversion)

I den här artikeln beskrivs begränsningar och problem med Azure Synapse Analytics (arbetsytor). Relaterad information finns i [Vad är Azure Synapse Analytics (arbetsytor)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (arbetsytor) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- Problem och kundpåverkan: Arbetsytor som skapats av SDK kan inte starta Synapse Studio

- Lösning: Slutför följande steg: 
  1.    Skapa arbetsyta genom `az synapse workspace create`att köra 2.    Extrahera hanterad identitets-ID genom att köra`$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`
  3.    Lägga till arbetsyta som roll i lagringskontot genom att köra` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`
  4.    Lägga till brandväggsregel genom att köra` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `

## <a name="next-steps"></a>Nästa steg

* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använd Synapse Studio](quickstart-synapse-studio.md)
* [Skapa en SQL-pool](quickstart-create-sql-pool.md)
* [Använda SQL på begäran](quickstart-sql-on-demand.md)
* [Skapa en Apache Spark-pool](quickstart-create-apache-spark-pool.md)