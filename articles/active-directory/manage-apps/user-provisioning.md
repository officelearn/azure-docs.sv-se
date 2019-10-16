---
title: Automatiserad SaaS app User-etablering i Azure AD | Microsoft Docs
description: En introduktion till hur du kan använda Azure AD för att automatiskt etablera, avetablera och kontinuerligt uppdatera användar konton över flera SaaS-program från tredje part.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ff6d9e33e15aa04adfa03705172166492f87e30
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330027"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory

Azure Active Directory (Azure AD) gör att du kan automatisera skapandet, underhåll och borttagning av användar identiteter i moln program ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), till exempel Dropbox, Salesforce, ServiceNow och mycket annat. Detta kallas automatisk användar etablering för SaaS-appar.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

Med den här funktionen kan du:

- Skapa automatiskt nya konton i rätt system för nya personer när de ansluter till ditt team eller din organisation.
- Inaktivera konton automatiskt i rätt system när personer lämnar teamet eller organisationen.
- Se till att identiteterna i dina appar och system hålls uppdaterade baserat på ändringar i katalogen eller i personal systemet.
- Etablera andra objekt än användar objekt, till exempel grupper, för program som stöder dem.

Automatisk användar etablering innehåller även den här funktionen:

- Möjlighet att matcha befintliga identiteter mellan käll-och mål system.
- Anpassningsbara mappningar av attribut som definierar vilka användar data som ska flöda från käll systemet till mål systemet.
- Valfria e-postaviseringar för etablering av fel.
- Rapporterings-och aktivitets loggar som hjälper dig med övervakning och fel sökning.

## <a name="why-use-automated-provisioning"></a>Varför ska jag använda automatisk etablering?

Några vanliga orsaker till att använda den här funktionen är:

- Undvika kostnader, ineffektivitet och mänskligt fel som är kopplat till manuella etablerings processer.
- Undvik kostnader som är kopplade till värd tjänster och underhåll av anpassade och utvecklade etablerings lösningar och skript.
- Att skydda din organisation genom att genast ta bort användarnas identiteter från viktiga SaaS-appar när de lämnar organisationen.
- Du kan enkelt importera ett stort antal användare till ett visst SaaS-program eller system.
- Med en enda uppsättning principer kan du bestämma vem som är etablerad och vem som kan logga in på en app.

## <a name="how-does-automatic-provisioning-work"></a>Hur fungerar automatisk etablering?

**Azure AD Provisioning-tjänsten** etablerar användare för att SaaS appar och andra system genom att ansluta till användar hanterings-API-slutpunkter som tillhandahålls av varje program leverantör. Dessa API-slutpunkter för användar hantering tillåter Azure AD att program mässigt skapa, uppdatera och ta bort användare. För valda program kan etablerings tjänsten också skapa, uppdatera och ta bort ytterligare ID-relaterade objekt, till exempel grupper och roller.

