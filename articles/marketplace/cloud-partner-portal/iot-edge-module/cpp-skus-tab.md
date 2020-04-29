---
title: 'Azure IoT Edge modul SKU: er | Azure Marketplace'
description: 'Skapa SKU: er för en IoT Edge-modul.'
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 0a394c14842fe0c4eb4baa18cbb13acb8ca3170a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142461"
---
# <a name="iot-edge-module-skus-tab"></a>Fliken SKU: er för IoT Edge modul

>[!Important]
>Från och med 13 april 2020 kommer vi att börja flytta hantering av IoT Edge modul-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett IoT Edge modul-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) för att hantera dina migrerade erbjudanden.

På fliken **SKU: er** på sidan **ny erbjudande** kan du skapa en eller flera SKU: er och koppla dem till ditt nya erbjudande.  Du kan använda olika SKU: er för att särskilja en lösning med hjälp av funktions uppsättningar, fakturerings modeller eller någon annan egenskap.


## <a name="sku-settings"></a>SKU-inställningar

När du börjar skapa ett nytt erbjudande finns det inga SKU: er kopplade till erbjudandet. Följ dessa steg om du vill skapa en ny SKU:

- På sidan **IoT Edge moduler > nytt erbjudande** väljer du fliken **SKU: er** .
- Under SKU: er väljer du **+ ny SKU** för att öppna en dialog ruta.

  ![Ny SKU-knapp på fliken ny erbjudande för IoT Edge moduler](./media/iot-edge-module-skus-tab-new-sku.png)

- I dialog rutan **ny SKU** anger du en identifierare för SKU: n och väljer sedan **OK**.
(Följande tabell innehåller namngivnings konventioner för identifierare.)

Fliken **SKU: er** uppdateras och visar de fält som du redigerar för att konfigurera SKU: n. En asterisk (*) som läggs till i fält namnet visar att det är obligatoriskt.

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU-ID\***       | Identifierare för denna SKU. Namnet får innehålla högst 50 tecken, bestående av gemena alfanumeriska tecken eller bindestreck (-), men får inte sluta med ett bindestreck. **Obs:** Du kan inte ändra namnet när erbjudandet har publicerats. Namnet är offentligt synligt i produkt-URL: er. |
|  |  |


## <a name="sku-details"></a>SKU-information

Konfigurera **SKU-informationen** för att definiera hur din SKU ska visas på Azure Marketplace och Azure Portal webbplatser.

![IoT Edge modul SKU metadata](media/iot-edge-module-skus-tab-metadata.png)

I följande tabell beskrivs syfte, innehåll och formatering för fält under SKU- **information**. Obligatoriska fält förutsägs av en asterisk (*).

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Avdelning\***        | Rubrik för denna SKU. Maximal längd på 50 tecken. <br/> Den visas i Azure Portal och används som standard namn för modul (utan blank steg och specialtecken) när den distribueras. Se bilderna nedan om du vill se exakt var det här fältet visas.|
| **Sammanfattning\***      | Kort sammanfattning av denna SKU. Maximal längd på 100 tecken. Sammanfatta **inte** erbjudandet, bara SKU: n.  Den här sammanfattningen visas på Azure Marketplace. Se bilderna nedan om du vill se exakt var det här fältet visas.|
| **Beskrivning\***  | Kort beskrivning av denna SKU. Maximal längd på 3000 tecken. Beskriv inte erbjudandet men bara den här SKU: n. Den kommer att visas på Azure Marketplace och i Azure Portal. I Azure Portal läggs den till i Marketplace-beskrivningen som beskriver erbjudandet som definierats på Marketplace-fliken.  Det kan vara samma som SKU-sammanfattningen. Se bilderna nedan om du vill se exakt var det här fältet visas.|
| **Dölj denna SKU\*** | Behåll standardinställningen, som är **Nej**. |
|  |  |


### <a name="sku-example"></a>SKU-exempel

 I följande exempel visas hur fälten SKU **title**, **Summary**och **Description** visas i olika vyer.
 

#### <a name="on-the-azure-marketplace-website"></a>På webbplatsen för Azure Marketplace:

