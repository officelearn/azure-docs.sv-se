---
title: Planera en automatisk distribution för användaretablering för Azure Active Directory
description: Vägledning för planering och körning av automatisk användaretablering
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d2f284fddfc49632e467adbf5877856b40a81dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522418"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Planera en distribution med automatisk användaretablering

Många organisationer förlitar sig på programvara som en tjänst (SaaS) program som ServiceNow, Zscaler och Slack för slutanvändarens produktivitet. Historiskt IT-personal har förlitat sig på manuell etablering metoder såsom ladda upp CSV-filer, eller använda anpassade skript för att säkert hantera användaridentiteter i varje SaaS-program. Dessa processer är felbenägna, osäkra och svåra att hantera.

Azure Active Directory (Azure AD) automatisk användaretablering förenklar den här processen genom att på ett säkert sätt automatisera skapandet, underhållet och borttagningen av användaridentiteter i SaaS-program baserat på affärsregler. Med den här automatiseringen kan du effektivt skala dina identitetshanteringssystem på både molnbaserade och hybridmiljöer när du utökar deras beroende av molnbaserade lösningar.

Se [Automatisera etablering av användare och avetablering till SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md) för att bättre förstå funktionerna.

## <a name="learn"></a>Läs mer

Tillhandahållande av användare skapar en grund för pågående identitetsstyrning och förbättrar kvaliteten på affärsprocesser som är beroende av auktoritära identitetsdata.

### <a name="key-benefits"></a>Viktiga fördelar

De viktigaste fördelarna med att aktivera automatisk användaretablering är:

* **Ökad produktivitet**. Du kan hantera användaridentiteter i SaaS-program med ett enda gränssnitt för användaretableringshantering. Det här gränssnittet har en enda uppsättning etableringsprinciper.

* **Hantera risker**. Du kan öka säkerheten genom att automatisera ändringar baserat på medarbetarstatus eller gruppmedlemskap som definierar roller och/eller åtkomst.

* **Ta itu med efterlevnad och styrning**. Azure AD stöder inbyggda granskningsloggar för varje begäran om användaretablering. Begäranden körs i både käll- och målsystemen. På så sätt kan du spåra vem som har åtkomst till program från en enda skärm.

* **Minska kostnaderna**. Automatisk etablering av användare minskar kostnaderna genom att undvika ineffektivitet och mänskliga fel i samband med manuell etablering. Det minskar behovet av specialutvecklade lösningar, skript och granskningsloggar för användare.

### <a name="licensing"></a>Licensiering

