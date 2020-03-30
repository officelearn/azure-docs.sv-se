---
title: Distribuera privilegierad identitetshantering (PIM) – Azure AD | Microsoft-dokument
description: Beskriver hur du planerar distributionen av Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/04/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8c77b3454026aa309d979bd938674e7c3ae7b6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026004"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Distribuera Azure AD-privilegierad identitetshantering (PIM)

I den här steg-för-steg-guiden beskrivs hur du planerar distributionen av PIM (Privileged Identity Management) i din Azure Active Directory -organisation (Azure AD).

> [!TIP]
> I den här artikeln visas objekt som är markerade som:
> 
> :heavy_check_mark: Microsoft **rekommenderar**
> 
> Det här är allmänna rekommendationer och du bör bara implementera om de gäller för dina specifika företagsbehov.

## <a name="learn-about-privileged-identity-management"></a>Läs mer om privilegierad identitetshantering

Azure AD Privileged Identity Management hjälper dig att hantera privilegierade administrativa roller i Azure AD, Azure-resurser och andra Microsoft Online-tjänster. I en värld där privilegierade identiteter tilldelas och glöms bort tillhandahåller Privileged Identity Management lösningar som just-in-time-åtkomst, begärandegodkännandearbetsflöden och fullständigt integrerade åtkomstgranskningar så att du kan identifiera, upptäcka och förhindra skadlig åtkomst aktiviteter med privilegierade roller i realtid. Distribuera privilegierad identitetshantering för att hantera dina privilegierade roller i hela organisationen kommer att minska risken avsevärt samtidigt som värdefulla insikter om aktiviteterna för dina privilegierade roller visas.

### <a name="business-value-of-privileged-identity-management"></a>Affärsvärde för privilegierad identitetshantering

**Hantera risker** – Skydda din organisation genom att tillämpa principen om [minsta behörighetsåtkomst](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) och åtkomst i tid. Genom att minimera antalet permanenta tilldelningar av användare till privilegierade roller och tvinga godkännanden och MFA för höjelse kan du avsevärt minska säkerhetsriskerna relaterade till privilegierad åtkomst i organisationen. Genom att upprätthålla lägsta behörighet och just-in-time-åtkomst kan du också visa en historik över åtkomst till privilegierade roller och spåra säkerhetsproblem när de inträffar.

**Adressefterlevnad och styrning** - Distribuera privilegierad identitetshantering skapar en miljö för pågående identitetsstyrning. Just-in-time-höjelse av privilegierade identiteter är ett sätt för privilegierad identitetshantering att hålla reda på aktiviteter med privilegierad åtkomst i organisationen. Du kan också visa och ta emot aviseringar för alla tilldelningar av permanenta och kvalificerade roller inom organisationen. Genom åtkomstgranskning kan du regelbundet granska och ta bort onödiga privilegierade identiteter och se till att din organisation är kompatibel med de mest rigorösa identitets-, åtkomst- och säkerhetsstandarderna.

**Minska kostnaderna** – Minska kostnaderna genom att eliminera ineffektivitet, mänskliga fel och säkerhetsproblem genom att distribuera privilegierad identitetshantering korrekt. Nettoresultatet är en minskning av it-brott i samband med privilegierade identiteter, som är kostsamma och svåra att återhämta sig från. Privilegierad identitetshantering hjälper också din organisation att minska kostnaderna för granskningsåtkomstinformation när det gäller att följa regler och standarder.

