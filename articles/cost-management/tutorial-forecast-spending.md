---
title: Självstudie – Skapa prognoser för utgifter i Cloudyn i Azure | Microsoft Docs
description: I den här självstudien lär du dig att skapa prognoser för utgifter med hjälp av historiska data om användning och utgifter.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: fcefbcc07886cbae3db869596255fbe841e018ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61021090"
---
# <a name="tutorial-forecast-future-spending"></a>Självstudie: Skapa prognoser för framtida utgifter

Cloudyn hjälper dig att förutse framtida kostnader med hjälp av förbrukningshistorik och information om utgifter. Du kan använda Cloudyn-rapporter till att visa alla data om prognostiserade kostnader. I exemplen i den här självstudien får du lära dig att granska kostnadsprognoser med hjälp av rapporterna. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa prognoser för framtida utgifter

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha ett Azure-konto.
- Du måste antingen ha en utvärderingsregistrering eller en betald prenumeration för Cloudyn.

## <a name="forecast-future-spending"></a>Skapa prognoser för framtida utgifter

Cloudyn innehåller rapporter för kostnadsprognoser som hjälper dig att göra prognoser för utgifter baserat på din förbrukning över tid. Det primära syftet är att du ska kunna se till att kostnadstrenderna inte överstiger förväntningarna inom organisationen. De rapporter du använder är Current Month Projected Cost (Beräknad kostnad aktuell månad) och Annual Projected Cost (Beräknad årlig kostnad). Både visar den beräknade framtida kostnaden om användningen förblir relativt lik användningen de senaste 30 dagarna.

I rapporten Current Month Projected Cost visas kostnaderna för dina tjänster. Kostnaderna från början av månaden samt föregående månad används till att visa den beräknade kostnaden. Gå till rapportmenyn överst i portalen och klicka på **Costs (Kostnader)** > **Projection and Budget (Planering och budget)** > **Current Month Projected Cost** (Planerad kostnad för aktuell månad). I följande bild visas ett exempel.

![Exempelinformation som visas i rapporten om beräknad kostnad för aktuell månad](./media/tutorial-forecast-spending/project-month01.png)

I det här exemplet ser du vilka tjänster som kostat mest. Kostnaderna för Azure var lägre än kostnaderna för AWS. Om du vill se detaljer om de beräknade kostnaderna för virtuella Azure-datorer går du till listan **Filter** och väljer **Azure/VM**.

![Exempel som visar beräknad kostnad för Azure VM för aktuell månad](./media/tutorial-forecast-spending/project-month02.png)

Följ samma grundprincip när du vill titta på månatliga beräknade kostnader för andra tjänster du är intresserad av.

I rapporten Annual Projected Cost visas extrapolerade kostnader för dina tjänster kommande 12 månader.

Gå till rapportmenyn överst i portalen och klicka på **Costs (Kostnader)** > **Projection and Budget (Planering och budget)** > **Annual Projected Cost** (Planerad årlig kostnad). I följande bild visas ett exempel.

![Exempel som visar rapport om beräknad årskostnad](./media/tutorial-forecast-spending/project-annual01.png)

I det här exemplet ser du vilka tjänster som kostat mest. Precis som i det månatliga exemplet var Azure-kostnaderna lägre än AWS-kostnaderna. Om du vill se detaljer om de beräknade kostnaderna för virtuella Azure-datorer går du till listan **Filter** och väljer **Azure/VM**.

![Exempel som visar beräknad årskostnad för virtuella datorer](./media/tutorial-forecast-spending/project-annual02.png)

I bilden ovan är den årliga beräknade kostnaden för virtuella Azure-datorer 28 374 USD.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa prognoser för framtida utgifter


Gå vidare till nästa självstudie om du vill lära dig hur du hanterar kostnader med kostnadsallokering och showback-rapporter.

> [!div class="nextstepaction"]
> [Hantera kostnader med kostnadsallokering och showback-rapporter](tutorial-manage-costs.md)
