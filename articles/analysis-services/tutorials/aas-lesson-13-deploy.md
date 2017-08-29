---
title: "Azure Analysis Services självstudiekurs 13: Distribuera | Microsoft Docs"
description: "Beskriver hur du distribuerar självstudieprojektet i Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/17/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 70dbf5786262f75199270aa8009e03b9b48b8559
ms.contentlocale: sv-se
ms.lasthandoff: 08/21/2017

---
# <a name="lesson-13-deploy"></a>Lektion 13: Distribuera

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Under den här lektionen konfigurerar du distributionsegenskaper, anger en Analysis Services-server i Azure att distribuera till och ett namn för modellen. Distribuera sedan modellen till den instansen. När modellen har distribuerats kan användarna ansluta till den med hjälp av ett rapporteringsklientprogram. Läs mer i [Distribuera till Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-deploy).  
  
Uppskattad tidsåtgång för den här lektionen: **5 minuter**  
  
## <a name="prerequisites"></a>Krav  
Det här avsnittet ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför uppgifterna under den här lektionen bör du ha slutfört föregående lektion: [Lektion 12: Analysera i Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).  

> [!IMPORTANT]  
> Du måste ha [administratörsbehörighet](../analysis-services-server-admins.md) på den fjärranslutna Analysis Services-servern för att distribuera till den.  

> [!IMPORTANT]  
> Om du har installerat exempeldatabasen AdventureWorksDW2014 på en lokal SQL Server och du distribuerar din modell till en Azure Analysis Services-server så krävs en [Lokal datagateway](../analysis-services-gateway.md).
  
## <a name="deploy-the-model"></a>Distribuera modellen  
  
#### <a name="to-configure-deployment-properties"></a>Så här konfigurerar du distributionsegenskaper  

  
1.  Högerklicka på projektet **AW Internet Sales** i **Solution Explorer** och sedan på **Egenskaper**.  
  
2.  I dialogrutan **AW Internet Sales Property Pages** (AW Internet Sales egenskapssidor) under **Distributionsserver** i egenskapen **Server** anger du namnet servern.  

    ![aas-lesson13-deploy-property](../tutorials/media/aas-lesson13-deploy-property.png)
  
3.  I egenskapen **Databas** anger du **Adventure Works Internet Sales**.  
  
4.  I egenskapen **Modellnamn** anger du **Adventure Works Internet Sales Model**.  
  
5.  Kontrollera dina val och klicka sedan på **OK**.  
  
#### <a name="to-deploy-the-adventure-works-internet-sales"></a>Så här distribuerar du Adventure Works Internet Sales
  
1.  Högerklicka på projektet **AW Internet Sales** i **Solution Explorer** > **Skapa**.  

2.  Högerklicka på projektet **AW Internet Sales** > **Distribuera**.

    När du distribuerar till Azure Analysis Services kan du uppmanas att ange ditt konto. Ange ditt organisationskonto och lösenord, till exempel nancy@adventureworks.com. Det här kontot måste finnas i Administratörer på servern.
  
    Dialogrutan Distribuera visas och visar distributionsstatus för metadata och varje tabell som ingår i modellen.  
    
    ![aas-lesson13-deploy-status](../tutorials/media/aas-lesson13-deploy-status.png)
  
3. När distributionen är klar kan du klicka på **Stäng**.  
  
## <a name="conclusion"></a>Slutsats  
Grattis! Du är färdig med att redigera och distribuera din första Analysis Services Tabular-modell. Den här självstudien har hjälpt dig att slutföra de vanligaste uppgifterna för att skapa en tabellmodell. Nu när Adventure Works Internet Sales-modellen har distribuerats kan du använda SQL Server Management Studio för att hantera modellen, skapa processkript och en säkerhetskopieringsplan. Användare kan nu även ansluta till modellen med ett rapportklientprogram, till exempel Microsoft Excel eller Power BI.  

![aas-lesson13-ssms](../tutorials/media/aas-lesson13-ssms.png)
  
  
  
## <a name="whats-next"></a>Nästa steg
[Anslut med Power BI Desktop](../analysis-services-connect-pbi.md)   
[Kompletterande lektion – Dynamisk säkerhet](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Kompletterande lektion – Detaljrader](../tutorials/aas-supplemental-lesson-detail-rows.md)   
[Kompletterande lektion – Ojämna hierarkier](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   

