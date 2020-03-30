---
title: Referensguide för Azure Active Directory Identity- och åtkomsthanteringsåtgärder
description: I referensguiden för åtgärder beskrivs de kontroller och åtgärder du bör vidta för att skydda identitets- och åtkomsthanteringsåtgärder
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298622"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Referensguide för Azure Active Directory Identity- och åtkomsthanteringsåtgärder

I det här avsnittet i [referensguiden för Azure AD-åtgärder](active-directory-ops-guide-intro.md) beskrivs de kontroller och åtgärder som du bör tänka på för att skydda och hantera livscykeln för identiteter och deras tilldelningar.

> [!NOTE]
> Dessa rekommendationer är aktuella från och med publiceringsdatumet men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sina identitetsmetoder när Microsofts produkter och tjänster utvecklas med tiden.

## <a name="key-operational-processes"></a>Viktiga operativa processer

### <a name="assign-owners-to-key-tasks"></a>Tilldela ägare till nyckeluppgifter

För att hantera Azure Active Directory krävs kontinuerlig körning av viktiga operativa uppgifter och processer som kanske inte ingår i ett distributionsprojekt. Det är fortfarande viktigt att du ställer in dessa uppgifter för att behålla din miljö. De viktigaste uppgifterna och deras rekommenderade ägare är:

| Aktivitet | Ägare |
| :- | :- |
| Definiera processen för hur du skapar Azure-prenumerationer | Varierar beroende på organisation |
| Bestäm vem som får Enterprise Mobility + Security-licenser | IAM Operations Team |
| Bestäm vem som får Office 365-licenser | Produktivitetsteam |
| Bestäm vem som får andra licenser, till exempel Dynamics, VSO | Programägare |
| Tilldela licenser | IAM Operations Team |
| Felsöka och åtgärda licenstilldelningsfel | IAM Operations Team |
| Etablera identiteter till program i Azure AD | IAM Operations Team |

När du granskar listan kan du behöva tilldela en ägare för uppgifter som saknar en ägare eller justera ägarskapet för uppgifter med ägare som inte är anpassade till rekommendationerna ovan.

#### <a name="assigning-owners-recommended-reading"></a>Tilldela ägare rekommenderad läsning

