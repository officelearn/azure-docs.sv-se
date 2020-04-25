---
title: Butik information om ett erbjudande för Power BI app | Azure Marketplace
description: Konfigurera butiks informations fält för ett Power BI app-erbjudande för Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 9db01941c21dbd26d149f3623319557e4c625350
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141800"
---
# <a name="power-bi-app-storefront-details-tab"></a>Fliken information för Power BI butik-app

>[!Important]
>Från och med 13 april 2020 börjar vi flytta hanteringen av dina Power BI app-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [Power BI översikt över skapande av appar](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) för att hantera dina migrerade erbjudanden.

På sidan **nytt erbjudande** använder du fliken **butik information** för att tillhandahålla information om marknadsföring, försäljning och juridisk information för dina potentiella kunder. På den här fliken kan du också konfigurera hantering för leads som genereras av Azure Marketplace. Det här långa formuläret är indelat i sex avsnitt: **information om erbjudanden**, **list information**, **marknadsförings artefakter**, **juridik**, **kund support**och **ledar hantering**.  En asterisk (*) i slutet av en fält etikett betyder att fältet är obligatoriskt.


## <a name="offer-details-section"></a>Avsnittet erbjudande information

I avsnittet **erbjudande information** anger du allmän information om ditt AppSource-erbjudande.

![Avsnittet erbjudande information på fliken butik information](./media/offer-details-section.png)

Följande tabell innehåller mer information om dessa fält. Obligatoriska fält förutsägs av en asterisk (*).  

|   Field               |   Beskrivning                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------|
| **Erbjudande Sammanfattning\***     | Ett kort syfte med appen med upp till 100 tecken.                             |
| **Beskrivning av erbjudande\*** | En beskrivning av appen med upp till 3 000 tecken. Det här fältet stöder enkel HTML-formatering. |
|   |    |


## <a name="listing-details-section"></a>List informations avsnitt

I avsnittet med **detaljerad** information samlas information om appens kontext: branscher där det vanligt vis används, kategorin app, kompatibla produkter och associerade Sök villkor.

![Avsnittet om List information på fliken butik information](./media/listing-details-section.png)

Använd följande tabell för att fylla i fälten i det här avsnittet.  Obligatoriska fält förutsägs av en asterisk (*).
 
|   Field                                  |   Beskrivning                                                        |
| --------------                           | ---------------------                                                |
| **Branscher**                           | Välj den bransch där appen passar bäst. Lämna fältet tomt om din app är relaterad till flera branscher.      |
| **Kategorier\***                           | Välj upp till tre kategorier som relaterar till din app.     |
| **Hjälp länk för din app\***               | Ange en webb adress till en sida som erbjuder direkt hjälp för din app.           |
| **Produkter som din app fungerar med (max 3)** | Välj det **nya** plus tecknet för att skapa ett textfält. I fältet anger du namnet på en produkt som din app fungerar med. Lista upp till tre produkter.       |
| **Sök Nyckelord (max 3)**              | Ange upp till tre nyckelord som användarna kan använda för att söka efter ditt program i AppSource. Om programmet till exempel kallas "min e-postappen" kan dina nyckelord vara **e-** post, **e-post**och **e-postappar**. |
|  |  |


## <a name="marketing-artifacts-section"></a>Avsnittet marknadsförings artefakter

I avsnittet **marknadsförings artefakter** laddar du upp anpassnings-och marknadsförings material som ska visas på AppSource.  Det här avsnittet är uppdelat i fyra underavsnitt: **logo typer**, **videor**, **dokument**och **skärm bilder**. Logo typer och skärm bilder är de enda nödvändiga marknadsförings artefakterna. Men för bästa möjliga kund fördelaktig rekommenderar vi att du lägger till videor och dokument också.

![Avsnittet marknadsförings artefakter på fliken butik information](./media/marketing-artifacts-section.png)

Använd följande tabell för att fylla i fälten i det här avsnittet. Obligatoriska fält förutsägs av en asterisk (*).
 
