---
title: Virtual machine Marketplace-fliken i Cloud Partner Portal för Azure | Microsoft Docs
description: Beskriver fliken Marketplace som används för att skapa ett erbjudande för Azure Marketplace-datorn.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 551f2bbc4846a1b47dafaa03cca08b424572ebf5
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214885"
---
# <a name="virtual-machine-marketplace-tab"></a>Virtual machine Marketplace-fliken

Den **Marketplace** fliken den **nytt erbjudande** sidan kan du ge din potentiella kunder marknadsföring, försäljning och juridisk information och avtal och hantera leads som genereras från den Marketplace. Den här diktering är uppdelad i fyra delar: **Översikt över**, **marknadsföring artefakter**, **leda Management**, och **juridiska**. 

## <a name="overview-section"></a>Översiktsavsnittet
I det här avsnittet ska ange du allmän information om dina Azure Marketplace erbjuder.  En tillagda asterisk (*) på fältnamnet indikerar att det krävs.

![Översiktsavsnittet Marketplace-fliken i formuläret nytt erbjudande för virtuella datorer](./media/publishvm_008.png)

I följande tabell beskrivs syftet med och innehållet i de här fälten.

|  **Fält**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| **Rubrik**                 | Rubrik för erbjudandet, ofta lång, formella namn. Den här rubriken visas på en framträdande plats i marketplace.  Högst 50 tecken. |
| **Sammanfattning**               | Kort syfte eller funktion på lösning.  Högst 100 tecken. |
| **Lång sammanfattning**          | Syfte eller funktion i lösningen.  Högst 256 tecken. |
| **Beskrivning**           | Beskrivning av lösning.  Maximal längd på 3000 tecken har stöd för enkel HTML-formatering. |
| **Microsoft CSP-återförsäljare kanal** | Cloud Solution Providers (CSP) partner channel anmälan är nu tillgänglig.  Se [Cloud Solution Providers](../../cloud-solution-providers.md) mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner kanaler. |
| **Identifierare för marknadsföring**  | En unik URL att koppla till det här erbjudandet innehåller normalt sett din organisation och lösningsnamn, maximal längd 50 tecken.  Exempel: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Förhandsversion av prenumerations-ID: N** | Lägg till en till 100 prenumeration identifierare av förhandsgranskningar. Dessa white visas prenumerationer har åtkomst till erbjudandet när den publiceras innan det lanseras. |
| **Användbara länkar**          | Lägga till URL: er till dokumentation, viktig information, vanliga frågor och svar och så vidare. |
| **Föreslås kategorier (Max 5)** | Flera val för verksamheten och teknisk kategorier som erbjuder kan kopplas bäst.  Maximalt fem tillåts.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Marknadsföring artefakter avsnittet

Den här andra delen är uppdelad i tre underavsnitt: **Logotyper**, **skärmbild**, och **videor**. Logotyper är den enda krävs marknadsföring artefakter, men alla rekommenderas för bästa kund ändring.

![Marknadsföring artefakter avsnittet Marketplace-fliken i formuläret nytt erbjudande för virtuella datorer](./media/publishvm_009.png)

|  **Fält**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| *Logotyper*  |  |
| **Liten**                 | 40 x 40 pixel .ico bitmapp                                                      |
| **Medel**                | 90 x 90 pixel .ico bitmapp                                                      |
| **Stora**                 | 115 x 115 pixel .ico bitmapp                                                   |
| **Brett**                  | 255 x 115 pixel .ico bitmapp                                                    |
| **Hero**                  | 815 x 290 bitmapp.  Valfritt, men en gång överförda ikonen hero-komponenten inte kan tas bort. |
| *Skärmbilder*  | Valfritt, men högst fem skärmbilder per SKU. |
| **Namn**                  | Namn eller rubrik <!-- TODO - max char length? none specified in UI -->                               |
| **Avbildning**                 | Skärmen infångningsavbildning, 533 x 324 pixel                                         |
| *Videoklipp*  |  |
| **Namn**                  | Namn eller rubrik  <!-- TODO - max char length? -->                              |
| **Länk**                  | Video-URL, finns på YouTube eller Vimeo                                        |
| **Miniatyr**             | 533 x 324 bitmapp                                                               |
|  |  |


