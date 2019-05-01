---
title: Marketplace-beskrivning för en Azure IoT Edge-modul | Azure Marketplace
description: Skapa marketplace-beskrivningen för en IoT Edge-modul.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: aa0c908e18233926965d8dc3cd1b474f876b8dda
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942390"
---
# <a name="iot-edge-module-marketplace-tab"></a>IoT Edge-modulen Marketplace fliken

Den **Marketplace** fliken den **nytt erbjudande** sidan kan du ge din potentiella kunder marknadsföring, försäljning och juridisk information och avtal och hantera leads som genereras från den Marketplace. Den här diktering är uppdelad i fyra delar: **Översikt över**, **marknadsföring artefakter**, **leda Management**, och **juridiska**.


## <a name="overview"></a>Översikt

I det här avsnittet ska ange du allmän information om dina Azure Marketplace erbjuder.  En asterisk (*) läggs till fältnamnet anger att det krävs.

![Översiktsavsnittet Marketplace-fliken i formuläret nytt erbjudande för IoT Edge-moduler](./media/iot-edge-module-marketplace-tab-overview.png)

I följande tabell beskrivs syftet med och innehållet i de här fälten. Required fields are indicted by an asterisk (*).

|  **Fält**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| **Rubrik\***                 | Rubrik för erbjudandet. Det ska visas på en framträdande plats i marketplace.  Högst 50 tecken. <!--ADD PICTURE IN ACTION-->|
| **Sammanfattning\***               | Kort sammanfattning av erbjudandet. Högst 100 tecken. <!--ADD PICTURE IN ACTION-->|
| **Lång sammanfattning\***          | Längre sammanfattning av erbjudandet (trots att den kan vara samma som den **sammanfattning**).  Högst 256 tecken. <!--ADD PICTURE IN ACTION-->|
| **Beskrivning\***           | Beskrivning av erbjudandet.  Maximal längd på 3000 tecken har stöd för enkel HTML-formatering.<br/> Det måste innehålla en *maskinvarukraven* punkt längst ned på sidan. Till exempel: <br/> <p><u>Minsta maskinvarukrav:</u> Linux x64 och arm32 Operativsystemet, 1 GB RAM, 500 Mb lagringsutrymme</p>
| **Identifierare för marknadsföring\***  | En unik URL att koppla till det här erbjudandet innehåller normalt sett din organisation och lösningsnamn, maximal längd 50 tecken.  Exempel: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Förhandsversion av prenumerations-ID: N** | Lägg till en till 100 prenumeration identifierare av förhandsgranskningar. Dessa white visas prenumerationer har åtkomst till erbjudandet när den publiceras innan det lanseras. |
| **Användbara länkar**          | Flera val för verksamheten och teknisk kategorier som erbjuder kan kopplas bäst.  Högst 10 tillåts. Se till att lägga till minst en länk till din dokumentation och en länk till kompatibla IoT Edge-enheter från den [Azure IoT-enhetskatalog](https://catalog.azureiotsolutions.com/). |
| **Föreslagna kategorier\*** | Välj upp till fem kategorier. De visas på din produktsidan för information. På sidan Bläddra alla IoT Edge-moduler visas den *Internet of Things \> IoT Edge-modul* kategori.|
|  |  |


### <a name="offer-example"></a>Erbjudande-exempel

 Följande exempel visar hur erbjudandet **rubrik**, **sammanfattning**, **beskrivning**, **logotyper**, och **skärmbilder** fält visas i olika vyer.

 
#### <a name="on-the-azure-marketplace-website"></a>På webbplatsen för Azure Marketplace:

- När du bläddrar efter erbjudanden:

    ![Hur marketplace-erbjudanden som visas på webbplatsen för Azure Marketplace – Bläddra](./media/iot-edge-module-ampdotcom-card.png)

- När du visar information om:

    ![Hur IoT Edge-modul som visas när du visar produktinformation på webbplatsen](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>På Azure portal-webbplatsen:

- När du bläddrar efter erbjudanden:

    ![Hur IoT Edge-modul som visas när du bläddrar efter Azure-portalen #1](./media/iot-edge-module-portal-browse.png)

    ![Hur IoT Edge-modul som visas när du bläddrar efter Azure-portalen #2](./media/iot-edge-module-portal-product-picker.png)

- När du söker efter ett erbjudande:

    ![Hur IoT Edge-modul som visas när du söker Azure-portalen](./media/iot-edge-module-portal-search.png)

- När du visar information om:

    ![Hur IoT Edge-modul som visas när du visar produktinformation i portalen](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Marknadsföring artefakter

Det här avsnittet innehåller följande underavsnitt: **Logotyper**, **skärmbild**, och **videor**. 

>[!Note]
>Logotyper är den enda krävs marknadsföring artefakter, men alla rekommenderas för bästa kund ändring.

![Marknadsföring artefakter avsnittet Marketplace-fliken i formuläret nytt erbjudande för virtuella datorer](./media/publishvm_009.png)

|  **Fält**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| *Logotyper*  | Se föregående skärm som samlar in om du vill se hur och var dina logotyper ska användas.  |
| **Liten\***                 | 40 x 40 pixel PNG-format                                                     |
| **Medel\***                | 90 x 90 pixel PNG-format                                                     |
| **Stora\***                 | 115 x 115 pixel PNG-format                                                  |
| **Wide\***                  | 255 x 115 pixel PNG-format                                                   |
| **Hero**                  | 815 x 290 pixel PNG-format.  Valfritt, men en gång överförda ikonen hero-komponenten inte kan tas bort. |
| *Skärmbilder*  | Skärmbilder visas på din produktsidan för information. Det är ett bra sätt att förmedla visuellt vad din IoT Edge-modul gör och hur det fungerar. Du kan visa Arkitekturdiagram eller använda fallet illustrationer exempelvis. Valfritt, men högst fem skärmbilder per SKU. |
| **Namn**                  | Namn eller rubrik. Högst 100 tecken.                             |
| **Avbildning**                 | Skärmen infångningsavbildning, 533 x 324 pixel PNG-format                               |
| *Videoklipp*  | Videor visas på din produktsidan för information. Det är ett bra sätt att förmedla visuellt vad din IoT Edge-modul gör och hur det fungerar. |
| **Namn**                  | Namn eller rubrik. Högst 100 tecken.                             |
| **Länk**                  | Video-URL, finns på YouTube eller Vimeo                                        |
| **Miniatyr**             | 533 x 324 pixel PNG-format                                                     |
|  |  |


### <a name="logo-guidelines"></a>Riktlinjer för logotyp

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Alla logotyper som överförts till Cloud Partner Portal bör följa riktlinjerna:

*  Azure-designen har en enkel färgpalett. Håll nere antalet primära och sekundära färger på din logotyp.
*  Temafärger i Azure-portalen är vit och svart. Undvik att använda dessa färger som bakgrundsfärg för din logotyper. Använd en färg som gör din logotyper framstående i Azure-portalen. Vi rekommenderar enkla primärfärger. Om du använder en genomskinlig bakgrund, se till att logotyper/text inte är vit, svart eller blå.
*  Använd inte en toning bakgrund på din logotyp.
*  Undvika att placera text – till och med ditt företag eller varumärke – på logotypen. Utseendet och känslan av din logotyp måste vara ”fast” och Undvik toningar.
*  Inte sträcka ut logotypen.


#### <a name="hero-logo"></a>Hero-logotyp

Hero-logotypen är valfritt.

>[!Important]
>När Hero-logotypen har överförts kan inte tas bort.

Använd följande riktlinjer för en Hero-logotyp: 

*  Svart, vit och transparent bakgrund är inte tillåtna.
*  Undvik att använda alla ljusare som bakgrund för logotypen.  Utgivaren visningsnamn, plan rubrik och erbjudandet lång sammanfattning visas i vit teckenfärg och måste står ut mot bakgrunden.
*  Undvik att använda de flesta text när du utformar logotypen. Utgivarens namn, plan rubrik, lång sammanfattning av erbjudandet och en knappen Skapa bäddas programmässigt inuti logotypen när erbjudandets visas. 
* Inkludera en rektangulär outnyttjat utrymme till höger logotypens hero. Den här tomt utrymme är 415 × 100 bildpunkter och förskjutning från vänster med 370 bildpunkter.  

<!-- P2: woudl be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Leadhantering

Det här avsnittet kan du ställa in alternativ för att samla in kund leads genererade från Azure Marketplace-erbjudanden. Du kan välja följande lagringsalternativ från en listruta.

* **Ingen** -standard, lead-information inte samlas in.
* Azure Table - skrivs till tabellen Azure som anges av en anslutningssträng.
* Dynamics CRM Online - skrivs till den [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) instans som anges av en URL och autentisering autentiseringsuppgifter.
* HTTPS-slutpunkt - skrivs till den angivna HTTPS-slutpunkten som en JSON-nyttolast.
* Marketo - skrivs till den angivna [Marketo](https://www.marketo.com/) instans som anges av server-ID, munchkin-ID och formulär-ID.
* Salesforce - skrivs till en [Salesforce](https://www.salesforce.com/) databas som anges av ett objekt-ID: T.

När du har publicerat ditt erbjudande, lead-anslutningen har verifierats och ett test-lead skickas automatiskt till det mål som du har konfigurerat. 

>[!Note]
>Lead-information ska hanteras kontinuerligt och dessa inställningar ska utan dröjsmål uppdateras när ändringar görs i din arkitektur för hantering av kunden.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>Juridisk information

Det här avsnittet kan du ange två juridiska dokument som krävs för varje erbjudande: Sekretesspolicy och användningsvillkoren.

|  **Fält**                    |     **Beskrivning**                                                          |
|  ---------                    |     ---------------                                                          |
| **Sekretesswebbadress för principen\***      | URL: en till din bokförda sekretesspolicy                                            |
| **Använd Standard Constract\***  | Om du vill använda Microsoft-mallen för standard-kontrakt.  Mer information finns i [standardavtal](https://docs.microsoft.com/azure/marketplace/standard-contract).   |
| **Användningsvillkor\***            | *Användningsvillkor* som infogade enkel HTML eller länka till ditt bokförda Användarvillkor     |
|  |  |


## <a name="next-steps"></a>Nästa steg

Använd den [stöder](./cpp-support-tab.md) flik för att tillhandahålla teknisk och användaren Supportresurser för ditt erbjudande.
