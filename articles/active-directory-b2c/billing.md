---
title: Faktureringsmodell för Azure Active Directory B2C
description: Lär dig mer om Azure AD B2C:s månatliga aktiva användare (MAU) faktureringsmodell och hur du aktiverar fakturering för en viss Azure-prenumeration.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190015"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Faktureringsmodell för Azure Active Directory B2C

Azure Active Directory B2C(Azure AD B2C) användning faktureras till en länkad Azure-prenumeration och använder en månatlig aktiv användare (MAU) faktureringsmodell. Lär dig hur du länkar en Azure AD B2C-resurs till en prenumeration och hur MAU-faktureringsmodellen fungerar i följande avsnitt.

> [!IMPORTANT]
> Den här artikeln innehåller inte prisinformation. Den senaste informationen om fakturering och prissättning för användning finns i [Azure Active Directory B2C-priser](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="monthly-active-users-mau-billing"></a>Fakturering för aktiva användare (MAU) varje månad

Azure AD B2C-fakturering mäts på antalet unika användare med autentiseringsaktivitet inom en kalendermånad, så kallad månatliga klientanvändare (MAU) fakturering.

Från och med **den 1 november 2019**faktureras alla nyligen skapade Azure AD B2C-klienter per månad aktiva användare (MAU). Befintliga klienter som är [länkade till en prenumeration](#link-an-azure-ad-b2c-tenant-to-a-subscription) den 1 november 2019 eller senare debiteras per månad aktiva användare (MAU).

Om du har en befintlig Azure AD B2C-klient som har länkats till en prenumeration före den 1 november 2019 kan du välja att göra något av följande:

* Uppgradera till den månatliga faktureringsmodellen för aktiva användare (MAU) eller
* Be hur du autentiseringsmodellen ska vara per autentisering

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Uppgradera till månatliga faktureringsmodell för aktiva användare

Azure-prenumerationsägare som har administrativ åtkomst till Azure AD B2C-resursen kan växla till MAU-faktureringsmodellen. Faktureringsalternativ konfigureras i din Azure AD B2C-resurs.

Övergången till månatliga aktiva användare (MAU) fakturering är **oåterkallelig**. När du har konverterat en Azure AD B2C-resurs till den MAU-baserade faktureringsmodellen kan du inte återställa resursen till faktureringsmodellen per autentisering.

Så här gör du övergången till MAU-fakturering för en befintlig Azure AD B2C-resurs:

1. Logga in på [Azure-portalen](https://portal.azure.com) som prenumerationsägare.
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den Azure AD B2C-katalog som du vill uppgradera till MAU-fakturering.<br/>
    ![Katalog- och prenumerationsfilter i Azure-portalen](./media/billing/portal-mau-01-select-b2c-directory.png)
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. På sidan **Översikt** för Azure AD B2C-klienten väljer du länken under **Resursnamn**. Du dirigeras till Azure AD B2C-resursen i din Azure AD-klientorganisation.<br/>
    ![Azure AD B2C-resurslänk markerad i Azure-portalen](./media/billing/portal-mau-02-b2c-resource-link.png)
1. På **sidan Översikt för** Azure AD B2C-resursen väljer du länken **Per autentisering (Ändra till MAU)** under **Fakturerbara enheter.**<br/>
    ![Ändra till MAU-länk markerad i Azure-portalen](./media/billing/portal-mau-03-change-to-mau-link.png)
1. Välj **Bekräfta** om du vill slutföra uppgraderingen till MAU-fakturering.<br/>
    ![DIALOGRUTAN MAU-baserad faktureringsbekräftelse i Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Vad du kan förvänta dig när du övergår till MAU-fakturering från fakturering per autentisering

MAU-baserad mätning är aktiverad så fort du, prenumerationen/resursägaren, bekräftar ändringen. Din månatliga faktura återspeglar de enheter av autentisering faktureras fram till ändringen, och nya enheter av MAU börjar med ändringen.

Användare räknas inte dubbel under övergångsmånaden. Unika aktiva användare som autentiserar före ändringen debiteras en frekvens per autentisering under en kalendermånad. Samma användare ingår inte i MAU-beräkningen för återstoden av prenumerationens faktureringsperiod. Ett exempel:

* Contoso B2C-klienten har 1 000 användare. 250 användare är aktiva under en viss månad. Prenumerationsadministratören ändras från autentisering per autentisering till månatliga aktiva användare (MAU) den 10:e i månaden.
* Fakturering för 1:a-10:e debiteras med hjälp av modellen per autentisering.
  * Om 100 användare loggar in under den här perioden (1:a–10:e) taggas dessa användare som *betalda för månaden*.
* Fakturering från den 10:e (den effektiva tidpunkten för övergången) faktureras till MAU-kursen.
  * Om ytterligare 150 användare loggar in under den här perioden (10-30) faktureras endast ytterligare 150.
  * Den fortsatta aktiviteten för de första 100 användarna påverkar inte faktureringen under resten av kalendermånaden.

Under faktureringsperioden för övergången ser prenumerationsägaren troligen poster för båda metoderna (per autentisering och per-MAU) som visas i deras Azure-prenumerationsfaktureringspolicy:

* En post för användningen fram till datum/tid för ändring som återspeglar per autentisering.
* En post för användningen efter ändringen som återspeglar månatliga aktiva användare (MAU).

Den senaste informationen om fakturering och prissättning av användning för Azure AD B2C finns i [Azure Active Directory B2C-priser](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Länka en Azure AD B2C-klient till en prenumeration

Användningsavgifter för Azure Active Directory B2C (Azure AD B2C) faktureras till en Azure-prenumeration. När en Azure AD B2C-klient skapas måste klientadministratören uttryckligen länka Azure AD B2C-klienten till en Azure-prenumeration.

Prenumerationslänken uppnås genom att skapa en Azure AD *B2C-resurs* inom mål Azure-prenumerationen. Flera Azure AD B2C-resurser kan skapas i en enda Azure-prenumeration, tillsammans med andra Azure-resurser som virtuella datorer, lagringskonton och Logic Apps. Du kan se alla resurser i en prenumeration genom att gå till Azure Active Directory (Azure AD) klient som prenumerationen är associerad med.

En prenumeration som är länkad till en Azure AD B2C-klient kan användas för fakturering av Azure AD B2C-användning eller andra Azure-resurser, inklusive ytterligare Azure AD B2C-resurser. Det går inte att använda för att lägga till andra Azure-licensbaserade tjänster eller Office 365-licenser i Azure AD B2C-klienten.

### <a name="prerequisites"></a>Krav

* [Azure-prenumeration](https://azure.microsoft.com/free/)
* [Azure AD B2C-klient som](tutorial-create-tenant.md) du vill länka till en prenumeration
  * Du måste vara klientadministratör
  * Klienten får inte redan vara kopplad till en prenumeration

### <a name="create-the-link"></a>Skapa länken

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** i den övre menyn och välj sedan den katalog som innehåller den Azure-prenumeration du vill använda (*inte* katalogen som innehåller Azure AD B2C-klienten).
1. Välj Skapa en `Active Directory B2C` **resurs,** ange i fältet Sök på **Marketplace** och välj sedan Azure Active **Directory B2C**.
1. Välj **Skapa**
1. Välj **Länka en befintlig Azure AD B2C-klient till min Azure-prenumeration**.
1. Välj en **Azure AD B2C-klient** i listrutan. Endast klienter som du är global administratör för och som inte redan är länkade till en prenumeration visas. Namnfältet **för Azure AD B2C-resurs** fylls i med domännamnet för den Azure AD B2C-klient som du väljer.
1. Välj en aktiv **Azure-prenumeration** som du är administratör för.
1. Under **Resursgrupp**väljer du **Skapa ny**och anger sedan **resursgruppsplatsen**. Resursgruppsinställningarna här påverkar inte din Azure AD B2C-klientplats, prestanda eller faktureringsstatus.
1. Välj **Skapa**.
    ![Sidan För att skapa Azure AD B2C-resurs i Azure-portalen](./media/billing/portal-01-create-b2c-resource-page.png)

När du har slutfört de här stegen för en Azure AD B2C-klient debiteras din Azure-prenumeration i enlighet med dina Azure Direct- eller Enterprise Agreement-detaljer, om tillämpligt.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Hantera dina Azure AD B2C-klientresurser

När du har skapat Azure AD B2C-resursen i en Azure-prenumeration bör du se en ny resurs av typen "B2C-klientorganisation" visas med dina andra Azure-resurser.

Du kan använda den här resursen för att:

* Navigera till prenumerationen för att granska faktureringsinformation
* Hämta Azure AD B2C-klientens klient-ID i GUID-format
* Gå till din Azure AD B2C-klient
* Skicka en supportbegäran
* Flytta din Azure AD B2C-klientresurs till en annan Azure-prenumeration eller resursgrupp

![Sidan B2C-resursinställningar i Azure-portalen](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Regionala begränsningar

Om du har fastställt regionala begränsningar för att skapa Azure-resurser i din prenumeration kan begränsningen hindra dig från att skapa Azure AD B2C-resursen.

För att minska problemet, slappna av dina regionala begränsningar.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>CSP-prenumerationer (Azure Cloud Solution Providers)

Csp-prenumerationer (Azure Cloud Solution Providers) stöds i Azure AD B2C. Funktionen är tillgänglig med API:er eller Azure-portalen för Azure AD B2C och för alla Azure-resurser. CSP-prenumerationsadministratörer kan länka, flytta och ta bort relationer med Azure AD B2C enligt andra Azure-resurser.

Hanteringen av Azure AD B2C med hjälp av rollbaserad åtkomstkontroll påverkas inte av kopplingen mellan Azure AD B2C-klienten och en Azure CSP-prenumeration. Rollbaserad åtkomstkontroll uppnås med hjälp av klientbaserade roller, inte prenumerationsbaserade roller.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Ändra faktureringsprenumerationen för Azure AD B2C-klienten

Azure AD B2C-klienter kan flyttas till en annan prenumeration om käll- och målprenumerationerna finns i samma Azure Active Directory-klientorganisation.

Mer information om hur du flyttar Azure-resurser som din Azure AD B2C-klient till en annan prenumeration finns i [Flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Innan du påbörjar flytten, se till att läsa hela artikeln för att till fullo förstå begränsningar och krav för ett sådant drag. Förutom instruktioner för att flytta resurser innehåller den viktig information som en checklista före flytten och hur du validerar flyttåtgärden.

## <a name="next-steps"></a>Nästa steg

Den senaste prisinformationen finns i [Azure Active Directory B2C-priser](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
