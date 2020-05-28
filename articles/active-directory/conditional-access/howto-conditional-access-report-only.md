---
title: Konfigurera en princip för villkorlig åtkomst i endast rapport läge – Azure Active Directory
description: Använda endast rapport läge i villkorlig åtkomst till stöd för införande
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46a00d55c58992be1009da1de5441ebe4e589a70
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994977"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Konfigurera en princip för villkorlig åtkomst i endast rapport läge

Så här konfigurerar du en princip för villkorlig åtkomst i endast rapport läge:

> [!IMPORTANT]
> Om din organisation inte redan har gjort det [konfigurerar du Azure Monitor integrering med Azure AD](#set-up-azure-monitor-integration-with-azure-ad). Den här processen måste äga rum innan data kan granskas.

1. Logga in på **Azure Portal** som administratör för villkorlig åtkomst, säkerhets administratör eller global administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Konfigurera princip villkoren och obligatoriska beviljade kontroller efter behov.
1. Under **Aktivera princip** ställer du in växla till **endast rapport** läge.
1. Välj **Spara**

> [!TIP]
> Du kan redigera inställningen **Aktivera princip** status för en befintlig princip från till **endast rapporter,** men **om** du gör det inaktive ras princip tillämpning. 

Visa endast rapport resultat i inloggnings loggar för Azure AD.

Så här visar du resultatet av en rapport princip för en viss inloggning:

1. Logga in på **Azure Portal** som en rapport läsare, säkerhets läsare, säkerhets administratör eller global administratör.
1. Bläddra till **Azure Active Directory**  >  **inloggningar**.
1. Välj en inloggning eller Lägg till filter för att begränsa resultaten.
1. I **informations** lådan väljer du fliken **endast rapporter** för att visa de principer som utvärderas under inloggningen.

> [!NOTE]
> När du laddar ned inloggnings loggar väljer du JSON-format för att inkludera endast resultat data för rapporten villkorlig åtkomst.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Konfigurera Azure Monitor-integrering med Azure AD

För att kunna visa den sammanlagda effekten av principer för villkorlig åtkomst med hjälp av den nya arbets boken för villkorlig åtkomst, måste du integrera Azure Monitor med Azure AD och exportera inloggnings loggarna. Det finns två steg för att konfigurera den här integrationen: 

1. [Registrera dig för en Azure Monitor prenumeration och skapa en arbets yta](/azure/azure-monitor/learn/quick-create-workspace).
1. [Exportera inloggnings loggarna från Azure AD till Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Mer information om Azure Monitor priser finns på [prissättnings sidan för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Resurser för att uppskatta kostnader, ange en daglig begränsning eller anpassa data lagrings perioden, finns i artikeln [Hantera användning och kostnader med Azure Monitor loggar](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Visa arbets boken för villkorlig åtkomst

När du har integrerat dina Azure AD-loggar med Azure Monitor kan du övervaka effekten av principer för villkorlig åtkomst med hjälp av de nya arbets böckerna för villkorlig åtkomst.

1. Logga in på **Azure Portal** som säkerhets administratör eller global administratör.
1. Bläddra till **Azure Active Directory**  >  **arbets böcker**.
1. Välj **insikter för villkorlig åtkomst**.
1. Välj en eller flera principer i list rutan för **villkorlig åtkomst princip** . Alla aktiverade principer är markerade som standard.
1. Välj ett tidsintervall (om tidsintervallet överskrider den tillgängliga data mängden visas alla tillgängliga data) i rapporten. När du har angett **principer för villkorlig åtkomst** och **tidsintervalls** parametrar kommer rapporten att läsas in.
   1. Du kan också söka efter enskilda **användare** eller **appar** för att begränsa rapportens omfattning.
1. Välj mellan att visa data inom tidsintervallet med antalet användare eller antalet inloggningar.
1. Beroende på **datavyn**visar **effekt sammanfattningen** antalet användare eller inloggningar i omfånget för de valda parametrarna, grupperat efter totalt antal, **lyckade**, **misslyckade** **användar åtgärder**och **verkställs inte**. Välj en panel för att undersöka inloggningar för en viss resultat typ. 
   1. Om du har ändrat arbets boks parametrarna kan du välja att spara en kopia för framtida användning. Välj ikonen Spara längst upp i rapporten och ange ett namn och en plats att spara till.
1. Rulla nedåt för att Visa fördelningen av inloggningar för varje villkor.
1. Visa **inloggnings informationen** längst ned i rapporten för att undersöka enskilda inloggnings händelser som filtrerats enligt valen ovan.

> [!TIP] 
> Behöver du öka detalj nivån för en viss fråga eller exportera inloggnings informationen? Klicka på knappen till höger om en fråga för att öppna frågan i Log Analytics. Välj Exportera för att exportera till CSV eller Power BI.

## <a name="common-problems-and-solutions"></a>Vanliga problem och lösningar

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Varför fungerar inte frågorna i arbets boken?

Kunder har lagt märke till att frågor ibland inte fungerar om fel eller flera arbets ytor är associerade med arbets boken. Åtgärda problemet genom att klicka på **Redigera** överst i arbets boken och sedan på inställnings växeln. Välj och ta sedan bort arbets ytor som inte är associerade med arbets boken. Det får bara finnas en arbets yta som är kopplad till varje arbets bok.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Varför innehåller inte List rutan villkorliga åtkomst principer några principer?

List rutan för villkorliga åtkomst principer fylls genom att fråga de senaste inloggningarna under en period på 4 timmar. Om en klient inte har några inloggningar under de senaste 4 timmarna, är det möjligt att List rutan blir tom. Om den här fördröjningen är ett beständigt problem, t. ex. i små klienter med ovanliga inloggningar, kan administratörer redigera frågan för List rutan med villkorliga åtkomst principer och förlänga tiden för frågan till en tid längre än 4 timmar.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

Mer information om Azure AD-arbetsböcker finns i artikeln [så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
