---
title: Marketplace-beskrivning för en Azure IoT Edge-modul | Azure Marketplace
description: Skapa Marketplace-beskrivningen för en IoT Edge-modul.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 57bae42eb44c174657e59fa8b017da2931ae681b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814606"
---
# <a name="iot-edge-module-marketplace-tab"></a>Fliken Marketplace för IoT Edge modulen

På fliken **Marketplace** på sidan **ny erbjudande** kan du ge dina potentiella kunder information om marknadsföring, försäljning och juridisk information och avtal och hantera leads som genereras från Marketplace. Det här långa formuläret är uppdelat i fyra avsnitt: **Översikt**, **marknadsförings artefakter**, **lead-hantering**och **juridiskt**.


## <a name="overview"></a>Översikt

I det här avsnittet anger du allmän information om erbjudandet för Azure Marketplace.  En asterisk (*) som läggs till i fält namnet visar att det är obligatoriskt.

![Översikts avsnittet på fliken Marketplace i formuläret ny erbjudande för IoT Edge moduler](./media/iot-edge-module-marketplace-tab-overview.png)

I följande tabell beskrivs syftet med och innehållet i dessa fält. Obligatoriska fält förutsägs av en asterisk (*).

|  **Fält**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| **Rubrik\***                 | Rubriken på erbjudandet. Den visas på ett framträdande sätt i Marketplace.  Maximal längd på 50 tecken. <!--ADD PICTURE IN ACTION-->|
| **Sammanfattning\***               | Kort sammanfattning av erbjudandet. Maximal längd på 100 tecken. <!--ADD PICTURE IN ACTION-->|
| **Lång Sammanfattning\***          | Längre sammanfattning av erbjudandet (även om det kan vara samma som i **sammanfattningen**).  Maximal längd på 256 tecken. <!--ADD PICTURE IN ACTION-->|
| **Beskrivning\***           | Beskrivning av erbjudandet.  Den maximala längden på 3000 tecken, stöder enkel HTML-formatering.<br/> Det måste innehålla ett stycke med *minsta maskin varu krav* längst ned. exempel: <br/> <p><u>Minsta maskin varu krav:</u> Linux x64 och ARM32 OS, 1 GB RAM, 500 MB lagrings utrymme</p>
| **Marknadsförings-ID\***  | En unik URL som associeras med det här erbjudandet, och innehåller vanligt vis organisations-och lösnings namn, högst 50 tecken.  Till exempel: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Förhandsgranska prenumerations-ID: n** | Lägg till en till 100 prenumerations-ID för för hands visare. Dessa vita prenumerationer kommer att ha åtkomst till erbjudandet när det har publicerats, innan det går live. |
| **Användbara länkar**          | Flera val av affärs-och teknik kategorier som erbjuder passar bäst för.  Högst 10 tillåts. Se till att lägga till minst en länk till din dokumentation och en länk till de kompatibla IoT Edge enheterna från [Azure IoT-katalogen](https://catalog.azureiotsolutions.com/). |
| **Föreslagna kategorier\*** | Hämta upp till fem kategorier. De visas på produkt informations sidan. På sidan bläddra visas alla IoT Edge moduler under kategorin *Sakernas Internet \> IoT Edge modul* .|
|  |  |


### <a name="offer-example"></a>Erbjudande exempel

 I följande exempel visas hur fälten **rubrik**, **Sammanfattning**, **Beskrivning**, **logo typer**och **skärm bilder** visas i olika vyer.

 
#### <a name="on-the-azure-marketplace-website"></a>På webbplatsen för Azure Marketplace:

- När du surfar erbjuder:

    ![Hur Marketplace-erbjudanden visas på webbplatsen för Azure Marketplace – Bläddra](./media/iot-edge-module-ampdotcom-card.png)

- När du tittar på erbjudande information:

    ![Hur IoT Edge-modulen visas när du tittar på produkt informationen på webbplatsen](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>På Azure Portal webbplats:

- När du surfar erbjuder:

    ![Hur IoT Edge-modulen visas när du bläddrar i Azure Portal #1](./media/iot-edge-module-portal-browse.png)

    ![Hur IoT Edge-modulen visas när du bläddrar i Azure Portal #2](./media/iot-edge-module-portal-product-picker.png)

- När du söker efter ett erbjudande:

    ![Hur IoT Edge-modulen visas när du söker i Azure Portal](./media/iot-edge-module-portal-search.png)

- När du tittar på erbjudande information:

    ![Hur IoT Edge modul visas när du tittar på produkt informationen i portalen](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Marknadsförings artefakter

I det här avsnittet finns följande avsnitt: **logo typer**, **skärm**bilder och **videor**. 

>[!Note]
>Logo typer är de enda marknadsförings artefakter som krävs, men alla är starkt rekommenderade för bästa möjliga kund överklaga.

![Avsnittet marknadsförings artefakter på fliken Marketplace i formuläret ny erbjudande för virtuella datorer](./media/publishvm_009.png)

|  **Fält**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| *Typerna*  | Se föregående skärm bild för att se hur och var dina logo typer kommer att användas.  |
| **Liten\***                 | PNG-format för 40x40-bild                                                     |
| **Medel\***                | PNG-format för 90x90-bild                                                     |
| **Stor\***                 | PNG-format för 115x115-bild                                                  |
| **Bred\***                  | PNG-format för 255x115-bild                                                   |
| **Hjälte**                  | PNG-format för 815x290-bild.  Valfritt, men när du har laddat upp hjälte ikonen kan du inte ta bort den. |
| *Par*  | Skärm dum par visas på produkt informations sidan. De är ett bra sätt att visuellt förmedla vad IoT Edge-modulen gör och hur det fungerar. Du kan visa arkitektur diagram eller användnings Falls illustrationer för en instans. Valfritt, men högst fem skärm bilder per SKU. |
| **Namn**                  | Namn eller rubrik. Maximal längd på 100 tecken.                             |
| **Avbildning**                 | Skärmdump-bild, 533x324 pixel PNG-format                               |
| *Videoklipp*  | Videor visas på produkt informations sidan. De är ett bra sätt att visuellt förmedla vad IoT Edge-modulen gör och hur det fungerar. |
| **Namn**                  | Namn eller rubrik. Maximal längd på 100 tecken.                             |
| **Länk**                  | Video-URL, som finns på YouTube eller Vimeo                                        |
| **Miniatyr**             | PNG-format för 533x324-bild                                                     |
|  |  |


### <a name="logo-guidelines"></a>Riktlinjer för logotyp

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Alla logo typer som laddats upp till Cloud Partner Portal bör följa rikt linjerna:

*  Azure-designen har en enkel färgpalett. Behåll antalet primära och sekundära färger på din logo typ låg.
*  Tema färgerna i Azure-portalen är vita och svarta. Undvik att använda dessa färger som bakgrunds färg för dina logo typer. Använd en färg som gör logo typerna framträdande i Azure Portal. Vi rekommenderar enkla primärfärger. Om du använder en genomskinlig bakgrund ser du till att logo typerna/texten inte är vita, svarta eller blå.
*  Använd inte en tonings bakgrund på din logo typ.
*  Undvik att placera text – även ditt företags-eller märkes namn – på logo typen. Utseendet på logo typen bör vara "flat" och undvika toningar.
*  Sträck inte ut logo typen.


#### <a name="hero-logo"></a>Hero-logotyp

Hjältens logo typ är valfri.

>[!Important]
>När hjälte logo typen har laddats upp går det inte att ta bort den.

Använd följande rikt linjer för en hjälte logo typ: 

*  Svarta, vita och transparenta bakgrunder är inte tillåtna.
*  Undvik att använda en ljus färg som bakgrund för logo typen.  Visnings namnet för utgivaren, plan rubriken och den långa sammanfattningen av erbjudandet visas i vit tecken färg och måste synas mot bakgrunden.
*  Undvik att använda den mesta texten när du utformar logo typen. Utgivar namnet, plan rubriken, erbjudandet lång Sammanfattning och en knappen Skapa bäddas in program mässigt inuti logo typen när erbjudandet visas. 
* Ta med ett oanvänt rektangulärt utrymme på den högra sidan av din hjälte logo typ. Det tomma utrymmet är 415x100 pixlar och förskjuts från vänster med 370 bild punkter.  

<!-- P2: woudl be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Leadhantering

I det här avsnittet kan du ställa in alternativen för att samla in kund leads som genereras från dina Azure Marketplace-erbjudanden. Du kan välja följande lagrings alternativ i en listruta.

* **Ingen** -standardvärdet för lead-information samlas inte in.
* Azure Table-skriven till Azure-tabellen som anges av en anslutnings sträng.
* Dynamics CRM Online – skrivet till [Microsoft Dynamics 365 online](https://dynamics.microsoft.com/) -instansen som anges av en URL och autentiseringsuppgifter för autentisering.
* HTTPS-slutpunkt – skrivs till den angivna HTTPS-slutpunkten som en JSON-nyttolast.
* Marketo-skriven på den angivna [Marketo](https://www.marketo.com/) -instansen, anges av Server-ID, Munchkin-ID och formulär-ID.
* Salesforce-skriven till en [Salesforce](https://www.salesforce.com/) -databas som anges av en objekt identifierare.

När du har publicerat erbjudandet verifieras ledar anslutningen och ett test-lead skickas automatiskt till det mål som du har konfigurerat. 

>[!Note]
>Lead-information ska hanteras kontinuerligt och de här inställningarna bör uppdateras när ändringar görs i din kund hanterings arkitektur.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>Juridisk information

I det här avsnittet kan du ange de två juridiska dokument som krävs för varje erbjudande: sekretess policy och användnings villkor.

|  **Fält**                    |     **Beskrivning**                                                          |
|  ---------                    |     ---------------                                                          |
| **URL för sekretess policy\***      | URL till din publicerade sekretess policy                                            |
| **Använd standard Constract-\***  | Om standard mal len för Microsoft-kontrakt ska användas.  Mer information finns i [standard kontrakt](https://docs.microsoft.com/azure/marketplace/standard-contract).   |
| **Användningsvillkor\***            | *Användningsvillkor* som infogad enkel HTML eller länka till dina publicerade användnings villkors sidor     |
|  |  |


## <a name="next-steps"></a>Nästa steg

Använd fliken [support](./cpp-support-tab.md) för att tillhandahålla support resurser för teknisk support och användare för ditt erbjudande.
