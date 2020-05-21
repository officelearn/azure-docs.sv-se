---
title: Ansluta till Azure Synapse-länken (för hands version) för Azure Cosmos DB
description: Så här ansluter du en Azure Cosmos DB till en Synapse-arbetsyta med Azure Synapse-länk
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 6bdc2eca3bdf02814ee851ff266e04bb57f7978c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658822"
---
# <a name="connect-to-azure-synapse-link-preview-for-azure-cosmos-db"></a>Ansluta till Azure Synapse-länken (för hands version) för Azure Cosmos DB

Den här artikeln beskriver hur du kommer åt en Azure Cosmos DB databas från Azure Synapse Analytics Studio med Azure Synapse-länken.

## <a name="prerequisites"></a>Krav

Innan du ansluter en Azure Cosmos DB-databas till din arbets yta behöver du följande:

* Befintliga Azure Cosmos DB databasen eller skapa ett nytt konto efter den här [snabb](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account) starten
* Befintlig arbets yta i Synapse eller skapa en ny arbets yta efter den här [snabb](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) starten 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Aktivera Azure Cosmos DB analys lager

Om du vill köra storskalig analys i Azure Cosmos DB utan att påverka din drifts prestanda rekommenderar vi att du aktiverar Synapse-länken för Azure Cosmos DB. Synapse-länken ger HTAP-kapacitet till en behållare och inbyggt stöd i Azure Synapse.

## <a name="navigate-to-synapse-studio"></a>Gå till Synapse Studio

Från din Synapse-arbetsyta väljer du **Starta Synapse Studio**. På Start sidan för Synapse Studio väljer du * * data, vilket leder dig till **Data Object Explorer**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Ansluta en Azure Cosmos DB-databas till en Synapse-arbetsyta

Anslutning av en Azure Cosmos DB databas görs som en länkad tjänst. Med en Azure Cosmos DB länkad tjänst kan användare söka efter och utforska data, läsa och skriva från Apache Spark för Azure Synapse Analytics eller SQL till Azure Cosmos DB.

Från data Object Explorer kan du ansluta direkt till en Azure Cosmos DB-databas genom att utföra följande steg:

1. Välj ***+*** ikon nära data
2. Välj **Anslut till externa data**
3. Välj det API som du vill ansluta till: SQL API eller API för MongoDB
4. Välj ***Fortsätt***
5. Namnge den länkade tjänsten. Namnet visas i Object Explorer och används av Synapse körnings tider för att ansluta till databasen och behållarna. Vi rekommenderar att du använder ett eget namn.
6. Välj det **Azure Cosmos DB konto namnet** och **databas namnet**
7. Valfritt Om ingen region anges dirigeras Synapse kör tids åtgärder till den närmaste region där analys lagret är aktiverat. Du kan dock ange manuellt vilken region du vill att användarna ska ha åtkomst till Azure Cosmos DB analys lager. Välj **ytterligare anslutnings egenskaper** och sedan **ny**. Under **egenskaps namn**skriver du ***PreferredRegions*** och anger **värdet** till den region som du vill ha (exempel: WestUS2, det finns inget blank steg mellan ord och nummer)
8. Välj ***skapa***

Azure Cosmos DB-databaser visas under den flik som är **länkad** i avsnittet Azure Cosmos dB. Med Azure Cosmos DB kan du särskilja en HTAP-aktiverad behållare från en skrivskyddad OLTP-behållare via följande ikoner:

**Endast behållare för OLTP**:

![OLTP-behållare](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**HTAP-aktiverad behållare**:

![HTAP-behållare](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Interagera snabbt med kod genererade åtgärder

Genom att högerklicka på en behållare har du en lista med gester som utlöser en spark-eller SQL-körning. Att skriva till en behållare sker via transaktions lagringen för Azure Cosmos DB och kommer att förbruka enheter för programbegäran.  

## <a name="next-steps"></a>Nästa steg

* [Lär dig vad som stöds mellan Synapse och Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Lär dig hur du frågar analys lagret med Spark](./how-to-query-analytical-store-spark.md)