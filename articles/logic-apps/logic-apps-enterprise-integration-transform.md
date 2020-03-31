---
title: Omvandla XML mellan format
description: Skapa transformeringar eller kartor som konverterar XML mellan format i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 500769a39ba7658b35c1abb80101f6234170c941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792389"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Skapa kartor som omvandlar XML mellan format i Azure Logic Apps med Enterprise Integration Pack

Enterprise integration Transform connector konverterar data från ett format till ett annat format. Du kan till exempel ha ett inkommande meddelande som innehåller det aktuella datumet i formatet YearMonthDay. Du kan använda en transformering för att formatera om datumet för att vara i Formatet MonthDayYear.

## <a name="what-does-a-transform-do"></a>Vad gör en transformering?
En transformering, som också kallas en karta, består av ett SOURCE XML-schema (indata) och ett Target XML-schema (utdata). Du kan använda olika inbyggda funktioner för att manipulera eller styra data, inklusive strängmanipuleringar, villkorsstyrda tilldelningar, aritmetiska uttryck, datumtidsformaterare och till och med looping-konstruktioner.

## <a name="how-to-create-a-transform"></a>Hur man skapar en transformering?
Du kan skapa en transformering/karta med hjälp av Visual Studio [Enterprise Integration SDK](https://aka.ms/vsmapsandschemas). När du är klar med att skapa och testa transformeringen laddar du upp transformeringen till ditt integrationskonto. 

## <a name="how-to-use-a-transform"></a>Så här använder du en transformering
När du har laddat upp transformeringen/kartan till ditt integrationskonto kan du använda den för att skapa en Logic-app. Logic-appen kör dina omvandlingar när Logic-appen utlöses (och det finns indatainnehåll som behöver omvandlas).

**Här är stegen för att använda en transformering:**

### <a name="prerequisites"></a>Krav

* Skapa ett integrationskonto och lägga till en karta i det  

Nu när du har tagit hand om förutsättningarna är det dags att skapa logikappen:  

1. Skapa en Logic-app och [länka den till ditt integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md "Lär dig att länka ett integrationskonto till en Logic-app") som innehåller kartan.
2. Lägga till en **begärandeutlösare** i logikappen  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Lägga till åtgärden **Omforma XML** genom att först välja Lägg till **en åtgärd**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Ange ordet *transformering* i sökrutan för att filtrera alla åtgärder till den som du vill använda  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Markera åtgärden **Omforma XML**   
6. Lägg till **xml-innehållet** som du omvandlar. Du kan använda alla XML-data som du får i HTTP-begäran som **INNEHÅLL**. I det här exemplet väljer du brödtexten för HTTP-begäran som utlöste logic-appen.

   > [!NOTE]
   > Kontrollera att innehållet för **TransformERA XML** är XML. Om innehållet inte finns i XML eller är base64-kodat måste du ange ett uttryck som bearbetar innehållet. Du kan till [functions](logic-apps-workflow-definition-language.md#functions)exempel använda ```@base64ToBinary``` funktioner , till ```@xml``` exempel för avkodning av innehåll eller för bearbetning av innehållet som XML.
 

7. Välj namnet på **den MAP** som du vill använda för att utföra omvandlingen. Kartan måste redan finnas i ditt integrationskonto. I ett tidigare steg har du redan gett logikappen åtkomst till ditt integrationskonto som innehåller din karta.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Spara ditt arbete  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Nu är du klar med att ställa in kartan. I ett verkligt program kanske du vill lagra transformerade data i ett LOB-program som SalesForce. Du kan enkelt som en åtgärd skicka utdata från transformeringen till Salesforce. 

Du kan nu testa din transformering genom att göra en begäran till HTTP-slutpunkten.  


## <a name="features-and-use-cases"></a>Funktioner och användningsfall
* Omvandlingen som skapas i en karta kan vara enkel, till exempel kopiera ett namn och en adress från ett dokument till ett annat. Du kan också skapa mer komplexa omvandlingar med hjälp av de färdiga kartåtgärderna.  
* Flera kartåtgärder eller funktioner är lätt tillgängliga, inklusive strängar, datumtidsfunktioner och så vidare.  
* Du kan göra en direkt datakopia mellan schemana. I Mapper som ingår i SDK är detta lika enkelt som att rita en linje som kopplar elementen i källschemat till sina motsvarigheter i målschemat.  
* När du skapar en karta visar du en grafisk representation av kartan, som visar alla relationer och länkar som du skapar.
* Använd funktionen Testa mappning för att lägga till ett exempel på XML-meddelande. Med ett enkelt klick kan du testa kartan du skapade och se den genererade utdata.  
* Ladda upp befintliga kartor  
* Inkluderar stöd för XML-formatet.

## <a name="advanced-features"></a>Avancerade funktioner

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referensmontering eller anpassad kod från kartor 
Transformeringsåtgärden stöder också kartor eller transformeringar med hänvisning till extern sammansättning. Med den här funktionen kan anrop till anpassad .NET-kod direkt från XSLT-kartor. Här är förutsättningarna för att använda montering i kartor.

* Kartan och sammansättningen som refereras från kartan måste [laddas upp till integrationskontot](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Karta och montering måste laddas upp i en viss ordning. Du måste ladda upp sammansättningen innan du laddar upp kartan som refererar till sammansättningen.

* Kartan måste också ha dessa attribut och ett CDATA-avsnitt som innehåller anropet till monteringskoden:

    * **namnet** är det anpassade sammansättningsnamnet.
    * **namnområdet** är namnområdet i sammansättningen som innehåller den anpassade koden.

  I det här exemplet visas en karta som refererar till en `circumreference` sammansättning med namnet "XslUtilitiesLib" och anropar metoden från sammansättningen.

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


### <a name="byte-order-mark"></a>Byte order märke
Som standard börjar svaret från omvandlingen med strukturlistan (Byte Order Mark). Du kan bara komma åt den här funktionen när du arbetar i code view-redigeraren. Om du vill inaktivera `disableByteOrderMark` den `transformOptions` här funktionen anger du för egenskapen:

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
* [Läs mer om Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise Integration Pack")  
* [Läs mer om kartor](../logic-apps/logic-apps-enterprise-integration-maps.md "Läs mer om företagsintegrationskartor")  

