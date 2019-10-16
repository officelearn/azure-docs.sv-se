---
title: Tilldela åtkomst till Azure Cost Management data
description: Den här artikeln vägleder dig när du tilldelar behörighet att Azure Cost Management data för olika åtkomstscope.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: ebc56d27b7adc8f1fea9eafabe1b211f3f0ad560
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375132"
---
# <a name="assign-access-to-cost-management-data"></a>Tilldela åtkomst till Cost Management data

För användare med Azure Enterprise-avtal definierar en kombination av behörigheter som beviljats i Azure Portal och Enterprise-portalen (EA) en användar nivå för åtkomst till Azure Cost Management data. För användare med andra typer av Azure-konton är det enklare att definiera en användar nivå för att Cost Management data genom att använda rollbaserad åtkomst kontroll i Azure. Den här artikeln vägleder dig genom att tilldela åtkomst till Cost Management data. När kombinationen av behörigheter har tilldelats visar användaren data i Cost Management baserat på den omfattning som de har åtkomst till och på den omfattning som de väljer i Azure Portal.

Omfattningen som en användare väljer används i hela Cost Management för att tillhandahålla data konsolidering och för att kontrol lera åtkomst till kostnads information. När du använder omfattningar, kan användarna inte välja flera. I stället väljer de ett större omfång som de underordnade omfattningarna lyfter upp till och sedan filtrerar de ned till vad de vill visa. Data konsolidering är viktigt att förstå eftersom vissa personer inte ska ha åtkomst till en överordnad omfattning som de underordnade omfattningarna lyfts upp till.