![Azure AD Provisioning-tjänsten @ no__t-1*bild 1: Azure AD Provisioning-tjänsten*

@no__t 0Outbound User Provisioning Workflow @ no__t-1*bild 2: "utgående" användar etablerings arbets flöde från Azure AD till populära SaaS-program*

![Inbound användar etablering arbets flöde @ no__t-1*bild 3: "inkommande" användar etablerings arbets flöde från populära HCM-program (Human kapital Management) till Azure Active Directory och Windows Server Active Directory*

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Vilka program och system kan jag använda med automatisk användar etablering i Azure AD?

Azure AD innehåller Förintegrerad support för många populära SaaS-appar och personal system, och allmänt stöd för appar som implementerar vissa delar av [SCIM 2,0-standarden](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

### <a name="pre-integrated-applications"></a>Förintegrerade program

En lista över alla program för vilka Azure AD stöder en förintegrerad etablerings koppling finns i [listan över program självstudier för användar etablering](../saas-apps/tutorial-list.md).

Kontakta Azure AD Engineering-teamet för att begära etablerings stöd för ytterligare program, skicka ett meddelande via [Azure Active Directory feedback-forumet](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> För att ett program ska kunna stödja automatisk användar etablering måste det först tillhandahålla nödvändiga API: er för användar hantering som gör det möjligt för externa program att automatisera skapande, underhåll och borttagning av användare. Därför är inte alla SaaS-appar kompatibla med den här funktionen. För appar som har stöd för API: er för användar hantering kan Azure AD Engineering-teamet sedan skapa en etablerings anslutning till dessa appar och det här arbetet prioriteras av behoven hos aktuella och potentiella kunder.

### <a name="connecting-applications-that-support-scim-20"></a>Ansluta program som stöder SCIM 2,0

Information om hur du kan ansluta program som implementerar SCIM 2,0-baserade API: er för användar hantering finns i [använda scim för att automatiskt etablera användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md).

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hur gör jag för att konfigurera automatisk etablering till ett program?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Använd Azure Active Directory Portal för att konfigurera Azure AD Provisioning-tjänsten för ett valt program.

1. Öppna **[Azure Active Directory-portalen](https://aad.portal.azure.com)** .
1. Välj **företags program** i det vänstra fönstret. En lista över alla konfigurerade appar visas.
1. Välj **+ nytt program** för att lägga till ett program. Lägg till något av följande beroende på ditt scenario:

   - Alternativet **Lägg till din egen app** stöder anpassade scim-integrationer.
   - Alla program i avsnittet **Lägg till i galleriet** > **aktuella program** stöder automatisk etablering. Se [listan över program självstudier för användar etablering](../saas-apps/tutorial-list.md) för ytterligare.

1. Ange information och välj **Lägg till**. Den nya appen läggs till i listan över företags program och öppnas på program hanterings skärmen.
1. Välj **etablering** för att hantera inställningar för etablering av användar konto för appen.

   ![Visar skärmen Inställningar för etablering](./media/user-provisioning/provisioning_settings0.PNG)

1. Välj det automatiska alternativet för **etablerings läget** för att ange inställningar för administratörs behörighet, mappningar, starta och stoppa och synkronisering.

   - Expandera **administratörsautentiseringsuppgifter** för att ange de autentiseringsuppgifter som krävs för att Azure AD ska kunna ansluta till programmets användar hanterings-API. I det här avsnittet kan du också aktivera e-postaviseringar om autentiseringsuppgifterna inte fungerar eller om etablerings jobbet placeras i [karantän](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).
   - Expandera **mappningar** för att visa och redigera användarattribut som flödar mellan Azure AD och mål programmet när användar konton har skapats eller uppdaterats. Om mål programmet stöder det kan du med hjälp av det här avsnittet Konfigurera etablering av grupper och användar konton. Välj en mappning i tabellen för att öppna mappnings redigeraren till höger, där du kan visa och anpassa användarattribut.

     **Omfångs filter** talar om för etablerings tjänsten vilka användare och grupper i käll systemet som ska etableras eller avetableras till mål systemet. I fönstret **attributmappning** väljer du **käll objekt omfång** för att filtrera efter vissa attributvärden. Du kan till exempel ange att endast användare med attributet ”Avdelning” för ”Försäljning” ska ingå i omfånget för etablering. Mer information finns i [Använda omfångsfilter](define-conditional-rules-for-provisioning-user-accounts.md).

     Mer information finns i [Anpassa mappningar av attribut](customize-application-attributes.md).

   - **Inställningar** styr driften av etablerings tjänsten för ett program, inklusive om den körs för närvarande. På menyn **omfång** kan du ange om endast tilldelade användare och grupper ska vara inom omfånget för etablering, eller om alla användare i Azure AD-katalogen ska tillhandahållas. Information om hur du ”tilldelar” användare och grupper finns i [Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](assign-user-or-group-access-portal.md).

På skärmen hantering av appar väljer du **etablerings loggar (för hands version)** för att visa poster för varje åtgärd som körs av Azure AD Provisioning-tjänsten. Mer information finns i [etablerings guide för rapportering](check-status-user-account-provisioning.md).

![Exempel – etablering av loggar skärm för en app](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Azure AD-tjänsten för användar etablering kan också konfigureras och hanteras med hjälp av [Microsoft Graph-API: et](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="what-happens-during-provisioning"></a>Vad händer under etableringen?

När Azure AD är käll systemet använder etablerings tjänsten [funktionen differentiell fråga i Azure AD-Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) för att övervaka användare och grupper. Etablerings tjänsten kör en första cykel mot käll systemet och mål systemet, följt av periodiska stegvisa cykler.

### <a name="initial-cycle"></a>Första cykeln

När etablerings tjänsten startas kommer den första synkronisering som någonsin att köras att:

1. Fråga alla användare och grupper från käll systemet och hämta alla attribut som definierats i [mappningarna för attribut](customize-application-attributes.md).
1. Filtrera de användare och grupper som returneras med hjälp av eventuella konfigurerade [tilldelningar](assign-user-or-group-access-portal.md) eller [attributbaserade definitions filter](define-conditional-rules-for-provisioning-user-accounts.md).
1. När en användare tilldelas eller omfånget för etablering, frågar tjänsten mål systemet efter en matchande användare med de angivna [matchande attributen](customize-application-attributes.md#understanding-attribute-mapping-properties). Exempel: om namnet på userPrincipal i käll systemet är det matchande attributet och mappar till användar namn i mål systemet, frågar etablerings tjänsten mål systemet efter användar namn som matchar userPrincipal-namnens värden i käll systemet.
1. Om en matchande användare inte hittas i mål systemet, skapas den med hjälp av de attribut som returneras från käll systemet. När användar kontot har skapats identifierar och cachelagrar etablerings tjänsten mål systemets ID för den nya användaren, som används för att köra alla framtida åtgärder på den användaren.
1. Om det finns en matchande användare uppdateras den med hjälp av de attribut som anges av käll systemet. När användar kontot har matchats identifierar och cachelagrar etablerings tjänsten mål systemets ID för den nya användaren, som används för att köra alla framtida åtgärder på den användaren.
1. Om attribut mappningarna innehåller "Reference"-attribut gör tjänsten ytterligare uppdateringar i mål systemet för att skapa och länka de refererade objekten. En användare kan till exempel ha ett "Manager"-attribut i mål systemet, som är länkat till en annan användare som skapats i mål systemet.
1. Behåll en vattenstämpel i slutet av den första cykeln, som ger start punkten för de senare stegvisa cyklerna.

Vissa program som ServiceNow, G Suite och Box stöder inte bara etablering av användare, utan även etablering av grupper och medlemmar. I dessa fall, om grupp etablering är aktiverat i [mappningarna](customize-application-attributes.md), synkroniserar etablerings tjänsten användarna och grupperna och synkroniserar sedan grupp medlemskapen senare.

### <a name="incremental-cycles"></a>Stegvisa cykler

Efter den första cykeln kommer alla andra cykler att:

1. Fråga käll systemet efter användare och grupper som har uppdaterats sedan den senaste vattenstämpeln lagrades.
1. Filtrera de användare och grupper som returneras med hjälp av eventuella konfigurerade [tilldelningar](assign-user-or-group-access-portal.md) eller [attributbaserade definitions filter](define-conditional-rules-for-provisioning-user-accounts.md).
1. När en användare tilldelas eller omfånget för etablering, frågar tjänsten mål systemet efter en matchande användare med de angivna [matchande attributen](customize-application-attributes.md#understanding-attribute-mapping-properties).
1. Om en matchande användare inte hittas i mål systemet, skapas den med hjälp av de attribut som returneras från käll systemet. När användar kontot har skapats identifierar och cachelagrar etablerings tjänsten mål systemets ID för den nya användaren, som används för att köra alla framtida åtgärder på den användaren.
1. Om det finns en matchande användare uppdateras den med hjälp av de attribut som anges av käll systemet. Om det är ett nyligen tilldelat konto som matchas identifierar och cachelagrar etablerings tjänsten mål systemets ID för den nya användaren, som används för att köra alla framtida åtgärder på den användaren.
1. Om attribut mappningarna innehåller "Reference"-attribut gör tjänsten ytterligare uppdateringar i mål systemet för att skapa och länka de refererade objekten. En användare kan till exempel ha ett "Manager"-attribut i mål systemet, som är länkat till en annan användare som skapats i mål systemet.
1. Om en användare som tidigare fanns i omfånget för etablering tas bort från omfattningen (inklusive otilldelade) inaktive ras användaren i mål systemet via en uppdatering.
1. Om en användare som tidigare var i omfång för etablering är inaktive rad eller mjuk raderas i käll systemet, inaktiverar tjänsten användaren i mål systemet via en uppdatering.
1. Om en användare som tidigare var i omfång för etablering är hårt borttagen i käll systemet, tar tjänsten bort användaren i mål systemet. I Azure AD tar användarna bort 30 dagar efter att de har mjuk borttagning.
1. Spara en ny vattenstämpel i slutet av den stegvisa cykeln, som ger start punkten för de senare stegvisa cyklerna.

> [!NOTE]
> Du kan välja att inaktivera åtgärderna **skapa**, **Uppdatera**eller **ta bort** genom att använda kryss rutorna **mål objekt åtgärder** i avsnittet [mappningar](customize-application-attributes.md) . Logiken för att inaktivera en användare under en uppdatering styrs också via en attributmappning från ett fält som "accountEnabled".

Etablerings tjänsten fortsätter att köra stegvisa stegvisa cykler, med intervall som definierats i [självstudien som är specifik för varje program](../saas-apps/tutorial-list.md), tills någon av följande händelser inträffar:

- Tjänsten stoppas manuellt med Azure Portal eller med lämpligt Graph API kommando 
- En ny första cykel utlöses med alternativet **Rensa tillstånd och starta om** i Azure Portal, eller med lämpligt Graph API kommando. Den här åtgärden rensar en lagrad vattenstämpel och gör så att alla käll objekt utvärderas igen.
- En ny första cykel utlöses på grund av en ändring av attributens mappningar eller omfångs filter. Den här åtgärden tar också bort alla lagrade vattenstämplar och gör så att alla käll objekt utvärderas igen.
- Etablerings processen placeras i karantän (se nedan) på grund av en hög fel frekvens och finns kvar i karantän i mer än fyra veckor. I så fall kommer tjänsten att inaktive ras automatiskt.

### <a name="errors-and-retries"></a>Fel och försök

Om en enskild användare inte kan läggas till, uppdateras eller tas bort i mål systemet på grund av ett fel i mål systemet, görs ett nytt försök vid nästa synkronisering. Om användaren fortsätter att Miss lyckas kommer Återförsöken att ske med minskad frekvens, vilket gradvis skalar tillbaka till bara ett försök per dag. För att lösa felet måste administratörerna kontrol lera [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) för att fastställa rotor saken och vidta lämpliga åtgärder. Vanliga problem kan vara:

- Användare som inte har något attribut ifyllt i käll systemet som krävs i mål systemet
- Användare som har ett attributvärde i käll systemet där det finns en unik begränsning i mål systemet, och samma värde finns i en annan användar post

De här felen kan lösas genom att justera attributvärdena för den berörda användaren i käll systemet, eller genom att justera mappningar av attribut till inte orsaka konflikter.

### <a name="quarantine"></a>Vistelse

Om de flesta eller alla anrop som görs mot mål systemet inte fungerar konsekvent på grund av ett fel (t. ex. för ogiltiga administratörsautentiseringsuppgifter), försätts etablerings jobbet i status "karantän". Det här läget anges i [sammanfattnings rapporten för etablering](check-status-user-account-provisioning.md) och via e-post om e-postaviseringar har kon figurer ats i Azure Portal.

När den är i karantän minskas frekvensen för stegvisa cykler till en gång per dag.

Etablerings jobbet tas bort från karantänen när alla fel som är felaktiga har åtgärd ATS och nästa synkronisering startar. Om etablerings jobbet finns kvar i karantän i mer än fyra veckor inaktive ras etablerings jobbet. Läs mer här om karantän status [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="how-long-will-it-take-to-provision-users"></a>Hur lång tid tar det att etablera användare?

Prestanda beror på om ditt etablerings jobb kör en inledande etablerings cykel eller en stegvis cykel. Mer information om hur lång etablering tar och hur du övervakar etablerings tjänstens status finns i [kontrol lera status för användar etablering](application-provisioning-when-will-provisioning-finish-specific-user.md).

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Hur vet jag om användare har allokerats korrekt?

Alla åtgärder som körs av användar etablerings tjänsten registreras i Azure AD [-etablerings loggarna (för hands version)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Detta omfattar alla Läs-och skriv åtgärder som gjorts för käll-och mål systemen och de användar data som lästs eller skrevs under varje åtgärd.

Information om hur du läser etablerings loggar i Azure Portal finns i [rapport guiden för etablering](check-status-user-account-provisioning.md).

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Hur gör jag för att felsöka problem med användar etablering?

För scenariobaserade vägledning om hur du felsöker automatisk användar etablering, se problem med [att konfigurera och konfigurera användare i ett program](application-provisioning-config-problem.md).

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Vilka är de bästa metoderna för att distribuera automatisk användar etablering?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Ett exempel på en steg-för-steg-distributions plan för utgående användar etablering till ett program finns i [identitets distributions guiden för användar etablering](https://aka.ms/userprovisioningdeploymentplan).

## <a name="frequently-asked-questions"></a>Vanliga frågor

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Fungerar automatisk användar etablering för SaaS-appar med B2B-användare i Azure AD?

Ja, det är möjligt att använda Azure AD-tjänsten för användar etablering för att etablera B2B (eller gäst) användare i Azure AD till SaaS-program.

För att B2B-användare ska kunna logga in på SaaS-programmet med hjälp av Azure AD, måste dock SaaS-programmet ha sin SAML-baserade enkel inloggnings funktion som kon figurer ATS på ett visst sätt. Mer information om hur du konfigurerar SaaS-program för att stödja inloggningar från B2B-användare finns i [Konfigurera SaaS-appar för B2B-samarbete]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Fungerar automatisk användar etablering för SaaS-appar med dynamiska grupper i Azure AD?

Ja. När Azure AD-tjänsten för användar etablering har kon figurer ATS för att endast synkronisera tilldelade användare och grupper, kan Azure AD-tjänsten för användar etablering etablera eller etablera användare i ett SaaS program baserat på om de är medlemmar i en [dynamisk grupp](../users-groups-roles/groups-create-rule.md). Dynamiska grupper fungerar också med alternativet Synkronisera alla användare och grupper.

Användningen av dynamiska grupper kan dock påverka den övergripande prestandan för användar etablering från slut punkt till slut punkt från Azure AD till SaaS-program. När du använder dynamiska grupper bör du tänka på dessa varningar och rekommendationer i åtanke:

- Hur snabbt en användare i en dynamisk grupp etableras eller avetableras i ett SaaS-program beror på hur snabbt den dynamiska gruppen kan utvärdera medlemskaps ändringar. Information om hur du kontrollerar bearbetnings statusen för en dynamisk grupp finns i [kontrol lera bearbetnings status för en medlemskaps regel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- När du använder dynamiska grupper måste reglerna noga beaktas med användar etablering och avetablering i åtanke, eftersom det går att förlora medlemskap i en avetablerings händelse.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Fungerar automatisk användar etablering för SaaS-appar med kapslade grupper i Azure AD?

Nej. När Azure AD-tjänsten för användar etablering inte har kon figurer ATS för att synkronisera endast tilldelade användare och grupper, kan inte användare i Azure AD-tjänsten läsa eller etablera användare i kapslade grupper. Det går bara att läsa och etablera användare som är omedelbara medlemmar i den uttryckligen tilldelade gruppen.

Detta är en begränsning av "gruppbaserade tilldelningar till program", som också påverkar enkel inloggning och beskrivs i [använda en grupp för att hantera åtkomst till SaaS-program](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Som en lösning bör du uttryckligen tilldela (eller på annat sätt [omfång i](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) grupper som innehåller de användare som behöver vara etablerade.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Är etableringen mellan Azure AD och ett mål program att använda en krypterad kanal?

Ja. Vi använder HTTPS SSL-kryptering för Server målet.

## <a name="related-articles"></a>Relaterade artiklar

- [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
- [Anpassa mappningar av attribut för användar etablering](customize-application-attributes.md)
- [Skriver uttryck för mappningar av attribut](functions-for-customizing-application-data.md)
- [Omfångs filter för användar etablering](define-conditional-rules-for-provisioning-user-accounts.md)
- [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure AD till program](use-scim-to-provision-users-and-groups.md)
- [Översikt över Azure AD Synchronization API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
