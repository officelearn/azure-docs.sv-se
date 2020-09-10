---
title: 'Viktig information: Azure Synapse Analytics (för hands versioner av arbets ytor)'
description: Viktig information för Azure Synapse Analytics (för hands versioner av arbets ytor)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c8212af727a47ab05936c8b59c781cd49c5b0ba6
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669687"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Viktig information om Azure Synapse Analytics (arbets ytans förhands granskning)

Den här artikeln beskriver begränsningar och problem med Azure Synapse Analytics (arbets ytor). Relaterad information finns i [Vad är Azure Synapse Analytics (arbets ytor)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Azure CLI

- Problem och kund påverkan: arbets ytor som skapats av SDK kan inte starta Synapse Studio

- Lösning: utför följande steg: 
  1.    Skapa arbets yta genom att köra `az synapse workspace create` .
  2.    Extrahera ID för hanterad identitet genom att köra `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Lägg till arbets yta som roll till lagrings kontot genom att köra ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` .
  4.    Lägg till brand Väggs regel genom att köra ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure Synapse](overview-what-is.md)
* [Komma igång](get-started.md)
* [Vanliga frågor och svar](overview-faq.md)