Se [hur du tilldelar åtkomst med Azure Cost Management](https://www.youtube.com/watch?v=J997ckmwTa8) video för att lära dig mer om hur du tilldelar åtkomst för att Visa kostnader och kostnader med rollbaserad åtkomst kontroll i Azure.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Cost Management omfattningar

Kostnads hantering stöder olika typer av Azure-konton. Om du vill visa en fullständig lista över typer av stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Konto typen fastställer tillgängliga omfattningar.

### <a name="azure-ea-subscription-scopes"></a>Azure EA-prenumerations omfång

Om du vill visa kostnads data för Azure EA-prenumerationer måste användaren ha minst Läs behörighet till ett eller flera av följande omfång.

| **Omfång** | **Definieras på** | **Nödvändig åtkomst för att visa data** | **Nödvändig EA-inställning** | **Konsoliderar data till** |
| --- | --- | --- | --- | --- |
| Faktureringskonto<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Företagsadministratör | Inget | Alla prenumerationer från Enterprise-avtalet |
| Avdelning | [https://ea.azure.com](https://ea.azure.com/) | Avdelningsadministratör | **Avgifter för da View** har Aktiver ATS | Alla prenumerationer som tillhör ett registreringskonto som är länkade till avdelningen |
| Registreringskonto<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Kontoägare | **Avgifter för Ao View** har Aktiver ATS | Alla prenumerationer från registreringskontot |
| Hanteringsgrupp | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-läsare (eller Läsare) | **Avgifter för Ao View** har Aktiver ATS | Alla prenumerationer under hanteringsgruppen |
| Prenumeration | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-läsare (eller Läsare) | **Avgifter för Ao View** har Aktiver ATS | Alla resurser/resursgrupper i prenumerationen |
| Resursgrupp | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-läsare (eller Läsare) | **Avgifter för Ao View** har Aktiver ATS | Alla resurser i resursgruppen |

<sup>1</sup> ett fakturerings konto kallas även Enterprise-avtal eller registrering.

<sup>2</sup> registrerings kontot kallas även för kontots ägare.

I följande diagram illustreras förhållandet mellan Cost Management omfattningar med inställningar för roller och EA-portalen.

![Diagram som visar relationen mellan Cost Management omfattningar med roller och inställningar för EA-portalen](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

När **da View-avgifter** inaktive ras i EA-portalen visas ett meddelande om att *kostnader har inaktiverats för din organisation* när du försöker Visa kostnader för avdelningar och konton.

När **Ao View-avgifter** har inaktiverats i EA-portalen visas ett meddelande om att *kostnader har inaktiverats för din organisation* när du försöker Visa kostnader för registrerings konton, hanterings grupper, prenumerationer och resurs grupper.

## <a name="other-azure-account-scopes"></a>Andra scope för Azure-konto

Om du vill visa kostnads data för andra Azure-prenumerationer måste användaren ha minst Läs behörighet till en eller flera av följande omfattningar:

- Azure-konto
- Hanteringsgrupp
- Resursgrupp

Olika omfattningar är tillgängliga efter att partners har publicerat kunder i ett kund avtal från Microsoft. CSP-kunder kan sedan använda Cost Management funktioner när de aktive ras av deras CSP-partner. Mer information finns i [Kom igång med Azure Cost Management för partner](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Ge till gång till kostnader i EA-portalen

Avdelnings omfånget kräver alternativet för att välja alternativ för **da View** **aktive rad** i EA-portalen. Alla andra omfattningar kräver alternativet alternativ för **Ao** -visning **aktiverat** i EA-portalen.

Så här aktiverar du ett alternativ:

1. Logga in på EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett företags administratörs konto.
2. Välj **Hantera** i det vänstra fönstret.
3. För de kostnads hanterings omfattningar som du vill ge åtkomst till aktiverar du alternativet avgift för **da Visa avgifter** och/eller **Ao Visa avgifter**.  
    fliken ![Enrollment visar alternativen för DA och AO View avgift @ no__t-1

När alternativen för Visa debitering har Aktiver ATS kräver de flesta scope även rollbaserad åtkomst kontroll (RBAC) behörighets konfiguration i Azure Portal.

## <a name="enterprise-administrator-role"></a>Rollen företags administratör

Som standard har en företags administratör åtkomst till fakturerings kontot (Enterprise-avtal/registrering) och till alla andra omfattningar, som är underordnade omfattningar. Företags administratören tilldelar åtkomst till omfattningar för andra användare. Som bästa praxis för affärs kontinuitet bör du alltid ha två användare med åtkomst till företags administratör. Följande avsnitt innehåller exempel på företags administratören som tilldelar åtkomst till omfattningar för andra användare.

## <a name="assign-billing-account-scope-access"></a>Tilldela åtkomst till fakturerings kontots omfång

Åtkomst till fakturerings konto omfånget kräver företags administratörs behörighet i EA-portalen. Företags administratören har åtkomst till att Visa kostnader för hela EA-registreringen eller flera registreringar. Ingen åtgärd krävs i Azure Portal för fakturerings konto omfånget.

1. Logga in på EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett företags administratörs konto.
2. Välj **Hantera** i det vänstra fönstret.
3. På fliken **registrering** väljer du den registrering som du vill hantera.  
    ![select din registrering i EA-portalen @ no__t-1
4. Klicka på **+ Lägg till administratör**.
5. I rutan Lägg till administratör väljer du autentiseringstyp och skriver användarens e-postadress.
6. Om användaren ska ha skrivskyddad åtkomst till kostnader och användnings data väljer du **Ja**under **skrivskyddad**.  Annars väljer du **Nej**.
7. Klicka på **Lägg till** för att skapa kontot.  
    ![example information som visas i rutan Lägg till administratör @ no__t-1

Det kan ta upp till 30 minuter innan den nya användaren kan komma åt data i Cost Management.

### <a name="assign-department-scope-access"></a>Tilldela åtkomst till avdelnings omfång

Åtkomst till avdelnings omfånget kräver åtkomst till avdelnings administratör (tilläggs avgifter) i EA-portalen. Avdelnings administratören har åtkomst till att Visa kostnader och användnings data som är kopplade till en avdelning eller till flera avdelningar. Data för avdelningen innehåller alla prenumerationer som hör till ett registrerings konto som är länkat till avdelningen. Ingen åtgärd krävs i Azure Portal.

1. Logga in på EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett företags administratörs konto.
2. Välj **Hantera** i det vänstra fönstret.
3. På fliken **registrering** väljer du den registrering som du vill hantera.
4. Klicka på fliken **avdelning** och klicka sedan på **Lägg till administratör**.
5. I rutan Lägg till avdelnings administratör väljer du autentiseringstyp och skriver sedan användarens e-postadress.
6. Om användaren ska ha skrivskyddad åtkomst till kostnader och användnings data väljer du **Ja**under **skrivskyddad**.  Annars väljer du **Nej**.
7. Välj de avdelningar som du vill ge avdelnings administratörs behörighet till.
8. Klicka på **Lägg till** för att skapa kontot.  
    ![enter information som krävs i rutan Lägg till avdelnings administratör @ no__t-1

## <a name="assign-enrollment-account-scope-access"></a>Tilldela åtkomstscope för registrerings konto

Åtkomst till registrerings konto omfånget kräver konto ägaren (AO View charges) i EA-portalen. Konto ägaren kan visa kostnader och användnings data som är associerade med prenumerationer som skapats från det registrerings kontot. Ingen åtgärd krävs i Azure Portal.

1. Logga in på EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett företags administratörs konto.
2. Välj **Hantera** i det vänstra fönstret.
3. På fliken **registrering** väljer du den registrering som du vill hantera.
4. Klicka på fliken **konto** och sedan på **Lägg till konto**.
5. I rutan Lägg till konto väljer du den **avdelning** som du vill koppla kontot till eller så lämnar du det som otilldelade.
6. Välj autentiseringstyp och skriv konto namnet.
7. Skriv användarens e-postadress och skriv sedan kostnads Center.
8. Klicka på **Lägg till** för att skapa kontot.  
    ![enter information som krävs i rutan Lägg till konto för ett registrerings konto @ no__t-1

När du har slutfört stegen ovan blir användar kontot ett registrerings konto i Enterprise Portal och kan skapa prenumerationer. Användaren kan komma åt information om kostnader och användning för de prenumerationer de skapar.

## <a name="assign-management-group-scope-access"></a>Tilldela åtkomst till hanterings gruppens omfång

Åtkomst till Visa hanterings gruppens omfång kräver minst Cost Management läsar behörighet (eller läsare). Du kan konfigurera behörigheter för en hanterings grupp i Azure Portal. Du måste åtminstone ha behörighet som administratör för användar åtkomst (eller ägare) för hanterings gruppen för att aktivera åtkomst för andra. Och för Azure EA-konton måste du också ha aktiverat inställningen **Ao View charges** i EA-portalen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **alla tjänster** i sido panelen, Sök efter _hanterings grupper_och välj sedan **hanterings grupper**.
3. Välj hanterings gruppen i hierarkin.
4. Klicka på **information**bredvid namnet på hanterings gruppen.
5. Välj **Access Control (IAM)** i det vänstra fönstret.
6. Klicka på **Lägg till**.
7. Under **roll**väljer du **Cost Management läsare**.
8. Under **tilldela åtkomst till**väljer du **Azure AD-användare, grupp eller program**.
9. Om du vill tilldela åtkomst söker du efter och väljer användaren.
10. Klicka på **Save** (Spara).  
    ![example information i rutan Lägg till behörigheter för en hanterings grupp @ no__t-1

## <a name="assign-subscription-scope-access"></a>Tilldela åtkomst till prenumerations omfång

Åtkomst till en prenumeration kräver minst Cost Management läsar behörighet (eller läsare). Du kan konfigurera behörigheter för en prenumeration i Azure Portal. Du måste minst ha behörighet som administratör för användar åtkomst (eller ägare) för prenumerationen för att aktivera åtkomst för andra. Och för Azure EA-konton måste du också ha aktiverat inställningen **Ao View charges** i EA-portalen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **alla tjänster** i sido panelen, Sök efter _prenumerationer_och välj sedan **prenumerationer**.
3. Välj din prenumeration.
4. Välj **Access Control (IAM)** i det vänstra fönstret.
5. Klicka på **Lägg till**.
6. Under **roll**väljer du **Cost Management läsare**.
7. Under **tilldela åtkomst till**väljer du **Azure AD-användare, grupp eller program**.
8. Om du vill tilldela åtkomst söker du efter och väljer användaren.
9. Klicka på **Save** (Spara).

## <a name="assign-resource-group-scope-access"></a>Tilldela omfångs åtkomst för resurs grupp

Åtkomst till en resurs grupp kräver minst behörigheten Cost Management läsare (eller läsare). Du kan konfigurera behörigheter till en resurs grupp i Azure Portal. Du måste minst ha behörighet som administratör för användar åtkomst (eller ägare) för resurs gruppen för att aktivera åtkomst för andra. Och för Azure EA-konton måste du också ha aktiverat inställningen **Ao View charges** i EA-portalen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **alla tjänster** i sido panelen, Sök efter _resurs grupper_och välj sedan **resurs grupper**.
3. Välj din resurs grupp.
4. Välj **Access Control (IAM)** i det vänstra fönstret.
5. Klicka på **Lägg till**.
6. Under **roll**väljer du **Cost Management läsare**.
7. Under **tilldela åtkomst till**väljer du **Azure AD-användare, grupp eller program**.
8. Om du vill tilldela åtkomst söker du efter och väljer användaren.
9. Klicka på **Save** (Spara).

## <a name="cross-tenant-authentication-issues"></a>Problem med autentisering mellan klienter

För närvarande har Azure Cost Management begränsat stöd för autentisering mellan klienter. I vissa fall när du försöker autentisera över klient organisationer kan du få **åtkomst nekade** fel vid kostnads analys. Det här problemet kan uppstå om du konfigurerar rollbaserad åtkomst kontroll (RBAC) till en annan klient prenumeration och sedan försöker visa kostnads data.

*Så här löser du problemet*: när du har konfigurerat RBAC för flera klienter väntar du en timme. Försök sedan att Visa kostnader i kostnads analys eller bevilja Cost Management åtkomst till användare i båda klient organisationerna.  


## <a name="next-steps"></a>Nästa steg

- Om du inte redan har slutfört den första snabb starten för Cost Management läser du den för att [börja analysera kostnaderna](quick-acm-cost-analysis.md).