- När du tittar på SKU-information:

    ![Hur SKU: er visas på webbplatsen för Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>På Azure Portal webbplats:

- När du bläddrar SKU: er:

    ![Hur IoT Edge-modulen visas när du bläddrar i Azure Portal #1](media/iot-edge-module-portal-browse.png)

    ![Hur IoT Edge-modulen visas när du bläddrar i Azure Portal #2](media/iot-edge-module-portal-product-picker.png)

- Vid sökning efter SKU: er:

    ![Hur IoT Edge-modulen visas när du söker i Azure Portal](media/iot-edge-module-portal-search.png)

- När du tittar på SKU-information:

    ![Hur IoT Edge modul visas när du tittar på produkt informationen i portalen](./media/iot-edge-module-portal-pdp.png)

- När du distribuerar modulen:
    
    ![Hur IoT Edge-modulen visas när den distribueras](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>SKU-innehåll

Under **Edge module-avbildningar**anger du den information vi behöver för att ladda upp din IoT Edge-modul.

Ge oss åtkomst till din [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) som innehåller din IoT Edge module-avbildning så att vi kan ladda upp den och certifiera den. När den har publicerats kopieras och distribueras IoT Edge-modulen med hjälp av ett offentligt behållar register som finns på Azure Marketplace.

Du kan rikta flera plattformar och tillhandahålla flera versioner genom taggar. Lär dig mer om [taggar och versioner i "förbereda din IoT Edge-modul tekniska till gångar"](./cpp-create-technical-assets.md).

![IoT Edge module-bilder](./media/iot-edge-module-skus-tab-acr.png)

I följande tabell beskrivs syftet, innehållet och formateringen av fälten för avsnitten **avbildning av lagrings plats** och **avbildnings version**.  Obligatoriska fält förutsägs av en asterisk (*).


|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Information om avbildnings lager***    |
| **Prenumerations-ID\***        | ID för Azure-prenumerationen för din ACR.|
| **Resurs grupps namn\***      | Resurs grupps namnet för din ACR.|
| **Registernamn\***  | Ditt ACR-register namn. Kopiera bara register namnet, inte inloggnings Server namnet (till exempel utan `azurecr.io`.) |
| **Namn på databas\***  | Databas namnet för din ACR som innehåller din IoT Edge-modul. **Obs:** När namnet har angetts kan det inte ändras senare. Använd ett unikt namn för att se till att inget annat erbjudande i ditt konto har samma namn. |
| **Användar\*** | Det användar namn som är associerat med din ACR (admin användar namn). |
| **lösenordsinställning\*** | Lösen ordet som är kopplat till din ACR. |
|    |  ***Bildversion***   |
| **Bildtagg eller sammanfattad\*** | Det måste minst innehålla en `latest` tagg och en versions tagg (till exempel från och med `xx.xx.xx-` där xx är ett tal). De bör vara [manifest-Taggar](https://github.com/estesp/manifest-tool) för att rikta in sig på flera plattformar. Alla Taggar som en manifest tag refererar till måste också läggas till så att vi kan ladda upp dem. Du kan lägga till flera versioner av en IoT Edge-modul med hjälp av taggar. Alla manifest Taggar (utom `latest`) måste börja med antingen `X.Y-` eller `X.Y.Z-` där X, Y, Z är heltal. Lär dig mer om [taggar och versioner i "förbereda din IoT Edge-modul tekniska till gångar"](./cpp-create-technical-assets.md). <br/> Om `latest` till exempel en tagg pekar på som pekar på `1.0.1-linux-x64`,, `1.0.1-linux-arm32`, och `1.0.1-windows-arm32`, måste du lägga till dessa 6-Taggar här. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Hjälp dina kunder att starta med hjälp av standardinställningar

Definiera de vanligaste inställningarna för att distribuera IoT Edge-modulen. Optimera kund distributioner genom att låta dem starta IoT Edge modulen med dessa standardinställningar.

![Standardinställningar för IoT Edge modul vid distribution](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

I följande tabell beskrivs ändamålet, innehållet och formateringen av fälten för **standard vägar**, **dubbla önskade egenskaper**, **standardvariabler**och standard- **CreateOptions**.

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Standardvägar**        | Varje standard väg namn och-värde måste innehålla färre än 512 tecken. Du kan definiera upp till fem standard vägar. Se till att du använder rätt [Route-syntax](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) i ditt flödes värde. Om du vill referera till din modul använder du dess standardmodulnamn, som är din **SKU-rubrik** utan blank steg och specialtecken. Om du vill referera till andra moduler som ännu inte är `<FROM_MODULE_NAME>` kända använder du konventionen för att låta kunderna veta att de behöver uppdatera den här informationen. Läs mer om [IoT Edge vägar](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Om modulen `ContosoModule` till exempel lyssnar efter indata `ContosoInput` och utdata i `ContosoOutput`, är det klokt att definiera följande två standard vägar:<br/>-Namn #1:`ToContosoModule`<br/>-Värde #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>-Namn #2:`FromContosoModuleToCloud`<br/>-Värde #2:`FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Standardvärden för dubbla önskade egenskaper**      | Alla standardvärden med dubbla önskade egenskaper måste vara färre än 512 tecken. Du kan definiera upp till 5 namn/värde-dubbla önskade egenskaper. Värdena för de dubbla önskade egenskaperna måste vara giltiga JSON, icke-Escaped, utan matriser och med en maximal kapslad hierarki på 4. Läs mer om de [dubbla önskade egenskaperna](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Om en modul till exempel stöder dynamiskt konfigurerbara uppdaterings hastigheter via de två önskade egenskaperna, är det klokt att definiera följande standardvärden för den dubbla önskade egenskapen:<br/> -Namn #1:`RefreshRate`<br/>-Värde #1:`60`|
| **Standardvariabler för miljön**  | Varje standard namn och värde för miljövariabler måste innehålla färre än 512 tecken. Du kan definiera upp till fem miljövariabler av namn/värde. <br/>Om en modul till exempel måste godkänna användnings villkoren innan den startas, kan du definiera följande miljö variabel:<br/> -Namn #1:`ACCEPT_EULA`<br/>-Värde #1:`Y`|
| **Standard createOptions**  | CreateOptions måste innehålla färre än 512 tecken. Det måste vara en giltig JSON, icke-Escaped. Läs mer om [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Om en modul till exempel kräver bindning av en port kan du definiera följande createOptions:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Spara inställningarna för SKU genom att välja **Spara** . 


## <a name="next-steps"></a>Nästa steg

Använd [fliken Marketplace](./cpp-marketplace-tab.md) för att skapa en Marketplace-beskrivning för ditt erbjudande.
