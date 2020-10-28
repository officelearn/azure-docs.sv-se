---
title: Få hjälp med Azures distribution av VMware-lösningar eller etablerings fel
description: Så här hämtar du den information du behöver från ditt privata moln i Azure VMware-lösningen för att skicka en tjänstbegäran om distribution av Azure VMware-lösningar eller etablerings fel.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 1f46dde895db417fd2b488a6203d5482e73d3c5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779500"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Få hjälp med Azures distribution av VMware-lösningar eller etablerings fel

Den här artikeln hjälper dig med distribution av Azure VMware-lösningar och etablerings problem. När du har problem med ditt privata moln måste du öppna en [supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support) (SR) i Azure Portal. 

Först måste du, om du behöver samla in viktig information i Azure Portal:

- Korrelations-ID
- ExpressRoute krets-ID

## <a name="collect-the-correlation-id"></a>Samla in korrelations-ID
 
Ett korrelations-ID genereras när du skapar ett privat moln eller en resurs i Azure. Varje Azure Resource Manager distribution genererar också ett korrelations-ID. Det här ID: t möjliggör snabbare generering och problemlösning i SR. 
 
Här är ett exempel på utdata från en misslyckad distribution av privata moln, med korrelations-ID markerat.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Det gick inte att distribuera privat moln med korrelations-ID.":::

Kopiera och spara det här korrelations-ID: t som ska ingå i tjänstbegäran. Mer information finns i [skapa en support förfrågan](#create-your-support-request) i slutet av den här artikeln.

Om ett fel inträffar i för validerings stegen genereras inget korrelations-ID. I det här fallet kan du ange den information som du använde när du skapade Azure VMware-lösningens privata moln, inklusive:

- Plats
- Resursgrupp
- Resursnamn
 
### <a name="collect-a-summary-of-errors"></a>Samla in en sammanfattning av fel

Informationen om eventuella fel kan också hjälpa dig att lösa problemet. Från föregående skärm väljer du varnings meddelandet för att visa en sammanfattning av fel.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Det gick inte att distribuera privat moln med korrelations-ID.":::

Kopiera och spara den här sammanfattningen så att den ingår i SR.
 
### <a name="retrieve-past-deployments"></a>Hämta tidigare distributioner

Du kan hämta tidigare distributioner, inklusive misslyckade, genom att söka i distributions aktivitets loggen som nås genom att välja ikonen meddelanden.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Det gick inte att distribuera privat moln med korrelations-ID.":::

I meddelanden väljer du **Fler händelser i aktivitets loggen** .

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Det gick inte att distribuera privat moln med korrelations-ID.":::

Sök sedan efter namnet på resursen eller annan information som används för att skapa resursen för att hitta den misslyckade distributionen och dess korrelations-ID. I följande exempel visas Sök resultat i en privat moln resurs (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Det gick inte att distribuera privat moln med korrelations-ID.":::
 
Om du väljer åtgärds namnet för den misslyckade distributionen öppnas ett fönster med information. Välj JSON-fliken och leta efter correlationId. Kopiera och inkludera i SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>Samla in ExpressRoute-ID (URI)
 
Du kanske försöker skala eller peera ett befintligt privat moln med ExpressRoute-kretsen för det privata molnet och det fungerar inte. I så fall behöver du ExpressRoute-ID: t. 

I Azure Portal väljer du **anslutnings > ExpressRoute** och kopierar **ExpressRoute-ID: t** till Urklipp.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Det gick inte att distribuera privat moln med korrelations-ID."::: 
 
> [!NOTE]
> I detta fall kan för validerings kontroller Miss lyckas före distributionen, och den enda tillgängliga informationen är fel-och fel meddelanden. Detta kan vara till hjälp vid många fel, till exempel problem med kvot problem, och det är viktigt att inkludera dessa meddelanden i support förfrågan. Information om hur du samlar in dessa finns i det tidigare avsnittet [samla in en sammanfattning av fel](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Skapa en support förfrågan

Allmän vägledning om hur du skapar en support förfrågan finns i [så här skapar du en support förfrågan för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Här är en detaljerad vägledning om hur du skapar en SR för distribution av VMware-lösningar eller etablerings problem i Azure.

1. Välj **Hjälp** ikonen och sedan **+ ny supportbegäran** .

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Det gick inte att distribuera privat moln med korrelations-ID.":::

2. Fyll i alla obligatoriska fält och på fliken **grundläggande** :

    - För **problem typ** väljer du **konfigurations-och installations problem** .

    - Under **typ av problem** väljer du **etablera ett privat moln** .

3. På fliken **information** :

    - Fyll i alla obligatoriska fält.

    - Klistra in ditt korrelations-ID eller ExpressRoute-ID i de angivna fälten. Om du inte ser ett speciellt fält kan du klistra in dem i text rutan under **Visa information om problemet.**

    - Klistra in fel information, inklusive Sammanfattning av fel som du har kopierat, i text rutan under **Visa information om problemet.**

4. Granska och välj **skapa** för att skapa din SR.
