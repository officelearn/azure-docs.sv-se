---
title: Storefront information för en Power BI-appen erbjuder – Azure Marketplace | Microsoft Docs
description: Konfigurera Storefront information fält till en Power BI-appen för Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: pbutlerm
ms.openlocfilehash: ffc0d8e9e41cf4d202b4040ce3f09e7180101c8b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666809"
---
# <a name="power-bi-apps-storefront-details-tab"></a>Fliken för Power BI-appar Storefront information

Använd den **Storefront information** fliken den **nytt erbjudande** sidan för att tillhandahålla marknadsföring, försäljning och juridisk information till potentiella kunder. Den här fliken kan du också ange hur du hanterar leads som genereras från marketplace. Den här diktering är uppdelad i sex delar: **Erbjudandeinformationen**, **listinformation**, **marknadsföring artefakter**, **juridiska**, **kundsupport**, och **Leda Management**.  En tillagda asterisk (*) för fältet etiketten visar att det krävs.


## <a name="offer-details-section"></a>Erbjudandet informationsavsnittet

I det här avsnittet ska ange du allmän information om din App källan erbjuder.

![Erbjudandet informationsavsnittet på fliken Storefront information](./media/offer-details-section.png)

I följande tabell beskrivs namn och syftet med de här fälten.

|   Fält               |   Beskrivning                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------|
| **Sammanfattning av erbjudandet**     | Kort syftet med appen. Högst 100 tecken.                             |
| **Beskrivning av erbjudande** | Beskrivning av appen. Maximal längd på 3000 tecken har stöd för enkel HTML-formatering. |
|                       |                                                                                         |


## <a name="listing-details-section"></a>Visa en lista över informationsavsnittet

Den här andra avsnittet ger ytterligare kontext för din app: vilka branscher det vanligtvis används i vilken kategori som bäst gäller till den kompatibla produkter och associerade Sök villkor.

![Visa en lista över informationsavsnittet på fliken Storefront information](./media/listing-details-section.png)

 I följande tabell beskrivs namn och syftet med de här fälten.
 
|   Fält                                  |   Beskrivning                                                        |
| --------------                           | ---------------------                                                |
| **Branscher**                           | Välj bransch som din app är bäst anpassad efter. Om din app är kopplad till flera olika branscher, kan du lämna det tomt.      |
| **Kategorier**                           | Välj kategorier som är relevanta för din app. Välj högst 3.     |
| **Hjälplänk för din app**               | URL till en sida med Onlinehjälp för din app           |
| **Produkter som din app fungerar med (Max 3)** | Lista de specifika produkter som din app fungerar med. Du kan visa upp till 3 produkter. Om du vill visa en produkt, klicka på plustecknet (bredvid nytt) och ett nytt öppna textfält kommer att skapas för dig att ange namnet på en produkt som din app fungerar med.      |
| **Sökord (Max 3)**              | AppSource kan kunden söker efter nyckelord. Du kan ange uppsättningen nyckelord som programmet kommer att visas för kunderna. Till exempel om programmet är ”min e-post-app” e-postmeddelanden, kanske skicka, e-postprogrammet vissa nyckelord. Välj typ av ord som användare använder sannolikt att söka för din app i rutan Sök AppSource. |
|  |  |


## <a name="marketing-artifacts-section"></a>Marknadsföring artefakter avsnittet

Det här avsnittet för tredje kan uppladdningen av anpassning och marknadsföringsmaterial.  Den är uppdelad i fyra underavsnitt: **Logotyper**, **videor**, **dokument**, och **skärmbilder**. Logotyper och skärmdumpar är obligatoriska marknadsföring artefakter; men rekommenderas alla för bästa kund ändring.

