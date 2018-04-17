---
title: Pausa och starta din Power BI Embedded kapacitet i Azure portal | Microsoft Docs
description: Den här artikeln beskrivs hur du pausar och starta en Power BI Embedded kapacitet i Microsoft Azure.
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
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: f96c62ea1d4fea6a724cd50cce9b54c02ddebdf5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="pause-and-start-your-power-bi-embedded-capacity-in-the-azure-portal"></a>Pausa och starta din Power BI Embedded kapacitet i Azure-portalen

Den här artikeln beskrivs hur du pausar och starta en Power BI Embedded kapacitet i Microsoft Azure. Detta förutsätter att du har skapat en Power BI Embedded kapacitet. Om du inte har Se [skapa Power BI Embedded kapacitet i Azure portal](create-capacity.md) att komma igång.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="pause-your-capacity"></a>Pausa din kapacitet

Pausa din kapacitet förhindrar du att debiteras. Pausa din kapacitet är bra om du inte behöver använda kapacitet för en viss tidsperiod. Använd följande steg för att pausa din kapacitet.

> [!NOTE]
> Pausa en kapacitet kan förhindra innehåll från att vara tillgängliga i Power BI. Se till att ta bort tilldelningen arbetsytor från din kapacitet innan paus för att förhindra avbrott.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **alla tjänster** > **Power BI Embedded** att se din kapacitet.

    ![Alla tjänster i Azure-portalen](media/pause-start/azure-portal-more-services.png)

3. Välj den kapacitet som du vill pausa.

    ![Power BI Embedded kapacitet listan i Azure-portalen](media/pause-start/azure-portal-capacity-list.png)

4. Välj **paus** inom kapacitet information.

    ![Pausa din kapacitet](media/pause-start/azure-portal-pause-capacity.png)

5. Välj **Ja**, som bekräftar att du vill pausa kapaciteten.

    ![Bekräfta paus](media/pause-start/azure-portal-confirm-pause.png)

## <a name="start-your-capacity"></a>Starta din kapacitet

Återuppta användning genom att starta din kapacitet. Starta din kapacitet också återupptar fakturering.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **alla tjänster** > **Power BI Embedded** att se din kapacitet.

    ![Alla tjänster i Azure-portalen](media/pause-start/azure-portal-more-services.png)

3. Välj den kapacitet som du vill starta.

    ![Power BI Embedded kapacitet listan i Azure-portalen](media/pause-start/azure-portal-capacity-list.png)

4. Välj **starta** inom kapacitet information.

    ![Starta din kapacitet](media/pause-start/azure-portal-start-capacity.png)

5. Välj **Ja**, som bekräftar att du vill starta kapaciteten.

    ![Bekräfta start](media/pause-start/azure-portal-confirm-start.png)

Om allt innehåll som har tilldelats den här kapaciteten, är den tillgänglig startas en gång.

## <a name="next-steps"></a>Nästa steg

Om du vill skala din kapacitet upp eller ned finns [skala din Power BI Embedded kapacitet](scale-capacity.md).

Om du vill börja bädda in Power BI-innehåll i ditt program, se [så att bädda in din Power BI-instrumentpaneler, rapporter och paneler](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Fler frågor? [Försök be Power BI-communityn](http://community.powerbi.com/)