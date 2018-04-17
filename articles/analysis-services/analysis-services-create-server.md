---
title: Skapa en Analysis Services-server i Azure | Microsoft Docs
description: Lär dig hur du skapar en Analysis Services-server-instans i Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c910416524f149c785aae299d576ca5c521abc6d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="create-an-azure-analysis-services-server-in-azure-portal"></a>Skapa en Azure Analysis Services-server i Azure-portalen
Den här artikeln vägleder dig genom att skapa en resurs för Analysis Services-server i din Azure-prenumeration.

## <a name="before-you-begin"></a>Innan du börjar
Följande krävs för att slutföra den här snabbstarten:

* **Azure-prenumeration**: Gå till [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) för att skapa ett konto.
* **Azure Active Directory**: din prenumeration måste vara kopplad till en Azure Active Directory-klient. Och du måste vara inloggad i Azure med ett konto i den Azure Active Directory. Microsoft-konton stöds inte. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).
* **Resursgruppen**: använda en resursgrupp som du redan har eller [skapa en ny](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> Att skapa en server kan resultera i en ny fakturerbar tjänst. Läs mer i [Priser för Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="to-create-a-server-in-the-azure-portal"></a>Så här skapar du en server i Azure-portalen
1. Logga in på [Azure Portal](https://portal.azure.com).  
2. Klicka på **+ ny** > **Data + analys** > **Analysis Services**.
3. I den **Analysis Services** bladet, Fyll i de obligatoriska fälten och tryck sedan på **skapa**.
   
    ![Skapa server](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Servernamnet**: Ange ett unikt namn som refererar till servern.
   * **Prenumerationen**: Välj den prenumeration som den här servern växlar till.
   * **Resursgruppen**: dessa behållare är avsedda att hjälpa dig att hantera en samling Azure-resurser. Läs mer i [resursgrupper](../azure-resource-manager/resource-group-overview.md).
   * **Plats**: servern är värd för platsen för den här Azure-datacenter. Välj en plats närmast största användarbasen.
   * **Prisnivån**: Välj en prisnivå. Tabular-modeller upp till 400 GB stöds. Läs mer i [priser för Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
4. Klicka på **Skapa**.

Skapa tar vanligtvis under en minut; ofta bara några sekunder. Om du har valt **lägga till till portalen**, gå till portalen för att se den nya servern. Eller navigera till **alla tjänster** > **Analysis Services** att se om din server är klar.

 ![Instrumentpanel](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Nästa steg
När du har skapat din server, kan du [distribuera en modell](analysis-services-deploy.md) till den med hjälp av SSDT eller med SSMS.

Om en modell som du distribuerar till din server ansluter till lokala datakällor, måste du installera en [lokala datagateway](analysis-services-gateway.md) på en dator i nätverket.