![Marknadsföring artefakter delen av fliken Storefront information](./media/marketing-artifacts-section.png)

 
|    Fält                             |    Beskrivning                                                    |
|   -----------                        |    -------------                                                  |
| *Logotyper*                              |                                                                   |
| **Erbjudandet logotyp (png-format, 48 x 48)**   | Visas på AppSource i översikten över appen eller app-resultat när du har slutfört en sökning. Endast png-format, med en upplösning på 48px\*48px stöds.  |
| **Erbjudandet logotyp (png-format, 216 x 216)** | Visas på AppSource på detaljsidan för din app.  Endast png-format, med en upplösning på 216px\*216px stöds.  |
| *Videoklipp*                             |                                                                   |
| **Namn**                             | Namn eller appens titel                                          |
| **URL**                              | Video-URL finns på YouTube eller Vimeo                              |
| **Miniatyr**                        | Miniatyr av appen.  Endast png-format med en upplösning på 1280px\*720px stöds.   |
| *Dokument*                          | Valfritt, men högst tre dokument. Docs som du laddar upp den här visas på AppSource under ”Läs mer”.  |
| **Namn**                             | Namn eller rubrik för stöd för dokument                              |
| **Fil**                             | Ladda upp dokument måste vara i PDF-format                             |
| *Skärmbilder*                        | Valfritt, men högst fem skärmbilder.                        |
| **Namn**                             | Namn eller rubrik för skärmbild                                       |
| **Avbildning**                            | Ladda upp skärmen insamlade avbildningen måste vara png-format med upplösning på 1280px\*720px  | 
|   |   |


### <a name="logo-guidelines"></a>Riktlinjer för logotyp

Alla logotyper överförs till den [Cloud Partner Portal](https://cloudpartner.azure.com) bör följa riktlinjerna:

- Använd inte en toning bakgrund på din logotyp.
- Undvika att placera text – inklusive företaget eller varumärke – på logotypen. Utseendet och känslan av din logotyp måste vara ”fast” och Undvik toningar.
- Stretch inte logotypen.


## <a name="legal-section"></a>Juridiska avsnittet

Det här fjärde avsnittet kan du ange två juridiska dokument som krävs för varje erbjudande: Sekretesspolicy och användningsvillkoren.

![Juridiska delen av fliken Storefront information](./media/legal-section.png)

|   Fält                |   Beskrivning                           |
|------------------------|--------------------------------------   |
| **Sekretesswebbadress för principen** | URL: en till din bokförda sekretesspolicy       |
| **Användningsvillkor**       | Princip för som oformaterad text eller enkel HTML-kod     |
|  |  |


## <a name="customer-support-section"></a>Kunden supportavdelning

Ange den **stöd för URL: en** för din online-kund supportsida.  Det är bäst om den här sidan Onlinesupport tillhandahåller kunder med flera Kontaktalternativ som telefon, e-post och live-chatt. 


## <a name="lead-management-section"></a>Leda hanteringsavsnittet

Det sista avsnittet kan du samla in kunder leads som skapas från AppSource-erbjudanden. Den erbjuder följande lagringsalternativ (från listrutan) för den här lead-information.

|    Fält               |   Leda mål                               |
|------------------------|--------------------------------------            |
|  **Ingen**              | Leads är inte samlas in (standard).  |
| **Azure Blob (inaktuell)** | En [Azure blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview), som anges av ett behållarnamn och en anslutningssträng.  Det här alternativet är föråldrad; Använd **Azure Table** i stället.  |
| **Azure Table**        | En [Azure-tabell](https://docs.microsoft.com/azure/cosmos-db/table-storage-overview), som anges av en anslutningssträng  |
| **Dynamics CRM Online** | En [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) instans som anges av en URL och autentisering autentiseringsuppgifter |
| **HTTPS-slutpunkt**     | Den angivna HTTPS-slutpunkten som en JSON-nyttolast   |
| **Marketo**            | En [Marketo](https://www.marketo.com/) instans som anges av server-ID, munchkin-ID och formulär-ID   |
| **Salesforce**         | En [Salesforce](https://www.salesforce.com/) databas som anges av ett objekt-ID |
|  |  |

När du har publicerat ditt erbjudande, lead-anslutningen har verifierats och ett test-lead skickas automatiskt till det angivna målet. Lead-information ska hanteras kontinuerligt och dessa inställningar ska uppdateras snabbt för att återspegla din aktuella hanteringsarkitektur för kunden.


## <a name="next-steps"></a>Nästa steg

I nästa [kontakter](./cpp-contacts-tab.md) fliken du tillhandahåller teknisk hjälp och användaren Supportresurser för ditt erbjudande.
