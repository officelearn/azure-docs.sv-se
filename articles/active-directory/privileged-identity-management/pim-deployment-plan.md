---
title: Distribuera Azure AD Privileged Identity Management (PIM) | Microsoft Docs
description: Beskriver hur du planerar distributionen av Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: c1ecca0bf8021fb1b97628a73eddbe7968a02548
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985157"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Distribuera Azure AD Privileged Identity Management (PIM)

Den här stegvisa guiden beskriver hur du planerar distributionen av Azure AD Privileged Identity Management (PIM) i din organisation.

> [!TIP]
> I det här dokumentet visas objekt som markerats som:
>
> :heavy_check_mark: **Microsoft rekommenderar att du**
>
> Dessa är allmänna rekommendationer och implementera endast om de gäller för specifika företagets behov.

## <a name="step-1-learn-about-pim"></a>Steg 1. Lär dig mer om PIM

Azure AD Privileged Identity Management (PIM) hjälper dig att hantera Privilegierade administrativa roller i Azure AD Azure-resurser och andra Microsoft Online Services. I en värld där Privilegierade identiteter är tilldelade och glömt tillhandahåller PIM lösningar som just-in-time-åtkomst, begäran godkännandearbetsflöden och helt integrerad åtkomst granskningar så att du kan identifiera avslöja och förhindra att skadliga aktiviteter av privileged roller i realtid. Distribuera PIM för att hantera dina Privilegierade roller i hela organisationen minskar avsevärt risken vid Visa värdefulla insikter om aktiviteterna som din Privilegierade roller.

### <a name="business-value-of-pim"></a>Med PIM

**Hantera risker** – skydda din organisation genom att göra principen om [minsta privilegium åtkomst](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) och just-in-time-åtkomst. Du kan avsevärt minska säkerhetsrisker som rör privilegierad åtkomst i din organisation genom att minimera antalet permanent tilldelningar av användare Privilegierade roller och framtvinga godkännanden och MFA om utökade privilegier. Tillämpa minsta behörighet och just-in-time-åtkomst också kan du visa en historik över åtkomst till Privilegierade roller och spåra säkerhetsproblem när de visar.

**-Kompatibilitet och styrning** – distribuera PIM skapar en miljö för pågående identitetsstyrning. Just-in-time-höjning av Privilegierade identiteter gör det möjligt för PIM att hålla reda på privilegierad åtkomst aktiviteter i din organisation. Du kommer även att kunna visa och ta emot meddelanden för alla tilldelningar för permanent och dessa roller i din organisation. Via åtkomstgranskning, kan du regelbundet granska och ta bort onödiga Privilegierade identiteter och kontrollera att din organisation är kompatibla med stränga standarder för identitet, åtkomst och säkerhet.

**Minska kostnaderna** – minska kostnaderna genom att eliminera ineffektivitet, mänskliga fel och säkerhetsproblem genom att distribuera PIM korrekt. Resultatet är en minskning av cyberhot crimes som är associerade med Privilegierade identiteter som är dyrt och svårt att återställa från. PIM även hjälpa din organisation att minska kostnaden för granskning komma åt information när det gäller att följa regler och förordningar.

