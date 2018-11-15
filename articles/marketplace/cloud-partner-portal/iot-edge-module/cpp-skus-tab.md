---
title: 'Azure IoT Edge-modul SKU: er | Microsoft Docs'
description: 'Skapa SKU: er för en IoT Edge-modul.'
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 205fd258ed397f5a9588773549368fc3c4aec058
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684825"
---
# <a name="iot-edge-module-skus-tab"></a>Fliken för IoT Edge modul SKU: er

Den **SKU: er** fliken den **nytt erbjudande** sidan kan du skapa en eller flera SKU: er och koppla dem till din nya erbjudandet.  Du kan använda olika SKU: er för att skilja mellan en lösning av funktioner, faktureringsmodellerna eller vissa andra egenskaper.

## <a name="sku-settings"></a>SKU-inställningar

När du börjar skapa ett nytt erbjudande, det är inte alla SKU: er som är kopplad till erbjudandet. Följ dessa steg om du vill skapa en ny SKU:

- På den **IoT Edge-moduler > nytt erbjudande** väljer den **SKU: er** fliken.
- Välj under SKU: er, **+ nya SKU: N** att öppna en dialogruta.

  ![Nya SKU-knappen på fliken nytt erbjudande för IoT Edge-moduler](./media/iot-edge-module-skus-tab-new-sku.png)

- På den **nya SKU: N** dialogrutan rutan, ange en identifierare för SKU: N och välj sedan **OK**.
(I följande tabell ger identifierare namnkonventionerna.)

Den **SKU: er** fliken uppdateras och visar de fält som du redigerar om du vill konfigurera SKU: N. En asterisk (*) läggs till fältnamnet anger att det krävs.

|  **Fält**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **SKU-ID**       | Identifierare för denna SKU. Det här namnet med högst 50 tecken, som består av gemena alfanumeriska tecken eller bindestreck (-), men får inte sluta med ett bindestreck. **Obs:** du kan inte ändra det här namnet när erbjudandet är publicerat. Namnet är offentligt synlig i URL: er i produkten. |

## <a name="sku-details"></a>SKU-information

Konfigurera den **SKU-information** att definiera hur din SKU ska visas på Azure Marketplace och Azure Portal-webbplatser.

![IoT Edge-modulen sku metadata](media/iot-edge-module-skus-tab-metadata.png)

I följande tabell beskrivs syftet, innehåll och formatering för fält under **SKU-information**.

|  **Fält**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Rubrik**        | Rubrik för denna SKU. Högst 50 tecken. <br/> Den kommer att visas i Azure Portal och används som en standard Modulnamn (utan blanksteg eller specialtecken) när den har distribuerats. Visa bilderna nedan för att se exakt var det här fältet visas.|
| **Sammanfattning**      | Kort sammanfattning av denna SKU. Högst 100 tecken. Gör **inte** sammanfatta erbjudandet, bara SKU: N.  Den här sammanfattningen visas i Azure Marketplace. Visa bilderna nedan för att se exakt var det här fältet visas.|
| **Beskrivning**  | Kort beskrivning av denna SKU. Maximal längd på 3000 tecken. Däremot inte erbjudandet men bara denna SKU. Det kommer att visas på azure marketplace och i Azure-portalen. I Azure-portalen läggs det till Marketplace-beskrivning som beskriver det erbjudande som definierats på fliken Marketplace.  Det kan vara samma som sammanfattningen SKU. Visa bilderna nedan för att se exakt var det här fältet visas.|
| **Dölj den här SKU: N** | Behålla standardinställningen, vilket är **nr**. |

### <a name="sku-example"></a>SKU-exempel

 Följande exempel visar hur SKU: N **rubrik**, **sammanfattning**, och **beskrivning** fält visas i olika vyer.
 
#### <a name="on-the-azure-marketplace-website"></a>På webbplatsen för Azure Marketplace:

- När du tittar på SKU-information:

    ![Hur SKU: er visas på webbplatsen för Azure Marketplace](media/iot-edge-module-ampdotcom-pdp-plans.png)

#### <a name="on-the-azure-portal-website"></a>På webbplatsen Azure Portal:

