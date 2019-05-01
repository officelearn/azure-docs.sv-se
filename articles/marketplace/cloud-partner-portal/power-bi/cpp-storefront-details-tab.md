---
title: Storefront information för en Power BI-appen erbjuder | Azure Marketplace
description: Konfigurera Storefront information fält till en Power BI-appen för Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 0816e7a7add430a65af9b3948aa8626f9567053a
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942632"
---
# <a name="power-bi-app-storefront-details-tab"></a>Fliken för Power BI-appen Storefront information

På den **nytt erbjudande** kan du använda den **Storefront information** fliken för att ange marknadsföring, försäljning och juridisk information för potentiella kunder. På den här fliken kan du också ställa in hantering för leads som genererar Azure Marketplace. Den här diktering är uppdelad i sex delar: **Erbjudandeinformationen**, **listinformation**, **marknadsföring artefakter**, **juridiska**, **kundsupport**, och **Leda Management**.  En asterisk (*) i slutet av en fältetikett innebär att fältet är obligatoriskt.


## <a name="offer-details-section"></a>Erbjudandet informationsavsnittet

I den **Erbjudandeinformationen** ange allmän information om erbjudandet AppSource.

![Erbjudandet informationsavsnittet på fliken Storefront information](./media/offer-details-section.png)

Följande tabell innehåller mer information om de här fälten. Required fields are indicted by an asterisk (*).  

|   Fält               |   Beskrivning                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------|
| **Sammanfattning av erbjudandet\***     | En kort syftet med appen, med upp till 100 tecken.                             |
| **Beskrivning av erbjudande\*** | En beskrivning av programmet, med upp till 3 000 tecken. Det här fältet stöder enkel HTML-formatering. |
|   |    |


## <a name="listing-details-section"></a>Visa en lista över informationsavsnittet

Den **lista information** avsnittet samlar in information om kontexten för din app: branscher där den används, app-kategori, kompatibla produkter och associerade söktermer.

![Visa en lista över informationsavsnittet på fliken Storefront information](./media/listing-details-section.png)

Använd följande tabell för att slutföra fälten i det här avsnittet.  Required fields are indicted by an asterisk (*).
 
|   Fält                                  |   Beskrivning                                                        |
| --------------                           | ---------------------                                                |
| **Branscher**                           | Välj bransch var din app passar bäst. Om din app är kopplad till flera olika branscher, lämna fältet tomt.      |
| **Kategorier\***                           | Välj upp till tre kategorier som är relaterade till din app.     |
| **Hjälplänk för din app\***               | Ange en URL till en sida som erbjuder Onlinehjälp för din app.           |
| **Produkter som din app fungerar med (Max 3)** | Välj den **New** plustecknet för att skapa ett textfält. I fältet, anger du namnet på en produkt som din app fungerar med. Lista över upp till tre produkter.       |
| **Sökord (Max 3)**              | Ange upp till tre nyckelord som användare använder sannolikt att söka i programmet i AppSource. Till exempel om programmet har anropats ”min e-post-app” nyckelorden kanske **e-postmeddelanden**, **e**, och **e-postprogrammet**. |
|  |  |


## <a name="marketing-artifacts-section"></a>Marknadsföring artefakter avsnittet

I den **marknadsföring artefakter** avsnittet, ladda upp anpassning och marknadsföringsmaterial ska visas på AppSource.  Det här avsnittet är uppdelad i fyra underavsnitt: **Logotyper**, **videor**, **dokument**, och **skärmbilder**. Logotyper och skärmdumpar är den enda krävs marknadsföring artefakter. Men för den bästa kund beslut, rekommenderar vi att lägga till videor och dokument.

![Marknadsföring artefakter delen av fliken Storefront information](./media/marketing-artifacts-section.png)

Använd följande tabell för att slutföra fälten i det här avsnittet. Required fields are indicted by an asterisk (*).
 
