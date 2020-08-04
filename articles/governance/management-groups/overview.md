---
title: Organisera dina resurser med hanterings grupper – Azure-styrning
description: Läs om hanteringsgrupperna, hur behörigheterna fungerar och hur du använder dem.
ms.date: 07/06/2020
ms.topic: overview
ms.openlocfilehash: 787658cebcb8345edd616bcdde485883ea43e8dc
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529355"
---
# <a name="what-are-azure-management-groups"></a>Vad är Azures hanterings grupper?

Om din organisation har många prenumerationer kan det behövas ett effektivt sätt att hantera åtkomst, principer och efterlevnad för prenumerationerna. Azure-hanteringsgrupper ger en omgångsnivå som omfattar prenumerationer. Du kan ordna prenumerationerna i containrar som kallas hanteringsgrupper och tillämpa styrningsvillkor för hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de villkor som tillämpas för hanteringsgruppen. Hanteringsgrupper tillhandahåller hantering i företagsklass i stor skala oavsett vilken typ av prenumeration du har.
Alla prenumerationer i en hanteringsgrupp måste ha förtroende för samma Azure Active Directory-klientorganisation.

Du kan till exempel tillämpa principer för en hanteringsgrupp som begränsar regionerna som är tillgängliga för att skapa virtuella datorer (VM). Den här principen tillämpas för alla hanteringsgrupper, prenumerationer och resurser under hanteringsgruppen genom att endast tillåta att virtuella datorer skapas i den regionen.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarki för hanteringsgrupper och prenumerationer

Du kan skapa en flexibel struktur för hanteringsgrupper och prenumerationer för att organisera dina resurser i en hierarki för enhetlig princip- och åtkomsthantering. Följande diagram visar ett exempel på hur du skapar en hierarki för styrning med hjälp av hanteringsgrupper.

:::image type="content" source="./media/tree.png" alt-text="Exempel på ett hierarkiträd för hanteringsgrupp" border="false":::

Du kan till exempel skapa en hierarki som tillämpar en princip som begränsar VM-platser till regionen USA, västra i gruppen med namnet ”Produktion”. Den här principen ärver till alla Enterprise-avtal-prenumerationer (EA) som är underordnade till den hanterings gruppen och gäller för alla virtuella datorer under dessa prenumerationer. Den här säkerhetsprincipen kan inte ändras av resursen eller prenumerationsägaren, vilket leder till bättre styrning.

Ett annat scenario där du kan använda hanteringsgrupper är för att ge användaråtkomst till flera prenumerationer. Genom att flytta flera prenumerationer under hanteringsgruppen kan du skapa en tilldelning av [rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC) till den. Detta gör även att alla prenumerationer ärver åtkomsten. Genom att tilldela till hanteringsgruppen kan du ge användarna åtkomst till allt de behöver istället för att skapa skript för RBAC för flera prenumerationer.

### <a name="important-facts-about-management-groups"></a>Viktiga fakta om hanteringsgrupper

- 10 000 hanteringsgrupper kan användas i en enskild katalog.
- Ett träd för hanteringsgruppen har stöd för upp till sex nivåer.
  - Den här gränsen omfattar inte rotnivån eller prenumerationsnivån.
