---
title: Fastställ publicerings alternativet – Microsoft Commercial Marketplace
description: Den här artikeln beskriver berättiganderegler och krav för publicering av erbjudanden till Microsoft AppSource och Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 1b0846c68f6860b5c7dac9e93808088dac4f6a05
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607440"
---
# <a name="determine-your-publishing-option"></a>Fastställa publiceringsalternativ

Det publicerings alternativ som du väljer för ditt erbjudande relaterar direkt till både behörighets kraven och GTM-förmånerna för kommersiella platser. Det är viktigt att valet av publicerings alternativ och erbjudande typ definierar hur användarna ska interagera med ditt kommersiella Marketplace-erbjudande.

Om du vill konfigurera ditt erbjudande måste du förstå följande viktiga kommersiella Marketplace-koncept: publicerings alternativ, erbjudande typer och konfiguration samt anrop till åtgärd som styr hur och var ditt erbjudande presenteras i de kommersiella onlinebutiker online.

I den här artikeln lär du dig att:

- Hur du fastställer en lämplig onlinebutik för din lösning.
- Vilka publicerings alternativ och anrop till åtgärd som är tillgängliga i varje onlinebutik.
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

&#42;&#42; Microsoft 365 erbjudanden är kostnads fria att installeras och kan säljas via SaaS-erbjudandet som en licens tjänst. Mer information finns i köpa [ditt Office 365-tillägg via Microsofts kommersiella Marketplace](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-call-to-action"></a>Välj ett anrop till åtgärden

De publicerings alternativ som är tillgängliga erbjuder differentierade kund engagemang samtidigt som du får åtkomst till lead-delning och [kommersiella marknads förmåner](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Anteckna anrop till åtgärd som motsvarar publicerings alternativet:

| **Publicerings alternativ**    | **Beskrivning**  |
| :------------------- | :-------------------|
| **Lista** | En enkel lista över ditt program eller din tjänst som gör det möjligt för en kommersiell Marketplace-användare att be dig att ansluta till kunden via **kontakten mig** samtal till åtgärd. |
| **Utvärdering** | Använd den kommersiella Marketplace för att förbättra identifieringen och automatisera etableringen av lösningens utvärderings version, vilket gör det möjligt för potentiella användare att använda din SaaS, IaaS eller Microsoft app-upplevelse utan kostnad under en begränsad tid innan de köper. Anrop till åtgärd som används för alternativet för utvärdering av utvärdering är antingen **kostnads fri utvärderings version** eller **test enhet**. |
| **BYOL** | Använd den kommersiella Marketplace för att förbättra identifieringen och automatisera etableringen av din lösning och slutföra den finansiella transaktionen separat. Typer av BYOL-erbjudanden är idealiska för lokala och molnbaserade migreringar. Anropet till åtgärd är att **Hämta det nu**.
| **Transaktion** | Transact-erbjudanden säljs via den kommersiella marknads platsen. Microsoft ansvarar för fakturering och samlingar. Anropet till åtgärd är att **Hämta det nu**.|

> [!Note]
> När du använder alternativet för Transact-publicering är det viktigt att förstå pris-, fakturerings-, fakturerings-och utbetalnings aspekter innan du väljer en erbjudande typ och skapar ditt erbjudande. Läs mer i artikeln om de [kommersiella Transact-funktionerna i Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="selecting-an-online-store"></a>Välja en onlinebutik

Varje onlinebutik tjänar unika kund krav och riktar sig till specifika mål grupper. Din erbjudande typ, Transact-funktioner och kategori avgör var ditt erbjudande ska publiceras. Kategorier och under Kategorier mappas till varje onlinebutik baserat på mål mål gruppen:

**Microsoft AppSource** riktas till företags användare som söker efter branschspecifika eller bransch lösningar och konsult tjänster för Dynamics 365, Microsoft 365 och Power Platform.

**Azure Marketplace** riktar sig till IT-proffs och utvecklare som söker efter lösningar som skapats för eller på Azure samt konsult tjänster som påskyndar användningen av Azure.

Välj den kategori och under kategori som bäst passar din mål grupp. Till exempel bör en brand vägg för webbaserade program publiceras på Azure Marketplace, under kategorin säkerhet, eftersom den avsedda mål gruppen är IT-proffs. En app för hantering av kontrakt bör i stället publiceras till AppSource under försäljnings kategorin eftersom den avsedda mål gruppen är företags användare. Om du väljer fel kategori eller under kategori kan ditt erbjudande publiceras till fel onlinebutik.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Publicera till båda onlinebutiker (endast SaaS-erbjudanden)

SaaS-erbjudanden kan publiceras på Azure Marketplace eller AppSource. Om ditt SaaS-erbjudande är avsett för *både* en teknisk publik (Azure Marketplace) och en företags mål grupp (AppSource) väljer du en kategori och/eller en under kategori som gäller för varje onlinebutik. Erbjudanden som publiceras i båda onlinebutiker bör ha ett värde förslag som sträcker sig till IT-proffs *och* företags användare.

> [!IMPORTANT]
> SaaS-erbjudanden med avgiftsbelagda faktureringar är tillgängliga via Azure Marketplace och Azure Portal. SaaS-erbjudanden med enbart privata planer är tillgängliga via Azure Portal.

| Avgiftsbelagd fakturering | Offentligt abonnemang | Privat plan | Tillgängligt i: |
|---|---|---|---|
| Ja             | Ja         | Nej           | Azure Marketplace och Azure Portal |
| Ja             | Ja         | Ja          | Azure Marketplace och Azure Portal * |
| Ja             | Nej          | Ja          | Endast Azure Portal |
| Nej              | Nej          | Ja          | Endast Azure Portal |

&#42; privat plan för erbjudandet är endast tillgängligt via Azure Portal

Ett erbjudande med till exempel fakturering och enbart privat plan (ingen offentlig plan) kommer att köpas av kunder i Azure Portal. Lär dig mer om [privata erbjudanden på Microsofts kommersiella marknads platser](private-offers.md).

### <a name="categories"></a>Kategorier

Kategorier och under Kategorier mappas till varje onlinebutik baserat på mål mål gruppen. Välj de kategorier och under kategorier som bäst passar ditt erbjudande och den avsedda mål gruppen. Du kan välja:

- Minst en och upp till två kategorier. Du kan välja en primär kategori och en sekundär kategori.
- Upp till två under Kategorier för varje primär och/eller sekundär kategori. Om du inte väljer någon under kategori kommer ditt erbjudande fortfarande att kunna identifieras under den valda kategorin.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Nästa steg

- När du bestämmer dig för ett publicerings alternativ kan du [välja den erbjudande typ](./publisher-guide-by-offer-type.md) som ska användas för att presentera ditt erbjudande.
- Granska behörighets kraven i avsnittet publicerings alternativ per erbjudande typ för att slutföra valet och konfigurationen av ditt erbjudande.
- Granska publicerings mönstren per onlinebutik för att få exempel på hur din lösning mappar till en erbjudande typ och konfiguration.
