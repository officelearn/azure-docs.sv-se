---
title: Konvertera XML-data med transformeringar - Azure Logic Apps | Microsoft Docs
description: "Skapa transformeringar eller mapps att konvertera XML-data mellan formaten i logikappar med hjälp av Enterprise Integration-SDK"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: fb6027769377b3527b11f7831dab3bb8d7061c84
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="enterprise-integration-with-xml-transforms"></a>Enterprise integration med XML-transformeringar
## <a name="overview"></a>Översikt
Enterprise integration transformeringen kopplingen konverterar data från ett format till ett annat format. Du kan till exempel ha ett inkommande meddelande som innehåller det aktuella datumet i formatet YearMonthDay. Du kan använda en transformering formatera om datumet som ska vara i formatet MonthDayYear.

## <a name="what-does-a-transform-do"></a>Vad är en omvandling?
En transformering som kallas även en karta, består av källa XML-schema (indata) och ett mål XML-schema (utdata). Du kan använda olika inbyggda funktioner för att ändra eller kontrollera data, inklusive strängändringar, villkorlig tilldelningar, aritmetiska uttryck, datum tid formaterare och även slingor konstruktioner.

## <a name="how-to-create-a-transform"></a>Så här skapar du en omvandling?
Du kan skapa en transformering/karta med hjälp av Visual Studio [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas). När du är klar att skapa och testa transformeringen överför transformeringen till ditt konto för integrering. 

## <a name="how-to-use-a-transform"></a>Hur du använder en transformering
Du kan använda det för att skapa en logikapp när du har överfört transformering/mappa till ditt konto för integrering. Logikappen körs din transformationer när logikappen utlöses (och det finns indata innehåll som måste omvandlas).

**Här följer stegen för att använda en transformering**:

### <a name="prerequisites"></a>Krav

* Skapa ett konto för integrering och Lägg till en karta  

Nu när du har åtgärdat förutsättningarna, är det dags att skapa din logikapp:  

1. Skapa en logikapp och [länka till ditt konto integration](../logic-apps/logic-apps-enterprise-integration-accounts.md "Lär dig hur du länkar ett integration konto till en logikapp") som innehåller mappningen.
2. Lägg till en **begära** utlösaren till din logikapp  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Lägg till den **transformera XML** åtgärden genom att först välja **lägga till en åtgärd**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Ange ordet *transformera* i sökrutan för att filtrera vilka åtgärder som det som du vill använda  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Välj den **transformera XML** åtgärd   
6. Lägga till XML **innehåll** som du omvandla. Du kan använda en XML-data som visas i HTTP-begäran som den **innehåll**. I det här exemplet väljer du innehållet i HTTP-begäran som utlöste logikappen.
7. Välj namnet på den **KARTAN** som du vill använda för att utföra omvandlingen. Kartan måste redan finnas i ditt konto för integrering. I ett tidigare steg gav du redan din logik appåtkomst till ditt konto för integrering med kartan.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Spara ditt arbete  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Nu är du klar med att ställa in kartan. I ett verkligt program kanske du vill lagra omvandlade data i en LOB-program, till exempel SalesForce. Du kan enkelt som en åtgärd för att skicka utdata från transformeringen till Salesforce. 

Nu kan du testa din transformeringen genom att göra en begäran till HTTP-slutpunkten.  

## <a name="features-and-use-cases"></a>Funktioner och användningsområden
* Transformering som skapats i en karta kan vara enkel, till exempel kopiera ett namn och adress från ett dokument till en annan. Eller så kan du skapa mer komplexa omformningar med hjälp av åtgärderna out box-karta.  
* Flera kartan operations eller funktioner är tillgängliga, inklusive strängar, datum tidsfunktioner och så vidare.  
* Du kan göra en kopia direkt mellan scheman. I Mapper ingår i SDK, är så enkelt som att rita en linje som sammanbinder elementen i datakällans schema med deras motsvarigheter i mål-schemat.  
* När du skapar en karta, kan du visa en grafisk representation av kartan som visar alla relationer och länkar som du skapar.
* Med funktionen testa kartan att lägga till en XML-exempelmeddelande. Du kan testa kartan som du skapade och se utdata skapas med en enkel klickning.  
* Överför befintliga mappningar  
* Innehåller stöd för XML-format.

## <a name="learn-more"></a>Läs mer
* [Mer information om Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  
* [Mer information om maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Lär dig mer om enterprise integration maps")  