### <a name="logo-guidelines"></a>Riktlinjer för logotyp

<!-- TD: It seems like this section could be better located in some common area, maybe a AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Alla logotyper som överförts till Cloud Partner Portal bör följa riktlinjerna:

*  Azure-designen har en enkel färgpalett. Håll nere antalet primära och sekundära färger på din logotyp.
*  Temafärger i Azure Portal är vit och svart. Undvik därför att använda dessa färger som bakgrundsfärgen för din logotyper. Använd en färg som gör din logotyper framstående i Azure-portalen. Vi rekommenderar enkla primärfärger. Om du använder genomskinlig bakgrund, se till att logotyper/text inte är vit eller svart eller blå.
*  Använd inte en toning bakgrund på din logotyp.
*  Undvika att placera text – till och med ditt företag eller varumärke – på logotypen. Utseendet och känslan av din logotyp måste vara ”fast” och Undvik toningar.
*  Stretch inte logotypen.

#### <a name="hero-logo"></a>Hero-logotyp

Hero-logotypen är valfritt. men när du har överfört ikonen hero-komponenten inte kan tas bort.  Ikonen Hero-logotypen bör följa riktlinjerna:

*  Svart, vit och transparent bakgrunder tillåts inte för hero-ikoner.
*  Undvik att använda alla ljusare som bakgrund till hero-ikonen.  Visningsnamn för utgivaren, plan rubrik och erbjudandet lång sammanfattning visas i vit teckenfärg och måste står ut mot bakgrunden.
*  Undvik att använda de flesta text när du utformar hero-logotypen.  Utgivarens namn, plan rubrik, lång sammanfattning av erbjudandet och en knappen Skapa bäddas programmässigt inuti hero-ikon när erbjudandet visas. 
* Inkludera en oanvända rektangel på höger sida av din hero-ikonen för storlek 415 × 100 bildpunkter och förskjutning 370 px från vänster.  

Till exempel är följande hero-ikon för Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Exempel hero-ikonen för Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Exempel på marknadsföring 

Följande bild visar hur marknadsföring information visas på den huvudsakliga produktsidan för Microsoft Windows Server.

![Exempel produktsida för Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Leda hanteringsavsnittet
<!-- this all should be referenced in a common location for lead management, not in this file. nothing unique for a vm specifically. -->

Det tredje avsnittet kan du samla in kunder leads som genereras från Azure Marketplace-erbjudanden. Den erbjuder följande lagringsalternativ (från en listruta) för den här lead-information.

* **Ingen** -standard, lead-information inte samlas in.
* Azure Table - skrivs till tabellen Azure som anges av en anslutningssträng.
* Dynamics CRM Online - skrivs till den [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) instans som anges av en URL och autentisering autentiseringsuppgifter.
* HTTPS-slutpunkt - skrivs till den angivna HTTPS-slutpunkten som en JSON-nyttolast.
* Marketo - skrivs till den angivna [Marketo](https://www.marketo.com/) instans som anges av server-ID, munchkin-ID och formulär-ID.
* Salesforce - skrivs till en [Salesforce](https://www.salesforce.com/) databas som anges av ett objekt-ID: T.

När du har publicerat ditt erbjudande, lead-anslutningen har verifierats och ett test-lead skickas automatiskt till det konfigurera målet. Lead-information ska hanteras kontinuerligt och dessa inställningar ska utan dröjsmål uppdateras när ändringar görs i din arkitektur för hantering av kunden.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Juridiska avsnittet

Det här sista avsnittet kan du ange två juridiska dokument som krävs för varje erbjudande: Sekretesspolicy och användningsvillkoren.

|  **Fält**                |     **Beskrivning**                                                          |
|  ---------                |     ---------------                                                          |
| **Sekretesswebbadress för principen**    | URL: en till din bokförda sekretesspolicy                                            |
| **Användningsvillkor**          | princip för som oformaterad text eller enkel HTML-kod.  <!-- TODO - max char length? -->       |
|  |  |

<br/>

I nästa [stöder](./cpp-support-tab.md) fliken du tillhandahåller teknisk hjälp och användaren Supportresurser för ditt erbjudande.

