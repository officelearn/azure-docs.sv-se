---
title: Fastställa din publicering alternativ i Azure Marketplace | Azure
description: Den här artikeln beskrivs kriterierna för kvalificering och publicera krav partner försöker förstå hur du publicerar appar på Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 49723a80bb198e8151c7e85a90453183068c9d2b
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962827"
---
# <a name="determine-your-publishing-option"></a>Fastställa din publiceringsalternativ
Publiceringsalternativ som du väljer för ditt erbjudande kopplat direkt till både marketplace GTM-fördelar och krav för berättigande. Det är viktigt att valet av publicerings alternativ och erbjudande typ definierar hur användarna ska interagera med ditt Marketplace-erbjudande.

Om du vill konfigurera ditt erbjudande måste du förstå följande viktiga Marketplace-koncept: publicerings alternativ, erbjudande typer och konfiguration samt anrop till åtgärd som styr hur och var ditt erbjudande presenteras i Marketplace-butiker.

![](./media/marketplace-publishers-guide/storefronts_options_table.png)


I den här artikeln får du lära dig...
<ul><li>    Så här kontrollerar du lämpliga storefront för din lösning </ul></li>
<ul><li>    Vilket publiceringsalternativ och anrop till åtgärden finns i varje butik </ul></li>
<ul><li>    Vilka erbjuder typer är tillgängliga för varje publiceringsalternativ </ul></li>


## <a name="selecting-a-storefront-publishing-option-and-offer-type-for-your-solution"></a>Att välja en butik, publicera och erbjudande ange för din lösning

Innan du väljer en publiceringsalternativ är det viktigt att förstå storefront kvalificeringskraven för marketplace-lösningar, appar och tjänster:

**Azure Marketplace** -program är tekniska "Build-block"-lösningar som är inbyggda eller inbyggda för Azure och som är avsedda för en IT-eller Developer-publik. Azure Marketplace-konsulttjänster är professionella tjänster erbjudanden som hjälpa kunderna att komma igång med eller accelerera användningen av Azure.

**AppSource** -program är branschspecifika lösningar som kan vara inbyggda i Azure eller som är inbyggda för: Dynamics 365, Office 365, Power BI eller Power Apps. AppSource-konsulttjänster är professionella tjänster-erbjudanden som hjälpa kunderna att komma igång med eller accelerera användningen av Dynamics 365 och Power BI.


## <a name="understand-storefront-selection"></a>Förstå storefront val

Butik där ditt erbjudande kommer att presenteras, Azure Marketplace och/eller AppSource bestäms automatiskt av din erbjudande information och mål grupp, samt de kategorier och branscher som du har valt när du skapar erbjudandet. 

>[!Note]
>”Cross-lista” (för SaaS-appar): när en lista eller en utvärderingsversion-baserade erbjudandet uppfyller kriterierna för både en tekniska och affärsmässiga målgrupp erbjudandet visas i båda butiker. Läs mer om publicering alternativen nedan.

## <a name="choose-a-publishing-option"></a>Välj en publiceringsalternativ

De publicerings alternativ som är tillgängliga erbjuder differentierade kund engagemang samtidigt som du får åtkomst till lead-delning och [kommersiella marknads förmåner](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits).  Observera den anrop-till-åtgärd som motsvarar publiceringsalternativet:

| **Publiceringsalternativ**    | **Beskrivning**  |
| :------------------- | :-------------------|
| **Lista** | Enkel lista över programmet eller tjänsten som gör det möjligt för en marketplace-användare att begära att du kan ansluta till kunden via den **kontakta mig** anrop till åtgärden. |
| **Utvärdering** | Använd Marketplace för att förbättra identifieringen och automatisera etableringen av lösningens utvärderings version, vilket gör det möjligt för potentiella användare att använda din SaaS, IaaS eller Microsoft app-upplevelse utan kostnad under en begränsad tid innan de köper. Anrop till åtgärd som används för alternativet för utvärdering av utvärdering är antingen: **Gratis utvärderings version** eller **test enhet**. |
|**BYOL**  |Använd Marketplace för att förbättra identifieringen och automatisera etableringen av lösningen och slutför den finansiella transaktionen separat. Typer av BYOL-erbjudanden är idealiska för lokala och molnbaserade migreringar. Anropet till åtgärden hämtas **nu**.
| **Transact** | Ditt program körs på Azure och kan tillhandahållas som en resurs direkt i kundens Azure-prenumeration när kunden väljer åtgärden **Hämta nu** till åtgärd. Din programvarulicensavgifter kan du kan också köpas och faktureras via kundens val av betalningsmedel och villkor och du kan välja att erbjuda tidsbegränsad åtkomst till din **kostnadsfri utvärderingsversion av programvara** (endast tillgängligt för Azure Marketplace.) |

>[!Note]
>När du använder Transact publicera alternativet, är det viktigt att förstå de priser, fakturering, fakturering och payout att tänka på innan du väljer en typ av erbjudande och skapa ditt erbjudande. Granska den [Marketplace fakturering och affärsmässiga överväganden mer artikeln](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Nästa steg

*   När du bestämmer dig för ett publiceringsalternativ är du redo att [Välj typ av erbjudande](./publisher-guide-by-offer-type.md) som ska användas för att presentera ditt erbjudande.
*   Granska krav för berättigande i publiceringsalternativ erbjudandet typ avsnittet för att slutföra valet av och konfigurationen av ditt erbjudande.
*   Granska publicering mönster genom butik för exempel på hur din lösning mappar till en typ av erbjudande och konfiguration.



