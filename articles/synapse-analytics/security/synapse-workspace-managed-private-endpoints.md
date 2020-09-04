---
title: Hanterade privata slutpunkter
description: En artikel som förklarar hanterade privata slut punkter i Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: dedf0795e72c4bdace430ba2dd07ade9d792f13c
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459547"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Synapse-hanterade privata slut punkter (förhands granskning)

Den här artikeln förklarar hanterade privata slut punkter i Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Hanterade privata slutpunkter

Hanterade privata slut punkter är privata slut punkter som skapats i den hanterade arbets ytan Microsoft Azure Virtual Network att upprätta en privat länk till Azure-resurser. Azure Synapse hanterar de här privata slut punkterna för din räkning.

Azure Synapse stöder privata länkar. Med privat länk kan du komma åt Azure-tjänster (till exempel Azure Storage och Azure Cosmos DB) och Azure-värdbaserade kund-och partner tjänster från Azure Virtual Network på ett säkert sätt.

När du använder en privat länk passerar trafiken mellan din Virtual Network och arbets ytan helt över Microsoft stamnät nätverket. Privat länk skyddar mot data exfiltrering-risker. Du upprättar en privat länk till en resurs genom att skapa en privat slut punkt.

Privat slut punkt använder en privat IP-adress från din Virtual Network för att effektivt ta tjänsten i Virtual Network. Privata slut punkter mappas till en angiven resurs i Azure och inte till hela tjänsten. Kunder kan begränsa anslutningen till en speciell resurs som godkänts av organisationen. Lär dig mer om [privata länkar och privata slut punkter](https://docs.microsoft.com/azure/private-link/).

>[!IMPORTANT]
>Hanterade privata slut punkter stöds bara i Azure Synapse-arbetsytor med en hanterad arbets yta Virtual Network.

>[!NOTE]
>All utgående trafik från den hanterade arbets ytan Virtual Network, förutom via hanterade privata slut punkter, kommer att blockeras i framtiden. Vi rekommenderar att du skapar hanterade privata slut punkter för att ansluta till alla dina Azure-datakällor utanför arbets ytan. 

En privat slut punkts anslutning skapas i ett väntande tillstånd när du skapar en hanterad privat slut punkt i Azure Synapse. Ett arbets flöde för godkännande har initierats. Ägaren till den privata länk resursen ansvarar för att godkänna eller avvisa anslutningen.

Om ägaren godkänner anslutningen upprättas den privata länken. Annars upprättas inte den privata länken. I båda fallen kommer den hanterade privata slut punkten att uppdateras med anslutningens status.

Endast en hanterad privat slutpunkt i ett godkänt tillstånd kan skicka trafik till en specifik privat länk resurs.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Hanterade privata slut punkter för SQL-poolen och SQL på begäran

SQL-poolen och SQL på begäran är analys funktioner i din Azure Synapse-arbetsyta. Dessa funktioner använder infrastruktur för flera innehavare som inte distribueras till den [hanterade arbets ytan Virtual Network](./synapse-workspace-managed-vnet.md).

När en arbets yta skapas skapar Azure Synapse två hanterade privata slut punkter till SQL-poolen och SQL på begäran på den arbets ytan. 

Dessa två hanterade privata slut punkter visas i Azure Synapse Studio. Välj **Hantera** i det vänstra navigerings fältet och välj sedan **hanterade virtuella nätverk** för att se dem i Studio.

Den hanterade privata slut punkten som är riktad mot SQL-poolen heter *Synapse- \<workspacename\> WS-SQL-* och den som är riktad mot SQL på begäran kallas *Synapse-WS \<workspacename\> -sqlOnDemand--*.
![Hanterade privata slut punkter för SQL-poolen och SQL på begäran](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Dessa två hanterade privata slut punkter skapas automatiskt åt dig när du skapar din Azure dataSynapses-arbetsyta. Du debiteras inte för dessa två hanterade privata slut punkter.

## <a name="next-steps"></a>Nästa steg

[Skapa hanterade privata slut punkter till dina data källor](./how-to-create-managed-private-endpoints.md)
