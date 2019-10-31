---
title: Distribuera till Azure Analysis Services med hjälp av Visual Studio | Microsoft Docs
description: Lär dig hur du distribuerar en tabell modell till en Azure Analysis Services-server med hjälp av Visual Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 60681739854515078a521a4ff795e52aa7d74183
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146964"
---
# <a name="deploy-a-model-from-visual-studio"></a>Distribuera en modell från Visual Studio

När du har skapat en server i din Azure-prenumeration är du redo att distribuera en tabellmodelldatabas till den. Du kan använda Visual Studio med Analysis Services projekt för att bygga och distribuera ett tabell modell projekt som du arbetar med. 

## <a name="prerequisites"></a>Krav

Du behöver följande för att komma igång:

* **Analysis Services-server** i Azure. Läs mer i [Skapa en Azure Analysis Services-server](analysis-services-create-server.md).
* **Tabell modell projekt** i Visual Studio eller en befintlig tabell modell på kompatibilitetsnivån 1200 eller högre. Har du aldrig skapat någon? Testa [Självstudier för Adventure Works Internetförsäljning – tabellmodell ](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Lokala gateway** – Om en eller flera datakällor finns lokalt i din organisations nätverk måste du installera en [lokal datagateway](analysis-services-gateway.md). Gatewayen är nödvändig för att din server i molnet ska kunna ansluta till dina lokala datakällor för att bearbeta och uppdatera data i modellen.

> [!TIP]
> Innan du distribuerar måste du kontrollera att du kan bearbeta data i dina tabeller. I Visual Studio klickar du på **modell** > **process** > **bearbeta alla**. Om bearbetningen misslyckas kan inte du distribuera.
> 
> 

## <a name="get-the-server-name"></a>Hämta Server namnet

Välj **Azure Portal** > server > **Översikt** > **Servernamn** och kopiera servernamnet.
   
![Hämta servernamnet i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Distribuera från Visual Studio

1. I Visual Studio > **Solution Explorer**högerklickar du på projektets > **Egenskaper**. Klistra sedan in servernamnet i **Distribution** > **Server**.   
   
    ![Klistra in servernamnet i egenskapen för distributionsservern](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. I **Solution Explorer** högerklickar du på **Egenskaper** och klickar sedan på **Distribuera**. Du kan uppmanas att logga in i Azure.
   
    ![Distribuera till en server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Distributionens status visas både i fönstret Utmatning och i Distribuera.
   
    ![Status för distribution](./media/analysis-services-deploy/aas-deploy-status.png)

Det var allt!


## <a name="troubleshooting"></a>Felsöka

Om distributionen Miss lyckas när du distribuerar metadata är det troligt att Visual Studio inte kunde ansluta till servern. Kontrollera att du kan ansluta till servern med hjälp av SSMS. Kontrollera egenskapen Distributionsserver för projektet är korrekt.

Om distributionen misslyckas för en tabell beror det förmodligen på att servern inte kunde ansluta till en datakälla. Om datakällan finns lokalt i din organisations nätverk måste du installera en [lokal datagateway](analysis-services-gateway.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat en tabellmodell till servern är du redo att ansluta till den. Du kan [ansluta till den med SQL Server Management Studio (SSMS)](analysis-services-manage.md) för att hantera den. Och du kan [ansluta till den med ett klientverktyg](analysis-services-connect.md), till exempel Power BI, Power BI Desktop eller Excel, och börja skapa rapporter.

