---
title: Transformera XML mellan format – Azure Logic Apps | Microsoft Docs
description: Skapa transformeringar eller som konverterar XML mellan format i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.date: 07/08/2016
ms.openlocfilehash: 4ebd96613378bbd907beb5109343a2427b1300b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60427288"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Skapa kartor som omvandlar XML mellan format i Azure Logic Apps med Enterprise-Integrationspaket

Enterprise integration transformeringen connector konverterar data från ett format till ett annat format. Du kan till exempel ha ett inkommande meddelande som innehåller det aktuella datumet i formatet YearMonthDay. Du kan använda en transformering för att formatera om datumet som ska vara i formatet MonthDayYear.

## <a name="what-does-a-transform-do"></a>Vad är en transformering?
En transformering som är även känd som en karta, består av en käll-XML-schema (indata) och ett mål XML-schema (utdata). Du kan använda olika inbyggda funktioner för att ändra eller kontrollera data, inklusive strängändringar, villkorlig tilldelningar, aritmetiska uttryck, datum tid formaterare och även slingor konstruktioner.

## <a name="how-to-create-a-transform"></a>Så här skapar du en transformering?
Du kan skapa en transformering/karta med hjälp av Visual Studio [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas). När du är klar skapar och testar transformering överföra du transformering i ditt integrationskonto. 

## <a name="how-to-use-a-transform"></a>Hur du använder en transformering
När du har överfört transformering/kartan i ditt integrationskonto kan du använda det för att skapa en logikapp. Logikappen körs dina transformeringar när logikappen utlöses (och det finns indata innehåll som måste omvandlas).

**Här följer stegen för att använda en transformering**:

### <a name="prerequisites"></a>Nödvändiga komponenter

* Skapar ett integrationskonto och lägger till en karta  

Nu när du har åtgärdat förutsättningarna, är det dags att skapa din logikapp:  

1. Skapa en logikapp och [länkar den till ditt integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md "Lär dig hur du länkar ett integrationskonto till en logikapp") som innehåller mappningen.
2. Lägg till en **begära** utlösare i logikappen  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Lägg till den **transformera XML** åtgärden genom att först välja **Lägg till en åtgärd**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Ange ordet *transformera* i sökrutan för att filtrera åtgärderna som ska det som du vill använda  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Välj den **transformera XML** åtgärd   
6. Lägga till XML-filen **innehåll** som du omvandla. Du kan använda valfri XML-data som du får i HTTP-begäran som den **innehåll**. I det här exemplet väljer du brödtexten i HTTP-begäran som utlöste logikappen.

   > [!NOTE]
   > Kontrollera att innehållet för den **transformera XML** är XML. Om innehållet är inte i XML-fil eller base64-kodad, måste du ange ett uttryck som bearbetar innehållet. Du kan till exempel använda [functions](logic-apps-workflow-definition-language.md#functions), till exempel ```@base64ToBinary``` för avkodning av innehåll eller ```@xml``` för bearbetning av innehåll som XML.
 

7. Välj namnet på den **KARTAN** att du vill använda för att utföra omvandlingen. Kartan måste redan finnas i ditt integrationskonto. I ett tidigare steg gav du redan din Logic appåtkomst till ditt konto för integrering med kartan.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Spara ditt arbete  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Nu är du klar med att ställa in kartan. I en verklig tillämpning kanske du vill lagra transformerade data i en LOB-program, till exempel SalesForce. Du kan enkelt som en åtgärd för att skicka utdata för transformering till Salesforce. 

Nu kan du testa din transformeringen genom att göra en begäran till HTTP-slutpunkten.  


## <a name="features-and-use-cases"></a>Funktioner och användningsfall
* Transformeringen som skapats på en karta kan vara enkel, till exempel kopiera ett namn och adress från ett dokument till en annan. Du kan också skapa mer komplexa transformationer out-of the box kartan åtgärder.  
* Många kartan åtgärder eller funktioner finns tillgängliga, inklusive strängar, datum tidsfunktioner och så vidare.  
* Du kan göra en direkt datakopiering mellan scheman. I den mappning som ingår i SDK, är det lika enkelt som att rita en linje som sammanbinder elementen i datakällans schema med deras motsvarigheter i mål-schemat.  
* När du skapar en karta kan visa du en grafisk representation av kartan som visar alla relationer och länkar som du skapar.
* Använda funktionen Test kartan för att lägga till en XML-exempelmeddelande. Med ett enkelt klick kan du testa kartan du har skapat och se utdata som genereras.  
* Ladda upp befintlig maps  
* Har stöd för XML-format.

## <a name="advanced-features"></a>Avancerade funktioner

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referens för sammansättningen eller anpassad kod från maps 
Åtgärden för att transformera också stöder maps eller omvandlar med hänvisning till externa sammansättningen. Den här funktionen gör det möjligt för anrop till anpassade .NET-kod direkt från XSLT-kartor. Här följer kraven för att använda sammansättningen i maps.

* Kartan och sammansättningen refereras från kartan måste vara [överförs till integrationskontot](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Kartan och sammansättningen måste anges som ska laddas upp i en viss ordning. Du måste ladda upp sammansättningen innan du laddar upp kartan som refererar till sammansättningen.

* Kartan måste också ha dessa attribut och ett CDATA-avsnitt som innehåller anrop till sammansättningen koden:

    * **namn på** är den anpassa namnet.
    * **namnområdet** är namnområdet i sammansättningen som innehåller anpassad kod.

  Det här exemplet visar en karta som refererar till en sammansättning med namnet ”XslUtilitiesLib” och anropar den `circumreference` metoden från sammansättningen.

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
     <circles>
        <xsl:for-each select="circle">
            <circle>
                <xsl:copy-of select="node()"/>
                    <circumference>
                        <xsl:value-of select="user:circumference(radius)"/>
                    </circumference>
            </circle>
        </xsl:for-each>
     </circles>
    </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>Byte-ordningsmarkering
Som standard startar svaret från omvandlingen med Byte Order Mark (BOM). Du kan komma åt den här funktionen endast när du arbetar i kodvy redigeraren. Om du vill inaktivera den här funktionen, ange `disableByteOrderMark` för den `transformOptions` egenskapen:

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>Läs mer
* [Mer information om Enterprise-Integrationspaketet](../logic-apps/logic-apps-enterprise-integration-overview.md "Lär dig mer om Enterprise-Integrationspaket")  
* [Läs mer om maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Lär dig mer om maps för enterprise-integration")  