- [Tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Styrning i Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>Lokal identitetssynkronisering

### <a name="identify-and-resolve-synchronization-issues"></a>Identifiera och lösa synkroniseringsproblem

Microsoft rekommenderar att du har en bra baslinje och förståelse för problemen i din lokala miljö som kan resultera i synkroniseringsproblem med molnet. Eftersom automatiserade verktyg som [IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) och [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) kan generera en hög mängd falska positiva, rekommenderar vi att du identifierar synkroniseringsfel som har lämnats oadresserade i mer än 100 dagar genom att rensa upp dessa objekt av misstag. Långsiktiga olösta synkroniseringsfel kan generera supportincidenter. [Felsökningsfel under synkroniseringen](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors) ger en översikt över olika typer av synkroniseringsfel, några av de möjliga scenarier som orsakar dessa fel och möjliga sätt att åtgärda felen.

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD Connect-synkroniseringskonfiguration

För att aktivera alla hybridupplevelser, enhetsbaserad säkerhetsposition och integrering med Azure AD krävs att du synkroniserar användarkonton som dina anställda använder för att logga in på sina skrivbord.

Om du inte synkroniserar skogsanvändarloggen i bör du ändra synkroniseringen så att den kommer från rätt skog.

#### <a name="synchronization-scope-and-object-filtering"></a>Synkroniseringsomfång och objektfiltrering

Att ta bort kända buckets av objekt som inte behöver synkroniseras har följande operativa fördelar:

- Färre synkroniseringsfel
- Snabbare synkroniseringscykler
- Mindre "skräp" att föra vidare från lokala, till exempel förorening av den globala adresslistan för lokala tjänstkonton som inte är relevanta i molnet

> [!NOTE]
> Om du upptäcker att du importerar många objekt som inte exporteras till molnet bör du filtrera efter organisationsenheten eller specifika attribut.

Exempel på objekt som ska uteslutas är:

- Tjänstkonton som inte används för molnprogram
- Grupper som inte är avsedda att användas i molnscenarier som de som används för att bevilja åtkomst till resurser
- Användare eller kontakter som är externa identiteter som är avsedda att representeras med Azure AD B2B-samarbete
- Datorkonton där anställda inte är avsedda att komma åt molnprogram från till exempel servrar

> [!NOTE]
> Om en enda mänsklig identitet har flera konton som etablerats från något som en äldre domänmigrering, sammanslagning eller förvärv, bör du bara synkronisera kontot som används av användaren på daglig basis, till exempel vad de använder för att logga in på sin dator .

Helst vill du nå en balans mellan att minska antalet objekt att synkronisera och komplexiteten i reglerna. I allmänhet är en kombination mellan [filtrering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) av OU/behållare plus en enkel attributmappning till molnetFiltered-attributet en effektiv filtreringskombination.

> [!IMPORTANT]
> Om du använder gruppfiltrering i produktionen bör du övergå till en annan filtreringsmetod.

#### <a name="sync-failover-or-disaster-recovery"></a>Synkronisera redundans eller haveriberedskap

Azure AD Connect spelar en nyckelroll i etableringsprocessen. Om synkroniseringsservern av någon anledning kopplas till kan ändringar i lokala inte uppdateras i molnet och kan resultera i åtkomstproblem för användare. Därför är det viktigt att definiera en redundansstrategi som gör att administratörer snabbt kan återuppta synkroniseringen när synkroniseringsservern är offline. Sådana strategier kan delas in i följande kategorier:

- **Distribuera Azure AD Connect Server(s) i mellanlagringsläge** - gör det möjligt för en administratör att "befordra" mellanlagringsservern till produktion med en enkel konfigurationsväxel.
- **Använd virtualisering** – Om Azure AD-anslutningen distribueras på en virtuell dator (VM) kan administratörer utnyttja sin virtualiseringsstack för att live migrera eller snabbt distribuera om den virtuella datorn och därför återuppta synkroniseringen.

Om din organisation saknar en katastrofåterställnings- och redundansstrategi för Sync bör du inte tveka att distribuera Azure AD Connect i mellanlagringsläge. Om det finns en obalans mellan produktions- och mellanlagringskonfigurationen bör du också ombaslinje azure AD Connect-mellanlagringsläge för att matcha produktionskonfigurationen, inklusive programvaruversioner och konfigurationer.

![En skärmbild av azure AD Connect-mellanlagringslägeskonfiguration](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Håll dig uppdaterad

Microsoft uppdaterar Azure AD Connect regelbundet. Håll dig uppdaterad för att dra nytta av prestandaförbättringar, buggfixar och nya funktioner som varje ny version tillhandahåller.

Om din Azure AD Connect-version ligger mer än sex månader efter bör du uppgradera till den senaste versionen.

#### <a name="source-anchor"></a>Källa ankare

Med **ms-DS-consistencyguid** som [källankare](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts) kan en enklare migrering av objekt över skogar och domäner, vilket är vanligt i AD-domänkonsolidering/rensning, sammanslagningar, förvärv och avyttringar.

Om du för närvarande använder **ObjectGuid** som källankare rekommenderar vi att du byter till **ms-DS-ConsistencyGuid**.

#### <a name="custom-rules"></a>Anpassade regler

Azure AD Connect anpassade regler ger möjlighet att styra flödet av attribut mellan lokala objekt och molnobjekt. Överanvända eller missbruka anpassade regler kan dock medföra följande risker:

- Felsöka komplexitet
- Försämrad prestanda vid komplexa åtgärder över objekt
- Högre sannolikhet för skillnader i konfiguration mellan produktionsservern och mellanlagringsservern
- Ytterligare omkostnader vid uppgradering av Azure AD Connect om anpassade regler skapas inom prioriteten större än 100 (används av inbyggda regler)

Om du använder alltför komplexa regler bör du undersöka orsakerna till komplexiteten och hitta möjligheter till förenkling. Om du har skapat anpassade regler med prioritetsvärde över 100 bör du också åtgärda reglerna så att de inte är i riskzonen eller står i konflikt med standarduppsättningen.

Exempel på hur du missbrukar anpassade regler är:

- **Kompensera för smutsiga data i katalogen** - I det här fallet rekommenderas att arbeta med ad-teamets ägare och rensa data i katalogen som en reparationsuppgift och justera processer för att undvika att felaktiga data återinförs.
- **Engångsåtgärd av enskilda användare** - Det är vanligt att hitta regler som specialfallsutfallsutgivare, vanligtvis på grund av ett problem med en viss användare.
- **Överkompilerad "CloudFiltering"** - Samtidigt som minska antalet objekt är en bra praxis, finns det en risk för att skapa och överkompilerad synkronisering omfattning med hjälp av många synkroniseringsregler. Om det finns komplex logik för att inkludera/utesluta objekt utanför organisationsenhetsfiltrering, rekommenderas att hantera den här logiken utanför synkroniseringen och märka objekten med ett enkelt "cloudFiltered"-attribut som kan flöda med en enkel synkroniseringsregel.

#### <a name="azure-ad-connect-configuration-documenter"></a>Konfigurationsdokumenterare för Azure AD Connect

[Azure AD Connect Configuration Documenter](https://github.com/Microsoft/AADConnectConfigDocumenter) är ett verktyg som du kan använda för att generera dokumentation av en Azure AD Connect-installation för att möjliggöra en bättre förståelse av synkroniseringskonfigurationen, skapa förtroende för att få saker rätt och veta vad som ändrades när du tillämpade en ny version eller konfiguration av Azure AD Connect eller lagt till eller uppdaterade anpassade synkroniseringsregler. De aktuella funktionerna i verktyget är:

- Dokumentation av den fullständiga konfigurationen av Azure AD Connect-synkronisering.
- Dokumentation av eventuella ändringar i konfigurationen av två Azure AD Connect-synkroniseringsservrar eller ändringar från en viss konfigurationsbaslinje.
- Generering av ett PowerShell-distributionsskript för att migrera synkroniseringsregelskillnader eller anpassningar från en server till en annan.

## <a name="assignment-to-apps-and-resources"></a>Tilldelning till appar och resurser

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Gruppbaserad licensiering för Microsofts molntjänster

Azure Active Directory effektiviserar hanteringen av licenser via [gruppbaserad licensiering](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) för Microsofts molntjänster. På så sätt tillhandahåller IAM gruppinfrastrukturen och delegerad hantering av dessa grupper till rätt team i organisationerna. Det finns flera sätt att konfigurera medlemskap i grupper i Azure AD, inklusive:

- **Synkroniserad från lokala** – Grupper kan komma från lokala kataloger, vilket kan vara en bra passform för organisationer som har etablerat grupphanteringsprocesser som kan utökas för att tilldela licenser i Office 365.

- **Attributbaserad/dynamisk** - Grupper kan skapas i molnet baserat på ett uttryck baserat på användarattribut, till exempel Avdelning är lika med "försäljning". Azure AD underhåller medlemmarna i gruppen och håller det förenligt med det definierade uttrycket. Genom att använda den här typen av grupp för licenstilldelning kan en attributbaserad licenstilldelning, vilket passar bra för organisationer som har hög datakvalitet i katalogen.

- **Delegerad ägarskap** - Grupper kan skapas i molnet och kan utses till ägare. På så sätt kan du ge företagare, till exempel Samarbetsteam eller BI-team, möjlighet att definiera vem som ska ha åtkomst.

Om du för närvarande använder en manuell process för att tilldela licenser och komponenter till användare rekommenderar vi att du implementerar gruppbaserad licensiering. Om den aktuella processen inte övervakar licensfel eller vad som är tilldelat kontra tillgängligt bör du definiera förbättringar av processen för att åtgärda licensfel och övervaka licensieringstilldelning.

En annan aspekt av licenshantering är definitionen av serviceplaner (komponenter i licensen) som ska aktiveras baserat på jobbfunktioner i organisationen. Om du beviljar åtkomst till tjänstplaner som inte är nödvändiga kan användarna se verktyg i Office-portalen som de inte har tränats för eller inte bör använda. Det kan driva ytterligare helpdesk volym, onödig etablering, och sätta din efterlevnad och styrning i riskzonen, till exempel när etablering OneDrive för företag till personer som kanske inte får dela innehåll.

Använd följande riktlinjer för att definiera serviceplaner för användare:

- Administratörer bör definiera "paket" av tjänstplaner som ska erbjudas användare baserat på deras roll, till exempel arbetare kontra golvarbetare.
- Skapa grupper efter kluster och tilldela licensen med serviceplan.
- Alternativt kan ett attribut definieras för att hålla paketen för användare.

> [!IMPORTANT]
> Gruppbaserad licensiering i Azure AD introducerar begreppet användare i ett licensieringsfeltillstånd. Om du upptäcker några licensfel bör du omedelbart [identifiera och lösa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems) eventuella licenstilldelningsproblem.

![En skärmbild av en datorskärm Beskrivning genereras automatiskt](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Livscykelhantering

Om du för närvarande använder ett verktyg, till exempel [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/) eller tredjepartssystem, som är beroende av en lokal infrastruktur, rekommenderar vi att du avlastar tilldelning från det befintliga verktyget, implementerar gruppbaserad licensiering och definierar en grupplivscykelhantering baserat på [grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups). Om din befintliga process inte tar hänsyn till nya medarbetare eller anställda som lämnar organisationen bör du distribuera gruppbaserad licensiering baserat på dynamiska grupper och definiera en livscykel för gruppmedlemskap. Slutligen, om gruppbaserad licensiering distribueras mot lokala grupper som saknar livscykelhantering, överväg att använda molngrupper för att aktivera funktioner som delegerad ägarskap eller attributbaserat dynamiskt medlemskap.

### <a name="assignment-of-apps-with-all-users-group"></a>Tilldelning av appar med gruppen Alla användare

Resursägare kan tro att gruppen **Alla användare** endast innehåller **företagsanställda** när de faktiskt kan innehålla både **företagsanställda** och **gäster**. Därför bör du vara särskilt försiktig när du använder gruppen **Alla användare** för programtilldelning och bevilja åtkomst till resurser som SharePoint-innehåll eller -program.

> [!IMPORTANT]
> Om gruppen **Alla användare** är aktiverad och används för principer för villkorlig åtkomst, app eller resurstilldelning kontrollerar du att [gruppen skyddas](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups) om du inte vill att den ska inkludera gästanvändare. Dessutom bör du åtgärda dina licenstilldelningar genom att skapa och tilldela till grupper som endast innehåller **företagsanställda.** Om du däremot upptäcker att gruppen **Alla användare** är aktiverad men inte används för att bevilja åtkomst till resurser, ska du se till att organisationens operativa vägledning avsiktligt använder den gruppen (som omfattar både **företagsanställda** och **gäster).**

### <a name="automated-user-provisioning-to-apps"></a>Automatiserad användaretablering till appar

[Automatiserad användaretablering](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) till program är det bästa sättet att skapa en konsekvent etablering, avetablering och livscykel för identiteter i flera system.

Om du för närvarande etablerar appar på ett ad hoc-sätt eller använder saker som CSV-filer, JIT eller en lokal lösning som inte behandlar livscykelhantering rekommenderar vi att du [implementerar programetablering](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application) med Azure AD för program som stöds och definierar ett konsekvent mönster för program som ännu inte stöds av Azure AD.

![Azure AD-etableringstjänst](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect deltasynkroniseringscykelbaslinje

Det är viktigt att förstå volymen av ändringar i organisationen och se till att det inte tar för lång tid att ha en förutsägbar synkroniseringstid.

Standardfrekvensen [för deltasynkronisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) är 30 minuter. Om deltasynkroniseringen tar längre tid än 30 minuter konsekvent, eller om det finns betydande skillnader mellan deltasynkroniseringsprestandan för mellanlagring och produktion, bör du undersöka och granska de [faktorer som påverkar prestanda för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect felsökning rekommenderas läsning

- [Förbereda katalogattribut för synkronisering med Office 365 med idFix-verktyget - Office 365](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: Felsöka fel under synkroniseringen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>Sammanfattning

Det finns fem aspekter på en säker identitetsinfrastruktur. Den här listan hjälper dig att snabbt hitta och vidta nödvändiga åtgärder för att skydda och hantera livscykeln för identiteter och deras rättigheter i din organisation.

- Tilldela ägare till nyckeluppgifter.
- Hitta och lösa synkroniseringsproblem.
- Definiera en redundansstrategi för haveriberedskap.
- Effektivisera hanteringen av licenser och tilldelning av appar.
- Automatisera etablering av användare till appar.

## <a name="next-steps"></a>Nästa steg

Kom igång med [autentiseringshanteringskontroller och åtgärder](active-directory-ops-guide-auth.md).
