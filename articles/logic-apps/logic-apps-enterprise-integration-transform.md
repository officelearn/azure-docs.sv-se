---
title: Transformera XML mellan format
description: Skapa transformeringar eller Maps som konverterar XML mellan format i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 500769a39ba7658b35c1abb80101f6234170c941
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792389"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Skapa Maps som transformerar XML mellan format i Azure Logic Apps med Enterprise-integrationspaket

Transformerings kopplingen för företags integrering konverterar data från ett format till ett annat. Du kan till exempel ha ett inkommande meddelande som innehåller det aktuella datumet i YearMonthDay-formatet. Du kan använda en transformering för att formatera om datumet i MonthDayYear-format.

## <a name="what-does-a-transform-do"></a>Vad gör en transformering?
En transformering, som även kallas en karta, består av ett käll-XML-schema (indata) och ett XML-schema för målet (utdata). Du kan använda olika inbyggda funktioner för att manipulera eller kontrol lera data, inklusive sträng manipulationer, villkors tilldelningar, aritmetiska uttryck, datum tids formatering och till och med loopa konstruktioner.

## <a name="how-to-create-a-transform"></a>Hur skapar jag en transformering?
Du kan skapa en transformering/mappning med hjälp av Visual Studio [Enterprise-integration SDK](https://aka.ms/vsmapsandschemas). När du har skapat och testat transformeringen överför du transformeringen till ditt integrations konto. 

## <a name="how-to-use-a-transform"></a>Använda en transformering
När du har överfört transformeringen/kartan till ditt integrations konto kan du använda den för att skapa en Logic app. Logic-appen kör dina omvandlingar när Logic-appen utlöses (och det finns inmatat innehåll som måste omvandlas).

**Här följer stegen för att använda en transformering**:

### <a name="prerequisites"></a>Krav

* Skapa ett integrations konto och Lägg till en karta  

Nu när du har vidtagit kraven är det dags att skapa din Logic app:  

1. Skapa en Logic-app och [länka den till ditt integrations konto](../logic-apps/logic-apps-enterprise-integration-accounts.md "Lär dig att länka ett integrations konto till en Logic app") som innehåller kartan.
2. Lägg till en **begär** ande utlösare till din Logic app  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Lägg till **transformerings-XML-** åtgärden genom att först välja **Lägg till en åtgärd**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Ange ordet *Transform* i sökrutan för att filtrera alla åtgärder till den som du vill använda  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Välj åtgärden **TRANSFORMERA XML**   
6. Lägg till det XML- **innehåll** som du transformerar. Du kan använda alla XML-data som du får i HTTP-begäran som **innehåll**. I det här exemplet väljer du innehållet i HTTP-begäran som utlöste Logic-appen.

   > [!NOTE]
   > Se till att innehållet för XML- **transformeringen** är XML. Om innehållet inte är i XML-format eller Base64-kodat måste du ange ett uttryck som bearbetar innehållet. Du kan till exempel använda [Functions](logic-apps-workflow-definition-language.md#functions), t ```@base64ToBinary``` . ex. för att ```@xml``` avkoda innehåll eller för att bearbeta innehållet som XML.
 

7. Välj namnet på den **karta** som du vill använda för att utföra omvandlingen. Kartan måste redan finnas i ditt integrations konto. I ett tidigare steg gav du redan till gång till din Logic app-åtkomst till ditt integrations konto som innehåller din karta.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Spara ditt arbete  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Nu är du färdig med konfigurationen av kartan. I ett verkligt världs program kanske du vill lagra transformerade data i ett LOB-program, till exempel SalesForce. Du kan enkelt som en åtgärd skicka utdata från transformeringen till Salesforce. 

Nu kan du testa transformeringen genom att göra en begäran till HTTP-slutpunkten.  


## <a name="features-and-use-cases"></a>Funktioner och användnings fall
* Omvandlingen som skapas i en karta kan vara enkel, till exempel kopiera ett namn och en adress från ett dokument till ett annat. Du kan också skapa mer komplexa omvandlingar med hjälp av de färdiga kart åtgärderna.  
* Flera kart åtgärder eller funktioner är enkelt tillgängliga, inklusive strängar, datum-och tids funktioner.  
* Du kan göra en direkt data kopiering mellan scheman. I Mapper som ingår i SDK är det lika enkelt som att rita en linje som ansluter elementen i käll schemat med deras motsvarigheter i mål schemat.  
* När du skapar en karta visar du en grafisk representation av kartan som visar alla relationer och länkar som du skapar.
* Använd test Map-funktionen för att lägga till ett exempel-XML-meddelande. Med ett enkelt klick kan du testa kartan som du skapade och se utdata som genererats.  
* Ladda upp befintliga kartor  
* Innehåller stöd för XML-format.

## <a name="advanced-features"></a>Avancerade funktioner

### <a name="reference-assembly-or-custom-code-from-maps"></a>Referens sammansättning eller anpassad kod från Maps 
Transformation-åtgärden stöder också Maps eller transformationer med en referens till en extern sammansättning. Den här funktionen möjliggör anrop till anpassad .NET-kod direkt från XSLT-mappningar. Följande är förutsättningarna för att använda sammansättning i Maps.

* Kartan och sammansättningen som refereras från kartan måste [överföras till integrations kontot](./logic-apps-enterprise-integration-maps.md). 

  > [!NOTE]
  > Karta och sammansättning måste överföras i en speciell ordning. Du måste ladda upp sammansättningen innan du laddar upp kartan som refererar till sammansättningen.

* Kartan måste också ha dessa attribut och ett CDATA-avsnitt som innehåller anropet till sammansättnings koden:

    * **namn** är namnet på den anpassade sammansättningen.
    * **namespace** är namn området i din sammansättning som innehåller den anpassade koden.

  I det här exemplet visas en karta som refererar till `circumreference` en sammansättning med namnet "XslUtilitiesLib" och anropar-metoden från sammansättningen.

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


### <a name="byte-order-mark"></a>Markering av byte ordning
Som standard börjar svaret från transformationen med byte ordnings tecknet (BOM). Du kan bara komma åt den här funktionen när du arbetar i kodvyn. Om du vill inaktivera den här `disableByteOrderMark` funktionen anger `transformOptions` du för egenskapen:

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
* [Läs mer om Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md "Läs mer om Enterprise-integrationspaket")  
* [Läs mer om Maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Lär dig mer om Enterprise integration Maps")  