|    Field                             |    Beskrivning                                                    |
|   -----------                        |    -------------                                                  |
| *Typerna*                              |                                                                   |
| **Erbjudande logo typ (PNG-format, 48x48)\***   | Ladda upp en logo typ som ska visas i översikten över appen eller i Sök resultaten för appar. AppSource stöder endast PNG-format, med en upplösning på 48 x 48 BPT.  |
| **Erbjudande logo typ (PNG-format, 216x216)\*** | Ladda upp en logo typ som ska visas på appens informations sida.  AppSource stöder endast PNG-format, med en upplösning på 216 x 216 BPT.  |
| *Videor*                             |                                                                   |
| **Namn**                             | Ange appens namn eller rubrik.                                          |
| **URL**                              | Ange URL: en för en video som finns på YouTube eller Vimeo.                              |
| **Miniatyr**                        | Lägg till en miniatyr bild av appen.  AppSource stöder endast PNG-format, med en upplösning på 1280 x 720 BPT.   |
| *Dokument*                          | Lägg till upp till tre dokument som ska visas på AppSource under rubriken **Läs mer** .  |
| **Namn**                             | Ange ett stöd dokuments namn eller rubrik.                              |
| **Fil**                             | Ladda upp en PDF-fil.                             |
| *Skärmbilder\**                      | Lägg till upp till fem skärm bilder.                        |
| **Namn**                             | Ange ett namn eller en rubrik för en skärm bild.                                       |
| **Bild**                            | Ladda upp en PNG-skärm. Dess upplösning måste vara 1280 x 720 BPT.  | 
|   |   |

De logo typer som du överför till [Cloud Partner Portal](https://cloudpartner.azure.com) ska *inte*:

- Använd en toning. Utseendet på logo typen bör vara platt.
- Inkludera företagets namn, varumärke eller annan text. 
- Ser utsträckt ut.

## <a name="legal-section"></a>Juridisk sektion

I det **juridiska** avsnittet anger du de två juridiska dokument som krävs för varje erbjudande: din sekretess policy och dina användnings villkor.

![Juridisk del av fliken butik information](./media/legal-section.png)

Använd följande tabell för att fylla i fälten i det här avsnittet:

|   Field                |   Beskrivning                           |
|------------------------|--------------------------------------   |
| **URL för sekretess policy\*** | URL för din publicerade sekretess policy       |
| **Användningsvillkor\***       | Din användnings princip, formaterad som oformaterad text eller enkel HTML     |
|  |  |


## <a name="customer-support-section"></a>Avsnittet kund support

I avsnittet **kund support** anger du support- **URL:** en för din kund support sida online.  Den här sidan ska innehålla flera kontakt alternativ, till exempel telefon, e-post och Live-chatt. 


## <a name="lead-management-section"></a>Avsnittet ledar hantering

I avsnittet **ledar hantering** ställer du in ditt system för att samla in kund leads som din AppSource erbjuder. Använd följande tabell för att välja lagrings alternativ för leads:

|    Field               |   Lead-mål                               |
|------------------------|--------------------------------------            |
|  **Inget**              | Samla inga leads. Detta är standard alternativet.  |
| **Azure-Blob (inaktuell)** | Ange [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) med hjälp av ett behållar namn och en anslutnings sträng.  Det här alternativet är föråldrat. Använd **Azure-tabellen** i stället.  |
| **Azure-tabell**        | Ange [Azure Table Storage](https://docs.microsoft.com/azure/cosmos-db/table-storage-overview) med hjälp av en anslutnings sträng.  |
| **Dynamics CRM Online** | Ange [Dynamics 365](https://dynamics.microsoft.com/) genom att använda en URL och autentiseringsuppgifter för autentisering. |
| **HTTPS-slutpunkt**     | Ange HTTPS-slutpunkten med hjälp av en JSON-nyttolast.   |
| **Marketo**            | Ange en [Marketo](https://www.marketo.com/) -instans med hjälp av ett server-ID, Munchkin-ID och formulär-ID.   |
| **Salesforce**         | Ange [Salesforce](https://www.salesforce.com/) genom att använda en objekt identifierare. |
|  |  |

När du har publicerat erbjudandet verifieras anslutningen till lead-anslutningen och ett test lead skickas automatiskt till det angivna målet. Hantera lead-information kontinuerligt och uppdatera dina inställningar så att de återspeglar din aktuella kund hanterings arkitektur.


## <a name="next-steps"></a>Nästa steg

På fliken [kontakter](./cpp-contacts-tab.md) anger du support resurser för teknisk support och användare för ditt erbjudande.
