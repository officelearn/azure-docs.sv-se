---
title: Fliken Marketplace för virtuell dator i Cloud Partner Portal för Azure Marketplace
description: Beskriver fliken Marketplace som används för att skapa ett erbjudande för virtuella Azure Marketplace-datorer.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 283274986c753fc8ad05b9b7b0dd87aea956bcce
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75762900"
---
# <a name="virtual-machine-marketplace-tab"></a>Fliken Marketplace för virtuell dator

På fliken **Marketplace** på sidan **ny erbjudande** kan du ge dina potentiella kunder information om marknadsföring, försäljning och juridisk information och avtal och hantera leads som genereras från Marketplace. Det här långa formuläret är uppdelat i fyra avsnitt: **Översikt**, **marknadsförings artefakter**, **lead-hantering**och **juridiskt**.


## <a name="overview-section"></a>Avsnittet Översikt
I det här avsnittet anger du allmän information om erbjudandet för Azure Marketplace.  En asterisk (*) som lagts till i fält namnet anger att det är obligatoriskt.

![Översikts avsnittet på fliken Marketplace för virtuella datorer](./media/publishvm_008.png)

I följande tabell beskrivs syftet med och innehållet i dessa fält. Obligatoriska fält förutsägs av en asterisk (*).

|  **Fält**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| **Rubrik\***                 | Rubriken på erbjudandet, ofta det långa, formella namnet. Den här rubriken visas på ett framträdande sätt i Marketplace.  Maximal längd på 50 tecken. |
| **Sammanfattning\***               | Kort syfte eller funktion i lösningen.  Maximal längd på 100 tecken. |
| **Lång Sammanfattning\***          | Syfte eller funktion i lösningen.  Maximal längd på 256 tecken. |
| **Beskrivning\***           | Beskrivning av lösning.  Den maximala längden på 3000 tecken, stöder enkel HTML-formatering. |
| **Microsoft CSP-återförsäljarens kanal\*** | Partner kanal för Cloud solution providers (CSP) är nu tillgängligt.  Se [leverantörer av moln lösningar](../../cloud-solution-providers.md) för mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner-kanaler. |
| **Marknadsförings-ID\***  | En unik URL som associeras med det här erbjudandet, och innehåller vanligt vis organisations-och lösnings namn, högst 50 tecken.  Ett exempel: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Förhandsgranska prenumerations-ID: n\*** | Lägg till en till 100 prenumerations-ID för för hands visare. Dessa vita prenumerationer kommer att ha åtkomst till erbjudandet när det har publicerats, innan det går live. |
| **Användbara länkar**          | Lägg till URL: er till dokument, viktig information, vanliga frågor och svar. |
| **Föreslagna kategorier\*** | Välj upp till två (2) kategorier, inklusive en primär och en sekundär kategori (valfritt). Välj upp till två (2) under Kategorier för varje primär och/eller sekundär kategori. Om ingen under kategori har valts kan du fortfarande bara identifiera i den valda kategorin. |
|  |  |


## <a name="marketing-artifacts-section"></a>Avsnittet marknadsförings artefakter

Det här andra avsnittet är uppdelat i tre underavsnitt: **logo typer**, **skärm**bilder och **videor**. Logo typer är de enda marknadsförings artefakter som krävs, men alla är starkt rekommenderade för bästa möjliga kund överklaga. 

![Avsnittet marknadsförings artefakter på fliken Marketplace i formuläret ny erbjudande för virtuella datorer](./media/publishvm_009.png)

I följande tabell beskrivs syftet med och innehållet i dessa fält. Obligatoriska fält förutsägs av en asterisk (*).

|  **Fält**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| *Typerna*  |  |
| **Liten\***                 | 40x40 pixel. ico-bitmapp                                                      |
| **Medel\***                | 90x90 pixel. ico-bitmapp                                                      |
| **Stor\***                 | 115x115 pixel. ico-bitmapp                                                   |
| **Bred\***                  | 255x115 pixel. ico-bitmapp                                                    |
| **Hjälte**                  | 815x290-bitmapp.  Valfritt, men när du har laddat upp hjälte ikonen kan du inte ta bort den. |
| *Par*  | Valfritt, men högst fem skärm bilder per SKU. |
| **Namn**                  | Namn eller rubrik <!-- TODO - max char length? none specified in UI -->                               |
| **Avbildning**                 | Skärmdump, 533x324-bild punkt                                         |
| *Videoklipp*  |  |
| **Namn**                  | Namn eller rubrik  <!-- TODO - max char length? -->                              |
| **Länk**                  | Video-URL, som finns på YouTube eller Vimeo                                        |
| **Miniatyr**             | 533x324-bitmapp                                                               |
|   |   |