Azure AD ger självbetjäningsintegrering av alla program med hjälp av mallar som finns på programgalleriets meny. En fullständig lista över licenskrav finns på [azure AD-licensieringssidan](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Licensiering av program

Du behöver lämpliga licenser för de program som du vill etablera automatiskt. Diskutera med programägarna om de användare som tilldelats programmet har rätt licenser för sina programroller. Om Azure AD hanterar automatisk etablering baserat på roller måste de roller som tilldelats i Azure AD anpassas till programlicenser. Felaktiga licenser som ägs i programmet kan leda till fel under etableringen/uppdateringen av en användare.

### <a name="terms"></a>Villkor

I den här artikeln används följande termer:

* CRUD-åtgärder - Åtgärder som vidtas på användarkonton: Skapa, Läsa, Uppdatera, Ta bort.

* Enkel inloggning (SSO) - Möjligheten för en användare att logga in en gång och komma åt alla SSO-aktiverade program. I samband med etablering av användare är SSO ett resultat av att användare har ett enda konto för att komma åt alla system som använder automatisk användaretablering.

* Källsystem - Lagringsplatsen för användare som Azure AD-avsättningarna från. Azure AD är källsystemet för de flesta förintegrerade etableringsappar. Det finns dock vissa undantag för molnprogram som SAP, Workday och AWS. Se till exempel [Användaretablering från Arbetsdag till AD](../saas-apps/workday-inbound-tutorial.md).

* Målsystem - Lagringsplatsen för användare som Azure AD-avsättningar till. Target-systemet är vanligtvis ett SaaS-program som ServiceNow, Zscaler och Slack. Målsystemet kan också vara ett lokalt system som AD.

* [System för scim (Cross-domain Identity Management)](https://aka.ms/scimoverview) - En öppen standard som möjliggör automatisering av användaretablering. SCIM kommunicerar användaridentitetsdata mellan identitetsleverantörer som Microsoft och tjänsteleverantörer som Salesforce eller andra SaaS-appar som kräver användaridentitetsinformation.

### <a name="training-resources"></a>Utbildningsresurser

| Resurser| Länk och beskrivning |
| - | - |
| Webbseminarier på begäran| [Hantera dina Företagsprogram med Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Lär dig mer om hur Azure AD kan hjälpa dig att uppnå SSO till dina SaaS-program för företag och metodtips för att kontrollera åtkomsten. |
| Videoklipp| [Vad är användaretablering i Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) <br> [Hur distribuerar du användaretablering i Active Azure Directory?](https://youtu.be/pKzyts6kfrw) <br> [Integrera Salesforce med Azure AD: Så här automatiserar du etablering av användare](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Onlinekurser| SkillUp Online: [Hantera identiteter](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Lär dig hur du integrerar Azure AD med många SaaS-program och skyddar användaråtkomst till dessa program. |
| Böcker| [Modern autentisering med Azure Active Directory för webbprogram (utvecklarreferens) 1st Edition](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Detta är en auktoritativ, djupdykningsguide för att skapa Active Directory-autentiseringslösningar för dessa nya miljöer. |
| självstudiekurserna| Se [listan över självstudier om hur du integrerar SaaS-appar med Azure AD](../saas-apps/tutorial-list.md). |
| VANLIGA FRÅGOR OCH SVAR| [Vanliga frågor om](../app-provisioning/user-provisioning.md) automatisk etablering av användare |

### <a name="solution-architectures"></a>Lösningsarkitekturer

Azure AD-etableringstjänsten etablerar användare till SaaS-appar och andra system genom att ansluta till API-slutpunkter för användarhantering som tillhandahålls av varje programleverantör. Dessa API-slutpunkter för användarhantering gör det möjligt för Azure AD att programmässigt skapa, uppdatera och ta bort användare.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Automatisk användaretablering för hybridföretag

I det här exemplet skapas användare och grupper i en HR-databas som är ansluten till en lokal katalog. Azure AD-etableringstjänsten hanterar automatisk användaretablering till mål-SaaS-programmen.

 ![etablering av användare](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Beskrivning av arbetsflödet:**

1. Användare/grupper skapas i ett lokalt HR-program/-system, till exempel SAP. 

1. **Azure AD Connect-agenten** kör schemalagda synkroniseringar av identiteter (användare och grupper) från den lokala AD till Azure AD.

1. **Azure AD-etableringstjänsten** börjar en [inledande cykel](../app-provisioning/user-provisioning.md) mot källsystemet och målsystemet. 

1. **Azure AD-etableringstjänst** frågar källsystemet för alla användare och grupper som ändrats sedan den första cykeln och skickar ändringar i [inkrementella cykler](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Automatisk användaretablering för företag med endast molnet

I det här exemplet sker användarskapande i Azure AD och Azure AD-etableringstjänsten hanterar automatisk användaretablering till målprogram (SaaS).

![Bild 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Beskrivning av arbetsflödet:**

1. Användare/grupper skapas i Azure AD.

1. **Azure AD-etableringstjänsten** börjar en [inledande cykel](../app-provisioning/user-provisioning.md) mot källsystemet och målsystemet. 

1. **Azure AD-etableringstjänst** frågar källsystemet för alla användare och grupper som uppdaterats sedan den inledande cykeln och utför alla [inkrementella cykler](../app-provisioning/user-provisioning.md).

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Automatisk användaretablering för moln-HR-program 

I det här exemplet skapas användare och eller grupper i ett moln-HR-program som Workday och SuccessFactors. Azure AD-etableringstjänsten och Azure AD Connect-etableringsagenten etablerar användardata från cloud HR-appklienten till AD. När kontona har uppdaterats i AD synkroniseras de med Azure AD via Azure AD Connect och e-postadresser och användarnamnsattribut kan skrivas tillbaka till moln-HR-appklientinnehavaren.

![Bild 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **HR-teamet** utför transaktionerna i molnet HR-app klienten.
2.  **Azure AD-etableringstjänsten** kör schemalagda cykler från cloud HR-appklienten och identifierar ändringar som måste bearbetas för synkronisering med AD.
3.  **Azure AD-etableringstjänsten** anropar Azure AD Connect-etableringsagenten med en begäranhetstjänst som innehåller AD-konto skapa/uppdatera/aktivera/inaktivera åtgärder.
4.  **Azure AD Connect-etableringsagenten** använder ett tjänstkonto för att hantera AD-kontodata.
5.  **Azure AD Connect** kör deltasynkronisering för att hämta uppdateringar i AD.
6.  **AD-uppdateringar** synkroniseras med Azure AD. 
7.  **Azure AD-etableringstjänsten** skriver tillbaka e-postattribut och användarnamn från Azure AD till cloud HR-appklientinnehavaren.

## <a name="plan-the-deployment-project"></a>Planera distributionsprojektet

Tänk på dina organisationsbehov för att fastställa strategin för att distribuera användaretablering i din miljö.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknikprojekt misslyckas är det vanligtvis på grund av felaktiga förväntningar på påverkan, resultat och ansvar. För att undvika dessa fallgropar, se till att [du engagerar rätt intressenter](https://aka.ms/deploymentplans) och att intressentrollerna i projektet är väl förstådda genom att dokumentera intressenterna och deras projektinmatning och kontoförbehåll.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikation är avgörande för att en ny tjänst ska lyckas. Kommunicera proaktivt med användarna hur deras upplevelse kommer att förändras, när den ändras och hur de får support om de får problem.

### <a name="plan-a-pilot"></a>Planera en pilot

Vi rekommenderar att den första konfigurationen av automatisk användaretablering är i en testmiljö med en liten delmängd användare innan den skalas till alla användare i produktion. Se [metodtips](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) för att köra en pilot.

#### <a name="best-practices-for-a-pilot"></a>Bästa praxis för en pilot  

En pilot kan du testa med en liten grupp innan du distribuerar en kapacitet för alla. Se till att varje användningsfall i organisationen testas noggrant som en del av testningen.

I din första våg, mål IT, användbarhet och andra lämpliga användare som kan testa och ge feedback. Använd den här feedbacken för att vidareutveckla den kommunikation och de instruktioner du skickar till användarna och för att ge insikter om vilka typer av problem som supportpersonalen kan se.

Bredda distributionen till större grupper av användare genom att öka omfattningen av den eller de grupper som är avsedda för. Detta kan göras genom [dynamiskt gruppmedlemskap](../users-groups-roles/groups-dynamic-membership.md)eller genom att manuellt lägga till användare i målgruppen/målgrupperna.

## <a name="plan-application-connections-and-administration"></a>Planera programanslutningar och administration

Använd Azure AD-portalen för att visa och hantera alla program som stöder etablering. Se [Hitta dina appar i portalen](../app-provisioning/configure-automatic-user-provisioning-portal.md).

### <a name="determine-the-type-of-connector-to-use"></a>Bestäm vilken typ av anslutning som ska användas

De faktiska stegen som krävs för att aktivera och konfigurera automatisk etablering varierar beroende på programmet. Om programmet som du vill etablera automatiskt visas i [Azure AD SaaS-appgalleriet](../saas-apps/tutorial-list.md)bör du välja den [appspecifika integrationshandledningen](../saas-apps/tutorial-list.md) för att konfigurera den förintegrerade användaretableringsappen.

Om inte, följ stegen nedan:

1. [Skapa en begäran om](../develop/howto-app-gallery-listing.md) en förintegrerad anslutningsapp för användare. Vårt team kommer att arbeta med dig och applikationsutvecklaren för att komma ombord på din applikation till vår plattform om den stöder SCIM.

1. Använd stöd för generiska användaretablering av [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) för appen. Detta är ett krav för Azure AD att etablera användare till appen utan en förintegrerad etableringsappen.

1. Om programmet kan använda BYOA SCIM-anslutningen, se sedan [BYOA SCIM integration handledning](../app-provisioning/use-scim-to-provision-users-and-groups.md) för att konfigurera BYOA SCIM-anslutningen för programmet.

Mer information finns i [Vilka program och system kan jag använda med Azure AD automatisk användaretablering?](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>Samla in information för att auktorisera åtkomst till program

Att ställa in automatisk användaretablering är en process per program. För varje program måste du ange [administratörsautentiseringsuppgifter](../app-provisioning/configure-automatic-user-provisioning-portal.md) för att ansluta till målsystemets slutpunkt för användarhantering.

Bilden nedan visar en version av de administratörsautentiseringsuppgifter som krävs:

![Etableringsskärmen för att hantera inställningar för etablering av användarkonton](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Vissa program kräver administratörens användarnamn och lösenord, men andra kan kräva en innehavartoken.

## <a name="plan-user-and-group-provisioning"></a>Planera etablering av användare och grupper

Om du aktiverar användaretablering för företagsappar styr [Azure-portalen](https://portal.azure.com/) sina attributvärden genom attributmappning.

### <a name="determine-operations-for-each-saas-app"></a>Bestäm åtgärder för varje SaaS-app

Varje program kan ha unika användar- eller gruppattribut som måste mappas till attributen i din Azure AD. Programmet kanske bara har en delmängd av CRUD-åtgärder tillgängliga.

För varje program dokumenterar du följande information:

* CRUD-etableringsåtgärder som ska utföras på användaren och eller Gruppera objekt för målsystemen. Till exempel kanske varje SaaS-appföretagare inte vill ha alla möjliga åtgärder.

* Attribut som är tillgängliga i källsystemet

* Attribut som är tillgängliga i målsystemet

* Kartläggning av attribut mellan system.

### <a name="choose-which-users-and-groups-to-provision"></a>Välj vilka användare och grupper som ska etableras

Innan du implementerar automatisk användaretablering måste du bestämma vilka användare och grupper som ska etableras i ditt program.

* Använd [omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) för att definiera attributbaserade regler som avgör vilka användare som etableras i ett program.

* Använd sedan [användar- och grupptilldelningar](../manage-apps/assign-user-or-group-access-portal.md) efter behov för ytterligare filtrering.

### <a name="define-user-and-group-attribute-mapping"></a>Definiera mappning av användar- och gruppattribut

Om du vill implementera automatisk användaretablering måste du definiera de användar- och gruppattribut som behövs för programmet. Det finns en förkonfigurerad uppsättning attribut och [attributmappningar](../app-provisioning/configure-automatic-user-provisioning-portal.md) mellan Azure AD-användarobjekt och varje SaaS-programs användarobjekt. Alla SaaS-appar aktiverar inte gruppattribut.

Azure AD stöder genom direkt attribut-till-attributmappning, tillhandahålla konstanta värden eller [skrivuttryck för attributmappningar](../app-provisioning/functions-for-customizing-application-data.md). Denna flexibilitet ger dig fin kontroll över vad som kommer att fyllas i det riktade systemets attribut. Du kan använda [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) och Graph Explorer för att exportera attributmappningar och schema för användare till en JSON-fil och importera den tillbaka till Azure AD.

Mer information finns i [Anpassa användaresetablering av attributmappningar för SaaS-program i Azure Active Directory](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Särskilda överväganden för tillhandahållande av användare

Tänk på följande för att minska problem efter distributionen:

* Kontrollera att de attribut som används för att mappa användar-/gruppobjekt mellan käll- och målprogram är elastiska. De bör inte göra att användare/grupper etableras felaktigt om attributen ändras (till exempel flyttar en användare till en annan del av företaget).

* Program kan ha särskilda begränsningar och/eller krav som måste uppfyllas för att användarens etablering ska fungera korrekt. Slack trunkerar till exempel värden för vissa attribut. Se [automatiska självstudier för användaretablering](../saas-apps/tutorial-list.md) som är specifika för varje program.

* Bekräfta schemakonsekvens mellan käll- och målsystem. Vanliga problem är attribut som UPN eller e-post som inte matchar. Till exempel UPN i Azure *john_smith@contoso.com* AD set som och *jsmith@contoso.com*i appen, det är . Mer information finns i [Användar- och gruppschemareferensen](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Planera testning och säkerhet

I varje skede av distributionen se till att du testar att resultaten är som förväntat och granska etableringscyklerna.

### <a name="plan-testing"></a>Planera testning

När du har konfigurerat automatisk användaretablering för programmet kör du testfall för att verifiera att den här lösningen uppfyller organisationens krav.

| Scenarier| Förväntat resultat |
| - | - |
| Användaren läggs till i en grupp som tilldelats målsystemet | Användarobjektet etableras i målsystemet. <br>Användaren kan logga in på målsystemet och utföra önskade åtgärder. |
| Användaren tas bort från en grupp som har tilldelats målsystemet | Användarobjektet avetableras i målsystemet.<br>Användaren kan inte logga in på målsystemet. |
| Användarinformation uppdateras i Azure AD av vilken metod som helst | Uppdaterade användarattribut återspeglas i målsystemet efter en inkrementell cykel |
| Användaren har inte omfattning | Användarobjektet är inaktiverat eller borttaget. <br>Det här beteendet åsidosätts för [etablering av arbetsdag](skip-out-of-scope-deletions.md). |

### <a name="plan-security"></a>Planera säkerhet

Det är vanligt att en säkerhetsgranskning krävs som en del av en distribution. Om du behöver en säkerhetsgranskning läser du de många Azure [AD-faktablad](https://www.microsoft.com/download/details.aspx?id=36391) som ger en översikt för identitet som en tjänst.

### <a name="plan-rollback"></a>Återställ plan

Om implementeringen av automatisk etablering av användare inte fungerar som önskat i produktionsmiljön kan följande återställningssteg nedan hjälpa dig att återgå till ett tidigare fungerande tillstånd:

1. Granska [den sammanfattande etableringsrapporten](../app-provisioning/check-status-user-account-provisioning.md) och [etableringsloggarna](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) för att ta reda på vilka felaktiga åtgärder som har inträffat för de berörda användarna och/eller grupperna.

1. Använd etableringsgranskningsloggar för att fastställa det senast fungerande tillståndet för de användare och/eller grupper som påverkas. Granska även källsystemen (Azure AD eller AD).

1. Arbeta med programägaren för att uppdatera de användare och/eller grupper som påverkas direkt i programmet med de senast fungerande tillståndsvärdena.

## <a name="deploy-automatic-user-provisioning-service"></a>Distribuera automatisk tjänst för tillhandahållande av användare

Välj de steg som anpassar sig till dina lösningskrav.

### <a name="prepare-for-the-initial-cycle"></a>Förbered dig för den inledande cykeln

När Azure AD-etableringstjänsten körs för första gången skapar den inledande cykeln mot källsystemet och målsystemen en ögonblicksbild av alla användarobjekt för varje målsystem.

När du aktiverar automatisk etablering för ett program kan den inledande cykeln ta allt från 20 minuter till flera timmar. Varaktigheten beror på storleken på Azure AD-katalogen och antalet användare i omfånget för etablering. Se [Så här förbättrar du etableringsprestanda](../app-provisioning/application-provisioning-when-will-provisioning-finish.md).

Etableringstjänsten lagrar tillståndet för båda systemen efter den inledande cykeln, vilket förbättrar prestandan för efterföljande inkrementella cykler.

### <a name="configure-automatic-user-provisioning"></a>Konfigurera automatisk användaretablering

Använd [Azure-portalen](https://portal.azure.com/) för att hantera automatisk etablering och avetablering av användarkonton för program som stöder den. Följ stegen i [Hur ställer jag in automatisk etablering till ett program?](../app-provisioning/user-provisioning.md)

Azure AD-användaretableringstjänsten kan också konfigureras och hanteras med Hjälp av [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Hantera automatisk etablering av användare

Nu när du har distribuerat måste du hantera lösningen.

### <a name="monitor-user-provisioning-operation-health"></a>Övervaka hälsotillstånd för åtgärder för etablering av användare

Efter en lyckad [inledande cykel](../app-provisioning/user-provisioning.md)körs inkrementella uppdateringar på obestämd tid med intervall som är specifika för varje program tills en av följande händelser inträffar:

* Tjänsten stoppas manuellt och en ny inledande cykel utlöses med hjälp av [Azure-portalen](https://portal.azure.com/)eller med hjälp av lämpligt [Microsoft Graph API-kommando.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* En ny inledande cykel utlöses av en ändring av attributmappningar eller omfångsfilter.

* Etableringsprocessen går i karantän på grund av en hög felfrekvens och förblir i karantän i mer än fyra veckor när den inaktiveras automatiskt.

Om du vill granska dessa händelser och alla andra aktiviteter som utförs av etableringstjänsten läser du Azure [AD-etableringsloggar](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

Om du vill förstå hur lång tid etableringscyklerna tar och övervakar förloppet för etableringsjobbet kan du [kontrollera statusen för användaretablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

### <a name="gain-insights-from-reports"></a>Få insikter från rapporter

Azure AD kan ge [ytterligare insikter](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om organisationens användaretableringsanvändning och driftshälsa via granskningsloggar och rapporter.

Administratörer bör kontrollera den sammanfattande etableringsrapporten för att övervaka driftshälsan för etableringsjobbet. Alla aktiviteter som utförs av etableringstjänsten registreras i Azure AD-granskningsloggarna. Se [Självstudiekurs: Rapportering om automatisk etablering av användarkonton](../app-provisioning/check-status-user-account-provisioning.md).

Vi rekommenderar att du övertar ägarskapet av och använder dessa rapporter på en kadens som uppfyller organisationens krav. Azure AD behåller de flesta granskningsdata i 30 dagar.

### <a name="troubleshoot"></a>Felsöka

Se följande länkar för att felsöka eventuella problem som kan uppstå under etableringen:

* [Problem med att konfigurera användaretablering till ett Azure AD Gallery-program](../app-provisioning/application-provisioning-config-problem.md)

* [Synkronisera ett attribut från din lokala Active Directory till Azure AD för etablering till ett program](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Användaretablering till ett Azure AD Gallery-program tar timmar eller mer](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [Problem med att spara administratörsuppgifter när du konfigurerar användaretablering till ett Azure Active Directory Gallery-program](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [Inga användare etableras i ett Azure AD Gallery-program](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Fel uppsättning användare etableras i ett Azure AD Gallery-program](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>Användbar dokumentation

* [Skriva uttryck för attributmappningar](../app-provisioning/functions-for-customizing-application-data.md)

* [Api-översikt för Azure AD-synkronisering](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [Hoppa över radering av användarkonton som inte omfattas](skip-out-of-scope-deletions.md)

* [Etablerande agent för Azure AD Connect: Versionsversionshistorik](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Resurser

* [Ge feedback på produkten](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Håll dig uppdaterad om vad som är nytt med Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Stack överflöd Azure AD forum](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Nästa steg
* [Konfigurera automatisk etablering av användare](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Exportera eller importera etableringskonfigurationen med hjälp av Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md)

* [Skriva uttryck för attributmappningar i Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md)