Mer information finns i [Vad är Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Licenskrav

Om du vill använda Privilegierad identitetshantering måste katalogen ha någon av följande betalda licenser eller utvärderingslicenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Mer information finns i [Licenskrav för att använda Privilegierad identitetshantering](subscription-requirements.md).

### <a name="key-terminology"></a>Viktig terminologi

| Term eller begrepp | Beskrivning |
| --- | --- |
| berättigad | En rolltilldelning som kräver att en användare utför en eller flera åtgärder för att använda rollen. Om en användare har gjorts berättigad för en roll innebär det att användaren kan aktivera rollen när det finns behov av att utföra privilegierade åtgärder. Det finns ingen skillnad i den åtkomst som ges till någon med en permanent kontra berättigad rolltilldelning. Den enda skillnaden är att vissa användare inte behöver den åtkomsten hela tiden. |
| aktivera | Processen med att utföra en eller flera åtgärder för att använda en roll som en användare är berättigad för. Det kan vara åtgärder som att utföra en kontroll av multifaktorautentisering (MFA), ange en affärsmotivering eller begära godkännande från utnämnda godkännare. |
| just-in-time-åtkomst (JIT) | En modell i vilken användarna får tillfällig behörighet att utföra privilegierade uppgifter, vilket hindrar skadliga eller obehöriga användare från att få åtkomst när behörigheterna har gått ut. Åtkomst beviljas endast när användare behöver den. |
| princip om minsta behörighetsåtkomst | En rekommenderad säkerhetsrutin där varje användare endast har fått de minsta privilegier som krävs för att utföra uppgifterna som de har behörighet att utföra. Den här metoden minimerar antalet globala administratörer och använder i stället specifika administratörsroller för vissa scenarier. |

Mer information finns i [Terminologi](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Översikt på hög nivå över hur privilegierad identitetshantering fungerar

1. Privilegierad identitetshantering har konfigurerats så att användarna är berättigade till privilegierade roller.
1. När en behörig användare behöver använda sin privilegierade roll aktiverar de rollen i Privilegierad identitetshantering.
1. Beroende på vilka inställningar för privilegierad identitetshantering som konfigurerats för rollen måste användaren utföra vissa steg (t.ex. utföra multifaktorautentisering, få godkännande eller ange en orsak.)
1. När användaren har aktiverat sin roll får de rollen för en förkonfigurerad tidsperiod.
1. Administratörer kan visa en historik över alla aktiviteter för privilegierad identitetshantering i granskningsloggen. De kan också ytterligare skydda sina Azure AD-organisationer och uppfylla efterlevnad med hjälp av privilegierade identitetshanteringsfunktioner som åtkomstgranskningar och aviseringar.

Mer information finns i [Vad är Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Roller som kan hanteras av privilegierad identitetshantering

**Azure AD-roller** – Dessa roller är alla i Azure Active Directory (till exempel Global Administratör, Exchange-administratör och Säkerhetsadministratör). Du kan läsa mer om rollerna och deras funktioner i [administratörsrollbehörigheter i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Mer information om hur du bestämmer vilka roller som ska tilldelas administratörer finns [i de minst privilegierade rollerna efter uppgift](../users-groups-roles/roles-delegate-by-task.md).

**Azure-resursroller** – Dessa roller är länkade till en Azure-resurs, resursgrupp, prenumeration eller hanteringsgrupp. Privilegierad identitetshantering ger just-in-time-åtkomst till både inbyggda roller som Ägare, Administratör för användaråtkomst och Deltagare samt [anpassade roller](../../role-based-access-control/custom-roles.md). Mer information om Azure-resursroller finns i [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md).

Mer information finns [i Roller som du inte kan hantera i Privilegierad identitetshantering](pim-roles.md).

## <a name="plan-your-deployment"></a>Planera distributionen

Det här avsnittet fokuserar på vad du behöver göra innan du distribuerar privilegierad identitetshantering i organisationen. Det är viktigt att följa instruktionerna och förstå begreppen i det här avsnittet eftersom de hjälper dig att skapa den bästa planen som är skräddarsydd för organisationens privilegierade identiteter.

### <a name="identify-your-stakeholders"></a>Identifiera dina intressenter

Följande avsnitt hjälper dig att identifiera alla intressenter som är involverade i projektet och behöver signera, granska eller hålla dig informerad. Den innehåller separata tabeller för distribution av privilegierad identitetshantering för Azure AD-roller och privilegierad identitetshantering för Azure-resursroller. Lägg till intressenter i följande tabell beroende på vad som är lämpligt för din organisation.

- SO = Signera på detta projekt
- R = Granska detta projekt och ge input
- I = Informerad om detta projekt

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Intressenter: Privilegierad identitetshantering för Azure AD-roller

| Namn | Roll | Åtgärd |
| --- | --- | --- |
| Namn och e-post | **Identitetsarkitekt eller Global Azure-administratör**<br/>En representant från identitetshanteringsgruppen som ansvarar för att definiera hur den här ändringen är anpassad till den grundläggande identitetshanteringsinfrastrukturen i organisationen. | SO/R/I |
| Namn och e-post | **Tjänstägare / Linjechef**<br/>En representant från IT-ägare av en tjänst eller en grupp av tjänster. De är viktiga för att fatta beslut och hjälpa till att lansera Privilegierad identitetshantering för sitt team. | SO/R/I |
| Namn och e-post | **Säkerhetsägare**<br/>En representant från säkerhetsteamet som kan signera att planen uppfyller organisationens säkerhetskrav. | SO/R |
| Namn och e-post | **IT-supportchef / Helpdesk**<br/>En representant från IT-supportorganisationen som kan ge input om supporten av den här ändringen ur ett helpdesk-perspektiv. | R/I |
| Namn och e-post för pilotanvändare | **Användare av privilegierade roller**<br/>Den grupp användare som privilegierad identitetshantering implementeras för. De måste veta hur de ska aktivera sina roller när privilegierad identitetshantering har implementerats. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Intressenter: Privilegierad identitetshantering för Azure-resursroller

| Namn | Roll | Åtgärd |
| --- | --- | --- |
| Namn och e-post | **Prenumeration / Resursägare**<br/>En representant från IT-ägarna för varje prenumeration eller resurs som du vill distribuera Privilegierad identitetshantering för | SO/R/I |
| Namn och e-post | **Säkerhetsägare**<br/>En representant från säkerhetsteamet som kan signera att planen uppfyller organisationens säkerhetskrav. | SO/R |
| Namn och e-post | **IT-supportchef / Helpdesk**<br/>En representant från IT-supportorganisationen som kan ge input om supporten av den här ändringen ur ett helpdesk-perspektiv. | R/I |
| Namn och e-post för pilotanvändare | **ANVÄNDARE AV RBAC-roll**<br/>Den grupp användare som privilegierad identitetshantering implementeras för. De måste veta hur de ska aktivera sina roller när privilegierad identitetshantering har implementerats. | I |

### <a name="enable-privileged-identity-management"></a>Aktivera privilegierad identitetshantering

Som en del av planeringsprocessen måste du först godkänna och aktivera privilegierad identitetshantering genom att följa vår start med hjälp av artikeln [Privilegierad identitetshantering.](pim-getting-started.md) Om du aktiverar privilegierad identitetshantering får du tillgång till vissa funktioner som är särskilt utformade för att hjälpa till med distributionen.

Om ditt mål är att distribuera privilegierad identitetshantering för Azure-resurser bör du följa våra [identifiera Azure-resurser för att hantera i artikeln Privilegierad identitetshantering.](pim-resource-roles-discover-resources.md) Endast ägare av prenumerationer och hanteringsgrupper kan identifiera och ombord på dessa resurser till Privilegierad identitetshantering. När den är inbyggd är PIM-funktionen tillgänglig för ägare på alla nivåer, inklusive hanteringsgrupp, prenumeration, resursgrupp och resurs. Om du är en global administratör som försöker distribuera privilegierad identitetshantering för dina Azure-resurser kan du [öka åtkomsten för att hantera alla Azure-prenumerationer](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) för att ge dig själv åtkomst till alla Azure-resurser i katalogen för identifiering. Vi rekommenderar dock att du får godkännande från var och en av dina prenumerationsägare innan de hanterar deras resurser med privilegierad identitetshantering.

### <a name="enforce-principle-of-least-privilege"></a>Framtvinga principen om lägsta behörighet

Det är viktigt att se till att du har tillämpat principen om lägsta behörighet i organisationen för både dina Azure AD- och Azure-resursroller.

#### <a name="azure-ad-roles"></a>Azure AD-roller

För Azure AD-roller är det vanligt att organisationer tilldelar rollen Global administratör till ett stort antal administratörer när de flesta administratörer bara behöver en eller två specifika administratörsroller. Privilegierade rolltilldelningar tenderar också att lämnas ohanterat.

> [!NOTE]
> Vanliga fallgropar i rolldelegering:
>
> - Administratören som ansvarar för Exchange får rollen Global administratör även om de bara behöver rollen Exchange-administratör för att utföra sitt dagliga arbete.
> - Rollen Global administratör tilldelas en Office-administratör eftersom administratören behöver både säkerhets- och SharePoint-administratörsroller och det är enklare att bara delegera en roll.
> - Administratören tilldelades en säkerhetsadministratörsroll för att utföra en uppgift för länge sedan, men togs aldrig bort.

Följ dessa steg för att tillämpa principen om lägsta behörighet för dina Azure AD-roller.

1. Förstå detaljernas kornighet genom att läsa och förstå de [tillgängliga Azure AD-administratörsrollerna](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Du och ditt team bör också referera till [administratörsroller efter identitetsuppgift i Azure AD](../users-groups-roles/roles-delegate-by-task.md), vilket förklarar den minst privilegierade rollen för specifika uppgifter.

1. Lista vem som har privilegierade roller i organisationen. Du kan använda [guiden Privilegierad identitetshantering](pim-security-wizard.md#run-the-wizard) för att komma till en sida som följande.

    ![Fönstret Identifiera privilegierade roller som visar vem som har privilegierade roller](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Ta reda på varför de behöver rollen för alla globala administratörer i organisationen. Baserat på att läsa den tidigare dokumentationen bör du, om personens jobb kan utföras av en eller flera detaljerade administratörsroller, ta bort dem från rollen Global administratör och göra tilldelningar därefter i Azure Active Directory (som referens: Microsoft har för närvarande bara ett tiotal administratörer med rollen Global administratör. Läs mer om [hur Microsoft använder Privilegierad identitetshantering](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. För alla andra Azure AD-roller, granska listan över tilldelningar, identifiera administratörer som inte längre behöver rollen och ta bort dem från sina tilldelningar.

Om du vill automatisera de två sista stegen kan du använda åtkomstgranskningar i Privilegierad identitetshantering. I början av steget för [att starta en åtkomstgranskning för Azure AD-roller i Privilegierad identitetshantering](pim-how-to-start-security-review.md)kan du ställa in en åtkomstgranskning för varje Azure AD-roll som har en eller flera medlemmar.

![Skapa ett åtkomstgranskningsfönster för Azure AD-roller](./media/pim-deployment-plan/create-access-review.png)

Du bör ställa in granskarna till **medlemmar (själv)**. Detta kommer att skicka ut ett e-postmeddelande till alla medlemmar i rollen för att få dem att bekräfta om de behöver åtkomst. Du bör också aktivera **Kräv orsak vid godkännande** i avancerade inställningar så att användarna kan ange varför de behöver rollen. Baserat på den här informationen kan du ta bort användare från onödiga roller och delegera mer detaljerade administratörsroller när det gäller globala administratörer.

Åtkomstgranskningar är beroende av e-postmeddelanden för att meddela andra att granska deras åtkomst till rollerna. Om du har privilegierade konton som inte har e-postmeddelanden länkade måste du fylla i det sekundära e-postfältet på dessa konton. Mer information finns [i attributet proxyAddresses i Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Azure-resursroller

För Azure-prenumerationer och resurser kan du ställa in en liknande åtkomstgranskningsprocess för att granska rollerna i varje prenumeration eller resurs. Målet med den här processen är att minimera tilldelningar av ägare och användaråtkomstadministratörer som är kopplade till varje prenumeration eller resurs samt att ta bort onödiga tilldelningar. Organisationer delegerar dock ofta sådana uppgifter till ägaren av varje prenumeration eller resurs eftersom de har en bättre förståelse för de specifika rollerna (särskilt anpassade roller).

Om du är IT-administratör med rollen Global administratör som försöker distribuera privilegierad identitetshantering för Azure-resurser i organisationen kan du [öka åtkomsten för att hantera alla Azure-prenumerationer](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) för att få åtkomst till varje prenumeration. Du kan sedan hitta varje prenumerationsägare och arbeta med dem för att ta bort onödiga tilldelningar och minimera ägarrolltilldelningen.

Användare med ägarrollen för en Azure-prenumeration kan också använda [åtkomstgranskningar för Azure-resurser för](pim-resource-roles-start-access-review.md) att granska och ta bort onödiga rolltilldelningar som liknar den process som beskrivs tidigare för Azure AD-roller.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Bestäm vilka rolltilldelningar som ska skyddas av privilegierad identitetshantering

När du har rensat privilegierade rolltilldelningar i organisationen måste du bestämma vilka roller du ska skydda med privilegierad identitetshantering.

Om en roll skyddas av Privilegierad identitetshantering måste berättigade användare som tilldelats den höjas för att använda de privilegier som beviljas av rollen. Höjningsprocessen kan också omfatta att erhålla godkännande, utföra multifaktorautentisering och/eller ge en anledning till varför de aktiverar. Privilegierad identitetshantering kan också spåra höjder via meddelanden och händelseloggar för privilegierad identitetshantering och Azure AD-granskning.

Att välja vilka roller som ska skyddas med privilegierad identitetshantering kan vara svårt och vara olika för varje organisation. Det här avsnittet innehåller våra råd om bästa praxis för Azure AD- och Azure-resursroller.

#### <a name="azure-ad-roles"></a>Azure AD-roller

Det är viktigt att prioritera att skydda Azure AD-roller som har flest antal behörigheter. Baserat på användningsmönster bland alla privilegierade identitetshanteringskunder är de 10 största Azure AD-rollerna som hanteras av Privilegierad identitetshantering:

1. Global administratör
1. Säkerhetsadministratör
1. Användaradministratör
1. Exchange-administratör
1. SharePoint-administratör
1. Intune-administratör
1. Säkerhetsläsare
1. Tjänstadministratör
1. Faktureringsadministratör
1. Skype for Business-administratör

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar att** du hanterar alla dina globala administratörer och säkerhetsadministratörer med privilegierad identitetshantering som ett första steg eftersom de är de som kan göra mest skada när äventyras.

Det är viktigt att tänka på vilka data och behörigheter som är mest känsliga för din organisation. Som ett exempel kan vissa organisationer vill skydda sin Power BI-administratörsroll eller sin teamadministratörsroll med hjälp av Privilegierad identitetshantering eftersom de har möjlighet att komma åt data och/eller ändra kärnarbetsflöden.

Om det finns några roller med gästanvändare tilldelade, är de särskilt sårbara för angrepp.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar att** du hanterar alla roller med gästanvändare som använder Privilegierad identitetshantering för att minska risken i samband med komprometterade gästanvändarkonton.

Läsarroller som Directory Reader, Message Center Reader och Security Reader anses ibland vara mindre viktiga jämfört med andra roller eftersom de inte har skrivbehörighet. Vi har dock sett att vissa kunder också skyddar dessa roller eftersom angripare som har fått åtkomst till dessa konton kan läsa känsliga data, till exempel personuppgifter. Du bör ta hänsyn till detta när du bestämmer om läsarroller i organisationen måste hanteras med privilegierad identitetshantering.

#### <a name="azure-resource-roles"></a>Azure-resursroller

När du bestämmer vilka rolltilldelningar som ska hanteras med privilegierad identitetshantering för Azure-resurs måste du först identifiera de prenumerationer/resurser som är viktigast för din organisation. Exempel på sådana prenumerationer/resurser är:

- Resurser som är värd för de mest känsliga data
- Resurser som är kärnberoende, kundinriktade program beror på

Om du är global administratör som har problem med att avgöra vilka prenumerationer/resurser som är viktigast bör du kontakta prenumerationsägare i organisationen för att samla in en lista över resurser som hanteras av varje prenumeration. Du bör sedan arbeta med prenumerationsägarna för att gruppera resurserna baserat på allvarlighetsgraden om de äventyras (låg, medel, hög). Du bör prioritera hantering av resurser med privilegierad identitetshantering baserat på den här allvarlighetsgraden.

> [!TIP]
> :heavy_check_mark: Microsoft **rekommenderar att** du arbetar med prenumerations-/resursägare av kritiska tjänster för att konfigurera arbetsflödet för privilegierad identitetshantering för alla roller i känsliga prenumerationer/resurser.

Privilegierad identitetshantering för Azure-resurser stöder tidsbundna tjänstkonton. Du bör behandla tjänstkonton exakt på samma sätt som hur du skulle behandla ett vanligt användarkonto.

För prenumerationer/resurser som inte är lika kritiska behöver du inte ställa in Privilegierad identitetshantering för alla roller. Du bör dock fortfarande skydda rollerna Ägare och Användaråtkomstadministratör med Privilegierad identitetshantering.

> [!TIP]
> :heavy_check_mark: Microsoft **rekommenderar att** du hanterar ägarroller och användaråtkomstadministratörsroller för alla prenumerationer/resurser med privilegierad identitetshantering.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Bestäm vilka rolltilldelningar som ska vara permanenta eller stödberättigande

När du har bestämt listan över roller som ska hanteras av Privilegierad identitetshantering måste du bestämma vilka användare som ska få den kvalificerade rollen jämfört med den permanent aktiva rollen. Permanent aktiva roller är de normala roller som tilldelas via Azure Active Directory och Azure-resurser, medan kvalificerade roller endast kan tilldelas i Privilegierad identitetshantering.

> [!TIP]
> :heavy_check_mark: Microsoft **rekommenderar att** du har noll permanent aktiva tilldelningar för både Azure AD-roller och Azure-resursroller än de rekommenderade [två nödsituationsåtkomstkontona för brytglas](../users-groups-roles/directory-emergency-access.md), som bör ha den permanenta globala administratörsrollen.

Även om vi rekommenderar noll stående administratör, är det ibland svårt för organisationer att uppnå detta direkt. Här är saker att tänka på när du fattar detta beslut:

- Höjdfrekvens – Om användaren bara behöver den privilegierade tilldelningen en gång ska de inte ha den permanenta tilldelningen. Å andra sidan, om användaren behöver rollen för sitt dagliga jobb och använda Privilegierad identitetshantering skulle kraftigt minska deras produktivitet, kan de övervägas för den permanenta rollen.
- Fall som är specifika för din organisation – Om den person som får den kvalificerade rollen är från ett mycket avlägset team eller en högt uppsatt chef till den grad att det är svårt att kommunicera och verkställa höjningsprocessen, kan de övervägas för den permanenta rollen.

> [!TIP]
> :heavy_check_mark: Microsoft **rekommenderar** att du ställer in återkommande åtkomstgranskningar för användare med permanenta rolltilldelningar (om du har några). Läs mer om återkommande åtkomstgranskningar i det sista avsnittet i den här distributionsplanen

### <a name="draft-your-privileged-identity-management-settings"></a>Utkast till inställningar för privilegierad identitetshantering

Innan du implementerar lösningen för privilegierad identitetshantering är det bra att utarbeta inställningarna för privilegierad identitetshantering för varje privilegierad roll som din organisation använder. Det här avsnittet innehåller några exempel på inställningar för privilegierad identitetshantering för vissa roller (de är bara till referens och kan vara olika för din organisation). Var och en av dessa inställningar förklaras i detalj med Microsofts rekommendationer efter tabellerna.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Privilegierade identitetshanteringsinställningar för Azure AD-roller

| Roll | Kräv MFA | Avisering | Incidentbiljett | Kräv godkännande | Approver | Varaktighet för aktivering | Permanent administratör |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Global administratör | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andra globala administratörer | 1 timme | Konton för nödåtkomst |
| Exchange-administratör | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Inget | 2 timmar | Inget |
| Administratör för helpdesk | :x: | :x: | :heavy_check_mark: | :x: | Inget | 8 timmar | Inget |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Privilegierade identitetshanteringsinställningar för Azure-resursroller

| Roll | Kräv MFA | Avisering | Kräv godkännande | Approver | Varaktighet för aktivering | Aktiv administratör | Aktiv förfallotid | Berättigat utgångsdatum |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Ägare av kritiska prenumerationer | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Övriga ägare av abonnemanget | 1 timme | Inget | Saknas | 3 månader |
| Administratör för användaråtkomst för mindre kritiska prenumerationer | :heavy_check_mark: | :heavy_check_mark: | :x: | Inget | 1 timme | Inget | Saknas | 3 månader |
| Virtuell datordeltagare | :x: | :heavy_check_mark: | :x: | Inget | 3 timmar | Inget | Saknas | 6 månader |

I följande tabell beskrivs var och en av inställningarna.

| Inställning | Beskrivning |
| --- | --- |
| Roll | Namn på den roll som du definierar inställningarna för. |
| Kräv MFA | Om den berättigade användaren måste utföra MFA innan rollen aktiveras.<br/><br/> : heavy_check_mark: Microsoft rekommenderar att du tillämpar MFA för alla **administratörsroller,** särskilt om rollerna har gästanvändare. |
| Avisering | Om värdet är true får Global administratör, administratör för privilegierad roll och säkerhetsadministratör i organisationen ett e-postmeddelande när en behörig användare aktiverar rollen.<br/><br/>**Anm.:** Vissa organisationer har ingen e-postadress knuten till sina administratörskonton, för att få dessa e-postmeddelanden bör du ange en alternativ e-postadress så att administratörer får dessa e-postmeddelanden. |
| Incidentbiljett | Om den berättigade användaren måste registrera ett incidentbiljettnummer när de aktiverar sin roll. Den här inställningen hjälper en organisation att identifiera varje aktivering med ett internt incidentnummer för att minska oönskade aktiveringar.<br/><br/> : heavy_check_mark: **Microsoft rekommenderar att** du utnyttjar incidentbiljettnummer för att knyta privilegierad identitetshantering till ditt interna system. Detta är särskilt användbart för godkännare som behöver kontext för aktiveringen. |
| Kräv godkännande | Om den berättigade användaren behöver godkänna att rollen aktiveras.<br/><br/> : heavy_check_mark: Microsoft rekommenderar att du **konfigurerar** godkännande för roller med störst behörighet. Baserat på användningsmönster för alla privilegierade identitetshanteringskunder är global administratör, användaradministratör, Exchange-administratör, säkerhetsadministratör och lösenordsadministratör de vanligaste rollerna med godkännandeinställningar. |
| Approver | Om godkännande krävs för att aktivera den kvalificerade rollen anger du de personer som ska godkänna begäran. Som standard anger Privilegierad identitetshantering godkännaren att vara alla användare som är en privilegierad rolladministratör oavsett om de är permanenta eller berättigade.<br/><br/>**Anm.:** Om en användare är både kvalificerad för en Azure AD-roll och en godkännare av rollen, kommer de inte att kunna godkänna sig själva.<br/><br/> : heavy_check_mark: **Microsoft rekommenderar** att du väljer godkännare att vara de som är mest kunniga om den specifika rollen och dess frekventa användare snarare än en global administratör. |
| Varaktighet för aktivering | Hur länge en användare aktiveras i rollen innan den upphör att gälla. |
| Permanent administratör | Lista över användare som kommer att vara en permanent administratör för rollen (aldrig behöver aktivera).<br/><br/> : heavy_check_mark: **Microsoft rekommenderar att** du har noll stående administratör för alla roller utom för globala administratörer. Läs mer om det i vem som ska göras stödberättigande och vem som ska vara permanent aktiv del av denna plan. |
| Aktiv administratör | För Azure-resurser är aktiv administratör listan över användare som aldrig behöver aktivera för att använda rollen. Detta kallas inte permanent administratör som i Azure AD-roller eftersom du kan ange en förfallotid för när användaren kommer att förlora den här rollen. |
| Aktiv förfallotid | En aktiv rolltilldelning för Azure-resursroller upphör att gälla efter den här konfigurerade tidsperioden. Du kan välja mellan 15 dagar, 1 månad, 3 månader, 6 månader, 1 år eller permanent aktiv. |
| Berättigat utgångsdatum | En kvalificerad rolltilldelning för Azure-resursroller upphör att gälla efter den här konfigurerade tidsperioden. Du kan välja mellan 15 dagar, 1 månad, 3 månader, 6 månader, 1 år eller permanent berättigad. |

## <a name="implement-your-solution"></a>Implementera din lösning

Grunden för korrekt planering är grunden för att du kan distribuera ett program med Azure Active Directory.  Det ger intelligent säkerhet och integration som förenklar introduktionen samtidigt som tiden för lyckade distributioner minskar.  Den här kombinationen säkerställer att ditt program är integrerat med lätthet samtidigt som du minskar tiden för dina slutanvändare.

### <a name="identify-test-users"></a>Identifiera testanvändare

Använd det här avsnittet om du vill identifiera en uppsättning användare och eller grupper av användare för att validera implementeringen. Baserat på de inställningar som du har valt i planeringsavsnittet identifierar du de användare som du vill testa för varje roll.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar att** du gör tjänstägare för varje Azure AD-roll till testanvändare så att de kan bekanta sig med processen och bli en intern förespråkare för distributionen.

I den här tabellen identifierar du de testanvändare som kontrollerar att inställningarna för varje roll fungerar.

| Rollnamn | Testa användare |
| --- | --- |
| &lt;Rollnamn&gt; | &lt;Användare att testa rollen&gt; |
| &lt;Rollnamn&gt; | &lt;Användare att testa rollen&gt; |

### <a name="test-implementation"></a>Testimplementering

Nu när du har identifierat testanvändarna använder du det här steget för att konfigurera Privilegierad identitetshantering för testanvändarna. Om din organisation vill införliva arbetsflödet för privilegierad identitetshantering i ditt eget interna program i stället för att använda Privilegierad identitetshantering i Azure-portalen, stöds alla åtgärder i Privilegierad identitetshantering också via vårt [diagram-API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Konfigurera privilegierad identitetshantering för Azure AD-roller

1. [Konfigurera azure AD-rollinställningarna](pim-how-to-change-default-settings.md) baserat på vad du planerade.

1. Navigera till **Azure AD-roller,** klicka på **Roller**och välj sedan den roll som du just konfigurerat.

1. Om de redan är en permanent administratör för gruppen testanvändare kan du göra dem kvalificerade genom att söka efter dem och konvertera dem från permanent till kvalificerat genom att klicka på de tre punkterna på deras rad. Om de inte har rolltilldelningarna ännu kan du [göra en ny kvalificerad uppgift](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Upprepa steg 1-3 för alla roller som du vill testa.

1. När du har konfigurerat testanvändarna bör du skicka länken till dem för hur du [aktiverar deras Azure AD-roll](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Konfigurera privilegierad identitetshantering för Azure-resursroller

1. [Konfigurera Azure-resursrollinställningarna](pim-resource-roles-configure-role-settings.md) för en roll i en prenumeration eller resurs som du vill testa.

1. Navigera till **Azure-resurser** för den prenumerationen och klicka på **Roller**, välj den roll du just konfigurerat.

1. Om de redan är en aktiv administratör för gruppen testanvändare kan du göra dem kvalificerade genom att söka efter dem och [uppdatera deras rolltilldelning](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Om de inte har rollen ännu kan du [tilldela en ny roll](pim-resource-roles-assign-roles.md#assign-a-role).

1. Upprepa steg 1-3 för alla roller som du vill testa.

1. När du har konfigurerat testanvändarna bör du skicka länken till dem för hur du [aktiverar deras Azure-resursroll](pim-resource-roles-activate-your-roles.md).

Du bör använda det här steget för att kontrollera om all konfiguration som du har ställt in för rollerna fungerar korrekt. Använd följande tabell för att dokumentera dina tester. Du bör också använda det här steget för att optimera kommunikationen med berörda användare.

| Roll | Förväntat beteende under aktivering | Faktiska resultat |
| --- | --- | --- |
| Global administratör | (1) Kräv MFA<br/>(2) Kräver godkännande<br/>(3) Godkännare får anmälan och kan godkänna<br/>(4) Rollen upphör att gälla efter förinställd tid |  |
| Ägare av abonnemang *X* | (1) Kräv MFA<br/>(2) stödberättigande tilldelning löper ut efter konfigurerad tidsperiod |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Kommunicera privilegierad identitetshantering till berörda intressenter

Om du distribuerar privilegierad identitetshantering introduceras ytterligare steg för användare med privilegierade roller. Även om privilegierad identitetshantering kraftigt minskar säkerhetsproblem som är associerade med privilegierade identiteter, måste ändringen kommuniceras effektivt före den klientomfattande distributionen. Beroende på antalet påverkade administratörer väljer organisationer ofta att skapa ett internt dokument, en video eller ett e-postmeddelande om ändringen. Ofta ingår i dessa meddelanden inkluderar:

- Vad är PIM
- Vad är fördelen för organisationen
- Vem kommer att påverkas
- När kommer PIM att rullas ut
- Vilka ytterligare steg krävs för att användarna ska kunna aktivera sin roll
    - Du bör skicka länkar till vår dokumentation:
    - [Aktivera Azure AD-roller](pim-how-to-activate-role.md)
    - [Aktivera Azure-resursroller](pim-resource-roles-activate-your-roles.md)
- Kontaktinformation eller helpdesk-länk för eventuella problem i samband med PIM

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du ställer in tid med hjälpavdelningen/supportteamet för att gå igenom arbetsflödet för privilegierad identitetshantering (om din organisation har ett internt IT-supportteam). Förse dem med lämpliga dokumentationer samt dina kontaktuppgifter.

### <a name="move-to-production"></a>Flytta till produktion

När testningen är klar och lyckas flyttar du Privilegierad identitetshantering till produktion genom att upprepa alla steg i testfaserna för alla användare av varje roll som du har definierat i konfigurationen för privilegierad identitetshantering. För privilegierad identitetshantering för Azure AD-roller testar och distribuerar organisationer ofta privilegierad identitetshantering för globala administratörer innan de testar och distribuerar privilegierad identitetshantering för andra roller. Under tiden för Azure-resurs testar och distribuerar organisationer normalt privilegierad identitetshantering en Azure-prenumeration i taget.

### <a name="in-the-case-a-rollback-is-needed"></a>Om en återställning behövs

Om Privilegierad identitetshantering inte fungerade som önskat i produktionsmiljön kan följande återställningssteg hjälpa dig att återgå till ett fungerande tillstånd innan du konfigurerar Privilegierad identitetshantering:

#### <a name="azure-ad-roles"></a>Azure AD-roller

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Öppna **Azure AD-privilegierad identitetshantering**.
1. Klicka på **Azure AD-roller** och sedan på **Roller**.
1. För varje roll som du har konfigurerat klickar du på ellipsen (**...**) för alla användare med en kvalificerad tilldelning.
1. Klicka på alternativet **Gör permanent** om du vill göra rolltilldelningen permanent.

#### <a name="azure-resource-roles"></a>Azure-resursroller

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Öppna **Azure AD-privilegierad identitetshantering**.
1. Klicka på **Azure-resurser** och sedan på en prenumeration eller resurs som du vill återställa.
1. Klicka på **Roller**.
1. För varje roll som du har konfigurerat klickar du på ellipsen (**...**) för alla användare med en kvalificerad tilldelning.
1. Klicka på alternativet **Gör permanent** om du vill göra rolltilldelningen permanent.

## <a name="next-steps-after-deploying"></a>Nästa steg efter distribution

Att distribuera privilegierad identitetshantering i produktion är ett viktigt steg framåt när det gäller att skydda organisationens privilegierade identiteter. Med distributionen av Privilegierad identitetshantering kommer ytterligare privilegierade identitetshanteringsfunktioner som du bör använda för säkerhet och efterlevnad.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Använd aviseringar om privilegierad identitetshantering för att skydda din privilegierade åtkomst

Du bör använda Privilegierad Identitetshanterings inbyggda aviseringar för att bättre skydda din klient. Mer information finns i [säkerhetsaviseringar](pim-how-to-configure-security-alerts.md#security-alerts). Dessa aviseringar inkluderar: administratörer använder inte privilegierade roller, roller tilldelas utanför Privilegierad identitetshantering, roller aktiveras för ofta och mer. För att skydda din organisation fullt ut bör du regelbundet gå igenom listan med aviseringar och åtgärda problemen. Du kan visa och åtgärda aviseringar på följande sätt:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Öppna **Azure AD-privilegierad identitetshantering**.
1. Klicka på **Azure AD-roller** och sedan på **Aviseringar**.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar att** du hanterar alla aviseringar som markerats med hög allvarlighetsgrad omedelbart. För varningar om medelhög och låg allvarlighetsgrad bör du hålla dig informerad och göra ändringar om du anser att det finns ett säkerhetshot.

Om någon av de specifika aviseringarna inte är användbara eller inte gäller för din organisation kan du alltid stänga aviseringen på sidan aviseringar. Du kan alltid återställa den här uppsägningen senare på sidan Azure AD-inställningar.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Konfigurera återkommande åtkomstgranskningar för att regelbundet granska organisationens privilegierade identiteter

Åtkomstgranskningar är det bästa sättet för dig att fråga användare som tilldelats med privilegierade roller eller specifika granskare om varje användare behöver den privilegierade identiteten. Åtkomst recensioner är bra om du vill minska attack yta och förbli kompatibel. Mer information om hur du startar en åtkomstgranskning finns i [Azure AD-rollers åtkomstgranskningar](pim-how-to-start-security-review.md) och [Azure-resursrollers åtkomstgranskningar](pim-resource-roles-start-access-review.md). För vissa organisationer krävs regelbunden åtkomstgranskning för att vara kompatibel med lagar och förordningar, medan åtkomstgranskning för andra är det bästa sättet att upprätthålla principen om lägsta behörighet i hela organisationen.

> [!TIP]
> :heavy_check_mark: Microsoft rekommenderar att du ställer in **kvartalsvisa åtkomstgranskningar** för alla dina Azure AD- och Azure-resursroller.

I de flesta fall är granskaren för Azure AD-roller användarna själva medan granskaren för Azure-resursroller är ägare till prenumerationen, som rollen finns i. Det är dock ofta fallet när företag har privilegierade konton som inte är kopplade till någon viss persons e-postadress. I dessa fall läser och granskar ingen åtkomsten.

> [!TIP]
> :heavy_check_mark: Microsoft **rekommenderar att** du lägger till en sekundär e-postadress för alla konton med privilegierade rolltilldelningar som inte är länkade till en regelbundet kontrollerad e-postadress

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Få ut mesta av granskningsloggen för att förbättra säkerhet och efterlevnad

Granskningsloggen är den plats där du kan hålla dig uppdaterad och följa reglerna. Privilegierad identitetshantering lagrar för närvarande en 30-dagarshistorik över hela organisationens historik i granskningsloggen, inklusive:

- Aktivering/avaktivering av kvalificerade roller
- Rolltilldelningsaktiviteter inom och utanför Privilegierad identitetshantering
- Ändringar i rollinställningar
- Begär/godkänna/neka aktiviteter för rollaktivering med godkännandeinställningar
- Uppdatera till aviseringar

Du kan komma åt dessa granskningsloggar om du är global administratör eller administratör för privilegierade roller. Mer information finns i [granskningshistorik för Azure AD-roller](pim-how-to-use-audit-log.md) och [granskningshistorik för Azure-resursroller](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: Microsoft rekommenderar att du har minst en administratör som läser igenom alla granskningshändelser varje vecka och exporterar dina **granskningshändelser** varje månad.

Om du vill lagra granskningshändelser automatiskt under en längre tid synkroniseras Granskningsloggen för privilegierad identitetshantering automatiskt i [Azure AD-granskningsloggarna](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: Microsoft rekommenderar att du konfigurerar [Azure-loggövervakning](../reports-monitoring/concept-activity-logs-azure-monitor.md) för att arkivera **granskningshändelser** i ett Azure-lagringskonto i behov av säkerhet och efterlevnad.
