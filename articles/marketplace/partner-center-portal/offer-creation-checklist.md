---
title: SaaS erbjuder checklista för att skapa skapande – Commercial Marketplace för Azure
description: De uppgifter du kan ange i SaaS erbjudandeskapande processen. - Kommersiell marknadsplats för Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 60111b6a23c76314383c5f95be3eb4b38f90d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281348"
---
# <a name="saas-offer-creation-checklist"></a>Checklista för skapande av SaaS-erbjudande

SaaS erbjudandeskapande processen tar dig igenom flera sidor. Här är detaljerna du kan ge på varje sida, med länkar för att lära dig mer om varje objekt.

Objekt som du måste ange eller ange anges nedan. Vissa områden är valfria eller har standardvärden som tillhandahålls, som du kan ändra efter behov. Du behöver inte arbeta med dessa avsnitt i den ordning som anges här.

| **Objekt**    | **Syfte**  |
| :---------- | :-------------------|
| [**Nytt erbjudande Modal**](#new-offer-modal) | Samlar in identitetsinformation.  |
| [Sidan Inställningar för erbjudande](#offer-setup-page) | Gör att du kan välja att använda viktiga funktioner och välja hur du vill sälja ditt erbjudande via Microsoft.  |
| [Sidan Egenskaper](#properties-page) | Definiera de kategorier och branscher som används för att gruppera ditt erbjudande på marknadsplatserna, de juridiska kontrakt som stöder ditt erbjudande och din appversion. |
| [Sidan Lista offer](#offer-listing-page) | Definiera de erbjudandeinformation som ska visas på marknadsplatsen, inklusive beskrivningar av ditt erbjudande och marknadsföringstillgångar. |
| [Förhandsgranska sida](#preview-page) | Definiera en begränsad förhandsmålgrupp för att släppa ditt erbjudande innan du publicerar ditt erbjudande live till den bredare marknadsplatsens målgrupper. |
| [Sidan För erbjudande teknisk konfiguration](#technical-configuration-page)  | Endast tillgängligt om du väljer att sälja erbjudandet via Microsoft. Definiera den tekniska information (URL-sökväg, webhook, klient-ID och app-ID) som används för att ansluta till ditt erbjudande. |
| [**Ny plan Modal**](#plan-identity-modal) | Samlar in identitetsinformation för planen.  |
| [Sida för listning av abonnemang](#plan-listing-page)  | Endast tillgängligt om du väljer att sälja erbjudandet via Microsoft. Definiera den information som används för att lista Planen på marknadsplatsen.  |
| [Planera pris & tillgänglighetssida](#plan-pricing--availability-page)  | Endast tillgängligt om du väljer att sälja erbjudandet via Microsoft.  Samlar in affärsegenskaper (prismodell), målgrupp och marknadstillgänglighet för varje plan (version) av ditt erbjudande.  |
| [Beskrivningssida för testkörning](#test-drive-listing-page)  | Endast tillgängligt om du väljer att erbjuda en provkörning för ditt erbjudande. Definiera den information som används för att lista provkörningen på marketplace.  |
| Teknisk konfigurationssida för testkörning  | Endast tillgängligt om du väljer att erbjuda en provkörning för ditt erbjudande. Definiera de tekniska detaljerna för demonstrationen (eller "provkörningen") som gör det möjligt för kunder att prova ditt erbjudande innan de bestämmer sig för att köpa det.  |
| [Granska och publicera sida](#review-and-publish-page)  | Välj de ändringar du vill publicera, se status för varje sida och ge anteckningar till certifieringsteamet.  |


## <a name="new-offer-modal"></a>Nytt erbjudande modal 

De första informationsbitarna som du kommer att bli ombedd att ange är ett ID och alias för ditt erbjudande. 

| **Fältnamn**    | **Obs!**   |  
| :---------------- | :-----------| 
| Erbjudande-ID  | Obligatoriskt, kan inte ändras när du har skapat. Max 50 tecken och får endast bestå av gemener, alfanumeriska tecken, streck eller understreck. |
| Alias för erbjudande  | Krävs. |

## <a name="offer-setup-page"></a>Inställningssida för erbjudande

Inställningssidan för erbjudandet är där du kan välja mellan olika kanaler och sälja rörelser, samt deklarera användningen av viktiga funktioner, såsom provkörning och kundleads. 

| **Fältnamn**    | **Obs!**   | 
| :---------------- | :-----------|  
| Vill du sälja via Microsoft?  | Krävs. Standard: Ja |
| Hur vill du att potentiella kunder ska interagera med erbjudandelistan? (Uppmaning)  | Krävs om inte sälja via Microsoft. Standard: Gratis provperiod, Alternativ: "Hämta den nu", "Fri provperiod", "Kontakta mig". |
| URL för utvärderingsversion  | Krävs om "Gratis provperiod" väljs, som hur kunderna ska interagera med erbjudandelistan. |
| Url för erbjudande  | Krävs om "Get it Now" väljs, eftersom hur kunderna ska interagera med erbjudandelistan |
| Kanaler  | Valfri. Standard: Inte valt till CSP-kanalen (återförsäljare).  |
| Test Drive | Valfri. Standard: Ingen provkörning aktiverad.  |
| Typ av provkörning | Krävs om en provkörning är aktiverad. Standard: Ingen markerad. Alternativ: Azure Resource Manager, Dynamics 365 för Business Central, Dynamics 365 för kundengagemang, Dynamics 365 for Operations, Logic-appen, Power BI.  |
| Lead Management - anslut till ett CRM-system | Krävs om du säljer via Microsoft, eller om listning erbjudanden som "Kontakta mig". Standard: inget CRM-system anslutet. CRM-alternativ: Azure-tabell, Azure-blob, Dynamics CRM online, HTTPs slutpunkt, Marketo, Salesforce  |

## <a name="properties-page"></a>Sidan Egenskaper

Egenskapssidan definierar de kategorier och branscher som används för att gruppera ditt erbjudande på marknadsplatserna, de juridiska kontrakt som stöder ditt erbjudande och din appversion. Var noga med att ge fullständig och korrekt information om ditt erbjudande på den här sidan, så att det visas på rätt sätt och erbjuds till rätt uppsättning kunder. 

| **Fältnamn**    | **Obs!**   | 
| :---------------- | :-----------|  
| Kategori och underkategori | Krävs 1 och max 3. Standard: Ingen markerad. |
| Industrier och underindustrier | Valfri. max 2 L1 Industries och max 2 subindustrier inom varje L1-bransch, Standard: Ingen vald |
| Appversion  | Valfri. Standard: Ingen. |
| Använd standardkontrakt  | Valfri. Standard: inte markerat.  | |
| Användningsvillkor  | Krävs om standardkontrakt inte är valt.  |

## <a name="offer-listing-page"></a>Sidan Lista offer

Listsidan anger den text och de bilder som kunderna ser när de visar erbjudandets uppgifter på marknadsplatsen. 

| **Fältnamn**    | **Obs!**   |
| :---------------- | :-----------| 
| Namn  | Krävs, max 50 tecken. |
| Sammanfattning  | Krävs, max 100 tecken. | 
| Beskrivning  | Krävs, max 3000 tecken. |
| Instruktioner för komma igång  | Krävs, max 3000 tecken. |
| Instruktioner för komma igång  | Krävs, max 3000 tecken. |
| Sök nyckelord  | Valfritt, rekommenderas, max 3 sökord. |
| Url till sekretesspolicy  | Krävs. |
| CSP Program Marknadsföringsmaterial URL  | Valfri. |
| Användbara länkar Titel + URL  | Valfri. |
| Rubrik + fil i namnuppring  | Krävs, min 1 och max 3. Måste vara PDF-filformat. |
| Skärmbilder  | Krävs, min 1 skärmdump och max 5; fyra eller fler rekommenderas. Måste vara 1280 X 720 i PNG-format. |
| Lagra logotyper (liten, medel, stor, bred, hjälte)  | Små (48 X 48) och Large (216 X 216) krävs; andra storlekar tillval men rekommenderas: Medium (90 x 90), Wide (255 x 115), Hero (815 x 290). Måste vara i PNG-format. |
| Videor namn + URL + miniatyr  | Tillval, rekommenderas, max 4 videor. Miniatyren måste vara 1280 x 720 i PNG-format. Video måste finnas på YouTube eller Vimeo. |
| Kontakter (CSP-program, teknik, support)  | Teknisk och supportkontakt krävs (namn, e-post och telefonnummer); CSP Program kontakt valfritt men rekommenderas. |
| Url:en för support  | Krävs. |

## <a name="preview-page"></a>Sidan Förhandsgranska

Förhandsgranskningssidan anger målgruppen för att få tillgång till förhandsgranskningen av erbjudandet för att verifiera att erbjudandet uppfyller alla dina krav innan det publiceras. 

| **Fältnamn**    | **Obs!**   | 
| :---------------- | :-----------| 
| AAD/MSA e-post + beskrivning | Krävs, min 1 och max 10 om de anges manuellt, eller upp till 20 om du laddar upp en CSV-fil. |

## <a name="technical-configuration-page"></a>Sida för teknisk konfiguration 

Den tekniska konfigurationssidan anger den tekniska information som Microsoft använder för att ansluta till ditt erbjudande. Den här sidan är inte synlig för dig om du har bestämt dig för att inte sälja via Microsoft.

| **Fältnamn**    | **Obs!**   |  
| :---------------- | :-----------| 
| URL till målsidan | Krävs om du säljer via Microsoft. |
| Anslutning webhook | Krävs om du säljer via Microsoft. |
| Azure AD-klient-ID | Krävs om du säljer via Microsoft. |
| Azure AD-app-ID | Krävs om du säljer via Microsoft. |

## <a name="plan-identity-modal"></a>Planera identitetsmodalitet

Den första informationen som du uppmanas att ange är ett namn och ett ID för din plan. Den här sidan är inte synlig för dig om du har bestämt dig för att inte sälja via Microsoft.

| **Fältnamn**    | **Obs!**   |  
| :---------------- | :-----------| 
| Plan-ID  | Krävs om du säljer via Microsoft. Det kan inte ändras efter skapandet. Max 50 tecken och får endast bestå av gemener, alfanumeriska tecken, streck eller understreck. |
| Plannamn  | Krävs om du säljer via Microsoft. Måste vara unik i alla planer i erbjudandet. Max 50 tecken. |

## <a name="plan-listing-page"></a>Sidan Planera listning

På listsidan finns den plats där du anger texten som kunderna kan se när de visar planen på marknadsplatsen. Den här sidan är inte synlig för dig om du har bestämt dig för att inte sälja via Microsoft.

| **Fältnamn**    | **Obs!**   |  
| :---------------- | :-----------| 
| Beskrivning av planen   | Krävs om du säljer via Microsoft. Max 500 tecken. | |

## <a name="plan-pricing--availability-page"></a>Planera pris & tillgänglighetssida

Sidan för planpriser och tillgänglighet definierar du affärsegenskaper, målgrupp och marknadstillgänglighet för varje plan (version) av erbjudandet. Den här sidan är inte synlig för dig om du har bestämt dig för att inte sälja via Microsoft.

| **Fältnamn**    | **Obs!**   | 
| :---------------- | :-----------| 
| Tillgänglighet på marknaden  | Krävs, min 1 och max 141. |
| Prismodell  | Krävs. Standard: Schablonbelopp. Alternativ: Schablonbelopp, per användare. |
| Lägsta och högsta platser  | Valfritt, endast tillgängligt om platsbaserad prismodell har valts. |
| Faktureringsperiod  | Krävs. Standard: Månadsvis. Alternativ: Månadsvis, årligt. |
| Pris  | Krävs USD per månad, om månatlig faktureringsperiod vald; USD per år om den årliga faktureringsperioden väljs. |
| Planera målgrupp  | Valfri. Standard: Offentlig plan. Alternativ: Offentligt, Privat efter klient-ID |
| Begränsad planmålgrupp (klient-ID + beskrivning)  | Krävs om privat plan väljs. Min 1 och max 10 klient-ID om de anges manuellt. Max 20000 om CSV-fil importeras. |

## <a name="test-drive-listing-page"></a>Sidan Test drive-lista

Endast tillgängligt om du väljer att erbjuda en provkörning för ditt erbjudande. Definiera den information som används för att lista provkörningen på marketplace.

| **Fältnamn**    | **Obs!**   | 
| :---------------- | :-----------| 
| Beskrivning  | Krävs. |
| Namn + fil för användarhandbok  | Krävs, max 1 doc. Måste vara PDF-format. |
| Videonamn, URL + miniatyr  | Valfritt, rekommenderas. Miniatyren måste vara 533 x 324 i JPGP- eller PNG-format. Video måste finnas på YouTube eller Vimeo. |

## <a name="review-and-publish-page"></a>Granska och publicera sida

| **Fältnamn**    | **Obs!**   | 
| :---------------- | :-----------| 
| Anmärkningar för certifiering  | Valfri. |

## <a name="next-steps"></a>Nästa steg

- [Skapa ett nytt SaaS-erbjudande](./create-new-saas-offer.md)
