---
title: Skapa Power BI Embedded kapacitet i Azure portal | Microsoft Docs
description: Den här artikeln beskrivs hur du skapar en Power BI Embedded kapacitet i Microsoft Azure.
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
ms.openlocfilehash: 2fadfde13aee3aaf965c6ba30188544a8a075b9d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>Skapa Power BI Embedded kapacitet i Azure-portalen

Den här artikeln beskrivs hur du skapar en Power BI Embedded kapacitet i Microsoft Azure. Power BI Embedded förenklar Power BI-funktioner genom att hjälpa dig att snabbt lägga till snygg visuell information, rapporter och instrumentpaneler i dina appar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

> [!VIDEO https://www.youtube.com/embed/aXrvFfg_iSk]

## <a name="before-you-begin"></a>Innan du börjar

Följande krävs för att slutföra den här snabbstarten:

* **Azure-prenumeration:** finns [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/) att skapa ett konto.
* **Azure Active Directory:** din prenumeration måste vara kopplad till en Azure Active Directory (AAD)-klient. Och, ***måste du vara inloggad i Azure med ett konto i den klienten***. Microsoft-konton stöds inte. Mer information finns i [Autentisering och användarbehörigheter](../analysis-services/analysis-services-manage-users.md).
* **Power BI-klient:** minst ett konto i AAD-klienten måste ha registrerat dig för Power BI.
* **Resursgrupp:** använder en resursgrupp som du redan har eller [skapa en ny](../azure-resource-manager/resource-group-overview.md).

## <a name="create-a-capacity"></a>Skapa en kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **skapar du en resurs** > **Data + analys**.

3. I sökrutan söker du efter *Power BI Embedded*.

4. I Power BI Embedded, Välj **skapa**.

5. Fyll i informationen som krävs och välj sedan **skapa**.

    ![Fält att fylla i att skapa ny kapacitet](media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |Inställning |Beskrivning |
    |---------|---------|
    |**Resursnamn**|Ett namn som identifierar kapaciteten. Resursnamnet visas i Power BI-administrationsportalen utöver Azure portal.|
    |**Prenumeration**|Den prenumeration som du vill skapa kapacitet mot.|
    |**Resursgrupp**|Resursgruppen som innehåller den här nya kapacitet. Välj från en befintlig resursgrupp eller skapa en ny. Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).|
    |**Power BI-administratör om kapacitet**|Power BI-kapacitet administratörer kan visa kapaciteten på administrationsportalen för Power BI och ge tilldelningsbehörighet till andra användare. Som standard är administratören kapacitet ditt konto. Administratören kapacitet måste vara inom din Power BI-klient.|
    |**Plats**|Den plats där Power BI är värd för din klient. Den här inställningen löses automatiskt, inte går att välja en annan plats.|
    |**prisnivå**|Välj SKU: N (v kärnor antal och minne storlek) som uppfyller dina behov.  Mer information finns i [priser för Power BI Embedded](https://azure.microsoft.com/pricing/details/power-bi-embedded/)|

6. Välj **Skapa**.

Skapa tar vanligtvis under en minut; ofta bara några sekunder. Om du har valt **fäst på instrumentpanelen**, gå till instrumentpanelen för att se din nya kapacitet. Eller navigera till **alla tjänster** > **Power BI Embedded** att se om din kapacitet är klar.

![Azure portal instrumentpanel med Power BI Embedded kapacitet](media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>Nästa steg

Om du vill använda din nya Power BI Embedded kapacitet, bläddra till Power BI-administrationsportalen för att tilldela arbetsytor. Mer information finns i [Manage capacities within Power BI Premium and Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/) (Hantera kapacitet i Power BI Premium och Power BI Embedded).

Om du inte behöver använda den här kapaciteten Pausa den stoppades fakturering. Mer information finns i [paus och starta din Power BI Embedded kapacitet i Azure portal](pause-start.md).

Om du vill börja bädda in Power BI-innehåll i ditt program, se [så att bädda in din Power BI-instrumentpaneler, rapporter och paneler](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Fler frågor? [Försök be Power BI-communityn](http://community.powerbi.com/)