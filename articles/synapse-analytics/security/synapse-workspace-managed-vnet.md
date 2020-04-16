---
title: Hanterade virtuella nätverk i Azure Synapse Analytics
description: En artikel som förklarar Hanterat virtuellt nätverk i Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 162d96244b01f8c5e1acf224475aadb9508f0aa5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423637"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Hanterade virtuella nätverk för Azure Synapse Analytics (förhandsversion)

I den här artikeln beskrivs Hanterat virtuellt nätverk i Azure Synapse Analytics.

## <a name="managed-workspace-vnet"></a>Hanterad arbetsyta VNet

När du skapar din Azure Synapse-arbetsyta kan du välja att associera den till ett virtuella nätverk. Det virtuella nätverk som är associerat med din arbetsyta hanteras av Azure Synapse. Det här virtuella nätverket kallas ett *hanterat arbetsområdevnät*.

Hanterad arbetsyta VNet ger dig värde på fyra sätt:

- Med ett hanterat virtuella nätverk för arbetsyta kan du avlasta bördan av att hantera det virtuella nätverket till Azure Synapse.
- Du behöver inte konfigurera inkommande NSG-regler på dina egna virtuella nätverk så att Azure Synapse-hanteringstrafik kan komma in i ditt virtuella nätverk. Felaktig konfiguration av dessa NSG-regler orsakar avbrott i tjänsten för kunder.
- Du behöver inte skapa ett undernät för Spark-kluster baserat på toppbelastning.
- Hanterad virtuella arbetsytan med hanterad privat slutpunkter skyddar mot dataexfiltration. Du kan bara skapa hanterade privata slutpunkter på en arbetsyta som har ett hanterat arbetsområde virtuella nätverk associerat med det.

Om du skapar en arbetsyta med ett kopplat fungerande virtuella nätverk med hanterad arbetsyta säkerställer du att arbetsytan är nätverksinsolerad från andra arbetsytor. Azure Synapse tillhandahåller olika analytiska funktioner på en arbetsyta: Dataintegration, Apache Spark, SQL-pool och SQL på begäran.

Om arbetsytan har ett hanterat virtuella nätverk för arbetsyta distribueras dataintegrerings- och Spark-resurser i den. Ett hanterat virtuella nätverk på arbetsyta ger också isolering på användarnivå för Spark-aktiviteter eftersom varje Spark-kluster finns i sitt eget undernät.

SQL-pool och SQL på begäran är funktioner för flera innehavare och finns därför utanför det hanterade arbetsytans virtuella nätverk. Kommunikation inom arbetsytan till SQL-pool och SQL on-demand använder privata Azure-länkar. Dessa privata länkar skapas automatiskt åt dig när du skapar en arbetsyta med ett hanterat virtuella nätverk för arbetsyta som är associerat till den.

>[!IMPORTANT]
>Du kan inte ändra den här arbetsytans konfiguration när arbetsytan har skapats. Du kan till exempel inte konfigurera om en arbetsyta som inte har ett kopplat arbetsområdesnätverk kopplat till den och associera ett virtuella nätverk till den. På samma sätt kan du inte konfigurera om en arbetsyta med ett hanterat virtuella nätverk för arbetsyta som är associerat till den och ta bort kopplingen till det virtuella nätverket från det.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Skapa en Azure Synapse-arbetsyta med ett hanterat virtuella nätverk för arbetsyta

Om du vill skapa en Azure Synapse-arbetsyta som har ett kopplat virtuellt nätverk med hanterad arbetsyta markerar du fliken **Säkerhet + nätverk** i Azure-portalen och kontrollerar kryssrutan Aktivera hanterade virtuella **nätverk.**

Om du lämnar kryssrutan avmarkerad har arbetsytan inget kopplat till det virtuella nätverket.

>[!IMPORTANT]
>Du kan bara använda privata länkar på en arbetsyta med ett hanterat arbetsområdes-virtuella nätverk.

![Aktivera hanterad arbetsytaVNet](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>All utgående trafik från det hanterade arbetsytans virtuella nätverk kommer att blockeras i framtiden. Vi rekommenderar att du ansluter till alla dina datakällor med hanterade privata slutpunkter.

Du kan kontrollera om din Azure Synapse-arbetsyta är associerad med ett hanterat virtuella nätverk för arbetsyta genom att välja **Översikt** från Azure-portalen.

![Översikt över arbetsytan i Azure-portalen](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Nästa steg

Skapa en [Azure Synapse-arbetsyta](../quickstart-create-workspace.md)

Läs mer om [Hanterade privata slutpunkter](./synapse-workspace-managed-private-endpoints.md)

[Skapa hanterade privata slutpunkter till dina datakällor](./how-to-create-managed-private-endpoints.md)
