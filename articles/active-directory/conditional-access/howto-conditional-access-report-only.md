---
title: Konfigurera en princip för villkorlig åtkomst i endast rapportläge - Azure Active Directory
description: Använda endast rapportläge i villkorad åtkomst till stöd vid antagande
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9bec829d7fb3e76eb243bda73423303670585e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295120"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>Konfigurera en princip för villkorlig åtkomst i endast rapportläge (förhandsgranskning)

Så här konfigurerar du en princip för villkorlig åtkomst i endast rapportläge:

> [!IMPORTANT]
> Om din organisation inte redan har gjort [det konfigurerar du Azure Monitor-integrering med Azure AD](#set-up-azure-monitor-integration-with-azure-ad). Denna process måste äga rum innan data kommer att finnas tillgängliga för granskning.

1. Logga in på **Azure-portalen** som administratör för villkorlig åtkomst, säkerhetsadministratör eller global administratör.
1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
1. Konfigurera principvillkoren och nödvändiga bidragskontroller efter behov.
1. Under **Aktivera princip** anger du växlingsknappen till endast **rapportläge.**
1. Välj **Spara**

> [!TIP]
> Du kan redigera **principtillståndet Aktivera** för en befintlig princip från **Endast på** **till rapport,** men om du gör det inaktiveras principförsändring. 

Visa rapport-bara resultera i Azure AD inloggningsloggar.

Så här visar du resultatet av en rapportprincip för en viss inloggning:

1. Logga in på **Azure-portalen** som rapportläsare, säkerhetsläsare, säkerhetsadministratör eller global administratör.
1. Bläddra till Azure Active > **Directory-inloggningar**. **Azure Active Directory**
1. Välj en inloggning eller lägg till filter för att begränsa resultatet.
1. I **informationslådan** väljer du fliken **Endast rapport (förhandsgranska)** för att visa de principer som utvärderats under inloggningen.

> [!NOTE]
> När du hämtar inloggningsloggarna väljer du JSON-format för att inkludera rapportdata med enbart villkorsstyrd åtkomst.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Konfigurera Azure Monitor-integrering med Azure AD

För att kunna visa den sammanlagda effekten av principer för villkorlig åtkomst med hjälp av den nya arbetsboken För villkorlig åtkomst insikter måste du integrera Azure Monitor med Azure AD och exportera inloggningsloggarna. Det finns två steg för att ställa in den här integrationen: 

1. [Registrera dig för en Azure Monitor-prenumeration och skapa en arbetsyta](/azure/azure-monitor/learn/quick-create-workspace).
1. [Exportera inloggningsloggarna från Azure AD till Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Mer information om Azure Monitor-priser finns på [prissidan för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Resurser för att uppskatta kostnader, ange ett dagligt tak eller anpassa datalagringsperioden finns i [artikeln, Hantera användning och kostnader med Azure Monitor Logs](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Visa arbetsbok för statistik för villkorsstyrda åtkomster

När du har integrerat dina Azure AD-loggar med Azure Monitor kan du övervaka effekten av principer för villkorlig åtkomst med hjälp av de nya arbetsböckerna för insikter om villkorlig åtkomst.

1. Logga in på **Azure-portalen** som säkerhetsadministratör eller global administratör.
1. Bläddra till Azure Active > **Directory-arbetsböcker**. **Azure Active Directory**
1. Välj **Statistik för villkorlig åtkomst (förhandsgranskning)**.
1. Välj en eller flera principer i listrutan **Princip för villkorlig åtkomst.** Alla aktiverade principer är markerade som standard.
1. Välj ett tidsintervall (om tidsintervallet överskrider den tillgängliga datauppsättningen visas alla tillgängliga data i rapporten). När du har angett parametrarna **för principen för principen för principer för villkorlig åtkomst** och **Tidsintervall** läses rapporten in.
   1. Du kan också söka efter enskilda **användare** eller **appar** för att begränsa rapportens omfattning.
1. Välj mellan att visa data i tidsintervallet efter antalet användare eller antalet inloggningar.
1. Beroende på **datavyn**visar **konsekvenssammanfattningen** antalet användare eller inloggningar i omfattningen av de valda parametrarna, grupperade efter totalt antal, **Framgång**, **Fel**, **Användaråtgärd som krävs**och **Inte tillämpat**. Välj en panel om du vill undersöka inloggningar av en viss resultattyp. 
   1. Om du har ändrat arbetsboksparametrarna kan du välja att spara en kopia för framtida bruk. Välj spara ikonen högst upp i rapporten och ange ett namn och en plats att spara till.
1. Bläddra nedåt för att visa fördelningen av inloggningar för varje villkor.
1. Visa **inloggningsinformation** längst ned i rapporten för att undersöka enskilda inloggningshändelser som filtrerats efter val ovan.

> [!TIP] 
> Behöver du öka detaljnivån för en viss fråga eller exportera inloggningsinformationen? Välj knappen till höger om en fråga för att öppna frågan i Logganalys. Välj Exportera om du vill exportera till CSV eller Power BI.

## <a name="common-problems-and-solutions"></a>Vanliga problem och lösningar

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Varför misslyckas frågorna i arbetsboken?

Kunder har märkt att frågor ibland misslyckas om fel eller flera arbetsytor är associerade med arbetsboken. Lös problemet genom att klicka på **Redigera** högst upp i arbetsboken och sedan på inställningsväxeln. Markera och ta sedan bort arbetsytor som inte är associerade med arbetsboken. Det bör bara finnas en arbetsyta som är associerad med varje arbetsbok.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Varför innehåller inte listrutan Principer för villkorlig åtkomst mina principer?

Listrutan Principer för principer för villkorlig åtkomst fylls i genom att de senaste inloggningarna efterfrågas under en period på 4 timmar. Om en klient inte har några inloggningar under de senaste 4 timmarna är det möjligt att listrutan är tom. Om den här fördröjningen är ett beständigt problem, till exempel i små klienter med ovanliga inloggningar, kan administratörer redigera frågan för listrutan Principer för villkorlig åtkomst och förlänga tiden för frågan till en tid som är längre än 4 timmar.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

Mer information om Azure AD-arbetsböcker finns i artikeln [Så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
