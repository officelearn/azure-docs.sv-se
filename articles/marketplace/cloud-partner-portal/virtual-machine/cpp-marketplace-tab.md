---
title: Fliken Marknadsplats för virtuella datorer i Cloud Partner Portal för Azure Marketplace
description: Beskriver fliken Marketplace som används för att skapa ett Azure Marketplace VM-erbjudande.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 567dc83e36020ef9af328f52dae89ef37b6e4908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277729"
---
# <a name="virtual-machine-marketplace-tab"></a>Fliken Marknadsplats för virtuell dator

På fliken **Marketplace** på sidan **Nytt erbjudande** kan du förse dina potentiella kunder med marknadsföring, försäljning och juridisk information och avtal och hantera leads som genereras från marknadsplatsen. Det här långa formuläret är indelat i fyra avsnitt: **Översikt**, **Marknadsföringsartefakter**, **Lead Management**och **Legal**.


## <a name="overview-section"></a>Avsnittet Översikt
I det här avsnittet anger du allmän information om ditt Azure Marketplace-erbjudande.  En bifogad asterisk (*) i fältnamnet anger att det krävs.

![Avsnittet Översikt på fliken Marketplace för virtuella datorer](./media/publishvm_008.png)

I följande tabell beskrivs syftet med och innehållet i dessa fält. Obligatoriska fält åtalas av en asterisk (*).

|  **Field**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| **Titel\***                 | Erbjudandets titel, ofta det långa, formella namnet. Den här titeln visas tydligt på marknaden.  Maximal längd på 50 tecken. |
| **Sammanfattning\***               | Kort syfte eller funktion av lösningen.  Maximal längd på 100 tecken. |
| **Lång sammanfattning\***          | Lösningens syfte eller funktion.  Maximal längd på 256 tecken. |
| **Beskrivning\***           | Beskrivning av lösning.  Maximal längd på 3000 tecken, stöder enkel HTML-formatering. |
| **Microsoft CSP-återförsäljarkanal\*** | Opt-in (Cloud Solution Providers) partnerkanal är nu tillgänglig.  Se [Cloud Solution Providers](../../cloud-solution-providers.md) för mer information om marknadsföring ditt erbjudande via Microsoft CSP partnerkanaler. |
| **Marknadsidentifierare\***  | En unik URL som ska associeras med det här erbjudandet, innehåller vanligtvis ditt organisations- och lösningsnamn, maximal längd på 50 tecken.  Ett exempel: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Förhandsgranska prenumerations-ID:er\*** | Lägg till en till 100 prenumerationsidentifierare för förhandsgranskningsspelare. Dessa vitlistade prenumerationer har tillgång till erbjudandet när det har publicerats, innan det publiceras. |
| **Användbara länkar**          | Lägg till webbadresser i dokumentationer, viktig information, vanliga frågor och svar. |
| **Föreslagna kategorier\*** | Välj upp till två (2) kategorier, inklusive en primär och en sekundär kategori (valfritt). Välj upp till två (2) underkategorier för varje primär och/eller sekundär kategori. Om ingen underkategori har valts kan du fortfarande bara identifieras i den valda kategorin. |
|  |  |


## <a name="marketing-artifacts-section"></a>Avsnittet Marknadsföringsartefakter

Det andra avsnittet är uppdelat i tre underavsnitt: **Logotyper,** **Skärmbild**och **Videor**. Logotyper är de enda nödvändiga marknadsföring artefakter, men alla rekommenderas starkt för bästa kund överklagande. 

![Avsnittet Marknadsföringsartefakter på fliken Marketplace i formuläret Nytt erbjudande för virtuella datorer](./media/publishvm_009.png)

I följande tabell beskrivs syftet med och innehållet i dessa fält. Obligatoriska fält åtalas av en asterisk (*).

|  **Field**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| *Logotyper*  |  |
| **Liten\***                 | 40x40 pixel .ico bitmapp                                                      |
| **Medium\***                | 90x90 pixel .ico bitmapp                                                      |
| **Stor\***                 | 115x115 pixel .ico-bitmapp                                                   |
| **Brett\***                  | 255 x 115 pixlar .ico-bitmapp                                                    |
| **Hjälte**                  | 815x290 bitmapp.  Valfritt, men när du har laddat upp hjälteikonen kan inte tas bort. |
| *Skärmbilder*  | Valfritt, men högst fem skärmbilder per SKU. |
| **Namn**                  | Namn eller titel <!-- TODO - max char length? none specified in UI -->                               |
| **Bild**                 | Bild av skärmtagning, 533x324 pixel                                         |
| *Videor*  |  |
| **Namn**                  | Namn eller titel  <!-- TODO - max char length? -->                              |
| **Länk**                  | Video-URL, som finns på YouTube eller Vimeo                                        |
| **Miniatyr**             | 533x324 bitmapp                                                               |
|   |   |

