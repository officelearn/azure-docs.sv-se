---
title: Erbjud skapa checklist - kommersiella Marketplace för Azure
description: Information som du kan ange under skapandeprocessen erbjudandet. -Kommersiella Marketplace för Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: eb824eb67e84ec4bdb93bc355ac6a6afa844ceb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701159"
---
# <a name="offer-creation-checklist"></a>Checklista för skapande av erbjudande

Process för att skapa erbjudandet tar dig igenom flera sidor. Här följer information kan du ge på varje sida, med länkar till mer information om varje objekt.

Objekt som du behöver ange eller ange anges nedan. Vissa områden är valfria eller har standardvärden anges så att du kan ändra efter behov. Du behöver att arbeta med dessa avsnitt i den ordning som visas här.

| **Objekt**    | **Syfte**  |
| :---------- | :-------------------|
| [**Nytt erbjudande Modal**](#new-offer-modal) | Samlar in erbjuder identitetsinformation.  |
| [Installationssidan för erbjudandet](#offer-setup-page) | Kan du välja att använda viktiga funktioner och välj hur du vill sälja ditt erbjudande via Microsoft.  |
| [Egenskapssidan](#properties-page) | Definiera kategorier och branscher som används för att gruppera ditt erbjudande i marknadsplatser, juridiska avtal som stöd för ditt erbjudande och din appversion. |
| [Erbjud lista sida](#offer-listing-page) | Definiera erbjudandeinformationen som ska visas i marketplace, inklusive beskrivningar av ditt erbjudande och marknadsföring tillgångar. |
| [Förhandsgranskningssidan](#preview-page) | Definiera en begränsad förhandsversion publik för lansera ditt erbjudande innan du publicerar ditt erbjudande live till den bredare marketplace-mottagare. |
| [Erbjud tekniska konfigurationssidan](#technical-configuration-page)  | Endast tillgänglig om du väljer för att sälja erbjudandet via Microsoft. Definiera de tekniska detaljerna (URL-sökvägen, webhook, klient-ID och app-ID) används för att ansluta till ditt erbjudande. |
| [**Ny Plan Modal**](#plan-identity-modal) | Samlar in planera identitetsinformation.  |
| [Planera lista sida](#plan-listing-page)  | Endast tillgänglig om du väljer för att sälja erbjudandet via Microsoft. Definiera information som används för att visa planen i marketplace.  |
| [Priserna för planer och tillgänglighet](#plan-pricing--availability-page)  | Endast tillgänglig om du väljer för att sälja erbjudandet via Microsoft.  Samlar in egenskaper för företag (prismodellen), målgrupp och marknaden tillgänglighet för varje plan (version) i ditt erbjudande.  |
| [Test Drive lista sida](#test-drive-listing-page)  | Endast tillgänglig om du väljer för att erbjuda en testenhet för ditt erbjudande. Definiera information som används för att lista provköra i marketplace.  |
| Teknisk konfigurationssidan för test-enhet  | Endast tillgänglig om du väljer för att erbjuda en testenhet för ditt erbjudande. Definiera de tekniska detaljerna för demonstration (eller ”Testkör”), vilket gör att kunder kan prova erbjudandet innan du bestämmer dig för att köpa den.  |
| [Granska och publicera sida](#review-and-publish-page)  | Välj de ändringar som du vill publicera, se status för varje sida och kommentarer till teamet för certifiering.  |


## <a name="new-offer-modal"></a>Nytt erbjudande modal 

De första uppgifter du blir ombedd att tillhandahålla är ett ID och alias för ditt erbjudande. 

| **Fältnamn**    | **Anteckningar**   |  
| :---------------- | :-----------| 
| Erbjudande-ID  | Krävs, kan inte ändras efter skapandet. Högst 50 tecken och måste bestå av gemener, alfanumeriska tecken, bindestreck eller understreck. |
| Erbjudandet alias  | Obligatoriskt. |

## <a name="offer-setup-page"></a>Installationssidan för erbjudandet

Installationssidan för erbjudandet är där du kan välja olika kanaler och säljande rörelser, samt deklarera användning av viktiga funktioner, till exempel provkörning och kunden leads. 

| **Fältnamn**    | **Anteckningar**   | 
| :---------------- | :-----------|  
| Vill du sälja via Microsoft?  | Obligatoriskt. standard: Ja |
| Hur vill du potentiella kunder att interagera med erbjudandet registrering? (Anrop till åtgärden)  | Krävs om inte sälja via Microsoft. standard: Kostnadsfri utvärderingsversion, alternativ: ”Hämta nu”, ”kostnadsfri utvärdering”, ”kontakta mig”. |
| Utvärderingsversion URL  | Krävs om ”kostnadsfri utvärdering” väljs när sätt kunder bör interagerar med erbjudandet-listan. |
| URL: en för erbjudandet  | Krävs om ”Hämta nu” är markerad, som kunderna sätt ska interagera med erbjudandet-lista |
| Kanaler  | Valfri. standard: Inte valt att kanalen CSP (återförsäljare).  |
| Test Drive | Valfri. standard: Inga provkörning aktiverat.  |
| Typ av Test Drive | Krävs om aktiverat en testenhet. standard: Inga valda. Alternativ: Med Azure Resource Manager, Dynamics 365 för Business Central, Dynamics 365 för kundengagemang, Dynamics 365 for Operations, logikapp, Power BI.  |
| Lead-hantering – Anslut till ett CRM-system | Krävs om försäljning via Microsoft, eller om lista ger när ”kontakta mig”. Standardvärde: ingen CRM-system är anslutna. CRM-alternativ: Azure-tabell, Azure blob, Dynamics CRM online, HTTPs-slutpunkt, Marketo, Salesforce  |

## <a name="properties-page"></a>Sidan Egenskaper

Egenskapssidan är här du definierar kategorier och branscher som används för att gruppera ditt erbjudande i marknadsplatser, juridiska avtal som stöd för ditt erbjudande och din appversion. Var noga med att ge fullständig och korrekt information om erbjudandet på den här sidan så att den visas på rätt sätt och att rätt antal kunder som erbjuds. 

| **Fältnamn**    | **Anteckningar**   | 
| :---------------- | :-----------|  
| Kategori och underkategori | Krävs 1 och högst 3. standard: Inga valda. |
| Branscher och subindustries | Valfri. högst 2 L1 branscher och max 2 subindustries inom varje L1 branschen, standard: Inga valda |
| Appversion  | Valfri. standard: Ingen. |
| Använda standardavtal  | Valfri. Standard: har inte valt.  | |
| Användningsvillkor  | Krävs om standardavtal inte är markerat.  |

## <a name="offer-listing-page"></a>Sidan för erbjudandet lista

Sidan lista kan du ge text och bilder som kunderna ser när de visar lista för ditt erbjudande i marketplace. 

| **Fältnamn**    | **Anteckningar**   |
| :---------------- | :-----------| 
| Namn  | Krävs, högst 50 tecken. |
| Sammanfattning  | Krävs, högst 100 tecken. | 
| Beskrivning  | 3000 tecken som krävs, max. |
| Komma igång-anvisningar  | 3000 tecken som krävs, max. |
| Komma igång-anvisningar  | 3000 tecken som krävs, max. |
| Sök efter nyckelord  | Valfritt, rekommenderas, max 3 nyckelord. |
| Sekretesswebbadress för principen  | Obligatoriskt. |
| CSP-programmet marknadsföring material URL  | Valfri. |
| Användbara länkar titel + URL  | Valfri. |
| Stöd för dokument titel + fil  | Krävs, min 1 och högst 3. Måste vara PDF-filformat. |
| Skärmbilder  | Krävs, min 1 skärmbild och max 5. fyra eller mer rekommenderas. Måste vara minst 1 280 X 720 i PNG-format. |
| Store logotyper (liten, medel, stor, hela, Hero)  | Liten (48 X 48) och stor (216 X 216) krävs. andra storlekar som är valfritt men rekommenderas: Medel (90 x 90) hela (255 x 115), Hero (815 x 290). Måste vara i PNG-format. |
| Videor namn, URL: en + miniatyr  | Valfritt, rekommenderas, högst 4 videor. Miniatyr måste vara minst 1 280 x 720 i PNG-format. Videon måste finnas i YouTube eller Vimeo. |
| Kontakter (CSP-programmet, teknik, Support)  | Teknik- och Supportkontakt krävs (namn, e-post och telefonnummer). Kontakta CSP-programmet är valfritt men rekommenderas. |
| Support-URL  | Obligatoriskt. |

## <a name="preview-page"></a>Förhandsgranskningssidan

Förhandsgranskningen är där du anger målgruppen ska ha åtkomst till ditt erbjudande förhandsversionen och kontrollera att erbjudandet uppfyller dina krav innan det lanseras. 

| **Fältnamn**    | **Anteckningar**   | 
| :---------------- | :-----------| 
| AAD/MSA e-post + beskrivning | Obligatoriskt, min 1 och högst 10 om du har angett manuellt, eller upp till 20 om överför en CSV-fil. |

## <a name="technical-configuration-page"></a>Teknisk konfigurationssidan 

Konfigurationssidan för tekniska är där du anger de tekniska detaljerna som används av Microsoft för att ansluta till ditt erbjudande. Den här sidan är inte synliga för dig om du inte valt att sälj via Microsoft.

| **Fältnamn**    | **Anteckningar**   |  
| :---------------- | :-----------| 
| Webbadress för informationssida | Krävs om försäljning via Microsoft. |
| Anslutningen webhook | Krävs om försäljning via Microsoft. |
| Azure AD-klient-ID | Krävs om försäljning via Microsoft. |
| Azure AD app-ID | Krävs om försäljning via Microsoft. |

## <a name="plan-identity-modal"></a>Planera identitet modal

De första typer av information som du uppmanas att ange är ett namn och ett ID för din Plan. Den här sidan är inte synliga för dig om du har inte valt att sälj via Microsoft.

| **Fältnamn**    | **Anteckningar**   |  
| :---------------- | :-----------| 
| Plan-ID  | Krävs om försäljning via Microsoft. Det går inte att ändra när du har skapat. Högst 50 tecken och måste bestå av gemener, alfanumeriska tecken, bindestreck eller understreck. |
| Plannamn  | Krävs om försäljning via Microsoft. Måste vara unikt för alla prenumerationer i erbjudandet. Max 50 tecken. |

## <a name="plan-listing-page"></a>Plan lista sida

Plan lista sidan ger du texten för kunder att se när du visar planen i marketplace. Den här sidan är inte synliga för dig om du inte valt att sälj via Microsoft.

| **Fältnamn**    | **Anteckningar**   |  
| :---------------- | :-----------| 
| Beskrivning av   | Krävs om försäljning via Microsoft. Max 500 tecken. | |

## <a name="plan-pricing--availability-page"></a>Planera sidan med priser och tillgänglighet

Sidan plan priser och tillgänglighet är här du definierar de egenskaper för företag, målgrupp och marknaden tillgänglighet för varje plan (version) i ditt erbjudande. Den här sidan är inte synliga för dig om du inte valt att sälj via Microsoft.

| **Fältnamn**    | **Anteckningar**   | 
| :---------------- | :-----------| 
| Marknaden tillgänglighet  | Krävs, 1 för min och max 141. |
| Prismodell  | Obligatoriskt. standard: Fast pris. Alternativ: Fast pris per användare. |
| Lägsta och högsta platser  | Valfritt, endast tillgänglig om klientbaserade Prismodell valt. |
| Fakturering termen  | Obligatoriskt. standard: Per månad. Alternativ: Månatliga, årliga. |
| Pris  | Nödvändiga USD per månad, om månadsfaktureringen termen som valts. eller USD per år om årlig fakturering termen som valts. |
| Planera målgrupp  | Valfri. standard: Offentliga plan. Alternativ: Offentlig, privat av klient-ID |
| Begränsad planera målgrupp (klient-ID + beskrivning)  | Krävs om privata plan har valt. Min 1 och högst 10 klient-ID: N om anges manuellt. Max 20000 om CSV-filen import. |

## <a name="test-drive-listing-page"></a>Test Drive lista sida

Endast tillgänglig om du väljer för att erbjuda en testenhet för ditt erbjudande. Definiera information som används för att lista provköra i marketplace.

| **Fältnamn**    | **Anteckningar**   | 
| :---------------- | :-----------| 
| Beskrivning  | Obligatoriskt. |
| Användarnamn för manuell + fil  | Krävs, max 1 dokument. Måste vara PDF-format. |
| Namn på videon, URL: en + miniatyr  | Valfritt, rekommenderas. Miniatyr måste vara 533 x 324 i JPGP eller PNG-format. Videon måste finnas i YouTube eller Vimeo. |

## <a name="review-and-publish-page"></a>Granska och publicera sida

| **Fältnamn**    | **Anteckningar**   | 
| :---------------- | :-----------| 
| Anteckningar för certifiering  | Valfri. |

## <a name="next-steps"></a>Nästa steg

- [Skapa ett nytt SaaS-erbjudande](./create-new-saas-offer.md)
