---
title: Fastställ publicerings alternativet – Microsoft Commercial Marketplace
description: Den här artikeln beskriver berättiganderegler och krav för publicering av erbjudanden till Microsoft AppSource och Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 09/04/2020
ms.openlocfilehash: 17b53d656d0344e4178b3034a972e26d6aa0bc15
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94492114"
---
# <a name="determine-your-publishing-option"></a>Fastställa publiceringsalternativ

Det publicerings alternativ som du väljer för ditt erbjudande relaterar direkt till både behörighets kraven och GTM-förmånerna för kommersiella platser. Det är viktigt att valet av publicerings alternativ och erbjudande typ definierar hur användarna ska interagera med ditt kommersiella Marketplace-erbjudande.

Om du vill konfigurera ditt erbjudande måste du förstå följande viktiga kommersiella Marketplace-koncept: publicerings alternativ, erbjudande typer och konfiguration och list alternativ som styr hur och var ditt erbjudande presenteras i de kommersiella onlinebutiker online.

I den här artikeln lär du dig att:

- Hur du fastställer en lämplig onlinebutik för din lösning.
- Vilka publicerings alternativ och list alternativ som finns tillgängliga i varje onlinebutik.
- Vilka erbjudande typer som är tillgängliga för varje publicerings alternativ.

## <a name="commercial-marketplace-publishing-options"></a>Publicerings alternativ för kommersiell Marketplace

I följande tabell visas publicerings alternativen för erbjudande typer i Microsoft AppSource och Azure Marketplace.

|   | **Lista (kontakt)**  | **Lista (utvärderings version)**  | **Kostnadsfri** | **BYOL** | **Transaktion**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Virtuell dator** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Azure-appar (flera virtuella datorer)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Behållar avbildning** |  |  | Azure Marketplace | Azure Marketplace |   |
| **IoT Edge modul** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Hanterade tjänster** |  |  |  | Azure Marketplace |   |
| **Konsulttjänster** | Båda onlinebutiker |  |  |  |   |
| **SaaS-app** | Båda onlinebutiker | Båda onlinebutiker | Båda onlinebutiker |  | Både onlinebutiker * |
| **Microsoft 365 app** | AppSource | AppSource |  |  | AppSource * *  |
| **Dynamics 365-tillägg** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; Transact-erbjudanden för SaaS-program i Microsoft AppSource är för närvarande endast kredit kort.

&#42;&#42; Microsoft 365 erbjudanden är kostnads fria att installeras och kan säljas via SaaS-erbjudandet som en licens tjänst. Mer information finns i avsnittet [om att köpa Microsoft 365-tillägget via Microsofts kommersiella marknads platser](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-listing-option"></a>Välj ett List alternativ

De List alternativ som är tillgängliga erbjuder differentierade kund engagemang samtidigt som du får åtkomst till lead-delning och [fördelarna med kommersiell marknads](./gtm-your-marketplace-benefits.md)plats. Observera de List alternativ som motsvarar publicerings alternativet:

| **Publicerings alternativ**    | **Beskrivning**  |
| :------------------- | :-------------------|
| **Lista** | En enkel lista över ditt program eller din tjänst som gör det möjligt för en kommersiell Marketplace-användare att begära att du ansluter till kunden via alternativ för **kontakt** listan. |
| **Utvärdering** | Använd den kommersiella Marketplace för att förbättra identifieringen och automatisera etableringen av lösningens utvärderings version, vilket gör det möjligt för potentiella användare att använda din SaaS, IaaS eller Microsoft app-upplevelse utan kostnad under en begränsad tid innan de köper. De List alternativ som används för publicerings alternativet för utvärdering är antingen **kostnads fria utvärderings versioner** eller **test enheter**. |
| **BYOL** | Använd den kommersiella Marketplace för att förbättra identifieringen och automatisera etableringen av din lösning och slutföra den finansiella transaktionen separat. Typer av BYOL-erbjudanden är idealiska för lokala och molnbaserade migreringar. List alternativet hämtas **nu**.
| **Transaktion** | Transact-erbjudanden säljs via den kommersiella marknads platsen. Microsoft ansvarar för fakturering och samlingar. List alternativet hämtas **nu**.|

