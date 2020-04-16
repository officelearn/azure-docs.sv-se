---
title: Hanterade privata slutpunkter
description: En artikel som förklarar hanterade privata slutpunkter i Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 08c6610541d987cddd7cf2aeb71c526cb2359598
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423686"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Synapse Hanterade privata slutpunkter (förhandsgranskning)

I den här artikeln beskrivs hanterade privata slutpunkter i Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Hanterade privata slutpunkter

Hanterade privata slutpunkter är privata slutpunkter som skapas i VNet för hanterad arbetsyta som upprättar en privat länk till Azure-resurser. Azure Synapse hanterar dessa privata slutpunkter för din räkning.

Azure Synapse stöder privata länkar. Med privat länk kan du komma åt Azure-tjänster (till exempel Azure Storage, Azure Cosmos DB och Azure SQL Data Warehouse) och Azure-värdbaserade kund-/partnertjänster från ditt Azure VNet på ett säkert sätt.

När du använder en privat länk passerar trafiken mellan ditt virtuella nätverk och arbetsytan helt via Microsofts stamnätsnätverk. Private Link skyddar mot dataexfiltrationsrisker. Du upprättar en privat länk till en resurs genom att skapa en privat slutpunkt.

Privat slutpunkt använder en privat IP-adress från ditt virtuella nätverk för att effektivt föra in tjänsten i ditt virtuella nätverk. Privata slutpunkter mappas till en viss resurs i Azure och inte hela tjänsten. Kunder kan begränsa anslutningen till en viss resurs som godkänts av organisationen. Läs mer om [privata länkar och privata slutpunkter](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>Hanterade privata slutpunkter stöds endast i Azure Synapse-arbetsytor med ett hanterat virtuella nätverk för arbetsyta.
>[!NOTE]
>Vi rekommenderar att du skapar hanterade privata slutpunkter för att ansluta till alla dina Azure-datakällor. All utgående trafik från det >hanterade arbetsytan VNet kommer att blockeras i framtiden.

En privat slutpunktsanslutning skapas i ett "Väntande" tillstånd när du skapar en hanterad privat slutpunkt i Azure Synapse. Ett arbetsflöde för godkännande initieras. Ägaren till den privata länkresursen är ansvarig för att godkänna eller avvisa anslutningen.

Om ägaren godkänner anslutningen upprättas den privata länken. Annars upprättas inte den privata länken. I båda fallen uppdateras den hanterade privata slutpunkten med anslutningens status.

Endast en hanterad privat slutpunkt i ett godkänt tillstånd kan skicka trafik till en viss privat länkresurs.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Hanterade privata slutpunkter för SQL-pool och SQL på begäran

SQL-pool och SQL on-demand är analytiska funktioner i din Azure Synapse-arbetsyta. Dessa funktioner använder infrastruktur för flera innehavare som inte distribueras till [det hanterade arbetsytans virtuella nätverk](./synapse-workspace-managed-vnet.md).

När en arbetsyta skapas skapar Azure Synapse två hanterade privata slutpunkter till SQL-pool och SQL på begäran på den arbetsytan. 

Dessa två hanterade privata slutpunkter visas i Azure Synapse Studio. Välj **Hantera** i den vänstra navigeringen och välj sedan **Hanterade virtuella nätverk** för att se sedan i Studion.

Den hanterade privata slutpunkten som riktar sig till SQL-poolen kallas *synapse-ws-sql--\<arbetsområdesnamn\> * och den som riktar sig till SQL on-demand kallas *synaps-ws-sqlOnDemand--\<workspacename\>*.
![Hanterade privata slutpunkter för SQL-pool och SQL på begäran](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Dessa två hanterade privata slutpunkter skapas automatiskt åt dig när du skapar din Azure Synapse-arbetsyta. Du debiteras inte för dessa två hanterade privata slutpunkter.

## <a name="next-steps"></a>Nästa steg

[Skapa hanterade privata slutpunkter till dina datakällor](./how-to-create-managed-private-endpoints.md)
