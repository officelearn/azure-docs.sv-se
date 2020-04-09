---
title: Marknadsplatsbeskrivning för en Azure IoT Edge-modul | Azure Marketplace
description: Skapa marketplace-beskrivningen för en IoT Edge-modul.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: df51a408cc47908a240ceb66bf510fe3df3ce87f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983406"
---
# <a name="iot-edge-module-marketplace-tab"></a>Fliken Marketplace för IoT Edge-modul

>[!Important]
>Från och med den 13 april 2020 börjar vi flytta hanteringen av din IoT Edge-modul till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett IoT Edge-modulerbjudande](https://aka.ms/AzureCreateIoT) för att hantera dina migrerade erbjudanden.

På fliken **Marketplace** på sidan **Nytt erbjudande** kan du förse dina potentiella kunder med marknadsföring, försäljning och juridisk information och avtal och hantera leads som genereras från marknadsplatsen. Det här långa formuläret är indelat i fyra avsnitt: **Översikt**, **Marknadsföringsartefakter**, **Lead Management**och **Legal**.


## <a name="overview"></a>Översikt

I det här avsnittet anger du allmän information om ditt Azure Marketplace-erbjudande.  En asterisk (*) som läggs till i fältnamnet anger att det krävs.

![Översiktsavsnittet på fliken Marketplace i formuläret Nytt erbjudande för IoT Edge-moduler](./media/iot-edge-module-marketplace-tab-overview.png)

I följande tabell beskrivs syftet med och innehållet i dessa fält. Obligatoriska fält åtalas av en asterisk (*).

|  **Field**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| **Titel\***                 | Erbjudandets titel. Det kommer att visas tydligt på marknaden.  Maximal längd på 50 tecken. <!--ADD PICTURE IN ACTION-->|
| **Sammanfattning\***               | Kort sammanfattning av erbjudandet. Maximal längd på 100 tecken. <!--ADD PICTURE IN ACTION-->|
| **Lång sammanfattning\***          | Längre sammanfattning av erbjudandet (även om det kan vara samma som **sammanfattningen**).  Maximal längd på 256 tecken. <!--ADD PICTURE IN ACTION-->|
| **Beskrivning\***           | Beskrivning av erbjudandet.  Maximal längd på 3000 tecken, stöder enkel HTML-formatering.<br/> Den måste innehålla ett stycke minimikrav på *maskinvarukrav* längst ned. till exempel: <br/> <p><u>Minimikrav på maskinvara:</u> Linux x64 och arm32 OS, 1 GB RAM, 500 Mb lagringsutrymme</p>
| **Marknadsidentifierare\***  | En unik URL som ska associeras med det här erbjudandet, innehåller vanligtvis ditt organisations- och lösningsnamn, maximal längd på 50 tecken.  Ett exempel: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Förhandsgranska prenumerations-ID:er** | Lägg till en till 100 prenumerationsidentifierare för förhandsgranskningsspelare. Dessa vitlistade prenumerationer har tillgång till erbjudandet när det har publicerats, innan det publiceras. |
| **Användbara länkar**          | Flerval av affärs- och teknikkategorier som erbjuder kan bäst associeras med.  Högst 10 tillåtna. Se till att lägga till minst en länk till din dokumentation och en länk till kompatibla IoT Edge-enheter från [Azure IoT-enhetskatalogen](https://catalog.azureiotsolutions.com/). |
| **Föreslagna kategorier\*** | Plocka upp till fem kategorier. De visas på din produktinformationssida. På bläddringssidorna visas alla IoT Edge-moduler under *ioT \> Edge-modulekategorin Internet* of Things.|
|  |  |


### <a name="offer-example"></a>Exempel på erbjudande

 Följande exempel visar hur fälten **Titel,** **Sammanfattning,** **Beskrivning**, **Logotyper**och **Skärmbilder** visas i olika vyer.

 
#### <a name="on-the-azure-marketplace-website"></a>På Webbplatsen för Azure Marketplace:

- När du surfar erbjudanden:

    ![Så här visas marketplace-erbjudanden på Azure Marketplace-webbplatsen – bläddra](./media/iot-edge-module-ampdotcom-card.png)

- När du tittar på erbjudande detaljer:

    ![Hur IoT Edge-modulen dyker upp när du tittar på produktinformationen på webbplatsen](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>På Azure-portalens webbplats:

- När du surfar erbjudanden:

    ![Så här visas IoT Edge-modulen när du surfar på Azure-portalen #1](./media/iot-edge-module-portal-browse.png)

    ![Så här visas IoT Edge-modulen när du surfar på Azure-portalen #2](./media/iot-edge-module-portal-product-picker.png)

- När du söker efter ett erbjudande:

    ![Så här visas IoT Edge-modulen när du söker i Azure-portalen](./media/iot-edge-module-portal-search.png)

- När du tittar på erbjudande detaljer:

    ![Hur IoT Edge-modulen visas när du tittar på produktinformationen i portalen](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Marknadsföring Artefakter

Det här avsnittet innehåller följande underavsnitt: **Logotyper,** **skärmbild**och **videor**. 

>[!Note]
>Logotyper är de enda nödvändiga marknadsföring artefakter, men alla rekommenderas starkt för bästa kund överklagande.

![Avsnittet Marknadsföringsartefakter på fliken Marketplace i formuläret Nytt erbjudande för virtuella datorer](./media/publishvm_009.png)

|  **Field**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| *Logotyper*  | Se föregående skärmdumpar för att se hur och var dina logotyper kommer att användas.  |
| **Liten\***                 | PNG-format med 40 x 40 pixlar                                                     |
| **Medel\***                | PNG-format med 90 x 90 pixlar                                                     |
| **Stor\***                 | PNG-format med 115 x 115 pixlar                                                  |
| **Brett\***                  | PNG-format med 255 x 115 pixlar                                                   |
| **Hjälte**                  | PNG-format med 815 x 290 pixlar.  Valfritt, men när du har laddat upp hjälteikonen kan inte tas bort. |
| *Skärmbilder*  | Skärmbilder visas på produktinformationssidan. De är ett bra sätt att visuellt kommunicera vad din IoT Edge-modul gör och hur den fungerar. Du kan visa arkitekturdiagram eller använda exempelvis exempelvis illustrationer. Valfritt, men högst fem skärmbilder per SKU. |
| **Namn**                  | Namn eller titel. Maximal längd på 100 tecken.                             |
| **Bild**                 | Bild av skärmtagning, PNG-format med 533 x 324 pixlar                               |
| *Videor*  | Videor visas på produktinformationssidan. De är ett bra sätt att visuellt kommunicera vad din IoT Edge-modul gör och hur den fungerar. |
| **Namn**                  | Namn eller titel. Maximal längd på 100 tecken.                             |
| **Länk**                  | Video-URL, som finns på YouTube eller Vimeo                                        |
| **Miniatyr**             | PNG-format med 533 x 324 pixlar                                                     |
|  |  |


### <a name="logo-guidelines"></a>Riktlinjer för logotyp

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Alla logotyper som laddas upp till Cloud Partner Portal bör följa riktlinjerna:

*  Azure-designen har en enkel färgpalett. Håll antalet primära och sekundära färger på logotypen lågt.
*  Temafärgerna i Azure Portal är vita och svarta. Undvik att använda dessa färger som bakgrundsfärg för logotyperna. Använd en färg som gör dina logotyper framträdande i Azure-portalen. Vi rekommenderar enkla primärfärger. Om du använder en genomskinlig bakgrund kontrollerar du att logotyperna/texten inte är vita, svarta eller blå.
*  Använd inte en övertoningsbakgrund på logotypen.
*  Undvik att placera text – även ditt företags- eller varumärke – på logotypen. Utseendet och känslan på din logotyp ska vara "platt" och bör undvika lutningar.
*  Sträck inte ut logotypen.


#### <a name="hero-logo"></a>Hero-logotyp

Hero-logotypen är valfri.

>[!Important]
>När Hero-logotypen har laddats upp kan den inte tas bort.

Använd följande riktlinjer för en Hero-logotyp: 

*  Svarta, vita och genomskinliga bakgrunder är inte tillåtna.
*  Undvik att använda ljus färg som bakgrund för logotypen.  Utgivarens visningsnamn, planrubrik och erbjudandets långa sammanfattning visas i vit teckenfärg och måste sticka ut mot bakgrunden.
*  Undvik att använda de flesta text när du utformar logotypen. Utgivarens namn, plantitel, erbjudandets långa sammanfattning och en skapa-knapp bäddas in programmässigt i logotypen när erbjudandet visas. 
* Inkludera ett oanvänt rektangulärt utrymme till höger om din hjältelogotyp. Det här tomma utrymmet är 415x100 pixlar och förskjuts från vänster med 370 pixlar.  

<!-- P2: would be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Leadhantering

I det här avsnittet kan du ställa in alternativen för att samla in kundleads som genereras från dina Azure Marketplace-erbjudanden. Du kan välja följande lagringsalternativ i en listruta.

* **Ingen** - standardinformationen samlas inte in.
* Azure Table - skrivet till Azure-tabellen som anges av en anslutningssträng.
* Dynamics CRM Online - skrivet till [Microsoft Dynamics 365](https://dynamics.microsoft.com/) Online-instansen, som anges av en URL och autentiseringsuppgifter.
* HTTPS Slutpunkt - skrivet till den angivna HTTPS-slutpunkten som en JSON-nyttolast.
* Marketo - skrivet [Marketo](https://www.marketo.com/) till den angivna Marketo-instansen, angiven av server-ID, munchkin-ID och formulär-ID.
* Salesforce - skrivet till en [Salesforce-databas,](https://www.salesforce.com/) angiven av en objektidentifierare.

När du har publicerat erbjudandet verifieras leadanslutningen och en testled skickas automatiskt till det mål som du konfigurerade. 

>[!Note]
>Leadinformation bör hanteras kontinuerligt och dessa inställningar bör uppdateras snabbt när ändringar görs i din kundhanteringsarkitektur.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>Juridisk information

I det här avsnittet kan du tillhandahålla de två juridiska dokument som krävs för varje erbjudande: Sekretesspolicy och användarvillkoren.

|  **Field**                    |     **Beskrivning**                                                          |
|  ---------                    |     ---------------                                                          |
| **Url till sekretesspolicy\***      | URL till din bokförda sekretesspolicy                                            |
| **Använd Standard Constract\***  | Om standardmallen för Microsoft-kontrakt ska användas.  Mer information finns i [Standardkontrakt](https://docs.microsoft.com/azure/marketplace/standard-contract).   |
| **Användningsvillkor\***            | *Användarvillkor* som infogad enkel HTML eller länk till din bokförda användningsvillkor sida     |
|  |  |


## <a name="next-steps"></a>Nästa steg

Använd fliken [Support](./cpp-support-tab.md) för att tillhandahålla tekniska resurser och användarsupport för ditt erbjudande.
