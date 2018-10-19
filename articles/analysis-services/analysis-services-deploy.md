---
title: Distribuera till Azure Analysis Services med SSDT | Microsoft Docs
description: Lär dig hur du distribuerar en tabellmodell till en Azure Analysis Services-server med SSDT.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5f56ee65cd2ec5dfb39929f6985c26ebc339953e
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429056"
---
# <a name="deploy-a-model-from-ssdt"></a>Distribuera en modell från SSDT
När du har skapat en server i din Azure-prenumeration är du redo att distribuera en tabellmodelldatabas till den. Du kan använda SQL Server Data Tools (SSDT) för att skapa och distribuera ett tabellmodellprojekt som du arbetar med. 

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att komma igång:

* **Analysis Services-server** i Azure. Läs mer i [Skapa en Azure Analysis Services-server](analysis-services-create-server.md).
* **Tabellmodellprojekt** i SSDT eller en befintlig tabellmodell på kompatibilitetsnivå 1 200 eller högre. Har du aldrig skapat någon? Testa [Självstudier för Adventure Works Internetförsäljning – tabellmodell ](https://msdn.microsoft.com/library/hh231691.aspx).
* **Lokala gateway** – Om en eller flera datakällor finns lokalt i din organisations nätverk måste du installera en [lokal datagateway](analysis-services-gateway.md). Gatewayen är nödvändig för att din server i molnet ska kunna ansluta till dina lokala datakällor för att bearbeta och uppdatera data i modellen.

> [!TIP]
> Innan du distribuerar måste du kontrollera att du kan bearbeta data i dina tabeller. Klicka på **Modell** > **Bearbeta** > **Bearbeta alla** i SSDT. Om bearbetningen misslyckas kan inte du distribuera.
> 
> 

## <a name="get-the-server-name"></a>Hämta namnet på

Välj **Azure Portal** > server > **Översikt** > **Servernamn** och kopiera servernamnet.
   
![Hämta servernamnet i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>Distribuera från SSDT

1. Välj SSDT > **Solution Explorer**, högerklicka på projektet > **Egenskaper**. Klistra sedan in servernamnet i **Distribution** > **Server**.   
   
    ![Klistra in servernamnet i egenskapen för distributionsservern](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. I **Solution Explorer** högerklickar du på **Egenskaper** och klickar sedan på **Distribuera**. Du kan uppmanas att logga in i Azure.
   
    ![Distribuera till en server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Distributionens status visas både i fönstret Utmatning och i Distribuera.
   
    ![Status för distribution](./media/analysis-services-deploy/aas-deploy-status.png)

Det var allt!


## <a name="troubleshooting"></a>Felsökning
Om distributionen misslyckas när du distribuerar metadata beror det förmodligen på att SSDT inte kunde ansluta till servern. Kontrollera att du kan ansluta till servern med hjälp av SSMS. Kontrollera egenskapen Distributionsserver för projektet är korrekt.

Om distributionen misslyckas för en tabell beror det förmodligen på att servern inte kunde ansluta till en datakälla. Om datakällan finns lokalt i din organisations nätverk måste du installera en [lokal datagateway](analysis-services-gateway.md).

## <a name="next-steps"></a>Nästa steg
Nu när du har distribuerat en tabellmodell till servern är du redo att ansluta till den. Du kan [ansluta till den med SSMS](analysis-services-manage.md) om du vill hantera den. Och du kan [ansluta till den med ett klientverktyg](analysis-services-connect.md), till exempel Power BI, Power BI Desktop eller Excel, och börja skapa rapporter.

