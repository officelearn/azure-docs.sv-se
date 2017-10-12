---
title: "Azure Analysis Services självstudiekurs 12: Analysera i Excel | Microsoft Docs"
description: "Beskriver hur du använder funktionen Analysera i Excel i Azure Analysis Services-självstudieprojektet."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/20/2017
ms.author: owend
ms.openlocfilehash: e257862a88d39b96360703117f544c43e82b0e3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-12-analyze-in-excel"></a>Lektion 12: Analysera i Excel

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Under den här lektionen använder du funktionen Analysera för att öppna Microsoft Excel, automatiskt skapa en anslutning till modellens arbetsyta och automatiskt lägga till en pivottabell i kalkylbladet. Funktionen Analysera i Excel är avsedd att tillhandahålla ett snabbt och enkelt sätt att testa effektiviteten av modelldesignen innan du distribuerar modellen. Du utför inte någon dataanalys under den här lektionen. Syftet med den här lektionen är du, modellskaparen, ska kunna bekanta dig med de verktyg du kan använda för att testa modelldesignen.   
  
För att slutföra den här lektionen måste Excel vara installerat på samma dator som SSDT.
  
Uppskattad tidsåtgång för den här lektionen: **Fem minuter**  
  
## <a name="prerequisites"></a>Krav  
Det här avsnittet ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför uppgifterna under den här lektionen bör du ha slutfört föregående lektion: [Lektion 11: Skapa roller](../tutorials/aas-lesson-11-create-roles.md).  
  
## <a name="browse-using-the-default-and-internet-sales-perspectives"></a>Bläddra med hjälp av standard- och Internet-försäljningsperspektiven  
I de första uppgifterna bläddrar du till modellen genom att använda både standardperspektivet, som omfattar alla modellobjekt, och genom att använda Internet-försäljningsperspektivet. Internet-försäljningsperspektivet exkluderar kundtabellobjekt.  
  
#### <a name="to-browse-by-using-the-default-perspective"></a>Så här bläddrar du med hjälp av standardperspektivet  
  
1.  Klicka på menyn **Modell** > **Analysera i Excel**.  
  
2.  I dialogrutan **Analysera i Excel** klickar du på **OK**.  
  
    Excel öppnas med en ny arbetsbok. En anslutning till en datakälla skapas med det aktuella användarkontot och standardperspektivet används för att definiera fält som visas. En pivottabell läggs automatiskt till i kalkylbladet.  
  
3.  I **fältlistan PivotTable** i Excel ser du hur måttgrupperna **DimDate** och **FactInternetSales** visas. Tabellerna **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** och **FactInternetSales** samt deras respektive kolumner visas också.  
  
4.  Stäng Excel utan att spara arbetsboken.  
  
#### <a name="to-browse-by-using-the-internet-sales-perspective"></a>Så här bläddrar du med hjälp av Internet-försäljningsperspektivet  
  
1.  Klicka på menyn **Modell** och klicka sedan på **Analysera i Excel**.  
  
2.  I dialogrutan **Analysera i Excel** behåller du **Aktuell Windows-användare** markerat, sedan väljer du **Internetförsäljning** i den nedrullningsbara listrutan **Perspektiv** och klickar på **OK**. 
    
    ![aas-lesson12-perspective](../tutorials/media/aas-lesson12-perspective.png)
    
3.  Observera att tabellen DimCustomer är exkluderad från fältlistan i **Pivottabellfält** i Excel.  
    
    ![aas-lesson12-fields](../tutorials/media/aas-lesson12-fields.png)
    
4.  Stäng Excel utan att spara arbetsboken.  
  
## <a name="browse-by-using-roles"></a>Bläddra med hjälp av roller  
Roller är en viktig del av alla tabellmodeller. Utan minst en roll där användarna läggs till som medlemmar kan användarna inte komma åt och analysera data med hjälp av modellen. Funktionen Analysera i Excel ger dig ett sätt att testa de roller som du har definierat.  
  
#### <a name="to-browse-by-using-the-sales-manager-user-role"></a>Bläddra med hjälp av användarrollen Säljchef  
  
1.  Klicka på menyn **Modell** i SSDT och klicka sedan på **Analysera i Excel**.  
  
2.  I **Ange det användarnamn eller den roll som ska användas för att ansluta till modellen** väljer du **Roll**, i den nedrullningsbara listrutan väljer du **Säljchef** och klickar sedan på **OK**.  
  
    Excel öppnas med en ny arbetsbok. En pivottabell skapas automatiskt. Fältlistan för pivottabellen innehåller alla datafält som är tillgängliga i den nya modellen.  
      
3.  Stäng Excel utan att spara arbetsboken.  
  
## <a name="whats-next"></a>Nästa steg
Gå till nästa lektion: [Lektion 13: Distribuera](../tutorials/aas-lesson-13-deploy.md).

  
  
  