### <a name="logo-guidelines"></a>Riktlinjer för logotyp

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Alla logotyper som laddas upp till Cloud Partner Portal bör följa riktlinjerna:

*  Azure-designen har en enkel färgpalett. Håll antalet primära och sekundära färger på logotypen lågt.
*  Temafärgerna i Azure-portalen är vita och svarta. Undvik därför att använda dessa färger som bakgrundsfärg på logotyperna. Använd en färg som gör dina logotyper framträdande i Azure-portalen. Vi rekommenderar enkla primärfärger. Om du använder genomskinlig bakgrund kontrollerar du att logotyperna/texten inte är vita eller svarta eller blå.
*  Använd inte en övertoningsbakgrund på logotypen.
*  Undvik att placera text – även ditt företags- eller varumärke – på logotypen. Utseendet och känslan på din logotyp ska vara "platt" och bör undvika lutningar.
*  Sträck inte ut logotypen.

#### <a name="hero-logo"></a>Hero-logotyp

Hero-logotypen är valfri. Men när den har laddats upp kan hjälteikonen inte tas bort.  Hero-ikonen bör följa riktlinjerna:

*  Svarta, vita och genomskinliga bakgrunder är inte tillåtna för hjälteikoner.
*  Undvik att använda någon ljus färg som bakgrund av hjälten ikonen.  Visningsnamnet, planrubriken och erbjudandets långa sammanfattning visas med vit teckenfärg och måste sticka ut mot bakgrunden.
*  Undvik att använda de flesta text medan du designar hjältelogotypen.  Utgivarens namn, plantitel, erbjudandets långa sammanfattning och en skapa-knapp bäddas in programmässigt i hjälteikonen när erbjudandet visas. 
* Inkludera en oanvänd rektangel till höger om din hjälteikon, med storlek 415x100 pixel och förskjutning 370 px från vänster.  

Som ett exempel är följande hjälteikon för Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Exempel på hjälteikon för Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Exempel på marknadsföringsinformation 

Följande bild visar hur marknadsföringsinformation visas på huvudproduktsidan för Microsoft Windows Server.

![Exempel på produktsida för Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Avsnittet Lead Management

I det tredje avsnittet kan du samla in kunders leads som genereras från dina Azure Marketplace-erbjudanden. Den erbjuder följande lagringsalternativ (från en listruta) för den här leadinformationen.

* **Ingen** - standardinformationen samlas inte in.
* Azure Table - skrivet till Azure-tabellen som anges av en anslutningssträng.
* Dynamics CRM Online - skrivet till [Microsoft Dynamics 365](https://dynamics.microsoft.com/) Online-instansen, som anges av en URL och autentiseringsuppgifter.
* HTTPS Slutpunkt - skrivet till den angivna HTTPS-slutpunkten som en JSON-nyttolast.
* Marketo - skrivet [Marketo](https://www.marketo.com/) till den angivna Marketo-instansen, angiven av server-ID, munchkin-ID och formulär-ID.
* Salesforce - skrivet till en [Salesforce-databas,](https://www.salesforce.com/) angiven av en objektidentifierare.

När du har publicerat erbjudandet valideras leadanslutningen och en testled skickas automatiskt till det konfigurerade målet. Leadinformation bör hanteras kontinuerligt och dessa inställningar bör uppdateras snabbt när ändringar görs i din kundhanteringsarkitektur.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Juridiska avsnitt

I det sista avsnittet kan du tillhandahålla de juridiska dokument som krävs för varje erbjudande.  

|  **Field**                    |     **Beskrivning**                                        |
|  ---------                    |     ---------------                                        |
| **Url till sekretesspolicy\***      | URL till din bokförda sekretesspolicy                          |
| **Vill du använda standardkontrakt?\***  |   |
| **Användningsvillkor\***            | princip som oformaterad text eller enkel HTML.                       |
|  |  |


## <a name="next-steps"></a>Nästa steg

På nästa [supportfliken](./cpp-support-tab.md) tillhandahåller du tekniska resurser och användarsupport för ditt erbjudande.
