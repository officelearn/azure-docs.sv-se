---
title: Visa och ladda ned din Azure-faktura
description: Beskriver hur du visar och laddar ned din Azure-faktura.
keywords: billing invoice,invoice download,azure invoice,azure usage
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: banders
ms.openlocfilehash: 16534343a831f0802a60a9214f567742153360e0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479010"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Visa och ladda ned din faktura för Microsoft Azure

Du kan ladda ned din faktura i [Azure-portalen](https://portal.azure.com/) eller få den skickad via e-post. Om du är Azure-kund med ett Enterprise-avtal (EA-kund) kan du inte ladda ned organisationens faktura. Fakturorna skickas i stället till den person som tar emot fakturor för registreringen.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="when-invoices-are-generated"></a>När fakturor genereras

En faktura genereras baserat på din typ av faktureringskonto. Fakturor skapas för faktureringskonton av typen Microsoft Online Service Program (MOSP), Microsoft-kundavtal (MCA) och Microsoft-partneravtal (MPA). Fakturor skapas också för faktureringskonton för Enterprise-avtal (EA). Fakturor för EA-faktureringskonton visas dock inte i Azure-portalen.

Du kan läsa mer om faktureringskonton och se vilken typ av faktureringskonto du har i [Visa faktureringskonton i Azure-portalen](../manage/view-all-accounts.md).

## <a name="invoices-for-mosp-billing-accounts"></a>Fakturor för MOSP-faktureringskonton

Ett MOSP-faktureringskonto skapas när du registrerar dig för Azure via Azure-webbplatsen. Det gäller till exempel när du registrerar dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) eller ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller som [Visual studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Kunder i utvalda regioner som registrerar sig via Azure-webbplatsen för ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) kan även ha ett faktureringskonto för ett MCA.

