---
title: Azure-program erbjuder Marketplace fliken | Microsoft Docs
description: Använd fliken Marketplace för att identifiera marknadsföring tillgångar för ett erbjudande för Azure-program.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 1203f33458c83bbde7a263022b78563bad183143
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745424"
---
# <a name="azure-application-marketplace-tab"></a>Azure Marketplace programfliken

Använd fliken Marketplace för att beskriva ditt Azure-program och ange marknadsföring tillgångar. Den här fliken innehåller följande sätt: Översikt över, marknadsföring artefakter, Lead hantering och juridiska krav.

## <a name="overview-form"></a>Översiktsformulär

Översikt över formuläret har obligatoriska och valfria fält som visas i nästa skärmdump. Required fields are indicted by an asterisk (*).

![Översiktsformulär](./media/azureapp-marketplace-overview.png)

I följande tabell beskrivs inställningarna som ska användas för att skapa en butik för erbjudandet.

|    **Fält**       |  **Beskrivning**  |
|  ---------------   |  ---------------  |
|        Rubrik            |        Rubrik för erbjudandet. Det ska visas på en framträdande plats i marketplace. Den maximala längden är 50 tecken.           |
|         Sammanfattning           |        Kort sammanfattning av erbjudandet. Den maximala längden är 100 tecken.           |
|         Lång sammanfattning           |        Längre sammanfattning av erbjudandet (även om det kan vara samma som sammanfattningen). Den maximala längden är 256 tecken.           |
|       Beskrivning            |         Beskrivning av erbjudandet. Den maximala längden är 3000 tecken. Enkel HTML-formatering tillåts, inklusive &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; och huvud-taggar.           |
|        Identifierare för marknadsföring            |         En unik URL att koppla till det här erbjudandet innehåller normalt sett din organisation och lösningsnamn, maximal längd 50 tecken. Välj en kort, eget marknadsföring identifierare för din tjänst. Detta ska användas i marketplace URL: er för det här erbjudandet. Till exempel om Publicerings-ID är ”contoso” och din marknadsföring ID är ”exempelapp för”, blir URL: en för ditt erbjudande i Azure Marketplace https://azuremarketplace.microsoft.com/en-us/marketplace/apps/contoso.sampleApp          |
|        Förhandsversion av prenumerations-ID: N            |         Lägg till en till 100 prenumeration identifierarna för förhandsgranskningar. Dessa white visas prenumerationer har åtkomst till ditt erbjudande även om det är tillgängligt i förhandsversionen när det publiceras innan det lanseras.          |
|         Användbara länkar           |        Du kan ange länkar till olika resurser för användare av ditt erbjudande, support, dokumentation, forum, t.ex. Se till att lägga till minst en länk till dokumentationen.            |
|         Föreslås kategorier (Max 5)           |         Välj upp till fem kategorier. De valda kategorierna används för att mappa ditt erbjudande till produktkategorier som är tillgängliga i Azure Marketplace och Azure-portalen. De kommer att visas på Bläddra sidor och på din produktsidan för information.          |

## <a name="marketing-artifacts"></a>Marknadsföring artefakter

Obligatoriska och valfria fält som visas i nästa skärmdumpen har formuläret marknadsföring artefakter. Required fields are indicted by an asterisk (*).

![Marknadsföring artefakter formulär](./media/azureapp-marketplace-artifacts.png)

I följande tabell beskrivs marknadsföring artefakter.

|    **Fält**       |  **Beskrivning**  |
|  ---------------   |  ---------------  |
|       Liten             |        40 x 40 bildpunkter i PNG-format           |
|       Medel             |        90 x 90 bildpunkter i PNG-format           |
|       Stor             |        115 x 115 bildpunkter i PNG-format         |
|       Brett             |        255 x 115 bildpunkter i PNG-format           |
|       Hjälte             |        815 x 290 bildpunkter i PNG-format. Valfri. **Obs!** Ikonen hero-komponenten kan inte tas bort när paketet har överförts.           |
|       Skärmbilder (Max 5)             |        Skärmbilder visas på din produktsidan för information. Det är ett bra sätt att förmedla visuellt vad appen gör och hur det fungerar. Du kan till exempel visa Arkitekturdiagram eller använda fallet illustrationer. Skärmbilderna är valfria, och du begränsad till 5 per SKU. Lägga till en skärmbild:<ul><li>Välj **+ Lägg till skärmbild** att öppna fönstret skärmbild</li><li>**Namn på** -ange ett namn/rubrik (maximal längd på 100 tecken).</li><li>**Ladda upp** – ladda upp avbildningen. Det måste vara i PNG-format och storleken är 533 x 324 bildpunkter.</li></ul>           |
|        Lägg till video            |        Videor visas på din produktsidan för information. Det är ett bra sätt att förmedla visuellt vad ditt program gör och hur det fungerar. Lägga till en video: <ul><li>Välj **+ Lägg till video** att öppna fönstret Video</li><li>**Namn på** -ange ett namn/rubrik (maximal längd på 100 tecken).</li><li>**Länken** – ange Webbadressen för den plats som är värd för video (YouTube eller Vimeo)</li><li>**Miniatyr** – ladda upp en miniatyrbild. Det måste vara i PNG-format och storleken är 533 x 324 bildpunkter.</li></ul>          |