- När du bläddrar efter SKU: er:

    ![Hur IoT Edge-modul som visas när du bläddrar efter Azure-portalen #1](media/iot-edge-module-portal-browse.png)

    ![Hur IoT Edge-modul som visas när du bläddrar efter Azure-portalen #2](media/iot-edge-module-portal-product-picker.png)

- När du söker efter SKU: er:

    ![Hur IoT Edge-modul som visas när du söker Azure-portalen](media/iot-edge-module-portal-search.png)

- När du tittar på SKU-information:

    ![Hur IoT Edge-modul som visas när du visar produktinformation i portalen](./media/iot-edge-module-portal-pdp.png)

- När du distribuerar modulen:
    
    ![Hur IoT Edge-modul som visas när distribueras](./media/iot-edge-module-deployment.png)

## <a name="sku-content"></a>SKU-innehåll

Under **Edge-modulen avbildningar**, innehåller den information vi behöver att ladda upp din IoT Edge-modul.

Ge oss åtkomst till din [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) som innehåller din IoT-Edge modulen bild så att vi kan ladda upp den och certifiera den. När den har publicerats IoT Edge-modul ska kopieras och distribueras via en offentlig container registry med Azure Marketplace.

Du kan når flera plattformar och har flera versioner via taggar. Läs mer om [taggar och versionshantering i ”Förbered din IoT-Edge tekniska resurser för modulen”](./cpp-create-technical-assets.md).

![IoT Edge-modulen avbildningar](./media/iot-edge-module-skus-tab-acr.png)

I följande tabell beskrivs syftet, innehåll, och formatering av fälten för:

- **Avbildningsdetaljerna som lagringsplats**
- **Avbildningsversion**

|  **Fält**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
|  ***Avbildningsdetaljerna som lagringsplats***   |  |
| **prenumerations-ID**        | Azure-prenumerations-ID för din ACR.|
| **Resursgruppens namn**      | Resursgruppens namn på din ACR.|
| **Registernamn**  | Namnet för din ACR-registret. Bara kopiera registernamnet inte namnet på inloggningsservern (till exempel utan den `azurecr.io`.) |
| **Databasnamn**  | Namnet på lagringsplatsen för din ACR som innehåller din IoT Edge-modul. **Obs:** när du har angett namnet kan inte ändras senare. Använd ett unikt namn för att säkerställa att inga andra erbjudanden i ditt konto har samma namn. |
| **Användarnamn** | Användarnamnet som är associerade med din ACR (administratörsanvändarnamn). |
| **Lösenord** | Lösenordet som associeras med din ACR. |
|  ***Avbildningsversion***   |  |
| **Bildtagg- eller** | Det måste innehålla minst en `latest` tagg och en tagg för version (till exempel att starta med `xx.xx.xx-` där xx är ett tal). De ska vara [manifest taggar](https://github.com/estesp/manifest-tool) till når flera plattformar. Alla taggar som refereras till av en manifest tagg måste också läggas till så att vi kan ladda upp dem. Du kan lägga till flera versioner av en IoT Edge-modul med hjälp av taggar. Alla manifest taggar (utom `latest`) måste börja med antingen `X.Y-` eller `X.Y.Z-` där X, Y, Z är heltal. Läs mer om [taggar och versionshantering i ”Förbered din IoT-Edge tekniska resurser för modulen”](./cpp-create-technical-assets.md). <br/> Till exempel om en `latest` tagga pekar som pekar på `1.0.1-linux-x64`, `1.0.1-linux-arm32`, och `1.0.1-windows-arm32`, dessa 6 taggar måste läggas till här. |

### <a name="help-your-customers-launch-your-iot-edge-module-by-using-default-settings"></a>Hjälp dina kunder att starta din IoT Edge-modul med standardinställningarna

Definiera de vanligaste inställningarna för att distribuera IoT Edge-modul. Optimera distributioner genom att låta dem starta din IoT Edge modulen out-of the box med standardinställningarna.

![IoT Edge-modulen standardinställningarna vid distributionen](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

I följande tabell beskrivs syftet, innehåll, och formatering av fälten för **standard vägar**, **standard twin önskade egenskaper**, **standard miljövariabler**, och **standard CreateOptions**.

|  **Fält**       |     **Beskrivning**                                                          |
|  ---------       |     ---------------                                                          |
| **Standardvägar**        | Varje standard väg namnet och värdet måste vara färre än 512 tecken. Du kan definiera upp till 5 standardvägar. Se till att använda en korrekt [dirigera syntax](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) route-värde. Refererar till din modul, använder du dess standardnamnet för modulen som blir din **SKU rubrik** utan blanksteg eller specialtecken. Om du vill referera till andra moduler har ännu inte använda den `<FROM_MODULE_NAME>` konvention att kunderna vet att de behöver för att uppdatera den här informationen. Läs mer om [IoT Edge dirigerar](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Till exempel om modulen `ContosoModule` lyssnar för indata på `ContosoInput` och utdata på `ContosoOutput`, är det praktiskt att definiera standardvägar som 2 följande:<br/>-Name #1: `ToContosoModule`<br/>-Värdet #1:`FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>-Namnet #2: `FromContosoModuleToCloud`<br/>-Värdet #2: `FROM /messages/modules/ContonsoModule/outputs/ContosoOuput INTO $upstream`<br/>  |
| **Standard enhetstvillingens egenskaper**      | Varje standard twin önskade egenskaper namnet och värdet måste vara färre än 512 tecken. Du kan definiera upp till 5 enhetstvillingens egenskaper för namn/värde. Värdena för enhetstvillingens egenskaper måste vara giltig JSON, icke-undantaget, utan matriser och med en kapslad maxhierarki på 4. Läs mer om [twin önskade egenskaper](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Till exempel om en modul stöder en dynamiskt konfigurerbara uppdateringsintervall via enhetstvillingens egenskaper kan det vara bra att definiera följande standard enhetstvillingens egenskap:<br/> -Name #1: `RefreshRate`<br/>-Värdet #1: `60`|
| **Standard-miljövariabler**  | Varje standard miljö variabler namnet och värdet måste vara färre än 512 tecken. Du kan definiera upp till 5 namn/värde-miljövariabler. <br/>Om en modul kräver för att godkänna användningsvillkoren innan startas, till exempel kan du definiera följande miljövariabler:<br/> -Name #1: `ACCEPT_EULA`<br/>-Värdet #1: `Y`|
| **Standard createOptions**  | CreateOptions måste vara färre än 512 tecken. Det måste vara giltig JSON, icke-undantaget. Läs mer om [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Till exempel om en modul kräver binda en port, kan du definiera följande createOptions:<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|

<br/> Välj **spara** att spara inställningarna för din SKU. 

## <a name="next-steps"></a>Nästa steg

Använd den [Marketplace fliken](./cpp-marketplace-tab.md) att skapa en marketplace-beskrivning av erbjudandet.