- Varje hanteringsgrupp och prenumeration har endast stöd för ett överordnat element.
- Varje hanteringsgrupp kan ha många underordnade.
- Alla prenumerationer och hanteringsgrupper ingår i en hierarki i varje katalog. Läs [Viktiga fakta om rothanteringsgruppen](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>En rothanteringsgrupp för varje katalog

Varje katalog tilldelas en hanteringsgrupp på översta nivån som kallas för rothanteringsgruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Den här rot hanterings gruppen gör att globala principer och Azure Role-tilldelningar kan tillämpas på katalog nivå. [Den globala administratören för Azure Active Directory måste först utöka sin behörighet](../../role-based-access-control/elevate-access-global-admin.md) till rollen Administratör för användaråtkomst för rotgruppen. Efter utökad åtkomst kan administratören tilldela en Azure-roll till andra katalog användare eller grupper för att hantera hierarkin. Som administratör kan du utse ditt eget konto till ägare av rothanteringsgruppen.

### <a name="important-facts-about-the-root-management-group"></a>Viktiga fakta om rothanteringsgruppen

- Som standard är rothanteringsgruppens visningsnamn **Klientorganisationens rotgrupp**. ID:t är samma som ID:t för Azure Active Directory.
- Om du vill ändra visningsnamnet måste ditt konto ha tilldelats rollen Ägare eller Deltagare i rothanteringsgruppen. Se [ändra namnet på en hanterings grupp](manage.md#change-the-name-of-a-management-group) för att uppdatera namnet på en hanterings grupp.
- Rothanteringsgruppen kan inte flyttas eller tas bort, till skillnad från andra hanteringsgrupper.  
- Alla prenumerationer och hanteringsgrupper är underordnade rothanteringsgruppen i katalogen.
  - Alla resurser i katalogen är underordnade rothanteringsgruppen för global hantering.
  - Nya prenumerationer tilldelas som standard till rothanteringsgruppen när de skapas.
- Alla Azure-kunder kan se rothanteringsgruppen, men alla kunder får inte hantera rothanteringsgruppen.
  - Alla som har åtkomst till en prenumeration kan se kontexten för den aktuella prenumerationens placering i hierarkin.  
  - Det är inte någon som får åtkomst till rothanteringsgruppen som standard. Globala administratörer för Azure Active Directory är de enda användarna som kan ge sig själva åtkomst. När de har åtkomst till rot hanterings gruppen kan de globala administratörerna tilldela en Azure-roll till andra användare som ska hanteras  
    företaget.
- I SDK fungerar rot hanterings gruppen eller klient roten, som en hanterings grupp.

> [!IMPORTANT]
> Alla tilldelningar av användaråtkomst eller principtilldelning för rothanteringsgruppen **gäller för alla resurser inom katalogen**. Alla kunder bör därför utvärdera behovet av objekt som definierats för det här området. Användar åtkomst och princip tilldelningar ska endast vara "måste ha"  
> utrymme.

## <a name="initial-setup-of-management-groups"></a>Initial konfiguration av hanteringsgrupper

När användarna börjar använda hanteringsgrupper måste de genomföra en initial konfigurationsprocess. Det första steget är att rothanteringsgruppen skapas i katalogen. När den här gruppen har skapats blir alla befintliga prenumerationer i katalogen underordnade rothanteringsgruppen.
Den här processen är till för att kontrollera att det endast finns en hanteringsgrupphierarki i en katalog. Hierarkin i katalogen gör det möjligt för administrativa kunder att använda global åtkomst och principer som andra kunder i katalogen inte kan kringgå. Allt som tilldelas på roten gäller för hela hierarkin, vilket omfattar alla hanteringsgrupper, prenumerationer, resursgrupper och resurser i Azure Active Directory-klientorganisationen.

## <a name="trouble-seeing-all-subscriptions"></a>Problem med att visa alla prenumerationer

Vissa kataloger som började använda hanteringsgrupper tidigt under förhandsgranskningen, innan den 25 juni 2018, märkte av ett problem där alla prenumerationer inte fanns i hierarkin. Processen för att lägga till prenumerationer i hierarkin implementerades efter det att en roll- eller principtilldelning utfördes på katalogens rothanteringsgrupp.

### <a name="how-to-resolve-the-issue"></a>Så här löser du problemet

Det finns två alternativ som du kan använda för att lösa problemet.

- Ta bort alla roll- och principtilldelningar från rothanteringsgruppen
  - När du tar bort princip- och rolltilldelningar från rothanteringsgruppen återfyller tjänsten alla prenumerationer till hierarkin nästa dagcykel. Den här principen är till för att kontrollera att det inte har getts någon oavsiktlig åtkomst eller principtilldelning till alla prenumerationer i klientorganisationen.
  - Det bästa sättet att genomföra processen utan att påverka tjänsterna är att tilldela roll- eller principtilldelningar på en nivå lägre än rothanteringsgruppen. Sedan kan du ta bort alla tilldelningarna från rotomfånget.
- Direktanropa API:t och påbörja återfyllningsprocessen
  - Alla kunder i katalogen kan anropa API:erna _TenantBackfillStatusRequest_ eller _StartTenantBackfillRequest_. När API:n StartTenantBackfillRequest anropas påbörjas den ursprungliga konfigurationsprocessen och alla prenumerationer flyttas till hierarkin. Processen gör även att alla nya prenumerationer blir underordnade rothanteringsgruppen.
    Den här processen kan utföras utan att ändra några tilldelningar på rotnivån. Genom att anropa API:et godkänner du att alla principer eller åtkomsttilldelningar på roten kan tillämpas på alla prenumerationer.

Om du har frågor om återfyllningsprocessen kan du kontakta: `managementgroups@microsoft.com`
  
## <a name="management-group-access"></a>Åtkomst till hanteringsgrupp

Azures hanterings grupper har stöd för [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) för alla resurs åtkomster och roll definitioner.
Dessa behörigheter ärvs av underordnade resurser som finns i hierarkin. Alla Azure-roller kan tilldelas till en hanterings grupp som kommer att ärva hierarkin till resurserna. Till exempel kan den virtuella Azure-rollens VM-deltagare tilldelas till en hanterings grupp. Rollen har ingen åtgärd för hanteringsgruppen, men ärver av alla virtuella datorer under hanteringsgruppen.

Följande diagram visar listan över roller och åtgärder som stöds för hanteringsgrupper.

| Namn på Azure-roll             | Skapa | Byt namn | Fart\*\* | Ta bort | Tilldela åtkomst | Tilldela princip | Läsa  |
|:-------------------------- |:------:|:------:|:--------:|:------:|:-------------:| :------------:|:-----:|
|Ägare                       | X      | X      | X        | X      | X             | X             | X     |
|Deltagare                 | X      | X      | X        | X      |               |               | X     |
|MG-deltagare\*            | X      | X      | X        | X      |               |               | X     |
|Läsare                      |        |        |          |        |               |               | X     |
|MG-läsare\*                 |        |        |          |        |               |               | X     |
|Deltagare för resursprincip |        |        |          |        |               | X             |       |
|Administratör för användaråtkomst   |        |        |          |        | X             | X             |       |

\*: MG Contributor och MG Reader tillåter endast att användare utför dessa åtgärder i hanterings gruppens omfattning.  
\*\*: Roll tilldelningar för rot hanterings gruppen krävs inte för att flytta en prenumeration eller hanterings grupp till och från den. Läs [Hantera dina resurser med hanteringsgrupper](manage.md) för mer information om att flytta objekt inom hierarkin.

## <a name="azure-custom-role-definition-and-assignment"></a>Anpassad roll definition och tilldelning i Azure

Azures anpassade roll stöd för hanterings grupper är för närvarande en för hands version med vissa [begränsningar](#limitations). Du kan definiera hanteringsgruppers omfattning i rolldefinitionens tilldelningsbara omfattning. Den anpassade Azure-rollen kommer sedan att vara tillgänglig för tilldelning för den hanterings gruppen och alla hanterings grupper, prenumerationer, resurs grupper och resurser under den. Den här anpassade rollen ärvs nedåt i hierarkin precis som en inbyggd roll.  

### <a name="example-definition"></a>Exempel definition

Att [definiera och skapa en anpassad roll](../../role-based-access-control/custom-roles.md) ändras inte med inkludering av hanterings grupper. Använd den fullständiga sökvägen för att definiera hanterings gruppens **/providers/Microsoft.Management/managementgroups/{GroupID}**.

Använd hanterings gruppens ID och inte hanterings gruppens visnings namn. Detta vanliga fel inträffar eftersom båda är anpassade fält som definieras när du skapar en hanterings grupp.

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Problem med att bryta sökvägen till roll definitionen och tilldelnings-hierarkin

Roll definitioner är tilldelnings bara omfång var som helst inom hanterings gruppens hierarki. En roll definition kan definieras i en överordnad hanterings grupp medan den faktiska roll tilldelningen finns i den underordnade prenumerationen. Eftersom det finns en relation mellan de två objekten får du ett fel meddelande när du försöker avgränsa tilldelningen från definitionen.

Låt oss till exempel titta på en liten del av en hierarki för ett visuellt objekt.

:::image type="content" source="./media/subtree.png" alt-text="under träd" border="false":::

Anta att du har definierat en anpassad roll i marknadsförings hanterings gruppen. Den anpassade rollen tilldelas sedan de två kostnads fria utvärderings prenumerationerna.  

Om vi försöker flytta en av dessa prenumerationer till en underordnad till produktions hanterings gruppen skulle den här flyttningen bryta sökvägen från prenumerations roll tilldelningen till roll definitionen för hanterings gruppen för marknadsföring. I det här scenariot får du ett fel meddelande om att flyttningen inte är tillåten eftersom den kommer att bryta den här relationen.  

Det finns ett par olika alternativ för att åtgärda det här scenariot:
- Ta bort roll tilldelningen från prenumerationen innan du flyttar prenumerationen till en ny överordnad MG.
- Lägg till prenumerationen i roll definitionen för det tilldelnings bara omfånget.
- Ändra det tilldelnings bara omfånget i roll definitionen. I exemplet ovan kan du uppdatera tilldelnings bara omfattningarna från marknadsföring till rot hanterings gruppen så att definitionen kan nås av båda grenar i hierarkin.  
- Skapa ytterligare en anpassad roll som ska definieras i den andra grenen. Den nya rollen kräver även att roll tilldelningen ändras i prenumerationen.  

### <a name="limitations"></a>Begränsningar  

Det finns begränsningar som finns när du använder anpassade roller i hanterings grupper. 

 - Du kan bara definiera en hanterings grupp i de tilldelnings bara omfånget för en ny roll. Den här begränsningen är på plats för att minska antalet situationer där roll definitioner och roll tilldelningar är frånkopplade. Den här situationen inträffar när en prenumeration eller hanterings grupp med en roll tilldelning flyttas till en annan överordnad som inte har roll definitionen.  
 - Det går inte att definiera åtgärder för RBAC-dataplan i anpassade roller för hanterings grupper. Den här begränsningen är på plats som ett problem med RBAC-åtgärder med RBAC-åtgärder för att uppdatera data planets resurs leverantörer.
   Den här svars tids frågan bearbetas och de här åtgärderna inaktive ras från roll definitionen för att minska riskerna.
 - Azure Resource Manager validerar inte hanterings gruppens existens i roll definitionens tilldelnings omfång. Om det finns ett skrivfel eller felaktigt ID för hanterings grupp i listan, kommer roll definitionen fortfarande att skapas.  

## <a name="moving-management-groups-and-subscriptions"></a>Flytta hanterings grupper och prenumerationer 

Om du vill flytta en hanterings grupp eller prenumeration till en underordnad till en annan hanterings grupp måste tre regler utvärderas som sant.

Om du utför flytt åtgärden behöver du: 

- Hanterings gruppens Skriv-och roll tilldelning Skriv behörigheter för den underordnade prenumerationen eller hanterings gruppen.
  - Exempel på inbyggd roll exempel **ägare**
- Skriv åtkomst till hanterings grupp för den överordnade mål hanterings gruppen.
  - Exempel på inbyggda roller: **ägare**, **deltagare**, **hanterings grupp deltagare**
- Skriv åtkomst till hanterings grupp för den befintliga överordnade hanterings gruppen.
  - Exempel på inbyggda roller: **ägare**, **deltagare**, **hanterings grupp deltagare**

**Undantag**: om målet eller den befintliga överordnade hanterings gruppen är rot hanterings gruppen gäller inte behörighets kraven. Eftersom rot hanterings gruppen är standard landnings platsen för alla nya hanterings grupper och prenumerationer behöver du inte behörigheter för den för att flytta ett objekt.

Om ägar rollen för prenumerationen ärvs från den aktuella hanterings gruppen är dina flyttnings mål begränsade. Du kan bara flytta prenumerationen till en annan hanterings grupp där du har ägar rollen. Du kan inte flytta den till en hanterings grupp där du är deltagare eftersom du förlorar prenumerationens ägarskap. Om du är direkt tilldelad till ägar rollen för prenumerationen (ärvs inte från hanterings gruppen) kan du flytta den till en hanterings grupp där du är deltagare.

## <a name="audit-management-groups-using-activity-logs"></a>Granska hanteringsgrupper med hjälp av aktivitetsloggar

Hanteringsgrupper kan användas i [Azure-aktivitetsloggar](../../azure-monitor/platform/platform-logs-overview.md). Du kan söka efter alla händelser i en hanteringsgrupp från samma centrala plats som andra Azure-resurser. Du kan till exempel se alla ändringar för rolltilldelningar eller principtilldelningar som gjorts i en viss hanteringsgrupp.

:::image type="content" source="./media/al-mg.png" alt-text="Aktivitetsloggar med hanteringsgrupper" border="false":::

När du vill fråga hanteringsgrupper utanför Microsoft Azure-portalen är målområdet för hanteringsgrupper: **"/providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="next-steps"></a>Nästa steg

Läs mer om hanteringslösningar här:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](./create.md)
- [Så här ändrar, raderar och hanterar du dina hanteringsgrupper](./manage.md)
- Se alternativ för [att skydda din resurs-hierarki](./how-to/protect-resource-hierarchy.md)