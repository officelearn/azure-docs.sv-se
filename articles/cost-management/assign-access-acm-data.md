---
title: Tilldela åtkomst till Azure Cost Management data
description: Den här artikeln vägleder dig även om att tilldela behörighet till Azure Cost Management-data för olika scope.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: e3140ee990127db6815828314103a09dff7cf26e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219851"
---
# <a name="assign-access-to-cost-management-data"></a>Tilldela åtkomst till Cost Management-data

För användare med Azure Enterprise-avtal definierar en kombination av behörigheter som beviljats i Azure Portal och Enterprise-portalen (EA) en användar nivå för åtkomst till Azure Cost Management data. För användare med andra typer av Azure-konton är det enklare att definiera en användar nivå för att Cost Management data genom att använda rollbaserad åtkomst kontroll i Azure. Den här artikeln beskriver hur du tilldelar åtkomst till Cost Management-data. När en kombination av behörigheter tilldelas baserat vyer användardata i Cost Management omfattningen som de har åtkomst till och omfattning att de väljer i Azure-portalen.

Omfattningen som en användare väljer används i hela kostnadshantering datakonsolidering och styra åtkomsten till kostnadsinformation. När du använder omfång kan användare inte flerval dem. I stället väljer de ett större område som underordnade omfattningar kavla upp till och sedan de filter ned för att de vill visa. Konsolidera data är viktigt att förstå eftersom vissa användare inte ska ha åtkomst till en överordnad omfattning som underordnade omfattningar slås upp till.