|    Fält                             |    Beskrivning                                                    |
|   -----------                        |    -------------                                                  |
| *Logotyper*                              |                                                                   |
| **Erbjudandet logotyp (png-format, 48 x 48)\***   | Ladda upp en logotyp som ska visas i översikten över appen eller i sökresultaten för appen. AppSource stöder endast PNG-format, med en upplösning på 48 x 48 bildpunkter.  |
| **Erbjudandet logotyp (png-format, 216 x 216)\*** | Ladda upp en logotyp som ska visas på detaljsidan för din app.  AppSource stöder endast PNG-format, med en upplösning på 216 x 216 bildpunkter.  |
| *Videoklipp*                             |                                                                   |
| **Namn**                             | Ange appens namn eller rubrik.                                          |
| **URL**                              | Ange Webbadressen till ett videoklipp på YouTube eller Vimeo.                              |
| **Miniatyr**                        | Lägg till en miniatyrbild av appen.  AppSource stöder endast PNG-format, med en upplösning på 1 280 x 720 bildpunkter.   |
| *Dokument*                          | Lägga till upp till tre dokument ska visas på AppSource, under den **mer** rubrik.  |
| **Namn**                             | Ange namn eller titeln på ett stöddokument.                              |
| **Fil**                             | Överföra en PDF-fil.                             |
| *Skärmbilder\**                      | Lägga till upp till fem skärmbilder.                        |
| **Namn**                             | Ange ett namn eller titeln på en skärmbild.                                       |
| **Avbildning**                            | Ladda upp en PNG-skärmbild. Upplösningen måste vara minst 1 280 x 720 bildpunkter.  | 
|   |   |

Logotyper som du överför till [Cloud Partner Portal](https://cloudpartner.azure.com) bör *inte*:

- Använd en övertoning. Utseendet och känslan av din logotyp ska vara fast.
- Inkludera företagets namn, varumärke eller annan text. 
- Titta sträckt.

## <a name="legal-section"></a>Juridiska avsnittet

I den **juridiska** avsnittet tillhandahåller två juridiska dokument som krävs för varje erbjudande: din sekretesspolicy och dina användningsvillkor.

![Juridiska delen av fliken Storefront information](./media/legal-section.png)

Använd följande tabell för att slutföra fälten i det här avsnittet:

|   Fält                |   Beskrivning                           |
|------------------------|--------------------------------------   |
| **Sekretesswebbadress för principen\*** | URL: en för din bokförda sekretesspolicy       |
| **Användningsvillkor\***       | Policy för användning som är formaterade som oformaterad text eller enkel HTML-kod     |
|  |  |


## <a name="customer-support-section"></a>Kunden supportavdelning

I den **kundsupporten** avsnittet tillhandahåller den **stöd för URL: en** för din online-kund supportsida.  Den här sidan bör ge flera Kontaktalternativ som telefon, e-post och live-chatt. 


## <a name="lead-management-section"></a>Leda hanteringsavsnittet

I den **leda Management** avsnittet Konfigurera datorn för att samla in kundleads som genererar AppSource-erbjudanden. Välj din lagringsalternativ för de potentiella kunderna genom att använda i följande tabell:

|    Fält               |   Leda mål                               |
|------------------------|--------------------------------------            |
|  **Ingen**              | Samla in inga leads. Det här är standardalternativet.  |
| **Azure Blob (inaktuell)** | Ange [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) med hjälp av ett behållarnamn och en anslutningssträng.  Det här alternativet är inaktuell. Använd **Azure Table** i stället.  |
| **Azure Table**        | Ange [Azure Table storage](https://docs.microsoft.com/azure/cosmos-db/table-storage-overview) med hjälp av en anslutningssträng.  |
| **Dynamics CRM Online** | Ange [Dynamics 365](https://dynamics.microsoft.com/) med hjälp av en URL och autentisering autentiseringsuppgifter. |
| **HTTPS-slutpunkt**     | Ange HTTPS-slutpunkten med hjälp av en JSON-nyttolast.   |
| **Marketo**            | Ange en [Marketo](https://www.marketo.com/) instans med hjälp av en server-ID, munchkin-ID och formulär-ID.   |
| **Salesforce**         | Ange [Salesforce](https://www.salesforce.com/) med hjälp av en objektidentifierare. |
|  |  |

När du har publicerat ditt erbjudande, lead-anslutningen har verifierats och ett test-lead skickas automatiskt till det angivna målet. Hantera kontinuerligt lead-information och uppdatera dina inställningar för att återspegla din aktuella hanteringsarkitektur för kunden.


## <a name="next-steps"></a>Nästa steg

På den [kontakter](./cpp-contacts-tab.md) fliken tillhandahåller teknisk hjälp och användaren Supportresurser för ditt erbjudande.
