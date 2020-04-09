---
title: 'Azure IoT Edge-modulSKU: er | Azure Marketplace'
description: Skapa SKU:er för en IoT Edge-modul.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: c1d1daea71ea8b69ef24031c643caf8dcd0d1fb7
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983338"
---
# <a name="iot-edge-module-skus-tab"></a>Fliken SKU:er för IoT-kantmodul

>[!Important]
>Från och med den 13 april 2020 börjar vi flytta hanteringen av din IoT Edge-modul till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett IoT Edge-modulerbjudande](https://aka.ms/AzureCreateIoT) för att hantera dina migrerade erbjudanden.

På fliken **SKU:er** på sidan **Nytt erbjudande** kan du skapa en eller flera SKU:er och koppla dem till ditt nya erbjudande.  Du kan använda olika SKU:er för att skilja en lösning efter funktionsuppsättningar, faktureringsmodeller eller någon annan egenskap.


## <a name="sku-settings"></a>SKU-inställningar

När du börjar skapa ett nytt erbjudande finns det inga SKU:er som är associerade med erbjudandet. Så här skapar du en ny SKU:

- På sidan **IoT Edge Modules > Nytt erbjudande** väljer du fliken **SKU:er.**
- Under SKU väljer du **+ Ny SKU** för att öppna en dialogruta.

  ![Knappen Ny SKU på fliken Nytt erbjudande för IoT Edge-moduler](./media/iot-edge-module-skus-tab-new-sku.png)

- I dialogrutan **Ny SKU** anger du en identifierare för SKU:n och väljer sedan **OK**.
(I följande tabell anges namngivningskonventionerna för identifierare.)

Fliken **SKU uppdateras** och visar de fält som du redigerar för att konfigurera SKU. En asterisk (*) som läggs till i fältnamnet anger att det krävs.

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU-ID\***       | Identifierare för den här SKU:n. Det här namnet har högst 50 tecken, som består av gemener alfanumeriska tecken eller streck (-), men kan inte sluta med ett streck. **Anm.:** Du kan inte ändra det här namnet när erbjudandet har publicerats. Namnet visas offentligt i produktadresser. |
|  |  |


## <a name="sku-details"></a>SKU-detaljer

Konfigurera **SKU-information** för att definiera hur din SKU ska visas på Azure Marketplace- och Azure Portal-webbplatserna.

![IoT Edge-modul sku metadata](media/iot-edge-module-skus-tab-metadata.png)

I följande tabell beskrivs syfte, innehåll och formatering för fält under **SKU-information**. Obligatoriska fält åtalas av en asterisk (*).

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Titel\***        | Titel för denna SKU. Maximal längd på 50 tecken. <br/> Den visas i Azure Portal och kommer att användas som standardmodulnamn (utan mellanslag och specialtecken) när den distribueras. Se bilderna nedan för att se exakt var det här fältet visas.|
| **Sammanfattning\***      | Kort sammanfattning av denna SKU. Maximal längd på 100 tecken. Sammanfatta **inte** erbjudandet, bara SKU.  Den här sammanfattningen visas på Azure Marketplace. Se bilderna nedan för att se exakt var det här fältet visas.|
| **Beskrivning\***  | Kort beskrivning av denna SKU. Maximal längd på 3 000 tecken. Beskriv INTE erbjudandet utan just denna SKU. Den visas på den azurblå marknadsplatsen och i Azure-portalen. I Azure-portalen läggs den till i Marketplace-beskrivningen som beskriver erbjudandet som definierats på fliken Marketplace.  Det kan vara samma som SKU-sammanfattningen. Se bilderna nedan för att se exakt var det här fältet visas.|
| **Dölj den här SKU:n\*** | Behåll standardinställningen, som är **Nej**. |
|  |  |


### <a name="sku-example"></a>Exempel på SKU

 Följande exempel visar hur fälten **SKU-rubrik,** **sammanfattning**och **Beskrivning** visas i olika vyer.
 

#### <a name="on-the-azure-marketplace-website"></a>På Webbplatsen för Azure Marketplace:

- När du tittar på SKU detaljer:

    ![Så här visas SKU:er på Azure Marketplace-webbplatsen](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>På Azure Portal-webbplatsen:

- När du surfar på SKU:

    ![Så här visas IoT Edge-modulen när du surfar på Azure-portalen #1](media/iot-edge-module-portal-browse.png)

    ![Så här visas IoT Edge-modulen när du surfar på Azure-portalen #2](media/iot-edge-module-portal-product-picker.png)

- När du söker efter SKU:

    ![Så här visas IoT Edge-modulen när du söker i Azure-portalen](media/iot-edge-module-portal-search.png)

- När du tittar på SKU detaljer:

    ![Hur IoT Edge-modulen visas när du tittar på produktinformationen i portalen](./media/iot-edge-module-portal-pdp.png)

- När du distribuerar modulen:
    
    ![Hur IoT Edge-modulen visas när den distribueras](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>SKU-innehåll

Under **Edge Module Images**kan vi tillhandahålla den information vi behöver för att ladda upp din IoT Edge-modul.

Ge oss åtkomst till ditt [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) som innehåller din IoT Edge-modulavbildning så att vi kan ladda upp den och certifiera den. När den har publicerats kopieras och distribueras IoT Edge-modulen med hjälp av ett offentligt behållarregister som finns i Azure Marketplace.

Du kan rikta in dig på flera plattformar och tillhandahålla flera versioner via taggar. Läs mer om [taggar och versionshantering i "Förbered dina tekniska resurser för IoT Edge-modulen".](./cpp-create-technical-assets.md)

![IoT Edge-modulbilder](./media/iot-edge-module-skus-tab-acr.png)

I följande tabell beskrivs syftet, innehållet och formateringen för fälten för avsnitten **Information om bilddatabasen** och **bildversion**.  Obligatoriska fält åtalas av en asterisk (*).


|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Information om bilddatabas***    |
| **Prenumerations-ID\***        | Azure-prenumerations-ID för din ACR.|
| **Namn på resursgrupp\***      | Resursgruppnamnet för ACR.|
| **Registernamn\***  | Ditt ACR-registernamn. Kopiera endast registrets namn, INTE inloggningsserverns namn `azurecr.io`(till exempel utan .) |
| **Databasnamn\***  | Databasnamnet på ACR som innehåller din IoT Edge-modul. **Anm.:** När namnet har angetts kan det inte ändras senare. Använd ett unikt namn för att säkerställa att inget annat erbjudande i ditt konto har samma namn. |
| **Användarnamn\*** | Användarnamnet som är kopplat till din ACR (admin användarnamn). |
| **Lösenord\*** | Lösenordet som är kopplat till acr-systemet. |
|    |  ***Bildversion***   |
| **Bildtagg eller sammanfattning\*** | Den måste åtminstone `latest` innehålla en tagg och en versionstagg (till exempel från `xx.xx.xx-` där xx är ett tal). De bör vara [manifesttaggar](https://github.com/estesp/manifest-tool) för att rikta in sig på flera plattformar. Alla taggar som refereras av en manifesttagg måste också läggas till så att vi kan ladda upp dem. Du kan lägga till flera versioner av en IoT Edge-modul med hjälp av taggar. Alla manifesttaggar `latest`(utom) `X.Y-` måste `X.Y.Z-` börja med antingen eller där X, Y, Z är heltal. Läs mer om [taggar och versionshantering i "Förbered dina tekniska resurser för IoT Edge-modulen".](./cpp-create-technical-assets.md) <br/> Om till exempel `latest` en tagg pekar `1.0.1-linux-x64` `1.0.1-linux-arm32`på att `1.0.1-windows-arm32`pekar på , , och måste dessa 6 taggar läggas till här. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Hjälp dina kunder att starta genom att använda standardinställningar

Definiera de vanligaste inställningarna för att distribuera IoT Edge-modulen. Optimera kunddistributioner genom att låta dem starta din IoT Edge-modul direkt med dessa standardvärden.

![Standardinställningar för IoT Edge-modul vid distribution](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

I följande tabell beskrivs syfte, innehåll och formatering för fälten för **Standardvägar**, **Önskade standardtvillingegenskaper**, **Standardmiljövariabler**och **Standard createoptions**.

|  **Field**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Standardvägar**        | Varje standardflödesnamn och värde måste vara färre än 512 tecken. Du kan definiera upp till 5 standardvägar. Se till att använda en korrekt [flödessyntax](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) i flödesvärdet. Om du vill referera till modulen använder du standardmodulnamnet, som blir din **SKU-titel** utan blanksteg och specialtecken. Om du vill referera till andra `<FROM_MODULE_NAME>` moduler som ännu inte är kända använder du konventionen för att informera dina kunder om att de behöver uppdatera den här informationen. Läs mer om [IoT Edge-rutter](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Om modulen `ContosoModule` till exempel lyssnar `ContosoInput` efter indata `ContosoOutput`på och utdata på är det klokt att definiera följande två standardvägar:<br/>- Namn #1:`ToContosoModule`<br/>- Värde #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- Namn #2:`FromContosoModuleToCloud`<br/>- Värde #2:`FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Standardegenskaper för dubbla önskade egenskaper**      | Varje standardtvillingalternativa egenskapsnamn och värde måste vara färre än 512 tecken. Du kan definiera upp till 5 namn/värde twin önskade egenskaper. Värden för dubbla önskade egenskaper måste vara giltiga JSON, som inte har rymts, utan matriser och med en maximal kapslad hierarki på 4. Läs mer om [dubbla önskade egenskaper](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Om en modul till exempel stöder en dynamiskt konfigurerbar uppdateringsfrekvens via dubbla önskade egenskaper, är det vettigt att definiera följande önskade standardtvillingegenskaper:<br/> - Namn #1:`RefreshRate`<br/>- Värde #1:`60`|
| **Standardmiljövariabler**  | Varje standardmiljövariablers namn och värde måste vara färre än 512 tecken. Du kan definiera upp till 5 namn/värde miljövariabler. <br/>Om en modul till exempel måste acceptera användningsvillkor innan den startas kan du definiera följande miljövariabel:<br/> - Namn #1:`ACCEPT_EULA`<br/>- Värde #1:`Y`|
| **Standard createOptions**  | CreateOptions måste vara färre än 512 tecken. Det måste vara giltigt JSON, utan flykt. Läs mer om [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Om en modul till exempel kräver bindning av en port kan du definiera följande createOptions:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Välj **Spara** om du vill spara SKU-inställningarna. 


## <a name="next-steps"></a>Nästa steg

Använd [fliken Marketplace](./cpp-marketplace-tab.md) för att skapa en marknadsplatsbeskrivning för ditt erbjudande.
