---
title: Fastställ publicerings alternativet – Microsoft Commercial Marketplace
description: Den här artikeln beskriver berättiganderegler och krav för publicering av erbjudanden till Microsoft AppSource och Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 06/22/2020
ms.openlocfilehash: 3d8692d3180e4164bff544f71a1216097a390773
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103661"
---
# <a name="determine-your-publishing-option"></a>Fastställa publiceringsalternativ

Det publicerings alternativ som du väljer för ditt erbjudande relaterar direkt till både behörighets kraven och GTM-förmånerna för kommersiella platser. Det är viktigt att valet av publicerings alternativ och erbjudande typ definierar hur användarna ska interagera med ditt kommersiella Marketplace-erbjudande.

Om du vill konfigurera ditt erbjudande måste du förstå följande viktiga kommersiella Marketplace-koncept: publicerings alternativ, erbjudande typer och konfiguration samt anrop till åtgärd som styr hur och var ditt erbjudande presenteras i den kommersiella Marketplace-butiker.

I den här artikeln lär du dig att:

- Så här fastställer du lämpliga butik för din lösning
- Vilka publicerings alternativ och anrop till åtgärd som är tillgängliga i varje butik
- Vilka erbjudande typer som är tillgängliga för varje publicerings alternativ

## <a name="commercial-marketplace-publishing-options"></a>Publicerings alternativ för kommersiell Marketplace

I följande tabell visas publicerings alternativen för erbjudande typer i Microsoft AppSource och Azure Marketplace.

|   | **Lista (kontakt)**  | **Lista (utvärderings version)**  | **Kostnadsfri** | **BYOL** | **Transact**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Virtuell dator** |  |  |  | Azure Marketplace |  Azure Marketplace |
| **Azure-appar (flera virtuella datorer)** |  |  | Azure Marketplace | Azure Marketplace | Azure Marketplace  |
| **Behållar avbildning** |  |  | Azure Marketplace | Azure Marketplace |   |
| **IoT Edge modul** |  |  | Azure Marketplace | Azure Marketplace |   |
| **Hanterade tjänster** |  |  |  | Azure Marketplace |   |
| **Konsulttjänster** | Båda butiker |  |  |  |   |
| **SaaS-app** | Båda butiker | Båda butiker | Båda butiker |  | Både butiker * |
| **Microsoft 365 app** | AppSource | AppSource |  |  | AppSource * *  |
| **Dynamics 365-tillägg** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; Transact-erbjudanden för SaaS-program i Microsoft AppSource är för närvarande endast kredit kort.

&#42;&#42; Microsoft 365 erbjudanden är kostnads fria att installeras och kan säljas via SaaS-erbjudandet som en licens tjänst. Mer information finns i köpa [ditt Office 365-tillägg via Microsofts kommersiella Marketplace](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="selecting-a-storefront"></a>Välja en butik

Innan du väljer ett publicerings alternativ är det viktigt att förstå kraven på butik för kommersiella Marketplace-lösningar, appar och tjänster. Varje butik hanterar unika kund krav och riktar sig till specifika mål grupper. Din erbjudande typ, Transact-funktioner och kategori eller bransch bestämmer var du vill publicera erbjudandet.

**Microsoft AppSource** program är branschspecifika lösningar som kan vara inbyggda i Azure eller som är inbyggda för: Dynamics 365, Office 365, Power BI eller Power Apps. AppSource Consulting Services är erbjudanden för professionella tjänster som hjälper kunder att komma igång med eller påskynda användningen av Dynamics 365 och Power BI.

**Azure Marketplace** -program är tekniska "Build-block"-lösningar som är inbyggda eller inbyggda för Azure och som är avsedda för en IT-eller Developer-publik. Konsult tjänster för Azure Marketplace är professionella tjänster som hjälper kunder att komma igång med eller påskynda användningen av Azure.

>[!Note]
>"Cross-List" (endast för SaaS-appar): om ditt SaaS-erbjudande är avsett för en teknisk publik (Azure Marketplace) och en företags mål grupp (AppSource) kan du välja en kategori och/eller under kategori som gäller för antingen butiker. Tänk på att mindful "Cross-List" ditt erbjudande ska baseras på ett värde förslag som sträcker sig till båda mål grupperna. Klicka [här](./gtm-offer-listing-best-practices.md#categories) om du vill visa kategorier som är tillämpliga för varje butik.

## <a name="choose-a-publishing-option"></a>Välj ett publicerings alternativ

De publicerings alternativ som är tillgängliga erbjuder differentierade kund engagemang samtidigt som du får åtkomst till lead-delning och [kommersiella marknads förmåner](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Anteckna anrop till åtgärd som motsvarar publicerings alternativet:

| **Publicerings alternativ**    | **Beskrivning**  |
| :------------------- | :-------------------|
| **Lista** | En enkel lista över ditt program eller din tjänst som gör det möjligt för en kommersiell Marketplace-användare att be dig att ansluta till kunden via **kontakten mig** samtal till åtgärd. |
| **Utvärdering** | Använd den kommersiella Marketplace för att förbättra identifieringen och automatisera etableringen av lösningens utvärderings version, vilket gör det möjligt för potentiella användare att använda din SaaS, IaaS eller Microsoft app-upplevelse utan kostnad under en begränsad tid innan de köper. Anrop till åtgärd som används för alternativet för utvärdering av utvärdering är antingen **kostnads fri utvärderings version** eller **test enhet**. |
| **BYOL** | Använd den kommersiella Marketplace för att förbättra identifieringen och automatisera etableringen av din lösning och slutföra den finansiella transaktionen separat. Typer av BYOL-erbjudanden är idealiska för lokala och molnbaserade migreringar. Anropet till åtgärd är att **Hämta det nu**.
| **Transact** | Transact-erbjudanden säljs via den kommersiella marknads platsen. Microsoft ansvarar för fakturering och samlingar. Anropet till åtgärd är att **Hämta det nu**.|

> [!Note]
> När du använder alternativet för Transact-publicering är det viktigt att förstå pris-, fakturerings-, fakturerings-och utbetalnings aspekter innan du väljer en erbjudande typ och skapar ditt erbjudande. Läs mer i artikeln om de [kommersiella Transact-funktionerna i Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Nästa steg

- När du bestämmer dig för ett publicerings alternativ kan du [välja den erbjudande typ](./publisher-guide-by-offer-type.md) som ska användas för att presentera ditt erbjudande.
- Granska behörighets kraven i avsnittet publicerings alternativ per erbjudande typ för att slutföra valet och konfigurationen av ditt erbjudande.
- Granska publicerings mönstren efter butik för exempel på hur din lösning mappar till en erbjudande typ och konfiguration.
