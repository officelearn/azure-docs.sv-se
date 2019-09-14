---
title: Fliken Marketplace för Azure-program
description: Använd fliken Marketplace för att identifiera marknadsförings till gångar för ett erbjudande för Azure-program.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: b5d54e4c6744675da0dea5b095590b71df6d2641
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962866"
---
# <a name="azure-application-marketplace-tab"></a>Fliken Azure Application Marketplace

Använd fliken Marketplace för att beskriva ditt Azure-program och tillhandahålla marknadsförings till gångar. Den här fliken innehåller följande formulär: Översikt, marknadsförings artefakter, ledar hantering och juridisk information.

## <a name="overview-form"></a>Översikts formulär

Formuläret översikt innehåller de obligatoriska och valfria fälten som visas i nästa skärmdump. Obligatoriska fält förutsägs av en asterisk (*).

![Översikts formulär](./media/azureapp-marketplace-overview.png)

I följande tabell beskrivs de inställningar som används för att skapa en butik för erbjudandet.   Fälten som läggs till med en asterisk måste anges.

|      Fält         |    Beskrivning    |
|  ---------------   |  ---------------  |
| **Avdelning\***        | Rubriken på erbjudandet. Den visas på ett framträdande sätt i Marketplace. Den maximala längden är 50 tecken. |
| **Drag\***      | Kort sammanfattning av erbjudandet. Den maximala längden är 100 tecken.           |
| **Lång Sammanfattning\*** | Längre sammanfattning av erbjudandet (även om det kan vara samma som i sammanfattningen). Den maximala längden är 256 tecken.           |
| **Beteckning\***  | Beskrivning av erbjudandet. Den maximala längden är 3000 tecken. Enkel HTML-formatering tillåts, inklusive &lt;p&gt;- &lt;,&gt;EM &lt;-&gt;, &lt;UL&gt;-,&gt; Li-, OL- &lt;och Head-taggar.  |
| **Marknadsförings-ID\*** | En unik URL som associeras med det här erbjudandet, och innehåller vanligt vis organisations-och lösnings namn, högst 50 tecken. Välj ett kort, eget marknadsförings-ID för din tjänst. Detta kommer att användas i Marketplace-URL: er för det här erbjudandet. Om ditt utgivar-ID t. ex. är "contoso" och marknadsförings identifieraren är "fråga" kommer URL: en för ditt erbjudande i Azure Marketplace att vara https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp  
| **Förhandsgranska prenumerations-ID: n\*** | Lägg till från en till 100 prenumerations-ID för för hands visare. De här vita prenumerationerna kommer att ha åtkomst till ditt erbjudande när det är tillgängligt i för hands versionen när det har publicerats, innan det går live.          |
| **Användbara länkar**    | Du kan också ange länkar till olika resurser för användare av ditt erbjudande, till exempel support, dokumentation, forum osv.  Vi rekommenderar att du lägger till minst en länk till din dokumentation.            |
| **Föreslagna kategorier (max 5)\*** | Välj en till fem kategorier. De valda kategorierna används för att mappa ditt erbjudande till produkt kategorierna som är tillgängliga i Azure Marketplace och Azure Portal. De visas på sidan Sök efter sidor och på produkt informations sidan. |
|  |  |


## <a name="marketing-artifacts"></a>Marknadsförings artefakter

Formuläret marknadsförings artefakter har de obligatoriska och valfria fälten som visas i nästa skärmdump. Obligatoriska fält förutsägs av en asterisk (*).

![Formuläret marknadsförings artefakter](./media/azureapp-marketplace-artifacts.png)

I följande tabell beskrivs marknadsförings artefakterna.

|      Fält         |    Beskrivning    |
|  ---------------   |  ---------------  |
| **Små\***        | Liten logo typ: 40x40 bild punkter i PNG-format     |
| **Säker\***       | Medelhög logo typ: 90x90 bild punkter i PNG-format    |
| **Förstoring\***        | Stor logo typ: 115x115 bild punkter i PNG-format   |
| **Täcka\***         | Bred logo typ: 255x115 bild punkter i PNG-format    |
| **Hjälte**           | Valfri hjälte logo typ: 815x290 bild punkter i PNG-format. **Obs:** Det går inte att ta bort hjälte ikonen när den har laddats upp. |
| **Skärm bilder (max 5)** |        Skärm dum par visas på produkt informations sidan. De är ett bra sätt att visuellt förmedla vad din app gör och hur det fungerar. Du kan till exempel Visa arkitektur diagram eller användnings Falls bilder. Skärm dum par är valfria och du kan begränsa till 5 per SKU. Så här lägger du till en skärm bild:<ul><li>Välj **+ Lägg till skärm bild** för att öppna skärm bilds fönstret</li><li>**Namn** – ange ett namn/en rubrik (högst 100 tecken.)</li><li>**Ladda upp** – Ladda upp avbildningen. Det måste vara i PNG-format och storleken är 533 x 324 bild punkter.</li></ul>           |
| **Lägg till video**      | Som tillval visas videor på din produkt informations sida. De är ett bra sätt att visuellt förmedla vad ditt program gör och hur det fungerar. Så här lägger du till en video: <ul><li>Öppna video fönstret genom att välja **+ Lägg till video**</li><li>**Namn** – ange ett namn/en rubrik (högst 100 tecken.)</li><li>**Länk** – ange URL: en för den webbplats som är värd för videon (YouTube eller Vimeo)</li><li>**Miniatyr** – Ladda upp en miniatyr bild. Det måste vara i PNG-format och storleken är 533 x 324 bild punkter.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Artefakt exempel i Azure Marketplace

