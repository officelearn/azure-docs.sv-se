---
title: Hanterat virtuellt nätverk i Azure Synapse Analytics
description: En artikel som förklarar hanterade virtuella nätverk i Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 162d96244b01f8c5e1acf224475aadb9508f0aa5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423637"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure Synapse Analytics-hanterad Virtual Network (för hands version)

I den här artikeln förklaras hanterade Virtual Network i Azure Synapse Analytics.

## <a name="managed-workspace-vnet"></a>VNet för hanterad arbets yta

När du skapar din Azure dataSynapses-arbetsyta kan du välja att koppla den till ett VNet. Det VNet som är associerat med din arbets yta hanteras av Azure-Synapse. Det här virtuella nätverket kallas för en *hanterad arbets yta VNet*.

VNet för hanterad arbets yta ger dig ett värde på fyra sätt:

- Med ett VNet för hanterad arbets yta kan du avlasta hanteringen av VNet till Azure-Synapse.
- Du behöver inte konfigurera inkommande NSG-regler på din egen virtuella nätverk för att tillåta Azure Synapse Management-trafik att ange ditt VNet. Felaktig konfiguration av dessa NSG-regler orsakar avbrott i tjänsten för kunder.
- Du behöver inte skapa ett undernät för dina Spark-kluster baserat på hög belastning.
- Hanterade arbets ytans VNet tillsammans med hanterade privata slut punkter skyddar mot data exfiltrering. Du kan bara skapa hanterade privata slut punkter i en arbets yta som har ett virtuellt nätverk för en hanterad arbets yta.

Om du skapar en arbets yta med en hanterad VNet-VNet som är associerad med det säkerställs att din arbets yta är isolerad från andra arbets ytor. Azure Synapse innehåller olika analys funktioner i en arbets yta: data integrering, Apache Spark, SQL-pool och SQL på begäran.

Om din arbets yta har ett VNet för hanterad arbets yta, distribueras data integrering och Spark-resurser i den. En hanterad arbets yta VNet tillhandahåller också isolering på användar nivå för Spark-aktiviteter eftersom varje Spark-kluster finns i ett eget undernät.

SQL-poolen och SQL på begäran är funktioner för flera klienter och finns därför utanför den hanterade arbets ytans VNet. Kommunikation inom arbets ytan till SQL-poolen och SQL på begäran använder Azures privata länkar. De här privata länkarna skapas automatiskt åt dig när du skapar en arbets yta med ett virtuellt nätverk för en hanterad arbets yta som är kopplat till den.

>[!IMPORTANT]
>Du kan inte ändra den här arbets ytans konfiguration när arbets ytan har skapats. Du kan till exempel inte konfigurera om en arbets yta som inte har en hanterad VNet-VNet som är kopplad till den och associera ett VNet till det. På samma sätt kan du inte konfigurera om en arbets yta med ett virtuellt nätverk för hanterad arbets yta som är kopplat till den och avassocierar det virtuella nätverket.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Skapa en Azure dataSynapses-arbetsyta med en hanterad arbets yta VNet

Om du vill skapa en Azure dataSynapses-arbetsyta som har ett virtuellt nätverk för en hanterad arbets yta, väljer du fliken **säkerhet + nätverk** i Azure Portal och markerar kryss rutan **Aktivera hanterat virtuellt nätverk** .

Om du lämnar kryss rutan avmarkerad så har din arbets yta ingen VNet kopplad till sig.

>[!IMPORTANT]
>Du kan bara använda privata länkar i en arbets yta som har en hanterad arbets yta VNet.

![Aktivera hanterad arbets yta VNet](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>All utgående trafik från VNet för hanterad arbets yta kommer att blockeras i framtiden. Vi rekommenderar att du ansluter till alla dina data källor med hjälp av hanterade privata slut punkter.

Du kan kontrol lera om din Azure Synapse-arbetsyta är kopplad till ett VNet för hanterad arbets yta genom att välja **Översikt** från Azure Portal.

![Översikt över arbets ytan i Azure Portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Nästa steg

Skapa en [Azure DataSynapses-arbetsyta](../quickstart-create-workspace.md)

Läs mer om [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md)

[Skapa hanterade privata slut punkter till dina data källor](./how-to-create-managed-private-endpoints.md)