Se [hur du tilldelar åtkomst med Azure Cost Management](https://www.youtube.com/watch?v=J997ckmwTa8) video för att lära dig mer om hur du tilldelar åtkomst för att Visa kostnader och kostnader med rollbaserad åtkomst kontroll i Azure.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Cost Management-scope

Kostnads hantering stöder olika typer av Azure-konton. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Konto typen fastställer tillgängliga omfattningar.

### <a name="azure-ea-subscription-scopes"></a>Azure EA-prenumerations omfång

Om du vill visa kostnads data för Azure EA-prenumerationer måste användaren ha minst Läs behörighet till ett eller flera av följande omfång.

| **Omfång** | **Definieras på** | **Nödvändig åtkomst för att visa data** | **Nödvändig EA-inställning** | **Konsoliderar data till** |
| --- | --- | --- | --- | --- |
| Faktureringskonto<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Företagsadministratör | Ingen | Alla prenumerationer från Enterprise-avtalet |
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

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Aktivera åtkomst till kostnader i EA-portalen

Avdelnings omfånget kräver alternativet för att välja alternativ för **da View** **aktive rad** i EA-portalen. Alla andra omfattningar kräver alternativet alternativ för **Ao** -visning **aktiverat** i EA-portalen.

För att aktivera ett alternativ:

1. Logga in på EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett företags administratörs konto.
2. Välj **Hantera** i det vänstra fönstret.
3. För de kostnads hanterings omfattningar som du vill ge åtkomst till aktiverar du alternativet avgift för **da Visa avgifter** och/eller **Ao Visa avgifter**.  
    fliken ![registrering visar alternativ för att visa alternativ för DA-och AO-vy](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

När Visningsalternativ för kostnad är aktiverade, kräver de flesta omfång också rollbaserad åtkomstkontroll (RBAC) behörighet konfiguration i Azure-portalen.

## <a name="enterprise-administrator-role"></a>Enterprise-administratörsrollen

En företagsadministratör har åtkomst till faktureringskonto (Enterprise Agreement/registrering) och alla andra scope, som är underordnade omfattningar som standard. Enterprise-administratören tilldelar åtkomst till omfång för andra användare. Som bästa praxis för affärskontinuitet, bör du alltid ha två användare med administratörsbehörighet för företag. I följande avsnitt är hanteringspaketen exempel på enterprise-administratören tilldela åtkomst till omfattningar för andra användare.

## <a name="assign-billing-account-scope-access"></a>Tilldela fakturering kontoåtkomst omfång

Åtkomst till faktureringen kontoomfånget kräver administratörsbehörighet för företag i EA-portalen. Enterprise-administratörer har behörighet att visa kostnaderna över hela EA-avtal eller flera registreringar. Ingen åtgärd krävs i Azure-portalen för fakturering kontoomfånget.

1. Logga in på EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett företags administratörs konto.
2. Välj **Hantera** i det vänstra fönstret.
3. På fliken **registrering** väljer du den registrering som du vill hantera.  
    ![välja din registrering i EA-portalen](./media/assign-access-acm-data/ea-portal.png)
4. Klicka på **+ Lägg till administratör**.
5. Välj autentiseringstyp och ange användarens e-postadress i rutan Lägg till administratör.
6. Om användaren ska ha skrivskyddad åtkomst till kostnader och användnings data väljer du **Ja**under **skrivskyddad**.  Annars väljer du **Nej**.
7. Klicka på **Lägg till** för att skapa kontot.  
    ![exempel information som visas i rutan Lägg till administratör](./media/assign-access-acm-data/add-admin.png)

Det kan ta upp till 30 minuter innan den nya användaren kan komma åt data i Cost Management.

### <a name="assign-department-scope-access"></a>Tilldela avdelning begränsa åtkomsten

Åtkomst till området avdelning kräver avdelning (DA visa debiteringar) administratörsåtkomst i EA-portalen. Avdelning administratörer har behörighet att visa kostnader och användningsdata som är associerat med en avdelning eller till flera avdelningar. Data för avdelningen innehåller alla prenumerationer som hör till ett konto för enhetsregistreringshanterare som är länkade till avdelningen. Ingen åtgärd krävs i Azure-portalen.

1. Logga in på EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett företags administratörs konto.
2. Välj **Hantera** i det vänstra fönstret.
3. På fliken **registrering** väljer du den registrering som du vill hantera.
4. Klicka på fliken **avdelning** och klicka sedan på **Lägg till administratör**.
5. Välj autentiseringstyp och Skriv användarens e-postadress i rutan Lägg till avdelningen administratör.
6. Om användaren ska ha skrivskyddad åtkomst till kostnader och användnings data väljer du **Ja**under **skrivskyddad**.  Annars väljer du **Nej**.
7. Välj de avdelningar som du vill bevilja avdelning administrativ behörighet till.
8. Klicka på **Lägg till** för att skapa kontot.  
    ![ange nödvändig information i rutan Lägg till avdelnings administratör](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Tilldela registreringsåtkomst konto omfång

Åtkomst till kontoomfånget registreringen kräver kontoåtkomst ägare (AO visa debiteringar) i EA-portalen. Ägare kan visa kostnader och användningsdata som är associerade med de prenumerationer som skapats från den registreringskontot. Ingen åtgärd krävs i Azure-portalen.

1. Logga in på EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett företags administratörs konto.
2. Välj **Hantera** i det vänstra fönstret.
3. På fliken **registrering** väljer du den registrering som du vill hantera.
4. Klicka på fliken **konto** och sedan på **Lägg till konto**.
5. I rutan Lägg till konto väljer du den **avdelning** som du vill koppla kontot till eller så lämnar du det som otilldelade.
6. Välj autentiseringstyp och ange namnet på kontot.
7. Skriv användarens e-postadress och du kan också ange kostnadsstället.
8. Klicka på **Lägg till** för att skapa kontot.  
    ![ange nödvändig information i rutan Lägg till konto för ett registrerings konto](./media/assign-access-acm-data/add-account.png)

När du har slutfört stegen ovan, användarkontot blir ett konto för enhetsregistreringshanterare i Enterprise portal och kan skapa prenumerationer. Användaren kan komma åt data om kostnader och användning för prenumerationer som de skapar.

## <a name="assign-management-group-scope-access"></a>Tilldela åtkomst till hanteringsgruppen omfång

Åtkomst till Visa hanterings gruppens omfång kräver minst Cost Management läsar behörighet (eller läsare). Du kan konfigurera behörigheter för en hanteringsgrupp i Azure-portalen. Du måste minst ha behörigheten administratör för användaråtkomst (eller ägare) att aktivera åtkomst för andra hanteringsgruppen. Och för Azure EA-konton måste du också ha aktiverat inställningen **Ao View charges** i EA-portalen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **alla tjänster** i sido panelen, Sök efter _hanterings grupper_och välj sedan **hanterings grupper**.
3. Välj hanteringsgruppen i hierarkin.
4. Klicka på **information**bredvid namnet på hanterings gruppen.
5. Välj **Access Control (IAM)** i det vänstra fönstret.
6. Klicka på **Lägg till**.
7. Under **roll**väljer du **Cost Management läsare**.
8. Under **tilldela åtkomst till**väljer du **Azure AD-användare, grupp eller program**.
9. Tilldela åtkomst genom att söka efter och välj sedan användaren.
10. Klicka på **Save** (Spara).  
    ![exempel information i rutan Lägg till behörigheter för en hanterings grupp](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Tilldela omfång prenumerationsåtkomst

Åtkomst till en prenumeration kräver minst behörigheten Cost Management Reader (eller läsare). Du kan konfigurera behörigheter till en prenumeration på Azure-portalen. Du måste minst ha behörigheten administratör för användaråtkomst (eller ägare) att aktivera åtkomst för andra prenumerationen. Och för Azure EA-konton måste du också ha aktiverat inställningen **Ao View charges** i EA-portalen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **alla tjänster** i sido panelen, Sök efter _prenumerationer_och välj sedan **prenumerationer**.
3. Välj din prenumeration.
4. Välj **Access Control (IAM)** i det vänstra fönstret.
5. Klicka på **Lägg till**.
6. Under **roll**väljer du **Cost Management läsare**.
7. Under **tilldela åtkomst till**väljer du **Azure AD-användare, grupp eller program**.
8. Tilldela åtkomst genom att söka efter och välj sedan användaren.
9. Klicka på **Save** (Spara).

## <a name="assign-resource-group-scope-access"></a>Tilldela resursåtkomst grupp omfång

Åtkomst till en resursgrupp kräver minst behörigheten Cost Management Reader (eller läsare). Du kan konfigurera behörigheter till en resursgrupp i Azure-portalen. Du måste minst ha behörigheten administratör för användaråtkomst (eller ägare) att aktivera åtkomst för andra resursgruppen. Och för Azure EA-konton måste du också ha aktiverat inställningen **Ao View charges** i EA-portalen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **alla tjänster** i sido panelen, Sök efter _resurs grupper_och välj sedan **resurs grupper**.
3. Välj din resursgrupp.
4. Välj **Access Control (IAM)** i det vänstra fönstret.
5. Klicka på **Lägg till**.
6. Under **roll**väljer du **Cost Management läsare**.
7. Under **tilldela åtkomst till**väljer du **Azure AD-användare, grupp eller program**.
8. Tilldela åtkomst genom att söka efter och välj sedan användaren.
9. Klicka på **Save** (Spara).

## <a name="cross-tenant-authentication-issues"></a>Problem med autentisering mellan klienter

För närvarande har Azure Cost Management begränsat stöd för autentisering mellan klienter. I vissa fall när du försöker autentisera över klient organisationer kan du få **åtkomst nekade** fel vid kostnads analys. Det här problemet kan uppstå om du konfigurerar rollbaserad åtkomst kontroll (RBAC) till en annan klient prenumeration och sedan försöker visa kostnads data.

*Så här löser du problemet*: när du har konfigurerat RBAC för flera klienter väntar du en timme. Försök sedan att Visa kostnader i kostnads analys eller bevilja Cost Management åtkomst till användare i båda klient organisationerna.  


## <a name="next-steps"></a>Nästa steg

- Om du inte redan har slutfört den första snabb starten för Cost Management läser du den för att [börja analysera kostnaderna](quick-acm-cost-analysis.md).
