---
title: Distribuera Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026004"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Distribuera Azure AD Privileged Identity Management (PIM)

Den här steg-för-steg-guiden beskriver hur du planerar distributionen av Privileged Identity Management (PIM) i din Azure Active Directory (Azure AD)-organisation.

> [!TIP]
> I den här artikeln visas objekt som är markerade som:
> 
> : heavy_check_mark: **Microsoft rekommenderar**
> 
> Detta är allmänna rekommendationer och du bör bara implementera om de gäller för dina företags behov.

## <a name="learn-about-privileged-identity-management"></a>Läs mer om Privileged Identity Management

Azure AD Privileged Identity Management hjälper dig att hantera privilegierade administrativa roller i Azure AD, Azure-resurser och andra Microsoft Online Services. I en värld där privilegierade identiteter är tilldelade och glömt, ger Privileged Identity Management lösningar som just-in-Time-åtkomst, begär godkännande arbets flöden och fullständigt integrerade åtkomst granskningar så att du kan identifiera, upptäcka och förhindra skadlig aktiviteter för privilegierade roller i real tid. Distribution av Privileged Identity Management för att hantera dina privilegierade roller i hela organisationen minskar risken avsevärt samtidigt som Visa värdefulla insikter om aktiviteterna i dina privilegierade roller.

### <a name="business-value-of-privileged-identity-management"></a>Verksamhets värde för Privileged Identity Management

**Hantera risk** – skydda din organisation genom att tvinga principen om [minsta behörighets åtkomst](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) och just-in-Time-åtkomst. Genom att minimera antalet permanenta tilldelningar av användare till privilegierade roller och verkställa godkännanden och MFA för utökade privilegier, kan du avsevärt minska säkerhets riskerna som är relaterade till privilegie rad åtkomst i din organisation. Genom att framtvinga minst privilegium och just-in-Time-åtkomst kan du också visa en historik över åtkomst till privilegierade roller och spåra säkerhets problem när de sker.

**Hantera efterlevnad och styrning** – distribution av Privileged Identity Management skapar en miljö för pågående identitets styrning. Just-in-Time-höjning av privilegierade identiteter är ett sätt för Privileged Identity Management att hålla reda på privilegierade åtkomst aktiviteter i din organisation. Du kan också visa och ta emot meddelanden för alla tilldelningar av permanenta och kvalificerade roller i din organisation. Genom åtkomst granskning kan du regelbundet granska och ta bort onödiga privilegierade identiteter och se till att organisationen är kompatibel med de mest rigorösa identiteterna, åtkomst-och säkerhets standarderna.

**Sänk kostnaderna** – minska kostnaderna genom att eliminera ineffektiva problem, mänskliga fel och säkerhets problem genom att distribuera Privileged Identity Management korrekt. Netto resultatet är en minskning av cyberhot brottslighet som är associerad med privilegierade identiteter, vilket är kostsamt och svårt att återställa från. Privileged Identity Management hjälper också organisationen att minska kostnaderna som är kopplade till gransknings åtkomst information när det gäller att följa regler och standarder.

