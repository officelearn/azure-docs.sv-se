---
title: Få hjälp med Azures distribution av VMware-lösningar eller etablerings fel
description: Så här hämtar du den information du behöver från det privata molnet i Azure VMware-lösningen (AVS) för att skicka en tjänstbegäran om AVS-distribution eller etablerings fel.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 05c9dee088b37c37cdcdee7e745cdcd2222b63e0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514585"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Få hjälp med Azures distribution av VMware-lösningar eller etablerings fel

I den här artikeln får du lära dig hur du får hjälp med distribution av Azure VMware-lösningar (AVS) eller etablerings fel i ditt privata moln genom att öppna en tjänstbegäran (SR) i Azure Portal. Först måste du, om du behöver samla in viktig information i Azure Portal. I de flesta fall behöver du:

- Korrelations-ID (av misslyckad distribution)
- ExpressRoute krets-ID (vid försök att skala eller peera ett befintligt privat moln med ExpressRoute-kretsen för det privata molnet och det Miss lyckas)

## <a name="collect-the-correlation-id"></a>Samla in korrelations-ID
 
Nu ska vi titta på korrelations-ID: t först. När du skapar ett privat moln (eller en resurs i Azure) genereras ett associerat korrelations-ID. Varje Azure Resource Manager distribution genererar också ett unikt korrelations-ID. Det här ID: t möjliggör snabbare generering och problemlösning i SR. 
 
Här är ett exempel på utdata från en misslyckad distribution av privata moln, med korrelations-ID markerat.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Det gick inte att distribuera privat moln med korrelations-ID.":::

Kopiera och spara det här korrelations-ID: t som ska ingå i tjänstbegäran. Mer information finns i [skapa en support förfrågan](#create-your-support-request) i slutet av den här artikeln.

Om ett fel inträffar i för validerings stegen, innan ett privat moln distribueras, genereras inget korrelations-ID. I det här fallet kan du bara ange den information som du använde när du skapade det privata AVS-molnet, inklusive:

- Position
- Resursgrupp
- Resursnamn
 
### <a name="collect-a-summary-of-errors"></a>Samla in en sammanfattning av fel

Information om eventuella fel kan också vara till hjälp när du löser problemet. Från föregående skärm väljer du **Klicka här om du vill se information** (markerad) och en sammanfattning av fel öppnas, som visas i följande skärm bild.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Sammanfattning av fel.":::

Kopiera och spara den här sammanfattningen så att den ingår i SR.
 
### <a name="retrieve-past-deployments"></a>Hämta tidigare distributioner

Du kan hämta tidigare distributioner, inklusive misslyckade, genom att söka i distributions aktivitets loggen som nås genom att välja ikonen meddelanden.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Öppna meddelanden.":::

I meddelanden väljer du **Fler händelser i aktivitets loggen**.

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Länk: fler händelser i aktivitets loggen.":::

Sök sedan efter namnet på resursen eller på en annan unik information som du använde när du skapade resursen för att hitta den misslyckade distributionen och dess korrelations-ID. I följande exempel visas Sök resultat i en privat moln resurs (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Hitta tidigare misslyckade AVS-distributioner.":::
 
Om du väljer åtgärds namnet för den misslyckade distributionen öppnas ett fönster med information. Välj JSON-fliken och leta efter correlationId. Kopiera och inkludera i SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>Samla in ExpressRoute-ID (URI)
 
Kanske har du redan ett privat moln och du får ett problem när du försöker skala det eller peer-datorn med ExpressRoute-kretsen för det privata molnet. I så fall kan ExpressRoute-ID: t för det privata molnet användas för att identifiera det när du skapar en SR.

När du visar ett privat moln i portalen väljer du **anslutning > ExpressRoute** och kopierar **ExpressRoute-ID: t** till Urklipp.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Samla in ett ExpressRoute-ID."::: 
 
Klistra in ExpressRoute-ID: t i lämpligt fält i den nya support förfrågan. Mer information finns i det här avsnittet [skapa en support förfrågan](#create-your-support-request).
 
> [!NOTE]
> Vid detta tillfälle kan för validerings kontroller Miss lyckas innan en distribution, och den enda tillgängliga informationen kommer att vara fel-och/eller fel meddelanden. Detta kan vara till hjälp i ett antal fel, till exempel på problem med kvoter, och det är viktigt att inkludera dessa meddelanden i support förfrågan. Information om hur du samlar in dessa finns i det tidigare avsnittet [samla in en sammanfattning av fel](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Skapa en support förfrågan

Allmän vägledning om hur du skapar en support förfrågan finns i [så här skapar du en support förfrågan för Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Här följer ytterligare information om hur du skapar en SR-installation för AVS-distribution eller konfigurations problem.

1. Välj **Hjälp** ikonen och sedan **+ ny supportbegäran**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Samla in ett ExpressRoute-ID.":::

2. Fyll i alla obligatoriska fält och på fliken **grundläggande** :

    - För **problem typ**väljer du **konfigurations-och installations problem**.

    - Under **typ av problem**väljer du **etablera ett privat moln**.

3. På fliken **information** :

    - Fyll i alla obligatoriska fält.

    - Klistra in ditt korrelations-ID eller ExpressRoute-ID i de angivna fälten. Om du inte ser ett speciellt fält för dessa kan du klistra in dem i text rutan under **Visa information om problemet.**

    - Klistra in fel information, inklusive Sammanfattning av fel som du har kopierat, i text rutan under **Visa information om problemet.**

4. Granska och välj **skapa** för att skapa din SR.
