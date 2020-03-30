---
title: Fliken Marketplace-program för Azure-program erbjuder
description: Använd fliken Marketplace för att identifiera marknadsföringsresurser för ett Azure-programerbjudande.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 94bbfb16a967a97b1ee6f6d51a5f55bc8ba13227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281773"
---
# <a name="azure-application-marketplace-tab"></a>Fliken Azure-program Marketplace

Använd fliken Marketplace för att beskriva ditt Azure-program och tillhandahålla marknadsföringsresurser. Den här fliken innehåller följande formulär: Översikt, Marknadsföringsartefakter, LeadHantering och Legal.

## <a name="overview-form"></a>Formulär över översikt

Formuläret Översikt har de obligatoriska och valfria fält som visas i nästa skärminspelning. Obligatoriska fält åtalas av en asterisk (*).

![Formulär över översikt](./media/azureapp-marketplace-overview.png)

I följande tabell beskrivs de inställningar som ska användas för att skapa ett skyltfönster för erbjudandet.   De fält som läggs till med en asterisk krävs.

|      Field         |    Beskrivning    |
|  ---------------   |  ---------------  |
| **Titel\***        | Erbjudandets titel. Det kommer att visas tydligt på marknaden. Den maximala längden är 50 tecken. |
| **Sammanfattning\***      | Kort sammanfattning av erbjudandet. Den maximala längden är 100 tecken.           |
| **Lång sammanfattning\*** | Längre sammanfattning av erbjudandet (även om det kan vara samma som sammanfattningen). Den maximala längden är 256 tecken.           |
| **Beskrivning\***  | Beskrivning av erbjudandet. Den maximala längden är 3000 tecken. Enkel HTML-formatering är &lt;tillåten, inklusive&gt; &lt;&gt;p, &lt;&gt; &gt; &lt;em&gt;, &lt;ul , li, ol och header tags.  |
| **Marknadsidentifierare\*** | En unik URL som ska associeras med det här erbjudandet, innehåller vanligtvis ditt organisations- och lösningsnamn, maximal längd på 50 tecken. Välj en kort, vänlig marknadsföringsidentifierare för din tjänst. Detta kommer att användas i marknadsplats url:er för det här erbjudandet. Om ditt utgivar-ID till exempel är "contoso" och din marknadsföringsidentifierare är "sampleApp", kommer url:en för ditt erbjudande på Azure Marketplace atthttps://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp  
| **Förhandsgranska prenumerations-ID:er\*** | Lägg till från en till 100 prenumerationsidentifierare för förhandsgranskningsspelare. Dessa vitlistade prenumerationer har tillgång till ditt erbjudande medan det är tillgängligt i förhandsversion när det har publicerats, innan det publiceras.          |
| **Användbara länkar**    | Alternativt kan du tillhandahålla länkar till olika resurser för användare av ditt erbjudande, till exempel support, dokumentation, forum, etc.  Vi rekommenderar att du lägger till minst en länk i dokumentationen.            |
| **Föreslagna kategorier (max 5)\*** | Välj en till fem kategorier. De valda kategorierna används för att mappa ditt erbjudande till de produktkategorier som är tillgängliga i Azure Marketplace och Azure Portal. De visas på bläddra sidor och på din produktinformation sida. |
|  |  |


## <a name="marketing-artifacts"></a>Marknadsföring Artefakter

Formuläret Marknadsföringsartefakter har de obligatoriska och valfria fält som visas i nästa skärminspelning. Obligatoriska fält åtalas av en asterisk (*).

![Formuläret Marknadsföringsartefakter](./media/azureapp-marketplace-artifacts.png)

I följande tabell beskrivs marknadsföringsartefakterna.

|      Field         |    Beskrivning    |
|  ---------------   |  ---------------  |
| **Liten\***        | Liten logotyp: 40x40 pixlar i PNG-format     |
| **Medium\***       | Medelhög logotyp: 90x90 pixlar i PNG-format    |
| **Stor\***        | Stor logotyp: 115x115 pixlar i PNG-format   |
| **Brett\***         | Bred logotyp: 255 x 115 pixlar i PNG-format    |
| **Hjälte**           | Valfri hjältelogotyp: 815x290 pixlar i PNG-format. **Anm.:** Hjälteikonen kan inte tas bort när den har laddats upp. |
| **Skärmdumpar (Max 5)** |        Skärmbilder visas på produktinformationssidan. De är ett bra sätt att visuellt kommunicera vad din app gör och hur den fungerar. Du kan till exempel visa arkitekturdiagram eller använda skiftlägesillustrationer. Skärmbilder är valfria och du är begränsad till 5 per SKU. Så här lägger du till en skärmbild:<ul><li>Välj **+ Lägg till skärmbild** för att öppna skärmbildsfönstret</li><li>**Namn** - Ange ett namn/en titel (Maximal längd på 100 tecken.)</li><li>**Ladda upp** - Ladda upp bilden. Den måste vara i PNG-format och storleken är 533 x 324 pixlar.</li></ul>           |
| **Lägg till video**      | Videor visas som tillval på produktinformationssidan. De är ett bra sätt att visuellt kommunicera vad ditt program gör och hur det fungerar. Så här lägger du till en video: <ul><li>Välj **+ Lägg till video** för att öppna videofönstret</li><li>**Namn** - Ange ett namn/en titel (Maximal längd på 100 tecken.)</li><li>**Länk** - Ange webbadressen för den webbplats som är värd för videon (YouTube eller Vimeo)</li><li>**Miniatyr** - Ladda upp en miniatyrbild. Den måste vara i PNG-format och storleken är 533 x 324 pixlar.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Exempel på artefakt på Azure Marketplace