> [!Note]
> När du använder alternativet för Transact-publicering är det viktigt att förstå pris-, fakturerings-, fakturerings-och utbetalnings aspekter innan du väljer en erbjudande typ och skapar ditt erbjudande. Läs mer i artikeln om de [kommersiella Transact-funktionerna i Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="selecting-an-online-store"></a>Välja en onlinebutik

I varje onlinebutik används olika kund krav för affärs-och IT-lösningar. Din erbjudande typ, Transact-funktioner och kategori avgör var ditt erbjudande ska publiceras. Kategorier och under Kategorier mappas till varje onlinebutik utifrån den typ av lösning som du publicerar:

**Microsoft AppSource** erbjuder affärs lösningar, till exempel bransch lösningar och konsult tjänster, för Dynamics 365, Microsoft 365 och Power Platform.

**Azure Marketplace** erbjuder IT-lösningar som skapats för eller på Azure, samt konsult tjänster som påskyndar kundernas användning av Azure.

Välj den kategori och under kategori som bäst överensstämmer med din lösnings typ. En brand vägg för webbaserade program är till exempel en IT-lösning som ska publiceras på Azure Marketplace, under kategorin säkerhet. En app för kontrakt hantering är en affärs lösning som ska publiceras på AppSource under försäljnings kategorin. Om du väljer fel kategori eller under kategori kan ditt erbjudande publiceras till fel onlinebutik.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Publicera till båda onlinebutiker (endast SaaS-erbjudanden)

SaaS-erbjudanden kan publiceras på Azure Marketplace eller AppSource. Om ditt SaaS-erbjudande är *både* en IT-lösning (Azure Marketplace) och en företags lösning (AppSource), väljer du en kategori och/eller en under kategori som gäller för varje onlinebutik. Erbjudanden som publiceras i båda onlinebutiker bör ha ett värde som en IT-lösning *och* en affärs lösning.

> [!IMPORTANT]
> SaaS-erbjudanden med [avgiftsbelagda faktureringar](partner-center-portal/saas-metered-billing.md) är tillgängliga via Azure Marketplace och Azure Portal. SaaS-erbjudanden med enbart privata planer är tillgängliga via Azure Portal.

| Avgiftsbelagd fakturering | Offentligt abonnemang | Privat plan | Tillgängligt i: |
|---|---|---|---|
| Ja             | Ja         | Nej           | Azure Marketplace och Azure Portal |
| Ja             | Ja         | Ja          | Azure Marketplace och Azure Portal * |
| Ja             | Nej          | Ja          | Endast Azure Portal |
| Nej              | Nej          | Ja          | Endast Azure Portal |

&#42; privat plan för erbjudandet är endast tillgängligt via Azure Portal

Ett erbjudande med till exempel fakturering och enbart privat plan (ingen offentlig plan) kommer att köpas av kunder i Azure Portal. Lär dig mer om [privata erbjudanden på Microsofts kommersiella marknads platser](private-offers.md).

### <a name="categories"></a>Kategorier

Kategorier och under Kategorier mappas till varje onlinebutik utifrån lösnings typen. Välj de kategorier och under kategorier som bäst passar din lösning. Du kan välja:

- Minst en och upp till två kategorier. Du kan välja en primär kategori och en sekundär kategori.
- Upp till två under Kategorier för varje primär och/eller sekundär kategori. Om du inte väljer någon under kategori kommer ditt erbjudande fortfarande att kunna identifieras under den valda kategorin.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Nästa steg

- När du har bestämt dig för ett publicerings alternativ granskar du [publicerings guiden per erbjudande typ](./publisher-guide-by-offer-type.md).