I nästa skärm bild visas ett exempel på ett Sök resultat i Marketplace.

![Sök Resultat för Marketplace-erbjudandet](./media/azureapp-marketplace-example-browse.png)

Följande bild visar hur erbjudandet visas på Marketplace när en kund klickar på erbjudandet i Sök resultatet.

![Sök Resultat information för Marketplace-erbjudandet](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Artefakt exempel i Azure Portal

Följande skärm dum par visar hur ett erbjudande visas i Azure Portal. Program erbjudandet i det här exemplet hittas genom att bläddra till **Marketplace > allt > dev + Test > Jenkins**. Jenkins-erbjudandet visar en logo typ, rubrik och visnings namn för utgivare.

![Sök efter erbjudanden i Azure Portal](./media/azureapp-portalbrowse-artifacts-jenkins.png)

Nästa skärm bild visar detaljerad information om programmet när en användare väljer Jenkins.

![Information om erbjudandet i Azure Portal](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Riktlinjer för logotyp

Alla logo typer som laddats upp till Cloud Partner Portal bör följa rikt linjerna:

- Azure-designen har en enkel färgpalett. Behåll antalet primära och sekundära färger på din logo typ låg.
- Tema färgerna i Azure-portalen är vita och svarta. Undvik att använda dessa färger som bakgrunds färg för dina logo typer. Använd en färg som gör logo typerna framträdande i Azure Portal. Vi rekommenderar enkla primärfärger. Om du använder en genomskinlig bakgrund ser du till att logo typerna/texten inte är vita, svarta eller blå.
- Använd inte en tonings bakgrund på din logo typ.
- Undvik att placera text, till och med ditt företags-eller märkes namn på logo typen. Utseendet på logo typen bör vara "flat" och undvika toningar.
- Sträck inte ut logo typen.


#### <a name="hero-logo"></a>Hero-logotyp

Hjältens logo typ är valfri.

>[!IMPORTANT]
>Du kan inte ta bort hjälte logo typen när den har laddats upp.

Använd följande rikt linjer för en hjälte logo typ:

- Svarta, vita och transparenta bakgrunder är inte tillåtna.
- Undvik att använda en ljus färg som bakgrund för logo typen. Visnings namnet för utgivaren, plan rubriken och den långa sammanfattningen av erbjudandet visas i vit tecken färg och måste synas mot bakgrunden.
- Undvik att använda den mesta texten när du utformar logo typen. Utgivar namnet, plan rubriken, erbjudandet lång Sammanfattning och en knappen Skapa bäddas in program mässigt inuti logo typen när erbjudandet visas.
- Ta med ett oanvänt rektangulärt utrymme på den högra sidan av din hjälte logo typ. Det tomma utrymmet är 415x100 bild punkter och förskjutning från vänster med 370 bild punkter.


## <a name="lead-management"></a>Hantering av lead

Formuläret lead management har ett valfritt fält för att konfigurera hantering av leads. Om du vill konfigurera hantering av lead väljer du målet för leadet i list rutan. I nästa skärm bild visas tillgängliga destinationer.

![Välj mål för lead-hantering](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Välj informations ikonen för att se det här meddelandet: "Välj det system där dina leads ska lagras. Lär dig hur du ansluter till ditt CRM-system [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) . "

Mer information finns i [Konfigurera kund leads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Juridisk information

Använd juridisk form för att tillhandahålla juridisk dokumentation som krävs för varje erbjudande.

Ange följande information:

- **URL\* för sekretess policy** – ange en länk till appens sekretess policy.
- **Användningsvillkor\***  – ange användnings villkoren för din app. Kunder måste acceptera dessa villkor innan de kan testa din app.

![Juridisk form](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Nästa steg

[fliken Stöd](./cpp-support-tab.md)