Nästa skärminspelning visar ett exempel på ett Marketplace-sökresultat.

![Marketplace erbjuder sökresultat](./media/azureapp-marketplace-example-browse.png)

Följande bild visar hur erbjudandet visas på Marketplace efter att en kund klickar på erbjudandets panel i sökresultatet.

![Marketplace erbjuder sökresultatinformation](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Artefaktexempel i Azure Portal

Följande skärminspelningar visar hur ett erbjudande visas i Azure Portal. Ansökan erbjudandet i det här exemplet finns genom att bläddra till **Marketplace>Allt>Dev + Test>Jenkins**. Jenkins-erbjudandet visar en logotyp, en titel och ett visningsnamn för utgivaren.

![Bläddra bland erbjudanden i Azure-portalen](./media/azureapp-portalbrowse-artifacts-jenkins.png)

Nästa skärminspelning visar detaljerad information om programmet när en användare väljer Jenkins.

![Erbjudandeinformation i Azure-portalen](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Riktlinjer för logotyp

Alla logotyper som laddas upp till Cloud Partner Portal bör följa riktlinjerna:

- Azure-designen har en enkel färgpalett. Håll antalet primära och sekundära färger på logotypen lågt.
- Temafärgerna i Azure Portal är vita och svarta. Undvik att använda dessa färger som bakgrundsfärg för logotyperna. Använd en färg som gör dina logotyper framträdande i Azure-portalen. Vi rekommenderar enkla primärfärger. Om du använder en genomskinlig bakgrund kontrollerar du att logotyperna/texten inte är vita, svarta eller blå.
- Använd inte en övertoningsbakgrund på logotypen.
- Undvik att placera text, även ditt företags eller varumärke, på logotypen. Utseendet och känslan på din logotyp ska vara "platt" och bör undvika lutningar.
- Sträck inte ut logotypen.


#### <a name="hero-logo"></a>Hero-logotyp

Hero-logotypen är valfri.

>[!IMPORTANT]
>Du kan inte ta bort Hero-logotypen när den har laddats upp.

Använd följande riktlinjer för en Hero-logotyp:

- Svarta, vita och genomskinliga bakgrunder är inte tillåtna.
- Undvik att använda ljus färg som bakgrund för logotypen. Utgivarens visningsnamn, planrubrik och erbjudandets långa sammanfattning visas i vit teckenfärg och måste sticka ut mot bakgrunden.
- Undvik att använda de flesta text när du utformar logotypen. Utgivarens namn, plantitel, erbjudandets långa sammanfattning och en skapa-knapp bäddas in programmässigt i logotypen när erbjudandet visas.
- Inkludera ett oanvänt rektangulärt utrymme till höger om din hjältelogotyp. Det här tomma utrymmet är 415x100 pixlar och förskjuts från vänster med 370 pixlar.


## <a name="lead-management"></a>Lead Management

Formuläret Lead Management har ett valfritt fält för att konfigurera leadhantering. Om du vill konfigurera leadhantering väljer du leadmålet i listrutan. Nästa skärminspelning visar tillgängliga destinationer.

![Välj mål för leadhantering](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Välj informationsikonen för att se det här meddelandet: "Välj det system där dina leads ska lagras. Läs om hur du ansluter till crm-systemet [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) ."

Mer information finns i [Konfigurera kundleads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Juridisk information

Använd formuläret Juridiska formulär för att tillhandahålla den juridiska dokumentation som krävs för varje erbjudande.

Ange följande information:

- **Url till\* sekretesspolicy** – Ange en länk till appens sekretesspolicy.
- **Användarvillkor\* ** - Ange användningsvillkoren för din app. Kunderna måste acceptera dessa villkor innan de kan prova din app.

![Juridisk form](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Nästa steg

[fliken Stöd](./cpp-support-tab.md)
