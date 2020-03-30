---
title: Förstå hur Azure AD-etablering fungerar | Microsoft-dokument
description: Förstå hur Azure AD-etablering fungerar
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 555fb39836054be05102f4c28167d72016805639
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481508"
---
# <a name="how-provisioning-works"></a>Så här fungerar etablering

Automatisk etablering avser att skapa användaridentiteter och roller i molnprogrammen som användarna behöver åtkomst till. Förutom att skapa användaridentiteter, automatisk etablering inkluderar underhåll och borttagning av användaridentiteter som status eller roller förändras. Innan du startar en distribution kan du granska den här artikeln om du vill veta hur Azure AD-etablering fungerar och få konfigurationsrekommendationer. 

**Azure AD-etableringstjänsten etablerar** användare till SaaS-appar och andra system genom att ansluta till ett SYSTEM för struken domänkontrollidentitetshantering (SCIM) 2.0-användarhanterings-API-slutpunkt som tillhandahålls av programleverantören. Med den här SCIM-slutpunkten kan Azure AD programmässigt skapa, uppdatera och ta bort användare. För valda program kan etableringstjänsten också skapa, uppdatera och ta bort ytterligare identitetsrelaterade objekt, till exempel grupper och roller. Kanalen som används för etablering mellan Azure AD och programmet krypteras med HTTPS TLS-kryptering.


![Azure AD-etableringstjänst](./media/how-provisioning-works/provisioning0.PNG)
*Figur 1: Azure AD-etableringstjänsten*

![Arbetsflöde för utgående](./media/how-provisioning-works/provisioning1.PNG)
användaretablering*Bild 2: Arbetsflöde för utgående användare etablerar från Azure AD till populära SaaS-program*

![Arbetsflöde för inkommande](./media/how-provisioning-works/provisioning2.PNG)
användaretablering Figur*3: "Inkommande" arbetsflöde för användaretablering från populära HCM-program (Human Capital Management) till Azure Active Directory och Active Directory för Windows Server*

## <a name="provisioning-using-scim-20"></a>Etablering med SCIM 2.0

Azure AD-etableringstjänsten använder [SCIM 2.0-protokollet](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) för automatisk etablering. Tjänsten ansluter till SCIM-slutpunkten för programmet och använder SCIM-användarobjektschema och REST-API:er för att automatisera etablering och avetablering av användare och grupper. En SCIM-baserad etableringsappen tillhandahålls för de flesta program i Azure AD-galleriet. När du skapar appar för Azure AD kan utvecklare använda SCIM 2.0-användarhanterings-API:et för att skapa en SCIM-slutpunkt som integrerar Azure AD för etablering. Mer information finns i [Skapa en SCIM-slutpunkt och konfigurera användaretablering](../app-provisioning/use-scim-to-provision-users-and-groups.md).

