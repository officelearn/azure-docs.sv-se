---
title: Skapa en hanterad privat slutpunkt för att ansluta till datakällresultaten.
description: Den här artikeln lär dig hur du skapar en hanterad privat slutpunkt till dina datakällor från en Azure Synapse-arbetsyta.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428906"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Skapa en hanterad privat slutpunkt till datakällan (förhandsgranskning)

Den här artikeln lär dig hur du skapar en hanterad privat slutpunkt till din datakälla i Azure. Se [Hanterade privata slutpunkter](./synapse-workspace-managed-private-endpoints.md) om du vill veta mer.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Steg 1: Öppna din Azure Synapse-arbetsyta i Azure-portalen

Du kan skapa en hanterad privat slutpunkt till din datakälla från Azure Synapse Studio. Välj fliken **Översikt** i Azure-portalen och välj **Starta Synapse Studio**.
![Starta Azure Synapse Studio](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Steg 2: Navigera till fliken Hanterade virtuella nätverk i Synapse Studio

I Azure Synapse Studio väljer du fliken **Hantera** från den vänstra navigeringen. Välj **Hanterade virtuella nätverk** och välj sedan + **Nytt**.
![Skapa en ny hanterad privat slutpunkt](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Steg 3: Välj datakälltyp

Välj datakälltyp. I det här fallet är måldatakällan ett ADLS gen2-konto. Välj **Fortsätt**.
![Välj en måldatakälltyp](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Steg 4: Ange information om datakällan

I nästa fönster anger du information om datakällan. I det här exemplet skapar vi en hanterad privat slutpunkt till ett ADLS gen2-konto. Ange ett **namn** för den hanterade privata slutpunkten. Ange en **Azure-prenumeration** och ett **lagringskontonamn**. Välj **Skapa**.
![Ange information om måldatakälla](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Steg 5: Kontrollera att den hanterade privata slutpunkten har skapats

När du har skickat begäran visas dess status. Kontrollera att den hanterade privata slutpunkten har skapats genom *att*kontrollera att den hanterade privata slutpunkten har skapats. Du kan behöva vänta 1 minut och välja **Uppdatera för** att uppdatera etableringstillståndet. Du kan se att den hanterade privata slutpunkten till ADLS gen2-kontot har skapats.

Du kan också se att *godkännandetillståndet* *väntar*. Ägaren till målresursen kan godkänna eller neka den privata slutpunktsanslutningsbegäran. Om ägaren godkänner den privata slutpunktsanslutningsbegäran upprättas en privat länk. Om nekad, då en privat länk inte upprättas.
![Status för begäran om att skapa för slutpunkt hanterad privat slutpunkt](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om [Hanterade privata slutpunkter](./synapse-workspace-managed-private-endpoints.md)