Mer information finns i [vad är Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Licensierings krav

Om du vill använda Privileged Identity Management måste katalogen ha någon av följande betalda eller utvärderings licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Mer information finns i [licens krav för att använda Privileged Identity Management](subscription-requirements.md).

### <a name="key-terminology"></a>Nyckel terminologi

| Term eller begrepp | Beskrivning |
| --- | --- |
| berättigad | En rolltilldelning som kräver att en användare utför en eller flera åtgärder för att använda rollen. Om en användare har gjorts berättigad för en roll innebär det att användaren kan aktivera rollen när det finns behov av att utföra privilegierade åtgärder. Det finns ingen skillnad i den åtkomst som ges till någon med en permanent kontra berättigad rolltilldelning. Den enda skillnaden är att vissa användare inte behöver den åtkomsten hela tiden. |
| aktivera | Processen med att utföra en eller flera åtgärder för att använda en roll som en användare är berättigad för. Det kan vara åtgärder som att utföra en kontroll av multifaktorautentisering (MFA), ange en affärsmotivering eller begära godkännande från utnämnda godkännare. |
| just-in-time-åtkomst (JIT) | En modell i vilken användarna får tillfällig behörighet att utföra privilegierade uppgifter, vilket hindrar skadliga eller obehöriga användare från att få åtkomst när behörigheterna har gått ut. Åtkomst beviljas endast när användare behöver den. |
| princip om minsta behörighetsåtkomst | En rekommenderad säkerhetsrutin där varje användare endast har fått de minsta privilegier som krävs för att utföra uppgifterna som de har behörighet att utföra. Den här metoden minimerar antalet globala administratörer och använder i stället specifika administratörsroller för vissa scenarier. |

Mer information finns i [terminologi](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Översikt över hur Privileged Identity Management fungerar

1. Privileged Identity Management har kon figurer ATS så att användarna är berättigade till privilegierade roller.
1. När en berättigad användare behöver använda sin privilegierade roll, aktive ras rollen i Privileged Identity Management.
1. Beroende på Privileged Identity Management inställningar som kon figurer ATS för rollen måste användaren slutföra vissa steg (till exempel utföra Multi-Factor Authentication, få godkännande eller ange en orsak).
1. När användaren har aktiverat sin roll får han eller hon rollen för en förkonfigurerad tids period.
1. Administratörer kan visa en historik över alla Privileged Identity Management aktiviteter i gransknings loggen. De kan också skydda sina Azure AD-organisationer ytterligare och möta kompatibiliteten med hjälp av Privileged Identity Management funktioner som åtkomst granskningar och aviseringar.

Mer information finns i [vad är Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Roller som kan hanteras av Privileged Identity Management

**Azure AD-roller** – dessa roller är alla i Azure Active Directory (till exempel global administratör, Exchange-administratör och säkerhets administratör). Du kan läsa mer om rollerna och deras funktioner i [Administratörs roll behörigheter i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). För hjälp med att bestämma vilka roller som ska tilldelas dina administratörer, se [minst privilegierade roller efter uppgift](../users-groups-roles/roles-delegate-by-task.md).

**Azure-resurs roller** – dessa roller är länkade till en Azure-resurs, resurs grupp, prenumeration eller hanterings grupp. Privileged Identity Management ger just-in-Time-åtkomst till båda inbyggda roller som ägare, användar åtkomst administratör och deltagare, samt [anpassade roller](../../role-based-access-control/custom-roles.md). Mer information om Azures resurs roller finns i [rollbaserad åtkomst kontroll (RBAC)](../../role-based-access-control/overview.md).

Mer information finns i [roller som du inte kan hantera i Privileged Identity Management](pim-roles.md).

## <a name="plan-your-deployment"></a>Planera distributionen

Det här avsnittet fokuserar på vad du behöver göra innan du distribuerar Privileged Identity Management i din organisation. Det är viktigt att följa anvisningarna och förstå begreppen i det här avsnittet, eftersom de hjälper dig att skapa det bästa plan som är anpassat för din organisations privilegierade identiteter.

### <a name="identify-your-stakeholders"></a>Identifiera dina intressenter

I följande avsnitt får du hjälp att identifiera alla intressenter som ingår i projektet och behöver logga ut, granska eller hålla dig informerad. Den innehåller separata tabeller för att distribuera Privileged Identity Management för Azure AD-roller och Privileged Identity Management för Azures resurs roller. Lägg till intressenter i följande tabell efter vad som är lämpligt för din organisation.

- SÅ = logga ut på det här projektet
- R = granska projektet och ange inmatade
- I = informeras om det här projektet

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Intressenter: Privileged Identity Management för Azure AD-roller

| Namn | Roll | Åtgärd |
| --- | --- | --- |
| Namn och e-postadress | **Identitets arkitekt eller Global Azure-administratör**<br/>En representant från identitets hanterings teamet som är ansvarig för att definiera hur den här ändringen justeras med infrastrukturen för kärn identitets hantering i din organisation. | SÅ/R/I |
| Namn och e-postadress | **Tjänst ägare/rads hanterare**<br/>En representant från IT-ägare till en tjänst eller en grupp av tjänster. De är viktiga för att fatta beslut och hjälpa till att distribuera Privileged Identity Management för sitt team. | SÅ/R/I |
| Namn och e-postadress | **Säkerhets ägare**<br/>En representant från säkerhets teamet som kan signera att planen uppfyller organisationens säkerhets krav. | SÅ/R |
| Namn och e-postadress | **IT support Manager/supportavdelningen**<br/>En representant från IT-supporten som kan tillhandahålla insikter om den här förändringen från ett support perspektiv. | R/I |
| Namn och e-post för pilot användare | **Privilegierade roll användare**<br/>Gruppen med användare som Privileged Identity Management implementeras för. De måste känna till hur de aktiverar sina roller när Privileged Identity Management implementeras. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Intressenter: Privileged Identity Management för Azures resurs roller

| Namn | Roll | Åtgärd |
| --- | --- | --- |
| Namn och e-postadress | **Prenumeration/resurs ägare**<br/>En representant från IT-ägarna till varje prenumeration eller resurs som du vill distribuera Privileged Identity Management för | SÅ/R/I |
| Namn och e-postadress | **Säkerhets ägare**<br/>En representant från säkerhets teamet som kan signera att planen uppfyller organisationens säkerhets krav. | SÅ/R |
| Namn och e-postadress | **IT support Manager/supportavdelningen**<br/>En representant från IT-supporten som kan tillhandahålla insikter om den här förändringen från ett support perspektiv. | R/I |
| Namn och e-post för pilot användare | **Användare av RBAC-roll**<br/>Gruppen med användare som Privileged Identity Management implementeras för. De måste känna till hur de aktiverar sina roller när Privileged Identity Management implementeras. | I |

### <a name="enable-privileged-identity-management"></a>Aktivera Privileged Identity Management

Som en del av planerings processen måste du först godkänna och aktivera Privileged Identity Management genom att följa vår artikel för att [börja använda Privileged Identity Management](pim-getting-started.md) . Om du aktiverar Privileged Identity Management får du till gång till vissa funktioner som är särskilt utformade för att hjälpa dig med din distribution.

Om målet är att distribuera Privileged Identity Management för Azure-resurser bör du följa våra [Azure-resurser för att hantera i Privileged Identity Management](pim-resource-roles-discover-resources.md) artikeln. Endast ägare av prenumerationer och hanterings grupper kan identifiera och publicera dessa resurser på Privileged Identity Management. När den har publicerats är PIM-funktionen tillgänglig för ägare på alla nivåer, inklusive hanterings grupp, prenumeration, resurs grupp och resurs. Om du är en global administratör som försöker distribuera Privileged Identity Management för dina Azure-resurser kan du [öka åtkomsten för att hantera alla Azure-prenumerationer](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) för att ge dig åtkomst till alla Azure-resurser i katalogen för identifiering. Vi rekommenderar dock att du får godkännande från var och en av dina prenumerations ägare innan du hanterar resurserna med Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Tillämpa principen för minsta behörighet

Det är viktigt att se till att du har tillämpat principen om minsta behörighet i din organisation för både din Azure AD-och Azure-resurs roller.

#### <a name="azure-ad-roles"></a>Azure AD-roller

För Azure AD-roller är det vanligt att organisationer tilldelar rollen global administratör till ett stort antal administratörer när de flesta administratörer bara behöver en eller två särskilda administratörs roller. Privilegierade roll tilldelningar tenderar också att lämnas ohanterade.

> [!NOTE]
> Vanliga fall GRO par i roll delegering:
>
> - Den administratör som är ansvarig för Exchange får rollen global administratör även om de bara behöver rollen Exchange-administratör för att utföra dagliga jobb.
> - Den globala administratörs rollen tilldelas till en Office-administratör eftersom administratören behöver både säkerhet och administratörs roller för SharePoint och det är lättare att bara delegera en roll.
> - Administratören har tilldelats en säkerhets administratörs roll för att utföra en aktivitet för länge sedan, men togs aldrig bort.

Följ dessa steg om du vill framtvinga principen om minsta behörighet för dina Azure AD-roller.

1. Förstå rollernas granularitet genom att läsa och förstå de [tillgängliga administratörs rollerna för Azure AD](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Du och ditt team bör också referera till [Administratörs roller efter identitets uppgift i Azure AD](../users-groups-roles/roles-delegate-by-task.md), som förklarar den minst privilegierade rollen för särskilda uppgifter.

1. Lista med privilegierade roller i din organisation. Du kan använda [guiden Privileged Identity Management](pim-security-wizard.md#run-the-wizard) för att komma till en sida som liknar följande.

    ![Fönstret identifiera privilegierade roller som visar vem som har privilegierade roller](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Ta reda på varför de behöver rollen för alla globala administratörer i din organisation. Baserat på att läsa den tidigare dokumentationen, om personens jobb kan utföras av en eller flera olika administratörs roller, bör du ta bort dem från den globala administratörs rollen och göra tilldelningar i enlighet med Azure Active Directory (som referens: Microsoft har för närvarande bara 10 administratörer med rollen global administratör. Läs mer om [hur Microsoft använder Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. För alla andra Azure AD-roller granskar du listan över tilldelningar, identifierar administratörer som inte längre behöver rollen och tar bort dem från deras tilldelningar.

Om du vill automatisera de två sista stegen kan du använda åtkomst granskningar i Privileged Identity Management. Genom att följa stegen i [starta en åtkomst granskning för Azure AD-roller i Privileged Identity Management](pim-how-to-start-security-review.md)kan du konfigurera en åtkomst granskning för varje Azure AD-roll som har en eller flera medlemmar.

![Skapa ett åtkomst gransknings fönster för Azure AD-roller](./media/pim-deployment-plan/create-access-review.png)

Du bör ställa in granskarna till **medlemmar (Self)** . Detta skickar ett e-postmeddelande till alla medlemmar i rollen för att få dem att bekräfta om de behöver åtkomst. Du bör också aktivera **Kräv orsak för godkännande** i de avancerade inställningarna så att användarna kan ange varför de behöver rollen. Utifrån den här informationen kommer du att kunna ta bort användare från onödiga roller och delegera mer detaljerade administratörs roller när det gäller globala administratörer.

Åtkomst granskningar förlitar sig på e-postmeddelanden för att meddela personer att granska deras åtkomst till rollerna. Om du har privilegierade konton som inte har e-post länkade måste du fylla i fältet sekundär e-post för dessa konton. Mer information finns i [proxyaddresses-attribut i Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Azure-resursroller

För Azure-prenumerationer och-resurser kan du konfigurera en liknande åtkomst gransknings process för att granska rollerna i varje prenumeration eller resurs. Målet med den här processen är att minimera ägar-och användar åtkomst administratörs tilldelningar som är kopplade till varje prenumeration eller resurs samt att ta bort onödiga tilldelningar. Organisationer delegerar ofta sådana uppgifter till ägaren av varje prenumeration eller resurs, eftersom de har en bättre förståelse för de olika rollerna (särskilt anpassade roller).

Om du är IT-administratör med rollen global administratör som försöker distribuera Privileged Identity Management för Azure-resurser i din organisation kan du [öka åtkomsten för att hantera alla Azure-prenumerationer](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) för att få åtkomst till varje prenumeration. Du kan sedan hitta varje prenumerations ägare och arbeta med dem för att ta bort onödiga tilldelningar och minimera ägar roll tilldelningen.

Användare med ägar rollen för en Azure-prenumeration kan också använda [åtkomst granskningar för Azure-resurser](pim-resource-roles-start-access-review.md) för att granska och ta bort onödiga roll tilldelningar som liknar processen som beskrivits tidigare för Azure AD-roller.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Bestäm vilka roll tilldelningar som ska skyddas av Privileged Identity Management

Efter att ha rensat privilegierade roll tilldelningar i din organisation måste du bestämma vilka roller som ska skyddas med Privileged Identity Management.

Om en roll skyddas av Privileged Identity Management, måste berättigade användare som är tilldelade till den utökas till att använda privilegier som beviljats av rollen. Höjnings processen kan också innehålla godkännande, utföra Multi-Factor Authentication och/eller ange en orsak till varför de aktive ras. Privileged Identity Management kan också spåra utökade privilegier via meddelanden och gransknings händelse loggar för Privileged Identity Management och Azure AD.

Det kan vara svårt att välja vilka roller som ska skyddas med Privileged Identity Management och de är olika för varje organisation. Det här avsnittet innehåller vår bästa praxis för Azure AD och Azures resurs roller.

#### <a name="azure-ad-roles"></a>Azure AD-roller

Det är viktigt att prioritera skyddet av Azure AD-roller som har flest antal behörigheter. Baserat på användnings mönster bland alla Privileged Identity Management-kunder är de viktigaste 10 Azure AD-rollerna som hanteras av Privileged Identity Management:

1. Global administratör
1. Säkerhetsadministratör
1. Användar administratör
1. Exchange-administratör
1. SharePoint-administratör
1. Intune-administratör
1. Säkerhetsläsare
1. Tjänstadministratör
1. Faktureringsadministratör
1. Skype för företag-administratör

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du hanterar alla dina globala administratörer och säkerhets administratörer med Privileged Identity Management som ett första steg eftersom de är de som kan göra de mest skadliga när de komprometteras.

Det är viktigt att tänka på vilka data och behörigheter som är mest känsliga för din organisation. Vissa organisationer kan till exempel vilja skydda sin Power BI administratörs roll eller deras team administratörs roll med Privileged Identity Management eftersom de kan komma åt data och/eller ändra kärn arbets flöden.

Om det finns roller med tilldelade gäst användare är de särskilt utsatta för angrepp.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du hanterar alla roller med gäst användare som använder Privileged Identity Management för att minska risken som är kopplad till komprometterade gäst användar konton.

Reader-roller som katalog läsaren, meddelande Center läsaren och säkerhets läsaren antas ibland vara mindre viktiga jämfört med andra roller eftersom de inte har Skriv behörighet. Vi har dock sett att vissa kunder också skyddar dessa roller eftersom angripare som har fått åtkomst till dessa konton kan läsa känsliga data, till exempel personliga data. Du bör tänka på detta när du bestämmer om läsarnas roller i din organisation måste hanteras med hjälp av Privileged Identity Management.

#### <a name="azure-resource-roles"></a>Azure-resursroller

När du bestämmer vilka roll tilldelningar som ska hanteras med Privileged Identity Management för Azure-resurser måste du först identifiera de prenumerationer/resurser som är mest viktiga för din organisation. Exempel på sådana prenumerationer/resurser är:

- Resurser som är värdar för de mest känsliga data
- Resurser som grundar sig på kund riktade program är beroende av

Om du är en global administratör som har problem med att avgöra vilka prenumerationer/resurser som är viktigast bör du kontakta Prenumerationens ägare i din organisation för att samla in en lista över resurser som hanteras av varje prenumeration. Du bör sedan arbeta med prenumerations ägarna för att gruppera resurserna baserat på allvarlighets grad, då de komprometteras (låg, medium, hög). Du bör prioritera hantering av resurser med Privileged Identity Management baserat på den här allvarlighets graden.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du arbetar med prenumerationer/resurs ägare av kritiska tjänster för att skapa Privileged Identity Management-arbetsflöde för alla roller i känsliga prenumerationer/resurser.

Privileged Identity Management för Azure-resurser stöder tids gränser för tjänst konton. Du bör behandla tjänst konton exakt på samma sätt som du skulle behandla ett vanligt användar konto.

För prenumerationer/resurser som inte är lika kritiska behöver du inte konfigurera Privileged Identity Management för alla roller. Du bör dock fortfarande skydda rollen ägare och användar åtkomst administratör med Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du hanterar ägar roller och administratörs roller för användar åtkomst för alla prenumerationer/resurser med hjälp av Privileged Identity Management.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Bestäm vilka roll tilldelningar som ska vara permanenta eller berättigade

När du har bestämt listan över roller som ska hanteras av Privileged Identity Management måste du bestämma vilka användare som ska få den kvalificerade rollen och den permanent aktiva rollen. Permanent aktiva roller är de normala roller som tilldelas via Azure Active Directory och Azure-resurser, medan berättigade roller bara kan tilldelas i Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du har noll aktiva tilldelningar för både Azure AD-roller och andra Azure-resurstilldelningar än de rekommenderade två återställnings [konton för brytar glas](../users-groups-roles/directory-emergency-access.md), som ska ha den permanenta globala administratörs rollen.

Även om vi rekommenderar ständig administratör är det ibland svårt för organisationer att uppnå detta direkt. Följande är saker att tänka på när du fattar det här beslutet:

- Frekvens av höjning – om användaren bara behöver den privilegierade tilldelningen en gång får de inte ha den permanenta tilldelningen. Å andra sidan, om användaren behöver rollen för sitt dagliga jobb och använder Privileged Identity Management avsevärt minskar deras produktivitet, kan de övervägas för den permanenta rollen.
- Fall som är specifikt för din organisation – om personen som har fått den berättigade rollen kommer från ett mycket långt team eller en chef med hög rangordning till den punkt som kommunicerar och framtvingar upphöjnings processen är svår, kan de övervägas för den permanenta rollen.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du konfigurerar återkommande åtkomst granskningar för användare med permanenta roll tilldelningar (om du har några). Läs mer om återkommande åtkomst granskning i den sista delen av den här distributions planen

### <a name="draft-your-privileged-identity-management-settings"></a>Utkast till dina Privileged Identity Management inställningar

Innan du implementerar din Privileged Identity Management-lösning är det bra att skissa dina Privileged Identity Management inställningar för varje privilegie rad roll som din organisation använder. Det här avsnittet innehåller några exempel på Privileged Identity Management inställningar för vissa roller (de är endast för referens och kan vara olika för din organisation). Var och en av dessa inställningar beskrivs i detalj med Microsofts rekommendationer efter tabellerna.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Privileged Identity Management inställningar för Azure AD-roller

| Roll | Kräv MFA | Avisering | Incident biljett | Kräv godkännande | God kännare | Varaktighet för aktivering | Permanent administratör |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Global administratör | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andra globala administratörer | 1 timme | Konton för nöd åtkomst |
| Exchange-administratör | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Inget | 2 timme | Inget |
| Support administratör | :x: | :x: | :heavy_check_mark: | :x: | Inget | 8 timmar | Inget |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Privileged Identity Management inställningar för Azure-resurs roller

| Roll | Kräv MFA | Avisering | Kräv godkännande | God kännare | Varaktighet för aktivering | Aktiv administratör | Aktiv utgång | Giltig förfallo datum |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Ägare till kritiska prenumerationer | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andra ägare till prenumerationen | 1 timme | Inget | Ej tillämpligt | 3 månad |
| Administratör för användar åtkomst för mindre kritiska prenumerationer | :heavy_check_mark: | :heavy_check_mark: | :x: | Inget | 1 timme | Inget | Ej tillämpligt | 3 månad |
| Virtuell dator deltagare | :x: | :heavy_check_mark: | :x: | Inget | 3 timmar | Inget | Ej tillämpligt | 6 månad |

I följande tabell beskrivs var och en av inställningarna.

| Inställning | Beskrivning |
| --- | --- |
| Roll | Namnet på den roll som du definierar inställningarna för. |
| Kräv MFA | Om den kvalificerade användaren behöver utföra MFA innan rollen aktive ras.<br/><br/> : heavy_check_mark: **Microsoft rekommenderar** att du tillämpar MFA för alla administratörs roller, särskilt om rollerna har gäst användare. |
| Avisering | Om detta är inställt på Sant får du ett e-postmeddelande när en berättigad användare aktiverar rollen.<br/><br/>**Obs:** Vissa organisationer har ingen e-postadress knutna till sina administratörs konton, för att få dessa e-postaviseringar ska du ange en alternativ e-postadress så att administratörer får e-postmeddelandena. |
| Incident biljett | Om den berättigade användaren behöver registrera ett incident biljett nummer när de aktiverar sin roll. Med den här inställningen kan en organisation identifiera varje aktivering med ett internt incident nummer för att minimera oönskade aktiveringar.<br/><br/> : heavy_check_mark: **Microsoft rekommenderar** att man drar nytta av incident nummer för att koppla Privileged Identity Management till ditt interna system. Detta är särskilt användbart för god kännare som behöver kontext för aktiveringen. |
| Kräv godkännande | Om den kvalificerade användaren behöver få godkännande för att aktivera rollen.<br/><br/> : heavy_check_mark: **Microsoft rekommenderar** att du ställer in godkännande för roller med mest behörighet. Baserat på användnings mönster för alla Privileged Identity Management-kunder, global administratör, användar administratör, Exchange-administratör, säkerhets administratör och lösen ords administratör är de vanligaste rollerna med godkännande inställningar. |
| God kännare | Om godkännande krävs för att aktivera den berättigade rollen, listar du de personer som ska godkänna begäran. Som standard ställer Privileged Identity Management god kännaren till alla användare som är privilegierade roll administratörer oavsett om de är permanenta eller berättigade.<br/><br/>**Obs:** Om en användare både är berättigad till en Azure AD-roll och en god kännare av rollen, kan de inte godkänna sig själva.<br/><br/> : heavy_check_mark: **Microsoft rekommenderar** att du väljer god kännare som är de som är mest kunniga om den aktuella rollen och dess frekventa användare snarare än en global administratör. |
| Varaktighet för aktivering | Hur lång tid en användare aktive ras i rollen innan den upphör att gälla. |
| Permanent administratör | Lista över användare som kommer att vara permanent administratör för rollen (måste aldrig aktivera).<br/><br/> : heavy_check_mark: **Microsoft rekommenderar** att du har ingen ständig administratör för alla roller förutom globala administratörer. Läs mer om dem i som ska vara berättigade och som bör vara aktiva permanent i den här planen. |
| Aktiv administratör | För Azure-resurser är Active Administrator en lista över användare som aldrig kommer att behöva aktivera för att använda rollen. Detta kallas inte permanent administratör som i Azure AD-roller eftersom du kan ange en förfallo tid för när användaren ska förlora den här rollen. |
| Aktiv utgång | En aktiv roll tilldelning för Azure-resurs roller upphör att gälla efter den här konfigurerade tids perioden. Du kan välja mellan 15 dagar, 1 månad, 3 månad, 6 månad, 1 år eller permanent aktiv. |
| Giltig förfallo datum | En berättigad roll tilldelning för Azure-resurs roller upphör att gälla efter den här konfigurerade tids perioden. Du kan välja mellan 15 dagar, 1 månad, 3 månad, 6 månad, 1 år eller permanent berättigad. |

## <a name="implement-your-solution"></a>Implementera din lösning

Grunden för lämplig planering är grunden för att du ska kunna distribuera ett program med Azure Active Directory.  Den ger intelligent säkerhet och integrering som fören klar onboarding samtidigt som du minskar tiden för lyckade distributioner.  Den här kombinationen säkerställer att ditt program är integrerat med lätthet samtidigt som du minimerar tiden för dina slutanvändare.

### <a name="identify-test-users"></a>Identifiera test användare

Använd det här avsnittet för att identifiera en uppsättning användare och eller grupper av användare för att verifiera implementeringen. Baserat på de inställningar som du valde i avsnittet planering, identifierar du de användare som du vill testa för varje roll.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du gör tjänst ägarna till varje Azure AD-roll som test användare så att de kan bekanta sig med processen och bli en intern rådgivare för distributionen.

I den här tabellen identifierar du de test användare som ska kontrol lera att inställningarna för varje roll fungerar.

| Rollnamn | Testa användare |
| --- | --- |
| &lt;roll namn&gt; | &lt;användare för att testa rollen&gt; |
| &lt;roll namn&gt; | &lt;användare för att testa rollen&gt; |

### <a name="test-implementation"></a>Testa implementering

Nu när du har identifierat test användare kan du använda det här steget för att konfigurera Privileged Identity Management för dina test användare. Om din organisation vill införliva Privileged Identity Management-arbetsflöde i ditt egna interna program i stället för att använda Privileged Identity Management i Azure Portal, stöds även alla åtgärder i Privileged Identity Management via vårt [Graph API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Konfigurera Privileged Identity Management för Azure AD-roller

1. [Konfigurera roll inställningarna för Azure AD](pim-how-to-change-default-settings.md) baserat på vad du har planerat.

1. Gå till **Azure AD-roller**, klicka på **roller**och välj sedan den roll som du just har konfigurerat.

1. För gruppen av test användare, om de redan är permanent administratör, kan du göra dem tillgängliga genom att söka efter dem och konvertera dem från permanent till kvalificerad genom att klicka på de tre punkterna på raden. Om de inte har roll tilldelningarna ännu kan du [göra en ny berättigad tilldelning](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Upprepa steg 1-3 för alla roller som du vill testa.

1. När du har konfigurerat test användarna bör du skicka länken för hur du [aktiverar deras Azure AD-roll](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Konfigurera Privileged Identity Management för Azures resurs roller

1. [Konfigurera Azures resurs roll inställningar](pim-resource-roles-configure-role-settings.md) för en roll i en prenumeration eller resurs som du vill testa.

1. Gå till **Azure-resurser** för den prenumerationen och klicka på **roller**och välj den roll som du just har konfigurerat.

1. För gruppen av test användare, om de redan är en aktiv administratör, kan du göra dem tillgängliga genom att söka efter dem och [Uppdatera roll tilldelningen](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Om de inte har rollen än kan du [tilldela en ny roll](pim-resource-roles-assign-roles.md#assign-a-role).

1. Upprepa steg 1-3 för alla roller som du vill testa.

1. När du har konfigurerat test användarna bör du skicka länken för hur du [aktiverar Azures resurs roll](pim-resource-roles-activate-your-roles.md).

Du bör använda det här steget för att kontrol lera om all konfiguration som du har konfigurerat för rollerna fungerar som den ska. Använd följande tabell för att dokumentera dina tester. Du bör också använda det här steget för att optimera kommunikationen med berörda användare.

| Roll | Förväntat beteende under aktiveringen | Faktiska resultat |
| --- | --- | --- |
| Global administratör | (1) Kräv MFA<br/>(2) Kräv godkännande<br/>(3) god kännare får meddelande och kan godkänna<br/>(4) rollen upphör att gälla efter den förinställda tiden |  |
| Prenumerationens ägare *X* | (1) Kräv MFA<br/>(2) den kvalificerade tilldelningen upphör att gälla efter den konfigurerade tids perioden |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Kommunicera Privileged Identity Management till berörda intressenter

Distribution av Privileged Identity Management kommer att införa ytterligare steg för användare av privilegierade roller. Även om Privileged Identity Management avsevärt minskar säkerhets problem som är kopplade till privilegierade identiteter, måste ändringen kommuniceras effektivt före distributionen av hela klienten. Beroende på antalet påverkade administratörer väljer organisationer ofta att skapa ett internt dokument, en video eller ett e-postmeddelande om ändringen. Här ingår ofta bland dessa kommunikationer:

- Vad är PIM
- Vad är fördelarna med organisationen?
- Som kommer att påverkas
- När kommer PIM att distribueras
- Vilka ytterligare steg krävs för att användarna ska kunna aktivera sin roll
    - Du bör skicka länkar till vår dokumentation:
    - [Aktivera Azure AD-roller](pim-how-to-activate-role.md)
    - [Aktivera Azures resurs roller](pim-resource-roles-activate-your-roles.md)
- Kontakt information eller supportavdelningen för eventuella problem som är associerade med PIM

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du ställer in tid hos supportavdelningen/support-teamet för att gå igenom Privileged Identity Management arbets flödet (om din organisation har ett internt IT-support team). Förse dem med lämpliga dokument och din kontakt information.

### <a name="move-to-production"></a>Flytta till produktion

När testningen är slutförd och genomförd flyttar du Privileged Identity Management till produktion genom att upprepa alla steg i test faserna för alla användare av varje roll som du har definierat i Privileged Identity Management konfigurationen. För Privileged Identity Management för Azure AD-roller, testar organisationer ofta Privileged Identity Management för globala administratörer innan de testar och distribuerar Privileged Identity Management för andra roller. Under tiden för Azure-resurser, testar organisationer vanligt vis och distribuerar Privileged Identity Management en Azure-prenumeration i taget.

### <a name="in-the-case-a-rollback-is-needed"></a>Om en återställning behövs

Om Privileged Identity Management inte fungerade som det behövs i produktions miljön, kan följande återställnings steg hjälpa dig att återgå till ett känt fungerande tillstånd innan du konfigurerar Privileged Identity Management:

#### <a name="azure-ad-roles"></a>Azure AD-roller

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Öppna **Azure AD Privileged Identity Management**.
1. Klicka på **Azure AD-roller** och klicka sedan på **roller**.
1. För varje roll som du har konfigurerat klickar du på ellipsen ( **...** ) för alla användare med en berättigad tilldelning.
1. Klicka på alternativet **gör permanent** för att göra roll tilldelningen permanent.

#### <a name="azure-resource-roles"></a>Azure-resursroller

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Öppna **Azure AD Privileged Identity Management**.
1. Klicka på **Azure-resurser** och klicka sedan på en prenumeration eller resurs som du vill återställa.
1. Klicka på **roller**.
1. För varje roll som du har konfigurerat klickar du på ellipsen ( **...** ) för alla användare med en berättigad tilldelning.
1. Klicka på alternativet **gör permanent** för att göra roll tilldelningen permanent.

## <a name="next-steps-after-deploying"></a>Nästa steg efter distribution

Att distribuera Privileged Identity Management i produktion är ett stort steg framåt när det gäller att skydda din organisations privilegierade identiteter. Med distributionen av Privileged Identity Management finns ytterligare Privileged Identity Management funktioner som du bör använda för säkerhet och efterlevnad.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Använd Privileged Identity Management aviseringar för att skydda privilegie rad åtkomst

Du bör använda Privileged Identity Management inbyggda aviserings funktioner för att bättre skydda din klient. Mer information finns i [säkerhets aviseringar](pim-how-to-configure-security-alerts.md#security-alerts). Dessa aviseringar är: administratörer som inte använder privilegierade roller, roller som tilldelas utanför Privileged Identity Management, roller aktive ras för ofta och mer. För att skydda din organisation fullständigt bör du regelbundet gå igenom listan över aviseringar och åtgärda problemen. Du kan visa och åtgärda aviseringar på följande sätt:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Öppna **Azure AD Privileged Identity Management**.
1. Klicka på **Azure AD-roller** och klicka sedan på **aviseringar**.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du hanterar alla aviseringar som är markerade med hög allvarlighets grad omedelbart. För aviseringar med medelhög och låg allvarlighets grad bör du hålla dig informerad och göra ändringar om du anser att det är ett säkerhetshot.

Om någon av de enskilda aviseringarna inte är användbara eller inte gäller för din organisation, kan du alltid stänga aviseringen på sidan aviseringar. Du kan alltid återställa den här avsändningen senare på sidan för Azure AD-inställningar.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Konfigurera återkommande åtkomst granskningar för att regelbundet granska din organisations privilegierade identiteter

Åtkomst granskningar är det bästa sättet att be användare som har tilldelats privilegierade roller eller vissa granskare oavsett om varje användare behöver den privilegierade identiteten. Åtkomst granskningar är bra om du vill minska angrepps ytan och vara kompatibel. Mer information om hur du startar en åtkomst granskning finns i åtkomst granskningar för [Azure AD-roller](pim-how-to-start-security-review.md) och [åtkomst granskningar för Azure-resurs roller](pim-resource-roles-start-access-review.md). För vissa organisationer krävs regelbunden åtkomst granskning för att vara kompatibel med lagar och föreskrifter, medan för andra, åtkomst granskning är det bästa sättet att genomdriva huvud kontot för minst behörighet i hela organisationen.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du konfigurerar kvartals Visa åtkomst granskningar för alla dina Azure AD-och Azure-resurs roller.

I de flesta fall är granskaren för Azure AD-roller användarna själva medan granskaren för Azures resurs roller är ägare till prenumerationen som rollen finns i. Det är dock ofta fallet om företag har privilegierade konton som inte är länkade till någon speciell persons e-postadress. I dessa fall får ingen läsning och granskning av åtkomsten.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du lägger till en sekundär e-postadress för alla konton med privilegierade roll tilldelningar som inte är länkade till en regelbundet kontrollerad e-postadress

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Få ut mesta möjliga av gransknings loggen för att förbättra säkerheten och efterlevnaden

Gransknings loggen är den plats där du kan hålla dig uppdaterad och vara kompatibel med reglerna. Privileged Identity Management lagrar för närvarande en 30-dagars historik över alla organisationens historik i sin Gransknings logg, inklusive:

- Aktivering/inaktive ring av berättigade roller
- Roll tilldelnings aktiviteter i och utanför Privileged Identity Management
- Ändringar i roll inställningar
- Begär/Godkänn/neka aktiviteter för roll aktivering med godkännande inställningar
- Uppdatera till aviseringar

Du kan komma åt dessa gransknings loggar om du är global administratör eller administratör för privilegierade roller. Mer information finns i [gransknings historik för Azure AD-roller](pim-how-to-use-audit-log.md) och [gransknings historik för Azures resurs roller](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du har minst en administratör läst igenom alla gransknings händelser varje vecka och exportera gransknings händelserna varje månad.

Om du automatiskt vill lagra gransknings händelser under en längre tids period, synkroniseras Privileged Identity Managementens Gransknings logg automatiskt i [Azure AD audit-loggarna](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du konfigurerar [Azure logg övervakning](../reports-monitoring/concept-activity-logs-azure-monitor.md) för att arkivera gransknings händelser i ett Azure Storage-konto för behovet av säkerhet och efterlevnad.