Om du är osäker på vilken typ av faktureringskonto du har kan du läsa mer under [Kontrollera typen av faktureringskonto](../manage/view-all-accounts.md#check-the-type-of-your-account) innan du följer anvisningarna i den här artikeln. 

Ett MOSP-faktureringskonto kan ha följande fakturor:

**Avgifter för Azure-tjänster** – en faktura skapas för varje Azure-prenumeration som innehåller Azure-resurser som används av prenumerationen. Fakturan innehåller debiteringar för en faktureringsperiod. Faktureringsperioden bestäms av den dag i månaden då prenumerationen skapades.

John skapar till exempel *Azure pren 01* den 5 mars och *Azure pren 02* den 10 mars. Fakturan för *Azure pren 01* kommer att ha avgifter från den femte dagen i månaden till den fjärde dagen i nästa månad. Fakturan för *Azure pren 02* omfattar avgifter från den tionde dagen i månaden till den nionde dagen i nästa månad. Fakturorna för alla Azure-prenumerationer skapas vanligtvis på den dag i månaden då kontot skapades, men det kan vara upp till två dagar senare. Om John till exempel skapade sitt konto den 2 februari, kommer fakturorna för både *Azure pren 01* och *Azure pren 02* normalt att skapas den andra dagen i varje månad, men det kan vara upp till två dagar senare.

**Azure Marketplace, reservationer och virtuella datorer** – en faktura skapas för reservationer, Marketplace-produkter och virtuella Spot-datorer som köpts med en prenumeration. Fakturan visar motsvarade avgifter från föregående månad. John har till exempel köpt en reservation den 1 mars och en annan reservation den 30 mars. En enda faktura skapas för båda reservationerna i april. Fakturan för Azure Marketplace, reservationer och virtuella Spot-datorer skapas alltid runt den nionde dagen i månaden.

Om du betalar för Azure med kreditkort och du köper en reservation, genererar Azure en omedelbar faktura. Men när du debiteras med en faktura debiteras du för reservationen på nästa månads faktura.

**Supportavtal för Azure** – en faktura genereras varje månad för prenumerationen på supportavtalet. Den första fakturan genereras på inköpsdagen eller upp till två dagar senare. Efterföljande fakturorna för supportavtalet skapas vanligtvis den dag i månaden då kontot skapades, men det kan vara upp till två dagar senare.

## <a name="download-your-mosp-azure-subscription-invoice"></a>Ladda ned din faktura för MOSP Azure-prenumeration

En faktura genereras bara för en prenumeration som tillhör ett faktureringskonto för ett MOSP. [Kontrollera åtkomsten till ett MOSP-konto](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Du måste ha rollen som kontoadministratör för en prenumeration om du ska kunna hämta fakturan. Användare med roller som ägare, deltagare eller läsare kan ladda ned fakturan om kontoadministratören har gett dem behörighet. Mer information finns i [Tillåta användare att ladda ned fakturor](../manage/manage-billing-access.md#opt-in).

1. Välj din prenumeration på [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen.
1. Välj **Fakturor** i faktureringsavsnittet.  
    ![Skärmbild som visar en användare som väljer alternativet Fakturor för en prenumeration](./media/download-azure-invoice/select-subscription-invoice.png)
1. Välj **Ladda ned** för att ladda ned en PDF-version av fakturan och välj sedan **Ladda ned** under fakturaavsnittet.  
    [![Skärmbild som visar faktureringsperioder, nedladdningsalternativet och de totala avgifterna för varje faktureringsperiod](./media/download-azure-invoice/download-invoice-subscription.png)](./media/download-azure-invoice/download-invoice-subscription-zoomed.png#lightbox)
1. Du kan även ladda ned en daglig uppdelning av förbrukade kvantiteter och avgifter genom att välja **Ladda ned** i avsnittet med användningsinformation. Det kan ta några minuter att förbereda CSV-filen.  
    ![Skärmbild som visar Ladda ned faktura och förbrukningssidan](./media/download-azure-invoice/usage-and-invoice-subscription.png)

Mer information om din faktura finns i [Förstå fakturan för Microsoft Azure](../understand/review-individual-bill.md). Om du behöver hjälp med att hantera dina kostnader kan du läsa [Förhindra oväntade kostnader med Azure-fakturering och kostnadshantering](../manage/getting-started.md).

## <a name="download-your-mosp-support-plan-invoice"></a>Ladda ned fakturan för MOSP supportavtal

En faktura genereras bara för en prenumeration på ett supportavtal som tillhör ett MOSP-faktureringskonto. [Kontrollera åtkomsten till ett MOSP-konto](../manage/view-all-accounts.md#check-the-type-of-your-account). 

Du måste ha rollen som kontoadministratör i prenumerationen för supportavtalet om du ska kunna hämta fakturan.

1.  Logga in på [Azure-portalen](https://portal.azure.com).
1.  Sök efter **Kostnadshantering + fakturering**.  
    ![Skärmbild som visar en sökning i portalen efter kostnadshantering och fakturering](./media/download-azure-invoice/search-cmb.png)
1.  Välj **Fakturor** till vänster.
1.  Välj prenumerationen för supportavtalet och välj sedan **Ladda ned**.  
    [![Skärmbild som visar en lista över faktureringsprofiler](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1.  Välj **Ladda ned** för att ladda ned en PDF-version av fakturan.  
    ![Skärmbild som visar faktureringsperioder, nedladdningsalternativet och de totala avgifterna för varje faktureringsperiod](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-an-mosp-subscription-invoice"></a>Tillåt andra att ladda ned en faktura för MOSP-prenumerationen

PDF-versionen av fakturan innehåller personlig information om kontoadministratören, så kontoadministratören för den här prenumerationen måste ge andra användare behörighet att ladda ned fakturan. När behörigheten har beviljats kan följande personer och användare med lämpliga roller ladda ned prenumerationsfakturor:

- Användare
- Grupper
- Tjänstens huvudnamn med en ägare
- Deltagare
- Läsare
- Administratör för användaråtkomst
- Faktureringsläsare
- Medadministratör
- Tjänstadministratör

Så här laddar du ned en faktura:

1.  Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör för prenumerationen.
1.  Sök efter **Kostnadshantering + fakturering**.  
1.  Välj **Fakturor** till vänster.
1.  Välj din Azure-prenumeration och välj sedan **Åtkomst till faktura**.  
    [![Skärmbild som visar val av åtkomst till faktura](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)
1.  Välj **På** och sedan **Spara** överst på sidan.  
    ![Skärmbild som visar val av På för åtkomst till faktura](./media/download-azure-invoice/cmb-access-to-invoice.png)

## <a name="get-mosp-subscription-invoice-in-email"></a>Få faktura för MOSP-prenumeration via e-post

Du måste ha rollen som kontoadministratör för en prenumeration eller ett supportavtal för att välja att ta emot fakturan via e-post. E-postfakturor är endast tillgängliga för prenumerationer och supportavtal, inte för reservationer eller Azure Marketplace-köp. När du har valt till alternativet kan du lägga till ytterligare mottagare som också får fakturan via e-post.

1.  Logga in på [Azure-portalen](https://portal.azure.com).
1.  Sök efter **Kostnadshantering + fakturering**.  
1.  Välj **Fakturor** till vänster.
1.  Välj din Azure-prenumeration eller supportavtalsprenumeration och välj sedan **E-postfaktura**.  
    [![Skärmbild som visar en lista över faktureringsprofiler](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
1. Välj **Anmäl** och godkänn villkoren.  
    ![Skärmbild som visar steg 2 i anmälningsflödet](./media/download-azure-invoice/invoice-article-step02.png)
1. Fakturan skickas till önskad e-postadress för kommunikation. Du kan uppdatera e-postadressen i [kontaktinformation för ditt faktureringskonto](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/Properties).  
    ![Skärmbild som visar steg 3 i anmälningsflödet](./media/download-azure-invoice/invoice-article-step03-verify-email.png)

## <a name="share-your-mosp-invoices-with-others-by-email"></a>Dela dina MOSP-fakturor med andra via e-post

Du kanske vill dela fakturorna för prenumerationen och supportavtalet varje månad med ditt redovisningsteam eller skicka dem till någon av dina andra e-postadresser.

1. Följ stegen i [Få prenumerationens och supportavtalets fakturor via e-post](#get-mosp-subscription-invoice-in-email) och välj **Konfigurera mottagare**.  
    ![Skärmbild som visar en användare som väljer att konfigurera mottagare](./media/download-azure-invoice/invoice-article-step03.png)
1. Ange en e-postadress och välj sedan **Lägg till mottagare**. Du kan lägga till flera e-postadresser.  
    ![Skärmbild som visar en användare som lägger till ytterligare mottagare](./media/download-azure-invoice/invoice-article-step04.png)
1. När du har lagt till alla e-postadresser väljer du **klar** längst ned på skärmen.

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>Fakturor för MCA- och MPA-faktureringskonton

Ett MCA-faktureringskonto skapas när din organisation arbetar med en Microsoft-representant för att signera ett MCA. Vissa kunder i utvalda regioner som registrerar sig via Azure-webbplatsen för ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) kan även ha ett faktureringskonto för ett MCA. Mer information finns i [Kom igång med ditt MCA-faktureringskonto](../understand/mca-overview.md).

Ett MPA-faktureringskonto skapas för CSP-partner (leverantörer av molnlösningar) för kundhantering i den nya köpupplevelsen. Partner måste ha minst en kund med en [Azure-plan](https://docs.microsoft.com/partner-center/purchase-azure-plan) för att kunna hantera sina faktureringskonton på Azure-portalen. Mer information finns i [Kom igång med ditt MPA-faktureringskonto](../understand/mpa-overview.md).

I början av månaden skapas en månadsfaktura för varje faktureringsprofil på ditt konto. Fakturan innehåller respektive avgifter för alla Azure-prenumerationer och andra inköp från föregående månad. John skapade till exempel *Azure pren 01* den 5 mars och *Azure pren 02* den 10 mars. Han köpte *Azure support 01*-prenumerationen den 28 mars med *faktureringsprofil 01*. John får en enda faktura i början av april som innehåller avgifter för både Azure-prenumerationerna och supportavtalet.

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>Ladda ned en faktura för en MCA-eller MPA-faktureringsprofil

Du måste ha rollen som ägare, deltagare, läsare eller fakturaansvarig för faktureringsprofilen för att kunna ladda ned fakturor från Azure-portalen. Användare med en roll som ägare, deltagare eller läsare för ett faktureringskonto kan ladda ned fakturor för alla faktureringsprofiler i kontot.

1.  Logga in på [Azure-portalen](https://portal.azure.com).
1.  Sök efter **Kostnadshantering + fakturering**.  
1. Välj **Fakturor** till vänster.
    [![Skärmbild som visar sidan fakturor för ett MCA-faktureringskonto](./media/download-azure-invoice/mca-billing-profile-invoices.png)](./media/download-azure-invoice/mca-billing-profile-invoices-zoomed.png#lightbox)
1. Välj den faktura du vill ladda ned i fakturatabellen.
1. Välj **Ladda ned en PDF-faktura** högst upp på sidan.  
    ![Skärmbild som visar nedladdning av faktura-PDF](./media/download-azure-invoice/mca-billing-profile-download-invoice.png)
1. Du kan även ladda ned en CSV-fil som innehåller en daglig uppdelning av förbrukade kvantiteter och beräknade avgifter genom att välja **Hämta Azure-användning**. Det kan ta några minuter att förbereda CSV-filen.

## <a name="get-your-billing-profiles-invoice-in-email"></a>Få fakturan för din faktureringsprofils via e-post

Du måste ha en roll som ägare eller deltagare för faktureringsprofilen eller dess faktureringskonto för att kunna uppdatera inställningarna för e-postfakturor. När du har valt till alternativet får alla användare med rollen ägare, deltagare, läsare eller fakturaansvarig i en faktureringsprofil dess faktura via e-post. 

1.  Logga in på [Azure-portalen](https://portal.azure.com).
1.  Sök efter **Kostnadshantering + fakturering**.  
1.  Välj **Fakturor** på vänster sida och välj sedan **E-postfaktura** överst på sidan.  
    [![Skärmbild som visar sidan fakturor för ett MCA-faktureringskonto](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  Om du har flera faktureringsprofiler väljer du en fakturerings profil och sedan **Välj**.  
    ![Skärmbild som visar sidan fakturor för ett MCA-faktureringskonto](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)
1.  Välj **Uppdatera**.

## <a name="give-others-access-to-mca-or-mpa-invoices"></a>Ge andra åtkomst till MCA- eller MPA-fakturor

Du kan ge andra åtkomst att visa, ladda ned och betala fakturor genom att tilldela dem rollen som fakturaansvarig för en MCA- eller MPA faktureringsprofil. Om du har anmält dig för att få din faktura via e-post får dessa användare också fakturorna via e-post.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering + fakturering**.  
1. Välj **Faktureringsprofiler** till vänster. I listan med faktureringsprofiler väljer du en faktureringsprofil som du vill tilldela en roll som fakturaansvarig för.  
   ![Skärmbild som visar en lista över faktureringsprofiler](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. Välj **Åtkomstkontroll (IAM)** på vänster sida och välj sedan **Lägg till** överst på sidan.  
    ![Skärmbild som visar sidan för åtkomstkontroll](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. I listrutan Roll väljer du **Fakturaansvarig**. Ange e-postadressen för den användare som ska få åtkomst. Välj **Spara** för att tilldela rollen.  
   ![Skärmbild som visar tillägg av en användare som fakturaansvarig](./media/download-azure-invoice/mca-added-invoice-manager.png)

## <a name="why-you-might-not-see-an-invoice"></a><a name="noinvoice"></a> Orsaker till att du kanske inte ser en faktura

Det kan finnas flera orsaker till att den inte visas:

- Det är mindre än 30 dagar sedan du började prenumerera på Azure. 
- Azure fakturerar dig några dagar efter faktureringsperiodens slut. Därför kan det hända att det inte har genererats någon faktura ännu.
- Du har inte behörighet att visa fakturor.
    Du måste ha ett MCA- eller MPA-faktureringskonto, du måste ha rollen ägare, deltagare, läsare eller fakturaansvarig för en faktureringsprofil eller rollen ägare, deltagare eller läsare för faktureringskontot för att kunna visa fakturor. För andra prenumerationer ser du kanske inte fakturorna om du inte är kontoadministratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](../manage/manage-billing-access.md).
- Om du har ett MOSP-faktureringskonto och har registrerat dig för ett kostnadsfritt Azure-konto eller en prenumeration med ett månatligt kreditbelopp får du bara en faktura när du överskrider det månatliga kreditbeloppet. Om du har ett faktureringskonto för ett MCA eller ett MPA får du alltid en faktura.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om fakturor och avgifter finns i:

- [Visa och ladda ned information om din användning och dina avgifter för Microsoft Azure](download-azure-daily-usage.md)
- [Förstå fakturan för Microsoft Azure](review-individual-bill.md)
- [Förstå termerna på din Azure-faktura](understand-invoice.md)

Om du har ett MCA kan du läsa mer här:

- [Förstå avgifterna på fakturan för din faktureringsprofil](review-customer-agreement-bill.md)
- [Förstå termerna på fakturan för din faktureringsprofil](mca-understand-your-invoice.md)
- [Förstå filen med användning och avgifter för Azure för din faktureringsprofil](mca-understand-your-usage.md)