### <a name="logo-guidelines"></a>Riktlinjer för logotyp

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Alla logo typer som laddats upp till Cloud Partner Portal bör följa rikt linjerna:

*  Azure-designen har en enkel färgpalett. Behåll antalet primära och sekundära färger på din logo typ låg.
*  Tema färgerna för Azure Portal är vita och svarta. Undvik därför att använda dessa färger som bakgrunds färg för dina logo typer. Använd en färg som gör logo typerna framträdande i Azure Portal. Vi rekommenderar enkla primärfärger. Om du använder genomskinlig bakgrund ser du till att logo typerna/texten inte är vita eller svarta eller blå.
*  Använd inte en tonings bakgrund på din logo typ.
*  Undvik att placera text – även ditt företags-eller märkes namn – på logo typen. Utseendet på logo typen bör vara "flat" och undvika toningar.
*  Sträck inte ut logo typen.

#### <a name="hero-logo"></a>Hero-logotyp

Hjältens logo typ är frivillig. När du har laddat upp kan du dock inte ta bort hjälte ikonen.  Ikonen för hjälte-logotypen bör följa rikt linjerna:

*  Svarta, vita och genomskinliga bakgrunder är inte tillåtna för hjälteiska ikoner.
*  Undvik att använda en ljus färg som bakgrund i hjälte ikonen.  Visnings namnet för utgivaren, plan rubriken och den långa sammanfattningen av erbjudandet visas i vit tecken färg och måste synas mot bakgrunden.
*  Undvik att använda den mesta texten när du utformar hjältens logo typ.  Utgivar namnet, plan rubriken, den långa sammanfattningen för erbjudandet och en skapa-knapp bäddas in program mässigt inuti hjälte ikonen när erbjudande listorna visas. 
* Ta med en oanvänd rektangel på den högra sidan av din hjälte ikon, med storlek 415x100 pixel och förskjutning 370 BPT från vänster.  

Som exempel är följande hjälte ikon för Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Exempel på hjälte ikon för Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Exempel på marknadsförings information 

Följande bild visar hur marknadsförings information visas på sidan huvud produkt för Microsoft Windows Server.

![Exempel på produkt sida för Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Avsnittet ledar hantering

I det tredje avsnittet kan du samla in kunders leads som genereras från dina Azure Marketplace-erbjudanden. Den erbjuder följande lagrings alternativ (från en listruta) för den här lead-informationen.

* **Ingen** -standardvärdet för lead-information samlas inte in.
* Azure Table-skriven till Azure-tabellen som anges av en anslutnings sträng.
* Dynamics CRM Online – skrivet till [Microsoft Dynamics 365 online](https://dynamics.microsoft.com/) -instansen som anges av en URL och autentiseringsuppgifter för autentisering.
* HTTPS-slutpunkt – skrivs till den angivna HTTPS-slutpunkten som en JSON-nyttolast.
* Marketo-skriven på den angivna [Marketo](https://www.marketo.com/) -instansen, anges av Server-ID, Munchkin-ID och formulär-ID.
* Salesforce-skriven till en [Salesforce](https://www.salesforce.com/) -databas som anges av en objekt identifierare.

När du har publicerat erbjudandet verifieras ledar anslutningen och ett test-lead skickas automatiskt till den konfigurerade destinationen. Lead-information ska hanteras kontinuerligt och de här inställningarna bör uppdateras när ändringar görs i din kund hanterings arkitektur.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Juridisk sektion

I det sista avsnittet kan du ange de juridiska dokument som krävs för varje erbjudande.  

|  **Fält**                    |     **Beskrivning**                                        |
|  ---------                    |     ---------------                                        |
| **URL för sekretess policy\***      | URL till din publicerade sekretess policy                          |
| **Använd standard kontrakt?\***  |   |
| **Användningsvillkor\***            | princip som oformaterad text eller enkel HTML.                       |
|  |  |


## <a name="next-steps"></a>Nästa steg

På nästa [support](./cpp-support-tab.md) -flik får du teknisk support och support resurser för ditt erbjudande.
