---
title: Organisera din faktura utifrån dina behov – Azure
description: Lär dig hur du organiserar kostnaden på din faktura.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c170a6a1a731a648c16e0081e760947256df8a0e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991066"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Organisera kostnader genom att anpassa ditt fakturerings konto

Ditt fakturerings konto för Microsofts kund avtal ger dig flexibilitet att organisera dina kostnader baserat på dina behov, oavsett om det är av avdelning, projekt eller utvecklings miljö. 

I den här artikeln beskrivs hur du kan använda Azure Portal för att organisera dina kostnader. Den gäller för ett fakturerings konto för ett Microsofts kund avtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Strukturera ditt konto med fakturerings profiler och faktura avsnitt

I fakturerings kontot för ett Microsofts kund avtal använder du fakturerings profiler och faktura avsnitt för att organisera dina kostnader.

![Skärm bild som visar Microsoft kund avtals fakturerings hierarki](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Faktureringsprofil

En fakturerings profil representerar en faktura och relaterad fakturerings information som betalnings metoder och fakturerings adress. I början av månaden skapas en månadsfaktura för varje faktureringsprofil på ditt konto. Fakturan innehåller avgifter för Azure-användning och andra inköp från föregående månad.

En fakturerings profil skapas automatiskt tillsammans med ditt fakturerings konto när du registrerar dig för Azure. Du kan skapa ytterligare fakturerings profiler för att organisera dina kostnader på flera månads fakturor. 

> [!IMPORTANT]
>
> Att skapa ytterligare fakturerings profiler kan påverka den totala kostnaden. Mer information finns i [saker att tänka på när du lägger till nya fakturerings profiler](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Fakturaavsnitt

Avsnittet faktura visar en gruppering av kostnader i din faktura. Avsnittet faktura skapas automatiskt för varje fakturerings profil i ditt konto. Du kan skapa ytterligare avsnitt för att organisera dina kostnader utifrån dina behov. Varje faktura avsnitt visas på fakturan med de kostnader som tillkommer den månaden. 

Bilden nedan visar en faktura med två faktura avsnitt – teknik och marknadsföring. Sammanfattnings-och detalj avgifterna för varje avsnitt visas på fakturan. Priserna som visas i bilden är endast till exempel syfte och representerar inte de faktiska priserna för Azure-tjänster. 

![Bild som visar en faktura med avsnitt](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Fakturerings konto struktur för vanliga scenarier

I det här avsnittet beskrivs vanliga scenarier för att ordna kostnader och motsvarande fakturerings konto strukturer:

|Scenario  |Struktur  |
|---------|---------|
|Jack-loggar för Azure och behöver en enda månads faktura. | En fakturerings profil och ett faktura avsnitt. Den här strukturen konfigureras automatiskt för uttag när han registrerar sig för Azure och inga ytterligare steg krävs. |

![Informations grafik för ett enkelt fakturerings scenario](./media/mca-section-invoice/organize-billing-scenario1.png)

|Scenario  |Struktur  |
|---------|---------|
|Contoso är en liten organisation som behöver en enda månads faktura men grupp kostnader genom sina avdelningar – marknadsföring och ekonomi.  | En fakturerings profil för Contoso och ett faktura avsnitt var för marknadsförings-och ekonomi avdelningen. |

![Informations grafik för ett enkelt fakturerings scenario](./media/mca-section-invoice/organize-billing-scenario2.png)

|Scenario  |Struktur  |
|---------|---------|
|Fabrikam är en organisation i medel stora företag som behöver separata fakturor för sina tekniker och marknadsförings avdelningar. För teknik avdelningen vill de gruppera kostnaderna efter miljöer – produktion och utveckling.  | En fakturerings profil för alla marknadsförings-och finans avdelningar. För marknadsförings avdelningen, ett faktura avsnitt var för produktions-och utvecklings miljön. |

![Informations grafik för ett enkelt fakturerings scenario](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Avsnittet Skapa en ny faktura

För att kunna skapa ett fakturaavsnitt behöver du vara **faktureringsprofilsägare** eller **faktureringsprofilsdeltagare**. Mer information finns i avsnittet [Hantera faktura avsnitt för fakturerings profil](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i portalen efter kostnadshantering och fakturering](./media/mca-section-invoice/search-cmb.png)

3. Välj **fakturerings profiler** i rutan till vänster. Välj en fakturerings profil i listan. Det nya avsnittet kommer att visas på fakturan för den valda fakturerings profilen. 

   [![skärm bild som visar listan över fakturerings profiler](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Välj **faktura avsnitt** i den vänstra rutan och välj sedan **Lägg till** överst på sidan.

   [![skärm bild som visar lägga till faktura avsnitt](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Ange ett namn för faktura avsnittet. 

   [![skärm bild som visar sidan för att skapa faktura avsnitt](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Välj **Skapa**.

## <a name="create-a-new-billing-profile"></a>Skapa en ny fakturerings profil

Om du vill skapa en fakturerings profil måste du vara ägare till ett **fakturerings konto** eller en **fakturerings konto deltagare**. Mer information finns i [hantera fakturerings profiler för fakturerings konto](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> Att skapa ytterligare fakturerings profiler kan påverka den totala kostnaden. Mer information finns i [saker att tänka på när du lägger till nya fakturerings profiler](#things-to-consider-when-adding-new-billing-profiles).

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i portalen efter kostnadshantering och fakturering](./media/mca-section-invoice/search-cmb.png)

3. Välj **fakturerings profiler** i rutan till vänster och välj sedan **Lägg till** överst på sidan.

   [![skärm bild som visar listan över fakturerings profiler](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Om du inte ser knappen Lägg till på sidan debiterings profil är funktionen inte tillgänglig för ditt konto. För närvarande är det bara tillgängligt för konton som har kon figurer ATS när du arbetar med en Microsoft-representant.

4. Fyll i formuläret och klicka på **Skapa**.

   [![skärm bild som visar sidan för att skapa fakturerings profil](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Field  |Definition  |
    |---------|---------|
    |Namn     | Ett visnings namn som hjälper dig att enkelt identifiera fakturerings profilen i Azure Portal.  |
    |INKÖPs nummer    | Ett valfritt inköps order nummer. Inköps order numret kommer att visas på fakturorna som genereras för fakturerings profilen. |
    |Faktureringsadress   | Fakturerings adressen kommer att visas på fakturorna som genereras för fakturerings profilen. |
    |E-postfaktura   | Markera rutan e-postfaktura om du vill ta emot fakturor för fakturerings profilen per e-post. Om du inte väljer kan du Visa och ladda ned fakturorna i Azure Portal.|

5. Välj **Skapa**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Länka avgifter till faktura avsnitt och fakturerings profiler

När du har anpassat ditt fakturerings konto baserat på dina behov kan du länka prenumerationer och andra produkter till önskat faktura avsnitt och fakturerings profil.

### <a name="link-a-new-subscription"></a>Länka en ny prenumeration

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Prenumerationer**.

   [![skärm bild som visar sökning i portalen för prenumeration](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Välj **Lägg till** längst upp på sidan.

   ![Skärm bild som visar knappen Lägg till i prenumerationsvyn](./media/mca-section-invoice/subscription-add.png)

4. Om du har åtkomst till flera fakturerings konton väljer du ditt fakturerings konto för ditt Microsoft-kund avtal.

   ![Skärm bild som visar knappen Lägg till i prenumerationsvyn](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Välj den fakturerings profil som ska faktureras för prenumerationens användning. Avgifterna för användning av Azure och andra inköp för den här prenumerationen kommer att faktureras till den valda fakturerings profilens faktura.

6. Välj faktura avsnittet om du vill länka prenumerationens kostnader. Avgifterna visas under det här avsnittet på fakturerings profilens faktura.

7. Välj en Azure-plan och ange ett eget namn för din prenumeration. 

9. Klicka på **Skapa**.  

### <a name="link-existing-subscriptions-and-products"></a>Länka befintliga prenumerationer och produkter

Om du har befintliga Azure-prenumerationer eller andra produkter, till exempel Azure Marketplace och käll resurser för appar, kan du flytta dem från deras befintliga faktura avsnitt till ett annat faktura avsnitt för att ordna om dina kostnader. 

> [!IMPORTANT]
>
> Prenumerationer och andra produkter kan bara flyttas mellan faktura avsnitt som tillhör samma fakturerings profil. Det går inte att flytta prenumerationer och produkter över faktura avsnitt i olika fakturerings profiler.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning efter prenumerationer i portalen](./media/mca-section-invoice/search-cmb.png)

3. Om du vill länka en prenumeration till en ny faktura-sektion väljer du **Azure-prenumerationer** på vänster sida av skärmen. För andra produkter, till exempel Azure Marketplace och käll resurser för appar, väljer du **återkommande avgifter**.

   [![skärm bild som visar alternativet att ändra faktura avsnitt](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. På sidan klickar du på ellips (tre punkter) för den prenumeration eller produkt som du vill länka till ett nytt faktura avsnitt. Välj **avsnittet Ändra faktura**.

5. Välj den nya fakturan-avsnittet i list rutan. List rutan visar bara faktura avsnitt som är associerade med samma fakturerings profil som avsnittet befintlig faktura.

    [![skärm bild som visar Välj ett nytt faktura avsnitt](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Välj **Spara**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Saker att tänka på när du lägger till nya fakturerings profiler

### <a name="azure-usage-charges-may-be-impacted"></a>Avgifter för Azure-användning kan påverkas

I ditt fakturerings konto för ett Microsofts kund avtal sammanställs Azure-användningen varje månad för varje fakturerings profil. Priserna för Azure-resurser med skiktat pris bestäms utifrån användningen för varje fakturerings profil separat. Användningen sammanställs inte i fakturerings profilerna när priset beräknas. Detta kan påverka den totala kostnaden för Azure-användning för konton med flera fakturerings profiler.

Nu ska vi titta på ett exempel på hur kostnaderna varierar i två scenarier. Priserna som används i scenarierna är endast till exempel syfte och representerar inte de faktiska priserna för Azure-tjänster.

#### <a name="you-only-have-one-billing-profile"></a>Du har bara en fakturerings profil.

Vi antar att du använder Azure Block Blob Storage, som kostar USD. 00184 per GB för de första 50 terabyte (TB) och sedan. 00177 per GB för nästföljande 450 terabyte (TB). Du har använt 100 TB i de prenumerationer som debiteras din fakturerings profil, så hur mycket du skulle debiteras.

|  Pris nivå (USD) |Kvantitet | Belopp (USD)|
|---------|---------|---------|
|1,84 per TB för den första 50 TB/månad    | 50 TB        | 92,0   |
|1,77 per TB för nästa 450 TB/månad    |  50 TB         | 88,5   |
|Totalt     |     100 TB  | 180,5

Det totala antalet avgifter för att använda 100 TB data i det här scenariot är **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>Du har flera fakturerings profiler.

Anta nu att du har skapat en annan fakturerings profil och använt 50 GB genom prenumerationer som faktureras till den första fakturerings profilen och 50 GB via prenumerationer som debiteras för den andra fakturerings profilen. här är hur mycket du skulle debiteras.

`Charges for the first billing profile`

|  Pris nivå (USD) |Kvantitet | Belopp (USD)|
|---------|---------|---------|
|1,84 per TB för den första 50 TB/månad    | 50 TB        | 92,0  |
|1,77 per TB för nästa 450 TB/månad    |  0 TB         | 0.0  |
|Totalt     |     50 TB  | 92,0 

`Charges for the second billing profile`

|  Pris nivå (USD) |Kvantitet | Belopp (USD)|
|---------|---------|---------|
|1,84 per TB för den första 50 TB/månad    | 50 TB        | 92,0  |
|1,77 per TB för nästa 450 TB/månad    |  0 TB         | 0.0  |
|Totalt     |     50 TB  | 92,0 

Den totala kostnaden för att använda 100 TB data i det här scenariot är **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Fördelarna med Azure reservation kanske inte gäller för alla prenumerationer

Azure-reservationer med delad omfattning tillämpas på prenumerationer i en enda fakturerings profil och delas inte mellan fakturerings profiler. 

![Informations grafik för reservations program för olika fakturerings konto strukturer](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

I bilden ovan har contoso två prenumerationer. Azure reservation-förmånen tillämpas på olika sätt beroende på hur fakturerings kontot är strukturerat. I scenariot till vänster tillämpas reservations förmånen för båda prenumerationerna som debiteras för den tekniska fakturerings profilen. I scenariot till höger kommer reservations förmånen endast att tillämpas på prenumeration 1 eftersom det är den enda prenumerationen som debiteras den tekniska fakturerings profilen. 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Skapa ytterligare en Azure-prenumeration för Microsoft-kundavtal](create-subscription.md)
- [Hantera faktureringsroller i Azure-portalen](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Få faktureringsägarskap för Azure-prenumerationer från användare i andra faktureringskonton](mca-request-billing-ownership.md)