Om du vill begära en automatisk Azure AD-etableringsappen för en app som för närvarande inte har en, fyller du i en [Azure Active Directory Application Request](https://aka.ms/aadapprequest).

## <a name="authorization"></a>Auktorisering

Autentiseringsuppgifter krävs för att Azure AD ska kunna ansluta till programmets API för användarhantering. När du konfigurerar om automatisk användaretablering för ett program måste du ange giltiga autentiseringsuppgifter. Du kan hitta autentiseringsuppgifter typer och krav för programmet genom att hänvisa till app självstudien. I Azure-portalen kan du testa autentiseringsuppgifterna genom att låta Azure AD försöka ansluta till appens etableringsapp med hjälp av de angivna autentiseringsuppgifterna.

Om SAML-baserad enkel inloggning också är konfigurerad för programmet är Azure AD:s interna lagringsgräns per program 1024 byte. Den här gränsen omfattar alla certifikat, hemliga token, autentiseringsuppgifter och relaterade konfigurationsdata som är associerade med en enda instans av ett program (kallas även en tjänsthuvudpost i Azure AD). När SAML-baserad enkel inloggning är konfigurerad förbrukar certifikatet som används för att signera SAML-token ofta över 50 % procent av utrymmet. Alla ytterligare objekt (hemliga token, URI:er, e-postadresser för meddelanden, användarnamn och lösenord) som du anger under installationen av användarens etablering kan överskrida lagringsgränsen. Mer information finns i [Spara administratörsautentiseringsuppgifter när du konfigurerar användaretablering](../manage-apps/application-provisioning-config-problem-storage-limit.md).

## <a name="mapping-attributes"></a>Mappa attribut

När du aktiverar användaretablering för ett SaaS-program från tredje part styr Azure-portalen sina attributvärden via attributmappningar. Mappningar bestämmer de användarattribut som flödar mellan Azure AD och målprogrammet när användarkonton etableras eller uppdateras.

Det finns en förkonfigurerad uppsättning attribut och attributmappningar mellan Azure AD-användarobjekt och varje SaaS-apps användarobjekt. Vissa appar hanterar andra typer av objekt tillsammans med användare, till exempel Grupper.

När du ställer in etablering är det viktigt att granska och konfigurera attributmappningar och arbetsflöden som definierar vilka användaregenskaper (eller gruppegenskaper) som flödar från Azure AD till programmet. Granska och konfigurera den matchande egenskapen (**Matcha objekt med det här attributet**) som används för att unikt identifiera och matcha användare/grupper mellan de två systemen.

Du kan anpassa standardattributmappningarna efter dina affärsbehov. Du kan därför ändra eller ta bort befintliga attributmappningar eller skapa nya attributmappningar. Mer information finns i [Anpassa användares etablering av attributmappningar för SaaS-program](../manage-apps/customize-application-attributes.md).

När du konfigurerar etablering till ett SaaS-program är en av de typer av attributmappningar som du kan ange en uttrycksmappning. För dessa mappningar måste du skriva ett skriptliknande uttryck som gör att du kan omvandla användarnas data till format som är mer acceptabla för SaaS-programmet. Mer information finns i [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md).

## <a name="scoping"></a>Miljöprövningens 
### <a name="assignment-based-scoping"></a>Tilldelningsbaserad omfång

För utgående etablering från Azure AD till ett SaaS-program [är](../manage-apps/assign-user-or-group-access-portal.md) det vanligaste sättet att avgöra vilka användare som är i omfång för etablering. Eftersom användartilldelningar också används för att aktivera enkel inloggning kan samma metod användas för att hantera både åtkomst och etablering. Tilldelningsbaserad omfång gäller inte för inkommande etableringsscenarier som Workday och Successfactors.

* **Grupper.** Med en Azure AD Premium-licensplan kan du använda grupper för att tilldela åtkomst till ett SaaS-program. När etableringsomfånget sedan är inställt på **Synkronisera endast tilldelade användare och grupper**etablerar azure AD-etableringstjänsten användare eller avetableringsanvändare baserat på om de är medlemmar i en grupp som har tilldelats programmet. Själva gruppobjektet etableras inte om inte programmet stöder gruppobjekt.

* **Dynamiska grupper.** Azure AD-tjänsten för användaretablering kan läsa och etablera användare i [dynamiska grupper](../users-groups-roles/groups-create-rule.md). Tänk på dessa varningar och rekommendationer:

  * Dynamiska grupper kan påverka prestanda för heltäckande etablering från Azure AD till SaaS-program.

  * Hur snabbt en användare i en dynamisk grupp etableras eller avetableras i ett SaaS-program beror på hur snabbt den dynamiska gruppen kan utvärdera medlemskapsändringar. Information om hur du kontrollerar bearbetningsstatus för en dynamisk grupp finns i [Kontrollera bearbetningsstatus för en medlemskapsregel](../users-groups-roles/groups-create-rule.md).

  * När en användare förlorar medlemskap i den dynamiska gruppen betraktas det som en avetableringshändelse. Tänk på det här scenariot när du skapar regler för dynamiska grupper.

* **Kapslade grupper.** Azure AD-användaretableringstjänsten kan inte läsa eller etablera användare i kapslade grupper. Tjänsten kan bara läsa och etablera användare som är omedelbara medlemmar i en uttryckligen tilldelad grupp. Den här begränsningen av "gruppbaserade tilldelningar till program" påverkar också enkel inloggning (se [Använda en grupp för att hantera åtkomst till SaaS-program](../users-groups-roles/groups-saasapps.md)). I stället direkt tilldela eller på annat sätt [omfattning i](define-conditional-rules-for-provisioning-user-accounts.md) de grupper som innehåller de användare som behöver etableras.

### <a name="attribute-based-scoping"></a>Attributbaserad omfång 

Du kan använda omfångsfilter för att definiera attributbaserade regler som avgör vilka användare som etableras i ett program. Den här metoden används ofta för inkommande etablering från HCM-program till Azure AD och Active Directory. Omfångsfilter konfigureras som en del av attributmappningarna för varje Azure AD-användaretableringskoppling. Mer information om hur du konfigurerar attributbaserade omfångsfilter finns i [Attributbaserad programetablering med omfångsfilter](define-conditional-rules-for-provisioning-user-accounts.md).

### <a name="b2b-guest-users"></a>B2B-användare (gäst)

Det är möjligt att använda Azure AD-användarens etableringstjänst för att etablera B2B-användare (eller gäst) användare i Azure AD till SaaS-program. För att B2B-användare ska kunna logga in på SaaS-programmet med Azure AD måste SaaS-programmet ha sin SAML-baserade enkel inloggningsfunktion konfigurerad på ett visst sätt. Mer information om hur du konfigurerar SaaS-program för att stödja inloggningar från B2B-användare finns i [Konfigurera SaaS-appar för B2B-samarbete](../b2b/configure-saas-apps.md).

Observera att userPrincipalName för en gästanvändare ofta lagras som@domain.com"alias#EXT# ". När userPrincipalName ingår i attributmappningarna som ett källattribut tas #EXT# bort från userPrincipalName. Om du kräver att #EXT# finns ersätter du userPrincipalName med originalUserPrincipalName som källattribut. 

## <a name="provisioning-cycles-initial-and-incremental"></a>Etableringscykler: Inledande och inkrementell

När Azure AD är källsystemet använder etableringstjänsten [frågan Använd delta för att spåra ändringar i Microsoft Graph-data](https://docs.microsoft.com/graph/delta-query-overview) för att övervaka användare och grupper. Etableringstjänsten kör en inledande cykel mot källsystemet och målsystemet, följt av periodiska inkrementella cykler.

### <a name="initial-cycle"></a>Inledande cykel

När etableringstjänsten startas kommer den första cykeln att:

1. Fråga alla användare och grupper från källsystemet och hämta alla attribut som definierats i [attributmappningarna](customize-application-attributes.md).

2. Filtrera de användare och grupper som returneras med hjälp av alla konfigurerade [tilldelningar](../manage-apps/assign-user-or-group-access-portal.md) eller [attributbaserade omfångsfilter](define-conditional-rules-for-provisioning-user-accounts.md).

3. När en användare tilldelas eller är i omfattning för etablering, frågar tjänsten målsystemet för en matchande användare med hjälp av de angivna [matchande attributen](customize-application-attributes.md#understanding-attribute-mapping-properties). Exempel: Om användarenPrincipnamn i källsystemet är det matchande attributet och mappar till userName i målsystemet, frågar etableringstjänsten målsystemet för användarnamn som matchar användarenPrincipnamnvärden i källsystemet.

4. Om en matchande användare inte hittas i målsystemet skapas den med hjälp av attributen som returneras från källsystemet. När användarkontot har skapats identifierar och cachelagrar etableringstjänsten målsystemets ID för den nya användaren. Det här ID:t används för att köra alla framtida åtgärder för den användaren.

5. Om en matchande användare hittas uppdateras den med hjälp av attributen som tillhandahålls av källsystemet. När användarkontot har matchats identifierar och cachelagrar etableringstjänsten målsystemets ID för den nya användaren. Det här ID:t används för att köra alla framtida åtgärder för den användaren.

6. Om attributmappningarna innehåller "referensattribut" gör tjänsten ytterligare uppdateringar på målsystemet för att skapa och länka de refererade objekten. En användare kan till exempel ha attributet "Manager" i målsystemet, som är länkat till en annan användare som skapats i målsystemet.

7. Framhärdar en vattenstämpel i slutet av den inledande cykeln, vilket ger utgångspunkten för de senare inkrementella cyklerna.

Vissa program som ServiceNow, G Suite och Box stöder inte bara etablering av användare, utan även etableringsgrupper och deras medlemmar. I dessa fall, om gruppetablering är aktiverat i [mappningarna,](customize-application-attributes.md)synkroniserar etableringstjänsten användarna och grupperna och synkroniserar sedan gruppmedlemskapen senare.

### <a name="incremental-cycles"></a>Inkrementella cykler

Efter den inledande cykeln kommer alla andra cykler:

1. Fråga källsystemet för alla användare och grupper som har uppdaterats sedan den senaste vattenstämpeln lagrades.

2. Filtrera de användare och grupper som returneras med hjälp av alla konfigurerade [tilldelningar](../manage-apps/assign-user-or-group-access-portal.md) eller [attributbaserade omfångsfilter](define-conditional-rules-for-provisioning-user-accounts.md).

3. När en användare tilldelas eller är i omfattning för etablering, frågar tjänsten målsystemet för en matchande användare med hjälp av de angivna [matchande attributen](customize-application-attributes.md#understanding-attribute-mapping-properties).

4. Om en matchande användare inte hittas i målsystemet skapas den med hjälp av attributen som returneras från källsystemet. När användarkontot har skapats identifierar och cachelagrar etableringstjänsten målsystemets ID för den nya användaren. Det här ID:t används för att köra alla framtida åtgärder för den användaren.

5. Om en matchande användare hittas uppdateras den med hjälp av attributen som tillhandahålls av källsystemet. Om det är ett nyligen tilldelat konto som matchas identifierar och cachelagrar etableringstjänsten målsystemets ID för den nya användaren. Det här ID:t används för att köra alla framtida åtgärder för den användaren.

6. Om attributmappningarna innehåller "referensattribut" gör tjänsten ytterligare uppdateringar på målsystemet för att skapa och länka de refererade objekten. En användare kan till exempel ha attributet "Manager" i målsystemet, som är länkat till en annan användare som skapats i målsystemet.

7. Om en användare som tidigare var i omfång för etablering tas bort från omfånget, inklusive att inte tilldelas, inaktiverar tjänsten användaren i målsystemet via en uppdatering.

8. Om en användare som tidigare var i omfång för etablering är inaktiverad eller mjuk-tas bort i källsystemet, tjänsten inaktiverar användaren i målsystemet via en uppdatering.

9. Om en användare som tidigare var i omfång för etablering är hårt borttagen i källsystemet, tar tjänsten bort användaren i målsystemet. I Azure AD tas användare bort hårt 30 dagar efter att de har tagits bort för alla.

10. Framhärdar en ny vattenstämpel i slutet av den inkrementella cykeln, som ger utgångspunkten för de senare inkrementella cyklerna.

> [!NOTE]
> Du kan också inaktivera åtgärderna **Skapa,** **Uppdatera**eller **Ta bort** med hjälp av kryssrutorna **Målobjektåtgärder** i avsnittet [Mappningar.](customize-application-attributes.md) Logiken för att inaktivera en användare under en uppdatering styrs också via en attributmappning från ett fält som "accountEnabled".

Etableringstjänsten fortsätter att köra inkrementella stegcykler på obestämd tid, med intervall som definieras i [självstudien som är specifik för varje program](../saas-apps/tutorial-list.md). Inkrementella cykler fortsätter tills någon av följande händelser inträffar:

- Tjänsten stoppas manuellt med hjälp av Azure-portalen eller med hjälp av lämpligt Microsoft Graph API-kommando.
- En ny inledande cykel utlöses med alternativet **Rensa tillstånd och omstart** i Azure-portalen eller med hjälp av lämpligt Microsoft Graph API-kommando. Den här åtgärden rensar alla lagrade vattenstämpel och gör att alla källobjekt utvärderas igen.
- En ny inledande cykel utlöses på grund av en ändring av attributmappningar eller omfångsfilter. Den här åtgärden rensar också alla lagrade vattenstämpel och gör att alla källobjekt utvärderas igen.
- Etableringsprocessen går i karantän (se nedan) på grund av en hög felfrekvens och förblir i karantän i mer än fyra veckor. I så fall inaktiveras tjänsten automatiskt.

### <a name="errors-and-retries"></a>Fel och återförsök

Om ett fel i målsystemet förhindrar att en enskild användare läggs till, uppdateras eller tas bort i målsystemet görs åtgärden på nytt i nästa synkroniseringscykel. Om användaren fortsätter att misslyckas börjar återförsöken ske med en reducerad frekvens, vilket gradvis skalas ned till bara ett försök per dag. För att lösa felet måste administratörer kontrollera [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) för att fastställa grundorsaken och vidta lämpliga åtgärder. Vanliga fel kan vara:

- Användare som inte har ett attribut ifyllt i källsystemet som krävs i målsystemet
- Användare som har ett attributvärde i källsystemet för vilka det finns en unik begränsning i målsystemet och samma värde finns i en annan användarpost

Lös dessa fel genom att justera attributvärdena för den berörda användaren i källsystemet eller genom att justera attributmappningarna så att de inte orsakar konflikter.

### <a name="quarantine"></a>Karantän

Om de flesta eller alla anrop som görs mot målsystemet konsekvent misslyckas på grund av ett fel (till exempel ogiltiga administratörsautentiseringsuppgifter) går etableringsjobbet in i ett "karantäntillstånd". Det här tillståndet anges i [komredovisningsrapporten](../manage-apps/check-status-user-account-provisioning.md) för etablering och via e-post om e-postmeddelanden har konfigurerats i Azure-portalen.

När i karantän, frekvensen av inkrementella cykler gradvis minskas till en gång per dag.

Etableringsjobbet avslutar karantänen när alla fel som inte har åtgärdats och nästa synkroniseringscykel startar. Om etableringsjobbet förblir i karantän i mer än fyra veckor inaktiveras etableringsjobbet. Läs mer här om karantänstatus [här](../manage-apps/application-provisioning-quarantine-status.md).

### <a name="how-long-provisioning-takes"></a>Hur lång tid tar etableringen?

Prestanda beror på om etableringsjobbet kör en inledande etableringscykel eller en inkrementell cykel. Mer information om hur lång etablering tar och hur du övervakar etableringstjänstens status finns i [Kontrollera status för användaretablering](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>Så här tar du upp om användarna etableras på rätt sätt

Alla åtgärder som körs av tjänsten för användaretablering registreras i Azure [AD-etableringsloggarna (förhandsversion)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Loggarna innehåller alla läs- och skrivåtgärder som görs till käll- och målsystemen och användardata som lästes eller skrevs under varje åtgärd. Information om hur du läser etableringsloggarna i Azure-portalen finns i [rapporteringsguiden](../manage-apps/check-status-user-account-provisioning.md)för etablering .

## <a name="de-provisioning"></a>Avetablering

Azure AD-etableringstjänsten håller käll- och målsystem synkroniserade genom att avetableringskonton när användare inte längre ska ha åtkomst. 

Azure AD-etableringstjänsten tar bort en användare i ett program när programmet suupportar mjuka borttagningar (uppdateringsbegäran med aktiv = false) och någon av följande händelser inträffar:

* Användarkontot tas bort i Azure AD
*   Användaren avtilldelas från programmet
*   Användaren uppfyller inte längre ett omfångsfilter och går utanför omfånget
    * Som standard tar Azure AD-etableringstjänsten bort eller inaktiverar användare som inte omfattas. Om du vill åsidosätta det här standardbeteendet kan du ange en flagga för att [hoppa över borttagningar utanför scopet](../app-provisioning/skip-out-of-scope-deletions.md).
*   Egenskapen AccountEnabled är inställd på Falskt

Om en av ovanstående fyra händelser inträffar och målprogrammet inte stöder mjuka borttagningar, skickar etableringstjänsten en DELETE-begäran om att ta bort användaren permanent från appen. 

30 dagar efter att en användare har tagits bort i Azure AD tas de bort permanent från klienten. Nu skickar etableringstjänsten en DELETE-begäran om att ta bort användaren permanent i programmet. När som helst under 30-dagarsfönstret kan du [manuellt ta bort en användare permanent](../fundamentals/active-directory-users-restore.md), som skickar en borttagningsbegäran till programmet.

Om du ser ett attribut som är IsSoftDeleted i attributmappningarna används det för att fastställa användarens tillstånd och om en uppdateringsbegäran ska skickas med aktiv = false för att ta bort användaren mjukt. 

## <a name="next-steps"></a>Efterföljande moment

[Planera en distribution med automatisk användaretablering](../app-provisioning/plan-auto-user-provisioning.md)

[Konfigurera etablering för en galleriapp](../manage-apps/configure-automatic-user-provisioning-portal.md)

[Skapa en SCIM-slutpunkt och konfigurera etablering när du skapar en egen app](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[Felsöka problem med att konfigurera och etablera användare i ett program](../manage-apps/application-provisioning-config-problem.md).
