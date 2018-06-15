---
title: Skala din Power BI Embedded kapacitet | Microsoft Docs
description: Den här artikeln beskrivs hur du skalar en Power BI Embedded kapacitet i Microsoft Azure.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/19/2018
ms.author: maghan
ms.openlocfilehash: a089d34b3f49188f3037ad57aab4d7bcc8c660f2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408293"
---
# <a name="scale-your-power-bi-embedded-capacity"></a>Skala din Power BI Embedded kapacitet

Den här artikeln beskrivs hur du skalar en Power BI Embedded kapacitet i Microsoft Azure. Skalning kan du öka eller minska storleken på din kapacitet.

Detta förutsätter att du har skapat en Power BI Embedded kapacitet. Om du inte har Se [skapa Power BI Embedded kapacitet i Azure portal](create-capacity.md) att komma igång.

> [!NOTE]
> En skalning åtgärd kan ta ungefär en minut. Kapaciteten blir inte tillgängliga under den här tiden. Inbäddat innehåll kanske inte kan läsas in.

## <a name="scale-a-capacity"></a>Skala en kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **alla tjänster** > **Power BI Embedded** att se din kapacitet.

    ![Alla tjänster i Azure-portalen](media/scale-capacity/azure-portal-more-services.png)

3. Välj den kapacitet som du vill skala.

    ![Power BI Embedded kapacitet listan i Azure-portalen](media/scale-capacity/azure-portal-capacity-list.png)

4. Välj **prisnivå** under **skala** inom din kapacitet.

    ![Priser för nivån alternativ under skala](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    Din aktuella prisnivån beskrivs i blått.

    ![Aktuella prisnivån i blått](media/scale-capacity/azure-portal-current-tier.png)

5. Välj ny nivå för att flytta till om du vill skala upp eller ned. Om du markerar en ny nivå visas en streckad blå kontur runt markeringen. Välj **Välj** att skala till det nya lagret.

    ![Välj ny nivå](media/scale-capacity/azure-portal-select-new-tier.png)

    Skala din kapacitet kan ta en minut att slutföra.

6. Bekräfta din nivå genom att visa översiktsfliken. Den aktuella prisnivån visas.

    ![Bekräfta aktuella nivån](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>Nästa steg

Om du vill pausa eller starta din kapacitet, se [pausa och starta din Power BI Embedded kapacitet i Azure portal](pause-start.md).

Om du vill börja bädda in Power BI-innehåll i ditt program, se [så att bädda in din Power BI-instrumentpaneler, rapporter och paneler](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Fler frågor? [Försök be Power BI-communityn](http://community.powerbi.com/)