Mer information finns i [vad är Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Licenskrav

Om du vill använda PIM måste din katalog ha ett av följande avgiftsbelagda eller utvärderingslicenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Mer information finns i [licenskraven för att använda PIM](subscription-requirements.md).

### <a name="key-pim-terminology"></a>Nyckeln PIM-terminologi

| Term eller begrepp | Beskrivning |
| --- | --- |
| berättigad | En rolltilldelning som kräver att en användare utför en eller flera åtgärder för att använda rollen. Om en användare har gjorts berättigad för en roll innebär det att användaren kan aktivera rollen när det finns behov av att utföra privilegierade åtgärder. Det finns ingen skillnad i den åtkomst som ges till någon med en permanent kontra berättigad rolltilldelning. Den enda skillnaden är att vissa användare inte behöver den åtkomsten hela tiden. |
| aktivera | Processen för att utföra en eller flera åtgärder för att använda en roll som användaren är berättigad för. Det kan vara åtgärder som att utföra en kontroll av multifaktorautentisering (MFA), ange en affärsmotivering eller begära godkännande från utnämnda godkännare. |
| just-in-time (JIT) åtkomst | En modell som där användarna får tillfällig behörighet att utföra Privilegierade uppgifter, vilket hindrar skadliga eller obehöriga användare från att få åtkomst när behörigheterna som har gått ut. Åtkomst beviljas endast när användare behöver den. |
| principen om lägsta behörighet åtkomst | En rekommenderad säkerhetsrutin där varje användare har angetts med endast de minsta privilegier som krävs för att utföra uppgifter som de har behörighet att utföra. Den här metoden minimerar antalet globala administratörer och i stället använder specifika administratörsroller för vissa scenarier. |

Mer information finns i [terminologi](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-pim-works"></a>Översikt över hur PIM fungerar

1. PIM har ställts in så att användare är berättigade till Privilegierade roller.
1. När en behörig användare behöver använda sina Privilegierade roller, aktiverar de rollen i PIM.
1. Beroende på vilka PIM-inställningar som konfigurerats för rollen, måste användaren utföra vissa åtgärder (till exempel utföra multifaktorautentisering, få godkännande eller att ange en anledning).
1. När användaren aktiverar har rollen, får de rollen för en förinställd tidsperiod.
1. Administratörer kan visa en historik över alla PIM-aktiviteter i granskningsloggen. De kan också ytterligare skydda sina klienter och uppfylla efterlevnadskrav med hjälp av PIM-funktioner som åtkomstgranskningar och aviseringar.

Mer information finns i [vad är Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-pim"></a>Roller som kan hanteras av PIM

**Azure AD-roller** – dessa roller är alla directory-roller i Azure Active Directory (till exempel Global administratör, Exchange-administratörer och säkerhetsadministratörer). Du kan läsa mer om rollerna och deras funktioner i [behörigheter för administratör i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Om du vill ha hjälp med att bestämma vilka roller till dina administratörer finns i [minst Privilegierade roller av aktivitet](../users-groups-roles/roles-delegate-by-task.md).

**Azure-resursroller** – dessa roller är länkade till en Azure-resurs, resursgrupp, prenumeration eller hanteringsgrupp. PIM ger just-in-time-åtkomst till både inbyggda roller som ägare, administratör för användaråtkomst och deltagare, samt [anpassade roller](../../role-based-access-control/custom-roles.md). Läs mer om Azure-resursroller [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md).

Mer information finns i [roller som du inte kan hantera i PIM](pim-roles.md).

## <a name="step-2-plan-your-deployment"></a>Steg 2. Planera distributionen

Det här avsnittet fokuserar på vad du behöver göra innan du distribuerar PIM i din organisation. Det är viktigt att följa anvisningarna och förstå begreppen i det här avsnittet eftersom de hjälper dig att skapa den bästa planen som skräddarsytts för din organisations Privilegierade identiteter.

### <a name="identify-your-stakeholders"></a>Identifiera dina intressenter

Följande avsnitt hjälper dig att identifiera alla intressenter som ingår i projektet och måste du logga ut, granska och håll dig informerad. Den innehåller separata tabeller för att distribuera PIM för Azure AD-roller och PIM för Azure-resursroller. Lägg till intressenter i tabellen nedan som passar din organisation.

- SÅ = logga ut på det här projektet
- R = granska det här projektet och ange indata
- Jag = information till det här projektet

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>Intressenter: PIM för Azure AD-roller

| Namn | Roll | Åtgärd |
| --- | --- | --- |
| Namn och e-post | **Identitet architect eller Global administratör för Azure**<br/>En representant från identity management-teamet ansvarar för definierar hur den här ändringen justeras med grundläggande identity management-infrastruktur i din organisation. | SO/R/I |
| Namn och e-post | **Service ägare / rad manager**<br/>En representant från IT-ägare av en tjänst eller en grupp med tjänster. De är nyckeln i att fatta beslut och hjälper till att lansera PIM för sina team. | SO/R/I |
| Namn och e-post | **Security ägare**<br/>En representant från säkerhetsteamet som kan logga ut att planen uppfyller säkerhetskraven för din organisation. | SO/R |
| Namn och e-post | **IT manager support / supportavdelningen**<br/>En representant från organisationen för IT-support som kan ge information om support för den här ändringen från ett perspektiv för supportavdelningen. | R/I |
| Namn och e-post för pilotanvändare | **Användare i Privilegierade roller**<br/>Gruppen med användare som privileged identity management tillämpas. De behöver du veta hur du aktiverar sina roller när PIM implementeras. | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>Intressenter: PIM för Azure-resursroller

| Namn | Roll | Åtgärd |
| --- | --- | --- |
| Namn och e-post | **Prenumeration / resursägaren**<br/>En representant från IT-ägare till varje prenumeration eller resursgrupp som du vill distribuera PIM för | SO/R/I |
| Namn och e-post | **Security ägare**<br/>En representant från säkerhetsteamet som kan logga ut att planen uppfyller säkerhetskraven för din organisation. | SO/R |
| Namn och e-post | **IT manager support / supportavdelningen**<br/>En representant från organisationen för IT-support som kan ge information om support för den här ändringen från ett perspektiv för supportavdelningen. | R/I |
| Namn och e-post för pilotanvändare | **RBAC-Rollanvändare**<br/>Gruppen med användare som privileged identity management tillämpas. De behöver du veta hur du aktiverar sina roller när PIM implementeras. | I |

### <a name="enable-pim"></a>Aktivera PIM

Som en del av planeringsprocessen, måste du först godkänna och aktivera PIM genom att följa våra [börja använda PIM dokumentet](pim-getting-started.md). Aktivera PIM ger dig åtkomst till vissa funktioner som är speciellt utformad för att underlätta distributionen.

Om ditt mål är att distribuera PIM för Azure-resurser, bör du följa våra [identifiera Azure-resurser du hanterar i PIM-dokumentet](pim-resource-roles-discover-resources.md). Endast ägare av varje resurs, resursgrupp och prenumeration kommer att kunna upptäcka dem i PIM. Om du är en Global administratör försöker distribuera PIM för Azure-resurser, kan du [utöka behörighet för att hantera alla Azure-prenumerationer ](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) att ge dig åtkomst till alla Azure-resurser i katalogen för identifiering. Vi rekommenderar dock att du får godkännande från var och en av din prenumerationsägare innan du hanterar sina resurser med PIM.

### <a name="enforce-principle-of-least-privilege"></a>Tillämpa principen om lägsta behörighet

Det är viktigt att se till att du har tillämpas principen om lägsta behörighet i din organisation för både din Azure AD och Azure-resursroller.

#### <a name="azure-ad-roles"></a>Azure AD-roller

Det är vanligt att organisationer kan tilldela rollen som Global administratör för en stor del av administratörer när de flesta administratörer behöver bara en eller två specifika administratörsroller för Azure AD-roller. Privilegierad roll tilldelningar tenderar att lämnas ohanterade.

> [!NOTE]
> Vanliga fallgropar i roller:
>
> - Administratören Exchange ges rollen Global administratör, även om de bara behöver rollen Exchange-administratören för att utföra sitt dagliga arbete.
> - Rollen som Global administratör tilldelas till en administratör eftersom denna administratör måste både säkerhets- och SharePoint-administratörsroller och det är lättare att delegera bara en roll.
> - Administratören har tilldelats rollen säkerhetsadministratör för att utföra en uppgift länge sedan, men aldrig har tagits bort.

Följ dessa steg för att tillämpa principen om lägsta behörighet för din Azure AD-roller.

1. Förstå Granulariteten för rollerna genom att läsa och förstå den [tillgängliga Azure AD-administratörsroller](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Du och ditt team bör också referera till [administratörsroller för identiteten aktiviteten i Azure AD](../users-groups-roles/roles-delegate-by-task.md), som förklarar den minst Privilegierade rollen för specifika uppgifter.

1. Lista som har privilegierad roller i din organisation. Du kan använda den [PIM guiden](pim-security-wizard.md#run-the-wizard) att komma till en sida som liknar följande.

    ![Identifiera privilegierade roller](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. Ta reda på varför de måste rollen för alla globala administratörer i din organisation. Baserat på läser den föregående dokumentationen om personens jobbet kan utföras av en eller flera detaljerade administratörsroller, bör du ta bort dem från den globala administratörsrollen och Ställ därefter tilldelningar i Azure Active Directory (som referens: Microsoft har för närvarande bara cirka 10 administratörer med rollen Global administratör. Läs mer på [hur Microsoft använder PIM](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. Granska listan över tilldelningar för alla Azure AD-roller, identifiera administratörer som inte längre behöver rollen och ta bort dem från deras tilldelningar.

Du kan använda funktionen åtkomst granska i PIM för att automatisera steg 3 och 4. Följa stegen i [starta en åtkomstgranskning för Azure AD-roller i PIM](pim-how-to-start-security-review.md), du kan ställa in en åtkomstgranskning för varje Azure AD-roller som har en eller flera medlemmar.

![Skapa en åtkomstgranskning](./media/pim-deployment-plan/create-access-review.png)

Du bör ange granskarna **medlemmar (själv)**. Detta skickar ett e-postmeddelande till alla medlemmar i rollen att få dem att bekräfta om de behöver åtkomst. Du bör också aktivera **Kräv orsak vid godkännande** i de avancerade inställningarna så att användarna kan ange varför de måste rollen. Baserat på den här informationen kan kommer du att kunna ta bort användare från onödiga roller och delegera mer detaljerade administratörsroller när det gäller globala administratörer.

Åtkomstgranskningar är beroende av e-postmeddelanden att meddela dem att granska åtkomsten till rollerna. Om du har Privilegierade konton som inte har e-postmeddelanden länkad, måste du fylla i fältet sekundära e-postadress för dessa konton. Mer information finns i [proxyAddresses attribut i Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Azure-resursroller

För Azure-prenumerationer och resurser kan ställa du in en liknande granskningsprocess för åtkomst och granska rollerna i varje prenumeration eller resursgrupp. Målet med den här processen är att minimera ägare och administratör för användaråtkomst tilldelningar som är kopplade till varje prenumeration eller resursgrupp samt för att ta bort onödiga tilldelningar. Däremot delegera organisationer ofta sådana uppgifter till ägaren av varje prenumeration eller resursgrupp eftersom de har en bättre förståelse för specifika roller (särskilt anpassade roller).

Om du är en IT-administratör med rollen Global administratör försöker distribuera PIM för Azure-resurser i din organisation, kan du [utöka behörighet för att hantera alla Azure-prenumerationer ](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) att få åtkomst till varje prenumeration. Du kan hitta varje Prenumerationens ägare och arbeta med dem för att ta bort onödiga tilldelningar och minimera ägarrollstilldelning.

Användare med rollen ägare för en Azure-prenumeration kan också använda [åtkomstgranskningar för Azure-resurser](pim-resource-roles-start-access-review.md) att granska och ta bort onödiga rolltilldelningar ungefär på samma sätt som beskrivs ovan för Azure AD-roller.

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>Bestäm vilka rolltilldelningar bör skyddas av PIM

När du rensar Priviligierade rolltilldelningar i din organisation, måste du bestämma vilka roller som ska skydda med PIM.

Om en roll skyddas av PIM, måste berättigade användare som tilldelats höjer du använder de behörigheter som beviljats av rollen. Processen med utökade privilegier också innefatta fått godkännande, utföra multifaktorautentisering och/eller att ange en orsak varför de aktiverar. PIM kan också spåra att rikta via meddelanden och Granska händelseloggarna PIM och Azure AD.

Välja vilka roller som ska skydda med PIM kan vara svårt och är olika för varje organisation. Det här avsnittet innehåller våra riktlinjer för Azure AD och Azure-resursroller.

#### <a name="azure-ad-roles"></a>Azure AD-roller

Det är viktigt att prioritera skydda Azure AD-roller som har flest antal behörigheter. Baserat på användningsmönster bland alla PIM-kunder kan är de översta 10 Azure AD-roller som hanteras av PIM:

1. Global administratör
1. Säkerhetsadministratör
1. Användarkonto-administratör
1. Exchange-administratör
1. SharePoint Administrator
1. Administratör för Intune-tjänsten
1. Säkerhetsläsare
1. Tjänstadministratör
1. Faktureringsadministratör
1. Skype för Business Administrator

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du hantera alla globala administratörer och säkerhetsadministratörer med PIM som ett första steg som de är de som kan göra de skada när äventyras.

Det är viktigt att tänka på vilka data och behörigheten är mest känsliga för din organisation. Exempelvis kanske vissa organisationer vill skydda sina Power BI-administratörsrollen eller sina team administratörsroll med PIM som de har möjlighet att komma åt data och/eller ändra core arbetsflöden.

Om det finns några roller med gästanvändare som tilldelats, är de särskilt sårbara för angrepp.

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du hanterar alla roller med gästanvändare med PIM för att minska riskerna med komprometterade gäst användarkonton.

Läsare roller som Directory läsare, Message Center läsare och Säkerhetsläsare antas ibland vara mindre viktiga jämfört med andra roller som de inte har behörighet att skriva. Men har vi sett vissa kunder även skydda dessa roller eftersom angripare som har fått åtkomst till dessa konton kan att kunna läsa känsliga data, till exempel personligt identifierbar information (PII). Du bör ha i åtanke när du bestämmer om läsare roller i din organisation behöver hanteras med PIM.

#### <a name="azure-resource-roles"></a>Azure-resursroller

När du bestämmer vilka rolltilldelningar som ska hanteras med hjälp av PIM för Azure-resurser, måste du först identifiera prenumerationer/resurser som är mest viktigt för din organisation. Exempel på sådana prenumerationer/resurser är:

- Resurser som är värdar för de mest känsliga data
- Resurser som core, kundinriktad program beroende

Om du är Global administratör har problem med att bestämma vilka prenumerationer/resurser som är viktigast, bör du kontakta prenumerationsägarna i din organisation att samla in en lista över resurser som hanteras av varje prenumeration. Du bör sedan arbeta med prenumerationsägare att gruppera resurser baserat på allvarlighetsgrad om de drabbade (låg, medelhög och hög). Du ska gälla för att hantera resurser med PIM baserat på den här allvarlighetsgrad.

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du arbetar med prenumeration/resursägare av kritiska tjänster för att konfigurera PIM-arbetsflöde för alla roller i känsliga prenumerationer/resurser.

PIM för Azure-resurser har stöd för tidsbegränsade service-konton. Du bör hantera tjänstkonton likadant som hur du skulle behandla ett vanligt användarkonto.

För prenumerationer/resurser som inte är lika kritiska, behöver du inte konfigurera PIM för alla roller. Du bör dock fortfarande skydda rollerna ägare och administratör för användaråtkomst med PIM.

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du hantera roller för ägare och administratör för användaråtkomst rollerna för alla prenumerationer/resurser med hjälp av PIM.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Bestäm vilka rolltilldelningar ska vara permanent eller berättigade

När du har valt i listan över roller som ska hanteras av PIM, måste du bestämma vilka användare ska få berättigad roll jämfört med rollen permanent aktiv. Permanent aktiv roller är de normala roller som tilldelats via Azure Active Directory och Azure-resurser medan berättigade roller kan endast tilldelas i PIM.

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du har noll permanent aktiv tilldelningar för både Azure AD-roller och Azure-resursroller än den rekommenderade [två konton för åtkomst vid akutfall av glas](../users-groups-roles/directory-emergency-access.md), som ska ha den permanent rollen som Global administratör.

Även om vi rekommenderar noll stående administratör är ibland det svårt för organisationer att uppnå detta direkt. Här följer saker att tänka på när du gör detta beslut:

- Frekvensen för höjning – om användaren bara behöver privilegierad tilldelningen en gång, de får inte ha permanent tilldelning. Å andra sidan, om användaren måste ha rollen för sitt dagliga arbete med hjälp av PIM skulle avsevärt minska sin produktivitet och kan de ses för permanent roll.
- Fall som är specifika för din organisation – om den person som är berättigade rollen är från ett mycket långt team eller en återanskaffningsförslag chef till den punkt som kommunikation och genomdriva processen med utökade privilegier är det svårt, de kan ses för permanent roll.

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** dig ställa in återkommande åtkomst går igenom för användare med permanenta rolltilldelningar (om du har någon). Mer information om återkommande åtkomstgranskning i den sista delen av den här distributionsplan

### <a name="draft-your-pim-settings"></a>Utkast för PIM-inställningar

Innan du implementerar din PIM-lösning är det bra att utkast PIM inställningarna för varje privilegierad roll som organisationen använder. Det här avsnittet innehåller några exempel på PIM-inställningar för specifika roller (de är bara som referens och kan vara olika för din organisation). Var och en av de här inställningarna förklaras i detalj med Microsofts rekommendationer efter tabellerna.

#### <a name="pim-settings-for-azure-ad-roles"></a>PIM-inställningar för Azure AD-roller

| Roll | Kräv MFA | Avisering | Biljett för incident | Godkännande krävs | Godkännare | Varaktighet för rollaktivering | Permanent administratör |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Global administratör | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andra globala administratörer | 1 timme | För åtkomst vid akutfall |
| Exchange-administratör | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | Ingen | 2 timmar | Ingen |
| Supportavdelningsadministratör | :x: | :x: | :heavy_check_mark: | :x: | Ingen | 8 timmar | Ingen |

#### <a name="pim-settings-for-azure-resource-roles"></a>PIM-inställningar för Azure-resursroller

| Roll | Kräv MFA | Avisering | Godkännande krävs | Godkännare | Varaktighet för rollaktivering | Active-administratör | Aktiva upphör att gälla | Berättigade upphör att gälla |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Ägare av kritiska prenumerationer | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andra ägare till prenumerationen | 1 timme | Ingen | Saknas | 3-månaders |
| Administratör för användaråtkomst mindre kritiska prenumerationer | :heavy_check_mark: | :heavy_check_mark: | :x: | Ingen | 1 timme | Ingen | Saknas | 3-månaders |
| Virtuell datordeltagare | :x: | :heavy_check_mark: | :x: | Ingen | 3 timme | Ingen | Saknas | 6 månader |

I följande tabell beskrivs var och en av inställningarna.

| Inställning | Beskrivning |
| --- | --- |
| Roll | Namnet på den roll som du definierar inställningarna för. |
| Kräv MFA | Om behörig användare behöver utföra MFA innan du aktiverar rollen.<br/><br/>:heavy_check_mark: **Microsoft rekommenderar att du** du införa MFA för alla administratörsroller, särskilt om rollerna har gästanvändare. |
| Avisering | Om sant, Global administratör, privilegierad Rolladministratör och säkerhetsadministratörer i organisationen får ett e-postmeddelande när en behörig användare aktiverar rollen.<br/><br/>**Obs!** Vissa organisationer har inte en e-postadress som är kopplad till deras administratörskonton, att få dessa e-postmeddelanden, bör du gå anger en alternativ e-postadress så administratörer får dessa e-postmeddelanden. |
| Biljett för incident | Om behörig användare behöver registrera en incident Biljettnummer när du aktiverar deras roll. Den här inställningen hjälper en organisation att identifiera varje aktivering med en intern incident för att undvika oönskad aktiveringar.<br/><br/>:heavy_check_mark: **Microsoft rekommenderar att du** att utnyttja incident Biljettnummer att knyta PIM med ditt interna system. Detta är särskilt användbart för godkännare som behöver kontext för aktiveringen. |
| Godkännande krävs | Om behörig användare behöver få godkännande för att aktivera rollen.<br/><br/>:heavy_check_mark: **Microsoft rekommenderar att du** du ställer in godkännande för roller med mest behörighet. Baserat på användningsmönster för alla PIM-kunder, är Global administratör, Användaradministratör, Exchange-administratör, säkerhetsadministratör och Lösenordsadministratör de vanligaste rollerna med för godkännande. |
| Godkännare | Om godkännande krävs för att aktivera rollen berättigade lista ut de personer som ska godkänna begäran. Som standard anger PIM godkännaren ska vara alla användare som är en privilegierad rolladministratör oavsett om de är permanenta eller berättigade.<br/><br/>**Obs!** Om en användare finns både som är berättigade till en Azure AD-rollen och godkännare för rollen, kan de inte godkänna själva.<br/><br/>:heavy_check_mark: **Microsoft rekommenderar att du** som du väljer godkännare vara personer som är mest kunskap om den specifika-rollen och dess frekventa användare i stället för en Global administratör. |
| Varaktighet för rollaktivering | Hur lång tid som en användare kommer att aktiveras i rollen innan den upphör. |
| Permanent administratör | Lista över användare som kommer att vara en permanent administratör för rollen (aldrig måste aktivera).<br/><br/>:heavy_check_mark: **Microsoft rekommenderar att du** du har noll stående administratör för alla roller utom globala administratörer. Läs mer om det i den som ska göras berättigade och vilka som ska vara permanent aktiv avsnitt i den här planen. |
| Active-administratör | För Azure-resurser är active administratör listan över användare som aldrig måste du aktivera om du vill använda rollen. Detta är inte kallas permanent administratör som i Azure AD-roller eftersom du kan ställa in en förfallotid för när användaren förlorar den här rollen. |
| Aktiva upphör att gälla | En aktiv rolltilldelning för Azure-resursroller upphör att gälla efter detta konfigurerat tidsperiod. Du kan välja mellan 15 dagar, 1 månad, 3-månaders, 6 månader, 1 år eller permanent aktiv. |
| Berättigade upphör att gälla | En berättigad rolltilldelning för Azure-resursroller upphör att gälla efter detta konfigurerat tidsperiod. Du kan välja mellan 15 dagar, 1 månad, 3-månaders, 6 månader, 1 år eller permanent berättigad. |

## <a name="step-3-implement-your-solution"></a>Steg 3. Implementera din lösning

Grunden för noggrann planering är grunden som du kan distribuera ett program har med Azure Active Directory.  Det ger intelligent säkerhets- och integrering som förenklar onboarding samtidigt som det minskar tiden för lyckade distributioner.  Den här kombinationen garanterar att ditt program är integrerat med enkel samtidigt som man minimerar driftstopp för dina slutanvändare.

### <a name="identify-test-users"></a>Identifiera testanvändare

Använd det här avsnittet för att identifiera en uppsättning användare och eller grupper av användare att verifiera implementeringen. Baserat på de inställningar som du har valt i planeringsavsnittet kan identifiera de användare som du vill testa för varje roll.

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du gör tjänsten ägare till varje Azure AD-rollen ska testa användare så att de kan bekanta dig med processen och bli en intern advocator för distributionen.

Identifiera de testanvändare som kontrollerar att inställningarna för varje roll fungerar i den här tabellen.

| Rollnamn | Testa användare |
| --- | --- |
| &lt;Rollnamn&gt; | &lt;Användare för att testa rollen&gt; |
| &lt;Rollnamn&gt; | &lt;Användare för att testa rollen&gt; |

### <a name="test-implementation"></a>Test-implementering

Nu när du har identifierat testanvändarna kan du använda det här steget för att konfigurera PIM för din testanvändare. Om din organisation vill ta med PIM-arbetsflöde i din egen interna program istället för att använda PIMS användargränssnittet för Azure-portalen, alla åtgärder i PIM finns också tillgänglig via våra [graph API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-pim-for-azure-ad-roles"></a>Konfigurera PIM för Azure AD-roller

1. [Konfigurera rollinställningar för Azure AD directory](pim-how-to-change-default-settings.md) baserat på vad du planerat.

1. Gå till **Azure AD-roller**, klickar du på **roller**, och väljer sedan den roll som du precis har konfigurerat.

1. För gruppen med testanvändare, om de redan är en permanent administratör du dem berättigade genom att söka efter dem och konvertera dem från permanent till berättigade genom att klicka på de tre punkterna på sina rad. Om de inte har rolltilldelningar än kan du [gör en ny berättigad uppgift](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Upprepa steg 1 – 3 för alla roller som du vill testa.

1. När du har konfigurerat testanvändarna du ska skicka länken för att [aktivera sina Azure AD-rollen](pim-how-to-activate-role.md).

#### <a name="configure-pim-for-azure-resource-roles"></a>Konfigurera PIM för Azure-resursroller

1. [Konfigurera Azure-resurs rollinställningar](pim-resource-roles-configure-role-settings.md) för en roll i en prenumeration eller resursgrupp som du vill testa.

1. Gå till **Azure-resurser** för den prenumeration och klicka på **roller**, Välj den roll som du precis har konfigurerat.

1. För gruppen med testanvändare, om de redan en aktiv administratör, du kan göra dem berättigade genom att söka efter dem och [uppdatera sina rolltilldelning](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Om de inte har rollen än kan du [tilldela en ny roll](pim-resource-roles-assign-roles.md#assign-a-role).

1. Upprepa steg 1 – 3 för alla roller som du vill testa.

1. När du har konfigurerat testanvändarna du ska skicka länken för att [aktivera sin roll i Azure-resurs](pim-resource-roles-activate-your-roles.md).

Du bör använda det här steget för att verifiera om all konfiguration du ställs in för rollerna fungerar korrekt. Använd följande tabell för att dokumentera dina tester. Du bör också använda det här steget för att optimera kommunikationen med användare som påverkas.

| Roll | Förväntat beteende under aktiveringen | De faktiska resultaten |
| --- | --- | --- |
| Global administratör | (1) Kräv MFA<br/>(2) Kräv godkännande<br/>(3) godkännare tar emot ett meddelande och kan godkänna<br/>(4) rollen upphör att gälla efter en förinställd tid |  |
| Ägare av prenumerationen *X* | (1) Kräv MFA<br/>(2) berättigad uppgift upphör att gälla efter konfigurerad tidsperiod |  |

### <a name="communicate-pim-to-affected-stakeholders"></a>Kommunicera PIM till berörda intressenter

Distribuera PIM kommer att införa ytterligare åtgärder för användare av Privilegierade roller. Även om PIM minskar säkerhetsproblem som är associerade med Privilegierade identiteter, måste ändringen förmedlas effektivt före distributionen för klienten som helhet. Beroende på antalet påverkade administratörer kan välja organisationer ofta att skapa ett internt dokument, en video eller ett e-postmeddelande om ändringen. Ofta ingår i dessa meddelanden inkluderar:

- Vad är PIM
- Vad är fördelen för organisationen
- Vem påverkas
- När PIM distribueras
- Vilka ytterligare steg måste utföras för användare att aktivera sin roll
    - Du bör skicka länkar till vår dokumentation:
    - [Aktivera Azure AD-roller](pim-how-to-activate-role.md)
    - [Aktivera Azure-resursroller](pim-resource-roles-activate-your-roles.md)
- Kontaktinformation eller helpdesk-länk om eventuella problem som är associerade med PIM

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du ställer in tid med din supportavdelning/support-teamet att igenom PIM-arbetsflödet (om din organisation har en intern IT-support team). Ange dem med lämplig dokumentation samt din kontaktinformation.

### <a name="move-to-production"></a>Flytta till produktion

När testet är klar och lyckas, flytta PIM till produktion genom att upprepa alla steg i testfaserna för alla användare av varje roll som du har definierat i PIM-konfigurationen. För PIM för Azure AD-roller organisationer ofta testa och distribuera PIM för globala administratörer innan testning och distribution av PIM för andra roller. Under tiden för Azure-resursen, organisationer normalt testa och distribuera PIM en Azure-prenumeration i taget.

### <a name="in-the-case-a-rollback-is-needed"></a>Om krävs en återställning

Om PIM inte kunde fungerar korrekt i produktionsmiljön, kan följande steg för återställning hjälpa dig att återgå till ett fungerande tillstånd innan du konfigurerar PIM:

#### <a name="azure-ad-roles"></a>Azure AD-roller

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Öppna **Azure AD Privileged Identity Management**.
1. Klicka på **Azure AD-roller** och klicka sedan på **roller**.
1. För varje roll som du har konfigurerat, klicka på ellipsen (**...** ) för alla användare med en berättigad uppgift.
1. Klicka på den **göra permanenta** alternativ för att göra rolltilldelningen permanent.

#### <a name="azure-resource-roles"></a>Azure-resursroller

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Öppna **Azure AD Privileged Identity Management**.
1. Klicka på **Azure-resurser** och klicka sedan på en prenumeration eller resursgrupp som du vill återställa.
1. Klicka på **roller**.
1. För varje roll som du har konfigurerat, klicka på ellipsen (**...** ) för alla användare med en berättigad uppgift.
1. Klicka på den **göra permanenta** alternativ för att göra rolltilldelningen permanent.

## <a name="step-4-next-steps-after-deploying-pim"></a>Steg 4. Nästa steg när du har distribuerat PIM

Distribution har PIM produktionsmiljön är ett viktigt steg framåt när det gäller skydda din organisation är Privilegierade identiteter. Med distributionen av PIM följer ytterligare PIM-funktioner som du ska använda för säkerhet och efterlevnad.

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>Använd PIM aviseringar för att skydda din Privilegierade åtkomst

Du bör använda PIMS inbyggda aviseringar funktioner för att bättre skydda din klient. Mer information finns i [säkerhetsaviseringar](pim-how-to-configure-security-alerts.md#security-alerts). Dessa aviseringar innehåller: administratörer inte använder Privilegierade roller, roller tilldelas utanför PIM, roller som aktiveras för ofta och mycket mer. För att helt skydda din organisation, bör du regelbundet gå igenom listan över aviseringar och åtgärda problemen. Du kan visa och åtgärda aviseringar på följande sätt:

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Öppna **Azure AD Privileged Identity Management**.
1. Klicka på **Azure AD-roller** och klicka sedan på **aviseringar**.

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du hantera alla aviseringar som markerats med hög allvarlighetsgrad omedelbart. Medel och låg allvarlighetsgrad aviseringar, bör du hålla dig informerad och gör ändringar om du tror att det finns ett säkerhetshot.

Om några av de specifika varningar inte användbar eller inte gäller för din organisation, kan du alltid stänga av aviseringen på aviseringssidan. Du kan alltid återgå till den här avsked senare i inställningssidan för Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Ställa in återkommande åtkomstgranskningar för att regelbundet granska din organisations Privilegierade identiteter

Åtkomstgranskningar är det bästa sättet att be användare som har tilldelats med Privilegierade roller eller specifika granskare om varje användare privilegierad identitet. Åtkomstgranskningar är bra om du vill minska risken för angrepp och fortsätter att kompatibla. Läs mer om hur du startar en åtkomstgranskning [åtkomstgranskningar för Azure AD-roller](pim-how-to-start-security-review.md) och [åtkomstgranskningar för Azure-resursroller](pim-resource-roles-start-access-review.md). För vissa organisationer utför regelbundna åtkomstgranskning krävs för att efterlever lagar och föreskrifter samtidigt som för andra, åtkomstgranskning är det bästa sättet att framtvinga huvudnamn om lägsta behörighet i hela organisationen.

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du ställer in kvartalsvis åtkomstgranskningar för alla Azure AD och Azure-resursroller.

I de flesta fall är granskare för Azure AD-roller användarna själva medan granskaren för Azure-resursroller är ägare av prenumerationen som rollen. Det är dock ofta är fallet där företag har Privilegierade konton som inte har länkats med en viss person e-postadress. I sådana fall kan ingen läser och granskar åtkomst.

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du lägger till en sekundär e-postadress för alla konton med Privilegierade rolltilldelningar som inte har länkats till en regelbundet kontrollerade e-postadress

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Få ut det mesta av din granskningslogg för att förbättra säkerhet och efterlevnad

Granskningsloggen är den plats där du kan hålla dig uppdaterad och vara kompatibla med föreskrifter. PIM lagrar för närvarande en 30-dagars historik över din organisations historik i dess audit log inklusive:

- Aktivering/inaktivering av berättigade roller
- Tilldelningen rollaktiviteter inuti och utanför PIM
- Ändringar i rollinställningar
- Begäran/godkänna/neka aktiviteter för rollaktivering med Godkännandeinställning
- Uppdatera aviseringar

Du kan komma åt dessa granskningsloggar om du är en Global administratör eller en privilegierad rolladministratör. Mer information finns i [granskningshistorik för Azure AD-roller](pim-how-to-use-audit-log.md) och [granskningshistorik för Azure-resursroller](azure-pim-resource-rbac.md).

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du har minst en administratör läsa igenom alla granskningshändelser veckovis och exportera din granskningshändelser per månad.

Om du vill lagra automatiskt din granskningshändelser under en längre tidsperiod PIMS granskningsloggen är synkroniseras automatiskt till den [Azure AD-granskningsloggar](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> :heavy_check_mark: **Microsoft rekommenderar att du** du ställer in [övervakning av Azure log](../reports-monitoring/concept-activity-logs-azure-monitor.md) att arkivera granskningshändelser i ett Azure storage-konto för behovet av säkerhet och efterlevnad.
