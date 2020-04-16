---
title: Ansluta till en Azure Synapse-arbetsyta med privata länkar
description: Den här artikeln lär dig hur du ansluter till din Azure Synapse-arbetsyta med hjälp av privata länkar
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432195"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Ansluta till din Azure Synapse-arbetsyta med privata länkar (förhandsversion)

Den här artikeln lär dig hur du skapar en privat slutpunkt till din Azure Synapse-arbetsyta. Se [privata länkar och privata slutpunkter](https://docs.microsoft.com/azure/private-link/) om du vill veta mer.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Steg 1: Öppna din Azure Synapse-arbetsyta i Azure-portalen

Välj **Privat slutpunktsanslutning** under **Säkerhet** och välj sedan + **Privat slutpunkt**.
![Öppna Azure Synapse-arbetsytan i Azure-portalen](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Steg 2: Välj information om prenumeration och region

Välj **prenumerations-** och **resursgrupp**under fliken Grunderna **Subscription** i fönstret **Skapa en privat slutpunkt** . Ge ett **namn** till den privata slutpunkt som du vill skapa. Välj den **region** där du vill att den privata slutpunkten ska skapas.

Privata slutpunkter skapas i ett undernät. Den valda prenumerationen, resursgruppen och regionen filtrerar de privata slutpunktsundernäten. Välj **Nästa: Resurs >** när du är klar.
![Välj information om prenumeration och region](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Steg 3: Välj information om Din Azure Synapse-arbetsyta

Välj **Anslut till en Azure-resurs i min katalog** på fliken **Resurs.** Välj den **prenumeration** som innehåller din Azure Synapse-arbetsyta. **Resurstypen** för att skapa privata slutpunkter till en Azure Synapse-arbetsyta är *Microsoft.Synapse/arbetsytor*.

Välj din Azure Synapse-arbetsyta som **resurs**. Varje Azure Synapse-arbetsyta har tre **Target-underresurs** som du kan skapa en privat slutpunkt till: SqlOnDemand och Dev.

Välj **Nästa: Konfiguration>** för att gå vidare till nästa del av installationen.
![Välj information om prenumeration och region](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

På fliken **Konfiguration** väljer du det **virtuella nätverket** och **undernätet** där den privata slutpunkten ska skapas. Du måste också skapa en DNS-post som mappar till den privata slutpunkten.

Välj **Ja** för **integrering med privat DNS-zon** om du vill integrera din privata slutpunkt med en privat DNS-zon. Om du inte har en privat DNS-zon som är associerad med ditt virtuella nätverk skapas en ny privat DNS-zon. Välj **Granska + skapa** när du är klar.

![Välj information om prenumeration och region](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

När distributionen är klar öppnar du din Azure Synapse-arbetsyta i Azure-portalen och väljer **Privata slutpunktsanslutningar**. Det nya privata slutpunkten och det privata slutpunktsanslutningsnamnet som är associerade till den privata slutpunkten visas.

![Välj information om prenumeration och region](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om [Hanterad arbetsyta VNet](./synapse-workspace-managed-vnet.md)

Läs mer om [Hanterade privata slutpunkter](./synapse-workspace-managed-private-endpoints.md)

[Skapa hanterade privata slutpunkter till dina datakällor](./how-to-create-managed-private-endpoints.md)
