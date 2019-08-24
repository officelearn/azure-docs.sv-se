---
title: Rollbaserad Access Control och Privileged Identity Management i Azure Australien
description: Vägledning om hur du implementerar rollbaserade Access Control och Privileged Identity Management inom australiensiska regioner för att uppfylla de särskilda kraven i den australiska regeringens politik, förordningar och lagstiftning.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e2a94f82e4830bd1e9c96039f5ef8fe6546b0d0b
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982664"
---
# <a name="role-based-access-control-rbac-and-privileged-identity-management-pim"></a>Rollbaserad Access Control (RBAC) och Privileged Identity Management (PIM)

Hantering av administratörs behörighet är ett viktigt steg i att säkerställa säkerheten i en IT-miljö. Att begränsa administratörs behörigheten genom att använda minsta behörighets säkerhet är ett krav för [ACSC-ISM](https://acsc.gov.au/infosec/ism/index.htm) och Forms som ingår i [ACSC Essentials](https://www.acsc.gov.au/infosec/mitigationstrategies.htm) -listan över säkerhets rekommendationer.

Microsoft tillhandahåller en uppsättning kontroller för att implementera just-in-Time och bara tillräckligt med åtkomst inom Microsoft Azure. Att förstå dessa kontroller är viktigt för en effektiv säkerhets position i molnet. I den här guiden får du en översikt över själva kontrollerna och viktiga design aspekter när du implementerar dem.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

Rollbaserad Access Control är central för hantering av åtkomst till alla resurser i Microsoft Azure och hantering av Azure Active Directory (Azure AD). RBAC kan implementeras tillsammans med ett antal kompletterande funktioner som är tillgängliga i Azure. Den här artikeln fokuserar på implementering av effektiv RBAC med Azure Hanteringsgrupper Azure Active Directory grupper och Azure-Privileged Identity Management (PIM).

Implementera RBAC kräver tre komponenter på hög nivå:

![RBAC-översikt](media/rbac-overview.png)

* **Säkerhets objekt**: Ett säkerhets objekt kan vara något av följande: en användare, en grupp, [tjänstens huvud namn](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)eller en [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Säkerhets objekt bör tilldelas behörigheter med hjälp av Azure Active Directory grupper.

* **Roll definitioner**: En roll definition, som även kallas en roll, är en samling behörigheter. Dessa behörigheter definierar de åtgärder som kan utföras av de säkerhets objekt som tilldelats roll definitionen. Den här funktionen tillhandahålls av Azures resurs roller och Azure Active Directory administratörs roller. Azure levereras med en uppsättning inbyggda roller (länk) som kan utökas med anpassade roller.

* **Omfång**: Omfattningen är den uppsättning av Azure-resurser som en roll definition gäller för. Azure-roller kan tilldelas till Azure-resurser med hjälp av Azure Hanteringsgrupper.

Dessa tre komponenter kombineras för att ge säkerhets objekt åtkomst som definierats i roll definitionerna till alla resurser som omfattas av Azure Hanteringsgrupper-omfattningen, kallas för en roll tilldelning. Flera roll definitioner kan tilldelas till ett säkerhets objekt, och flera säkerhets objekt kan tilldelas till ett enda omfång.

### <a name="azure-active-directory-groups"></a>Azure Active Directory grupper

När det är möjligt att tilldela behörigheter till enskilda användare eller team, ska tilldelningen, när så är möjligt, kopplas till en Azure Active Directory grupp och inte tilldelas direkt till användaren i fråga. Detta är samma rekommenderade metod som ärvts från lokala Active Directory-implementeringar. Om möjligt Azure Active Directory grupper skapas per team, kompletterar du den logiska strukturen i Azure-Hanteringsgrupper som du har skapat.

I ett scenario med hybrid moln kan lokala Windows Server Active Directory säkerhets grupper synkroniseras med din Azure Active Directory-instans. Om du redan har implementerat RBAC lokalt med hjälp av dessa Windows Server Active Directory säkerhets grupper, kan dessa grupper, när de har synkroniserats, användas för att implementera RBAC för dina Azure-resurser. I annat fall kan din moln miljö ses som en ren arbets miljö för att utforma och implementera en robust hanterings plan för privilegium som skapats runt din Azure Active Directory implementering.

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Azures resurs roller jämfört med Azure Active Directory administratörs roller

Microsoft Azure erbjuder en mängd inbyggda roller för [Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) och [Azure Active Directory Administration](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). Båda typerna av roller ger detaljerad åtkomst till antingen Azure-resurser eller Azure AD-administratörer. Det är viktigt att Observera att Azure-resurs roller inte kan användas för att ge administrativ åtkomst till Azure AD och Azure AD-roller ger inte särskild åtkomst till Azure-resurser.

Några exempel på typer av åtkomst som kan tilldelas till en Azure-resurs med hjälp av en inbyggd roll är:

* Tillåta en användare att hantera virtuella datorer i en prenumeration och en annan användare att hantera virtuella nätverk
* Tillåta en DBA-grupp att hantera SQL-databaser i en prenumeration
* Tillåta en användare att hantera alla resurser i en resursgrupp, till exempel virtuella datorer, webbplatser och undernät
* Tillåta att ett program får åtkomst till alla resurser i en resursgrupp

Exempel på typer av åtkomst som kan tilldelas för Azure AD-administration är:

* Tillåt supportavdelningen att återställa användar lösen ord
* Tillåt att personalen bjuder in externa användare till en Azure AD-instans för B2B-samarbete
* Tillåt administrativ personal Läs behörighet för att logga in och granska rapporter
* Tillåt att personalen hanterar alla användare och grupper, inklusive återställning av lösen ord.

Det är viktigt att ta tid att förstå den fullständiga listan över tillåtna åtgärder en inbyggd roll ger en inbyggd roll som garanterar att onödig åtkomst till inte beviljas. Listan över inbyggda roller och den åtkomst som de tillhandahåller utvecklas ständigt, och den fullständiga listan över roller och deras definitioner kan visas genom att granska dokumentationen som är länkad ovan eller genom att använda Azure PowerShell-cmdlet:

```PowerShell
PS C:\> Get-AzRoleDefinition

Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

eller Azure CLI-kommandot:

```bash
PS C:\> az role definition list
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

Det är också möjligt att skapa anpassade Azure-resurs roller efter behov. Dessa anpassade roller kan skapas i Azure Portal, via PowerShell eller Azure CLI. När du skapar anpassade roller är det viktigt att se till att syftet med rollen är unikt och att dess funktion inte redan tillhandahålls av en befintlig Azure-resurs roll. Detta minskar pågående hanterings komplexitet och minskar risken för att säkerhets objekt får onödiga privilegier. Ett exempel skulle skapa en anpassad Azure-adressresurs som finns mellan de inbyggda Azure-resurs rollerna, "virtuell dator deltagare" och "Administratörs inloggning för virtuell dator".

Den anpassade rollen kan baseras på den befintliga deltagar rollen, som ger följande åtkomst:

| Azure-resurs | Åtkomstnivå |
| --- | --- |
| Virtuella datorer | Kan hantera men kan inte komma åt |
| Virtual Network kopplad till virtuell dator | Kan inte komma åt |
| Lagring kopplad till virtuell dator | Kan inte komma åt |
|

Den anpassade rollen bevarar den här grundläggande åtkomst, men ger de utsedda användarna grundläggande ytterligare behörighet att ändra nätverks konfigurationen för de virtuella datorerna.

Azures resurs roller har också fördelen att kunna tilldelas resurser via Azure Hanteringsgrupper.

### <a name="azure-management-groups"></a>Hanteringsgrupper i Azure

Azure Hanteringsgrupper kan användas av en organisation för att hantera tilldelningen av roller till alla prenumerationer och deras resurser i ett Azure-innehav. Azure Hanteringsgrupper är utformade för att du ska kunna skapa hanterings-hierarkier, inklusive möjligheten att mappa din organisations struktur hierarkiskt i Azure. Att skapa organisations affär senheter som separata logiska entiteter gör det möjligt att tillämpa behörigheter inom en organisation baserat på varje Teams specifika krav. Azure Hanteringsgrupper kan användas för att definiera en hanterings-hierarki på upp till sex nivåer.

![Hanteringsgrupper](media/management-groups.png)

Azure-Hanteringsgrupper mappas till Azure-prenumerationer inom ett Azure-innehav. Detta gör det möjligt för en organisation att dela upp Azure-resurser som tillhör specifika affär senheter och ger en detalj nivå kontroll över både kostnads hantering och tilldelning av privilegier.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Microsoft har implementerat just-in-Time (JIT) och precis-tillräcklig-åtkomst (JEA) via Azure Privileged Identity Management. Den här tjänsten gör det möjligt för administrativ personal att styra, hantera och övervaka privilegie rad åtkomst till Azure-resurser. PIM tillåter att säkerhets objekt blir "berättigade" för en roll av administrativ personal, vilket gör det möjligt för användare att begära aktivering av rollen via Azure Portal eller via PowerShell-cmdletar. Som standard kan roll tilldelning aktive ras för en period på mellan 1 och 72 timmar. Vid behov kan användaren begära ett tillägg till sin roll tilldelning och alternativet att göra roll tilldelningen permanent finns. Kravet för Multi-Factor Authentication kan krävas när användarna begär aktivering av de berättigade rollerna. När den allokerade perioden för roll aktiveringen upphör att gälla, har säkerhetsobjektet inte längre privilegie rad åtkomst beviljad av rollen.

Användningen av PIM förhindrar vanliga problem med behörighets tilldelning som kan uppstå i miljöer som inte använder just-in-Time-åtkomst eller som inte utför rutinmässiga granskningar av behörighets tilldelning. Ett vanligt problem är att tilldelningen av förhöjda privilegier blir bortglömt och kvar på plats långt efter att aktiviteten som kräver förhöjd behörighet har slutförts. Ett annat problem är att förhöjda privilegier i en miljö genom kloning av åtkomst som tilldelats till ett säkerhets objekt när du konfigurerar andra liknande säkerhets objekt.

## <a name="key-design-considerations"></a>Viktiga design överväganden

När du designar en RBAC-strategi med avsikt att framtvinga minsta behörighet för säkerhet bör följande säkerhets krav övervägas:

* Begär Anden om privilegie rad åtkomst verifieras
* Administrativa privilegier är begränsade till den lägsta åtkomst som krävs för att utföra de angivna tullarna
* Administrativa privilegier är begränsade till den minsta tids period som krävs för att utföra de aktuella tullarna
* Regelbundna granskningar av beviljade administratörs privilegier sker

Processen för att utforma en RBAC-strategi kommer att kräva en detaljerad granskning av affärs funktioner för att förstå skillnaden i åtkomsten mellan olika affärs roller och typen och frekvensen av arbete som kräver förhöjda privilegier. Skillnaden i funktionen mellan en ansvarig för säkerhets kopiering, en säkerhets administratör och en granskare kräver olika åtkomst nivåer vid olika tidpunkter med olika nivåer av en pågående granskning.

## <a name="validate-requests-for-access"></a>Verifiera begär Anden om åtkomst

Utökade privilegier måste godkännas explicit. För att stödja detta måste en godkännande process utvecklas och lämplig personal fattas som ansvarar för att verifiera att alla begär Anden om ytterligare behörighet är berättigade. Privileged Identity Management tillhandahåller flera alternativ för att godkänna roll tilldelning. En begäran om att aktivera en roll kan konfigureras för att tillåta själv godkännande eller vara gated och kräva att avsedda god kännare manuellt granskar och godkänner alla roll aktiverings begär Anden. Aktiverings begär Anden kan också konfigureras för att kräva att ytterligare kompletterande information ingår i aktiverings förfrågan, t. ex. biljett nummer.

### <a name="restrict-privilege-based-on-duties"></a>Begränsa privilegier baserat på uppgifter

Att begränsa den behörighets nivå som beviljats för säkerhets objekt är kritisk, eftersom tilldelningen av behörigheter är en vanlig attack vektor för IT-säkerhet. De typer av resurser som hanteras och de team som ansvarar måste bedömas så att den lägsta behörighets nivån som krävs för dagliga uppgifter kan tilldelas. Ytterligare privilegier som går utöver dem som krävs för dagliga uppgifter bör bara beviljas för den tids period som krävs för att utföra en viss uppgift. Ett exempel på detta ger till gång till "deltagar"-åtkomst till en kund administratör, men gör det möjligt för dem att begära "ägar behörighet för en Azure-resurs för en specifik aktivitet som kräver tillfällig hög åtkomst till hög nivå.

Detta säkerställer att varje enskild administratör bara har förhöjd åtkomst under den kortaste tids perioden. Genom att säga dessa metoder minskar du den övergripande angrepps ytan för alla organisationers IT-infrastruktur.

### <a name="regular-evaluation-of-administrative-privilege"></a>Regelbunden utvärdering av administrativ behörighet

Det är viktigt att säkerhets objekt i en miljö granskas rutinmässigt för att säkerställa att rätt behörighets nivå för närvarande är tilldelad. Microsoft Azure är ett antal sätt att granska och utvärdera de privilegier som tilldelats Azures säkerhets objekt. Privileged Identity Management gör det möjligt för administrativ personal att regelbundet utföra "åtkomst granskningar" av de roller som beviljats säkerhets objekt. En åtkomst granskning kan utföras för granskning av både tilldelning av Azure-resurstilldelningar och Azure Active Directory administrativ roll tilldelning. En åtkomst granskning kan konfigureras med följande egenskaper:

* **Granska namn och granska start-och slutdatum**: Granskningarna bör konfigureras så att de blir tillräckligt långa för att de nominerade användarna ska kunna slutföra dem.

* **Roll som**ska granskas: Varje åtkomst granskning fokuserar på en enda Azure-roll.

* **Nominerade granskare**: Det finns tre alternativ för att utföra en granskning. Du kan tilldela granskningen till någon annan för att slutföra. du kan göra det själv, eller så kan du låta varje användare granska sin egen åtkomst.

* **Kräv att användarna måste ange en orsak**till åtkomsten: Användare kan behöva ange ett skäl för att bevara deras behörighets nivå när de slutför åtkomst granskningen.

Förloppet för väntande åtkomst granskningar kan övervakas när som helst via en instrument panel i Azure Portal. Åtkomst till rollen som granskas är oförändrad tills åtkomst granskningen har slutförts. Det är också möjligt att [Granska](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-use-audit-log) alla PIM-användarkonton och-aktiveringar inom en nominerad tids period.

## <a name="next-steps"></a>Nästa steg

Läs artikeln om [system övervakning i Azure Australien](system-monitor.md).