### <a name="artifact-examples-in-azure-marketplace"></a>Artefakten exemplen i Azure Marketplace

Nästa skärmdump visar ett exempel på ett Marketplace-sökresultat.

![Sökresultat för Marketplace-erbjudande](./media/azureapp-marketplace-example-browse.png)

Följande bild visar hur erbjudandet visas i Marketplace efter en kund klickar på erbjudandets panel i sökresultatet.

![Information om Marketplace-erbjudande search resultat](./media/azureapp-marketplace-example-details.png)

### <a name="artifact-examples-in-azure-portal"></a>Artefakten exemplen i Azure Portal

Följande skärmdumpar visar hur ett erbjudande visas i Azure Portal. Program-erbjudandet i det här exemplet hittas genom att bläddra till **Marketplace > allt > UTV + Test > Jenkins**. Jenkins-erbjudandet visar en logotyp, titel och visningsnamn för utgivare.

![Bläddra erbjudanden i Azure-portalen](./media/azureapp-portalbrowse-artifacts-jenkins.png)

Nästa skärmdump visar detaljerad information om programmet när en användare väljer Jenkins.

![Information om erbjudandet i Azure-portalen](./media/azureapp-portal-artifacts-jenkins-details.png)

### <a name="logo-guidelines"></a>Riktlinjer för logotyp

Alla logotyper som överförts till Cloud Partner Portal bör följa riktlinjerna:

- Azure-designen har en enkel färgpalett. Håll nere antalet primära och sekundära färger på din logotyp.
- Temafärger i Azure-portalen är vit och svart. Undvik att använda dessa färger som bakgrundsfärg för din logotyper. Använd en färg som gör din logotyper framstående i Azure-portalen. Vi rekommenderar enkla primärfärger. Om du använder en genomskinlig bakgrund, se till att logotyper/text inte är vit, svart eller blå.
- Använd inte en toning bakgrund på din logotyp.
- Undvik att placera text, även företaget eller varumärke namn på logotypen. Utseendet och känslan av din logotyp måste vara ”fast” och Undvik toningar.
- Inte sträcka ut logotypen.

#### <a name="hero-logo"></a>Hero-logotyp

Hero-logotypen är valfritt.

>[!IMPORTANT]
>Du kan inte ta bort Hero-logotypen när paketet har överförts.

Använd följande riktlinjer för en Hero-logotyp:

- Svart, vit och transparent bakgrund är inte tillåtna.
- Undvik att använda alla ljusare som bakgrund för logotypen. Utgivaren visningsnamn, plan rubrik och erbjudandet lång sammanfattning visas i vit teckenfärg och måste skilja mot bakgrunden.
- Undvik att använda de flesta text när du utformar logotypen. Utgivarens namn, plan rubrik, lång sammanfattning av erbjudandet och en knappen Skapa bäddas programmässigt inuti logotypen när erbjudandets visas.
- Inkludera en rektangulär outnyttjat utrymme till höger logotypens hero. Det här tomt utrymme är 415 × 100 bildpunkter och förskjutning från vänster med 370 bildpunkter.

## <a name="lead-management"></a>Lead-hantering

Formuläret leda Management har ett valfritt fält att konfigurera lead-hantering. Välj Lead-målet i listrutan om du vill konfigurera lead-hantering. Nästa skärmdump visar tillgängliga mål.

![Välj lead management mål](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Välj informationsikonen ska kunna se meddelandet: ”Välj systemet där dina leads ska lagras. Lär dig hur du ansluter till CRM-systemet [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) ”.


Mer information finns i [konfigurera kundleads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).

## <a name="legal"></a>Juridisk information

Använda juridiska formuläret för att tillhandahålla juridisk dokumentation som krävs för varje erbjudande.

Ange följande information:

- **Sekretesswebbadress för principen** – ange en länk till din Apps sekretesspolicy.
- **Användningsvillkor** – ange villkor för användning av din app. Kunder måste acceptera villkoren innan de kan testa din app.

![Juridiska formulär](./media/azureapp-marketplace-legal.png)

## <a name="next-steps"></a>Nästa steg

[fliken Stöd](./cpp-support-tab.md)