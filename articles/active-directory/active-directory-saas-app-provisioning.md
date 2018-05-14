---
title: Automatisk SaaS app användaretablering i Azure AD | Microsoft Docs
description: En introduktion till hur du kan använda Azure AD för att automatiskt etablera avetablera och kontinuerligt uppdatera användarkonton över flera SaaS-program från tredje part.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 72f796f0a4522b66feb55b827b02a83dcfdd3a01
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatisera användaren etablering och avetablering för SaaS-program med Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Vad är automatisk användaretablering för SaaS-appar?
Azure Active Directory (Azure AD) kan du automatisera skapandet, underhållet och borttagningen av användaridentiteter i molnet ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) program som Dropbox, Salesforce, ServiceNow med mera.

**Här följer några exempel på vad den här funktionen kan du göra:**

* Automatiskt skapa nya konton i de högra system för nya personer när de ansluter till din grupp eller organisation.
* Inaktivera automatiskt konton i de högra system när personer lämnar grupp eller organisation.
* Se till att identiteter i dina appar och -system hålls uppdaterade baserat på ändringar i katalogen, eller personal-system.
* Etablera icke-användare objekt, till exempel grupper för program som stöder dessa.

**Automatisk användaretablering innehåller även följande funktioner:**

* Möjligheten att matcha befintliga identiteter mellan käll- och system.
* Anpassningsbara attributmappning som definierar vilka användardata som ska flödas från källsystemet till målsystemet.
* Valfri e-postaviseringar för att etablera fel
* Rapporterings- och aktivitet loggar för bättre övervakning och felsökning.

## <a name="why-use-automated-provisioning"></a>Varför använda Automatisk etablering?
Några vanliga motiveringen för att använda den här funktionen är:

* Undvika kostnader, ineffektiviteter och handhavarfel vid manuell allokering processer.
* Undvika kostnader i samband med värd och underhålla lösningar för anpassad utvecklat etablering och skript
* Att skydda din organisation genom att direkt ta bort användarens identitet från viktiga SaaS-appar när de lämnar organisationen.
* Enkelt importera ett stort antal användare till ett visst SaaS-program eller system.
* Att få med en enda uppsättning principer för att fastställa vem som har etablerats och som kan logga in på en app.


## <a name="how-does-automatic-provisioning-work"></a>Hur fungerar automatisk etablering?
    
Den **Etableringstjänsten i Azure AD** etablerar du SaaS-appar och andra system, genom att ansluta till användaren hanterings-API-slutpunkter som tillhandahålls av leverantören av tillämpningsprogrammet. Dessa användare management API-slutpunkter kan Azure AD för att skapa, uppdatera och ta bort användare. För valda program tjänsten allokering kan också skapa, uppdatera och ta bort ytterligare identity-relaterade objekt, till exempel grupper och roller. 

![Etablering](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*bild 1: Azure AD etablering av tjänst*

![Utgående etablering](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*bild 2: ”utgående” användaretablering arbetsflödet från Azure AD populära SaaS-program*

![Inkommande etablering](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*bild 3: ”inkommande” arbetsflödet från populära mänsklig kapital Management (HCM) program till Azure Active Directory och Windows Server Active Directory för användaretablering*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Vilka program och system kan du använda med Azure AD automatisk användaretablering?

Azure AD-funktioner integrerade redan stöd för en mängd olika populära SaaS-appar och personal-system, samt allmänna stöd för appar som implementerar vissa delar av SCIM 2.0-standarden.

En lista över alla program som stöder Azure AD en förintegrerade etablering koppling, finns det [lista över program självstudier för användaretablering](active-directory-saas-tutorial-list.md).

Mer information om hur du lägger till stöd för Azure AD användaretablering till ett program finns [använda SCIM för att automatiskt etablera användare och grupper från Azure Active Directory till program](active-directory-scim-provisioning.md).

Kontakta Azure AD engineering team för att begära etablering support för ytterligare program, skicka ett meddelande via den [Azure Active Directory Feedbackforum](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).    

> [!NOTE]
> För ett program med stöd för automatisk användaretablering, måste det först ange nödvändiga Användarhantering API: er som gör att externa program att automatisera skapande, underhåll och borttagning av användare. Därför inte alla SaaS-appar som är kompatibla med den här funktionen. Azure AD-Utvecklingsteamet kommer sedan att kunna skapa en allokering koppling till apparna för appar som har stöd för användarhantering API: er, och detta verk prioriteras av behoven hos aktuella och potentiella kunder. 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hur ställer jag in automatisk etablering till ett program?

Konfigurationen av Azure AD etableras för ett valt program startar i den  **[Azure-portalen](https://portal.azure.com)**. I den **Azure Active Directory > företagsprogram** väljer **Lägg till**, sedan **alla**, och Lägg sedan till något av följande beroende på ditt scenario:

* Alla program i den **aktuella program** avsnittet stöd för automatisk etablering. Finns det [lista över program självstudier för användaretablering](active-directory-saas-tutorial-list.md) för ytterligare mallar.

* Använd alternativet ”icke-galleriet program” för anpassad utvecklat SCIM integreringar

![Galleri](./media/active-directory-saas-app-provisioning/gallery.png)

På skärmen application management etablering har konfigurerats i den **etablering** fliken.

![Inställningar](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* **Administratörsautentiseringsuppgifter** måste anges till Azure AD etablering av tjänst som gör att den kan ansluta till Användarhantering API som tillhandahålls av programmet. Det här avsnittet kan du aktivera e-postmeddelanden om autentiseringsuppgifterna misslyckas eller etableringsjobbet för den försätts i [karantän](#quarantine).

* **Attributet mappningar** kan konfigureras som anger vilka fält i källsystemet (exempel: Azure AD) kommer har innehållet synkroniseras till vilka fält i målsystemet (exempel: ServiceNow). Om målprogrammet stöder den kan i det här avsnittet du också konfigurera etablering av grupper förutom användarkonton. ”Matchande egenskaper” kan du välja vilka fält som används för att matcha konton mellan systemen. ”[Uttryck](active-directory-saas-writing-expressions-for-attribute-mappings.md)” gör att du kan ändra och transformera värdena som hämtas från källsystemet innan de skrivs till målsystemet. Mer information finns i [anpassa attributmappning](active-directory-saas-customizing-attribute-mappings.md).

![Inställningar](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **Omfångsfilter** berätta tjänsten etablering för vilka användare och grupp i källsystemet ska etableras eller avetableras till målsystemet. Det finns två aspekter att Omfångsfilter som utvärderas ihop som bestämmer vem som ingår i omfånget för etablering:

    * **Filtret på attributvärden** -”källa Objektområde”-menyn i attributet avbildningar kan filtrering på attributvärden. Du kan till exempel ange att endast användare med attributet ”avdelning” med ”försäljning” ska vara i omfånget för etablering. Mer information finns i [med hjälp av målgrupp filter](active-directory-saas-scoping-filters.md).

    * **Filtret på tilldelningar** -”Scope”-menyn i etableringen > Inställningar av portalen kan du ange om endast ”tilldelade” användare och grupper bör vara i omfånget för etablering eller om vara alla användare i Azure AD-katalog etableras. Mer information om ”tilldela” användare och grupper finns [tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* **Inställningar för** styr driften av tjänsten etablering för ett program, inklusive om den körs för tillfället eller inte.

* **Granskningsloggar** ange posterna för varje åtgärd som utförs av Azure AD etableras. Mer information finns i [etablering rapporteringsguide](active-directory-saas-provisioning-reporting.md).

![Inställningar](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

> [!NOTE]
> Azure AD-användare som etablerar tjänsten kan också konfigureras och hanteras med den [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Vad som händer under etablering?

När Azure AD är källsystemet, etablering tjänsten använder den [differentiell frågan funktion i Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) att övervaka användare och grupper. Tjänsten etablering körs en inledande synkronisering mot källsystemet och måldatorn, följt av periodiska inkrementella synkroniseringar. 

### <a name="initial-sync"></a>Inledande synkronisering
När etablering tjänsten startas, kommer den första synkroniseringen skulle utföras:

1. Fråga alla användare och grupper från källsystemet, hämtas alla attribut som definierats i den [attributet mappningar](active-directory-saas-customizing-attribute-mappings.md).
2. Filtrera användare och grupper som returneras genom att använda någon konfigurerad [tilldelningar](active-directory-coreapps-assign-user-azure-portal.md) eller [attributbaserad Omfångsfilter](active-directory-saas-scoping-filters.md).
3. När en användare hittas tilldelas eller i omfånget för att etablera tjänsten frågar målsystemet för en matchande användare med hjälp av den avsedda [matchar attribut](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties). Exempel: Om userPrincipal namn i källsystemet är attributet matchande och mappar till användarnamn i målsystemet och tjänsten etablering frågar målsystemet användarnamn som matchar namnvärden userPrincipal i källsystemet.
4. Om en matchande användare inte finns i målsystemet, skapas med hjälp av de attribut som returnerades från källsystemet.
5. Om en matchande användare hittas uppdateras med hjälp av attributen från källsystemet.
6. Om attributmappning innehåller ”referensattribut”, utför tjänsten ytterligare uppdateringar på måldatorn för att skapa och länka de refererade objekt. En användare kan ha attributet ”Manager” i målsystemet, som är kopplad till en annan användare som har skapats i målsystemet.
7. Kvar vattenstämpel i slutet av den första synkroniseringen, som utgör startpunkten för efterföljande inkrementella synkroniseringar.

Vissa program som stöd för ServiceNow, Google Apps och rutan inte bara etablering användare, utan också etablering grupper och deras medlemmar. I dessa fall, om gruppen etablering har aktiverats i den [mappningar](active-directory-saas-customizing-attribute-mappings.md), Etablerar tjänsten synkroniserar användare och grupper och därefter synkroniserar gruppmedlemskap. 

### <a name="incremental-syncs"></a>Inkrementella synkroniseringar
Efter den första synkroniseringen kommer alla efterföljande synkroniseringar

1. Fråga källsystemet för alla användare och grupper som har uppdaterats sedan senaste vattenmärket lagrades.
2. Filtrera användare och grupper som returneras genom att använda någon konfigurerad [tilldelningar](active-directory-coreapps-assign-user-azure-portal.md) eller [attributbaserad Omfångsfilter](active-directory-saas-scoping-filters.md).
3. När en användare hittas tilldelas eller i omfånget för att etablera tjänsten frågar målsystemet för en matchande användare med hjälp av den avsedda [matchar attribut](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties).
4. Om en matchande användare inte finns i målsystemet, skapas med hjälp av de attribut som returnerades från källsystemet.
5. Om en matchande användare hittas uppdateras med hjälp av attributen från källsystemet.
6. Om attributmappning innehåller ”referensattribut”, utför tjänsten ytterligare uppdateringar på måldatorn för att skapa och länka de refererade objekt. En användare kan ha attributet ”Manager” i målsystemet, som är kopplad till en annan användare som har skapats i målsystemet.
7. Om en användare som tidigare var i omfånget för etablering tas bort från omfång (inklusive som otilldelade) inaktiverar tjänsten användaren i målsystemet via en uppdatering.
8. Om en användare som tidigare var i omfånget för etablering är inaktiverat eller ej permanent borttagna i källsystemet, inaktiverar tjänsten användaren i målsystemet via en uppdatering.
9. Om en användare som tidigare var i omfånget för etablering har tagits bort i källsystemet, tas tjänsten bort användaren i målsystemet. Användare kan i Azure AD-bort 30 dagar efter att de är ej permanent borttagna.
10. Spara en ny vattenstämpel i slutet av inkrementell synkronisering, som utgör startpunkten för efterföljande inkrementella synkroniseringar.

>[!NOTE]
> Du kan om du vill inaktivera skapa-, update- eller delete-åtgärder med hjälp av den **riktade händelser för objektet** kryssrutor i den [attributmappning](active-directory-saas-customizing-attribute-mappings.md) avsnitt. Logik för att inaktivera en användare under en uppdatering kontrolleras även via en attributmappning från ett fält, till exempel ”accountEnabled”.

Etablering tjänsten fortsätter att köras på obestämd tid back-to-back inkrementella synkroniseringar med intervall som definieras i den [självstudier som är specifika för varje program](active-directory-saas-tutorial-list.md), tills någon av följande händelser inträffar:

* Tjänsten stoppats manuellt med hjälp av Azure-portalen eller med hjälp av kommandot Graph API 
* En ny inledande synkronisering aktiveras med hjälp av den **Rensa status och starta om** alternativet i Azure-portalen eller med hjälp av kommandot Graph API. Detta tar bort alla lagrade vattenstämpel och medför att alla källobjekt som ska utvärderas igen.
* En ny inledande synkronisering initieras på grund av en ändring i attributmappning eller målgrupp filter. Detta tar bort alla lagrade vattenstämpel och medför att alla källobjekt som ska utvärderas igen.
* Etableringen hamnar i karantän (se nedan) på grund av en hög hastighet och är i karantän i mer än fyra veckor. I detta fall kan inaktiveras tjänsten automatiskt.

### <a name="errors-and-retries"></a>Fel och försök 
Om en användare inte kan läggas till, uppdateras eller tas bort i målsystemet på grund av ett fel i målsystemet, provas igen i nästa synkronisering cykel. Om användaren fortfarande misslyckas, börjar återförsöken ska ske vid en mindre ofta gradvis skalning tillbaka till ett enda försök per dag. För att lösa felet kan administratörerna kontrollera de [granskningsloggar](active-directory-saas-provisioning-reporting.md) för ”processen escrow” händelser för att avgöra roten orsaka och vidta lämplig åtgärd. Vanliga fel kan inkludera följande:

* Användare som inte har ett attribut i källsystemet som krävs i målsystemet
* Användarna har ett attributvärde i källsystemet som har en unik begränsning i målsystemet och samma värde som finns i en annan användarpost

Dessa fel kan lösas genom att justera attributvärden för den berörda användaren i källsystemet eller genom att justera attributmappning för att inte orsakar konflikter.   

### <a name="quarantine"></a>Placera i karantän
Om de flesta eller alla av anrop görs mot målsystemet konsekvent misslyckas på grund av ett fel (till exempel som i fallet med ogiltig administratörsautentiseringsuppgifter), sedan etableringsjobbet för den försätts i tillståndet ”karantän”. Detta visas i den [etablering sammanfattningsrapport](active-directory-saas-provisioning-reporting.md), och via e-post om e-postaviseringar har konfigurerats i Azure-portalen. 

När du är i karantän, minska frekvensen av inkrementella synkroniseringar gradvis till en gång per dag. 

Etableringsjobbet för den tas bort från karantän efter alla felaktiga fel fastställs och nästa synkronisering cykel startar. Om etableringsjobbet för den är i karantän i mer än fyra veckor, har etableringsjobbet för den inaktiverats.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Hur lång tid tar det för att etablera Mina användare?**

Prestanda är olika beroende på om din Etableringsjobbet genomför en inledande synkronisering eller en inkrementell synkronisering.

För inledande synkronisering är den tid det tar för att slutföra direkt beroende av hur många användare, grupper och medlemmar i gruppen finns i källsystemet. Mycket små källsystem hundratals objekt kan slutföra inledande synkronisering på bara några minuter. Dock tar källsystem hundratals tusentals eller miljontals kombinerade objekt längre tid.

Den tid det tar beror på antalet ändringar som har påträffats i den sync cykeln för inkrementell synkronisering. Om det finns mindre än 5 000 användare eller ändringar i gruppmedlemskap upptäckt, kan dessa ofta synkroniseras inom en cykel 40 minuter. 

Observera att prestandan är beroende av både käll- och system. Vissa målsystem implementera begäran hastighetsbegränsningar och begränsning att kan påverkan prestanda vid stora synkroniseringsåtgärder och förskapad Azure AD etablering kopplingar för dessa system ta hänsyn till.

Prestanda är också längre tid om det finns många (registreras i den [granskningsloggar](active-directory-saas-provisioning-reporting.md)) och tjänsten etablering är i tillståndet ”karantän”.

**Hur kan jag för att förbättra prestanda för synkronisering?**

De flesta prestandaproblem sker under inledande synkronisering av datorer som har ett stort antal grupper och medlemmar i gruppen.

Om det inte krävs synkroniseringen av grupper eller gruppmedlemskap, förbättras sync prestanda avsevärt genom att:

1. Ange den **etablering > Inställningar > Scope** menyn till **synkronisera alla**, i stället för att synkronisera tilldelade användare och grupper.
2. Använd [Omfångsfilter](active-directory-saas-scoping-filters.md) i stället för tilldelningar för att filtrera listan över användare som har etablerats.

> [!NOTE]
> För program som har stöd för etablering av namn och egenskaper (till exempel ServiceNow och Google Apps), inaktiverar detta minskar också risken för den tid det tar för en inledande synkronisering att slutföra. Om du inte vill att etablera gruppnamn och gruppmedlemskap i ditt program, kan du inaktivera detta i den [attributet mappningar](active-directory-saas-customizing-attribute-mappings.md) av etablering konfigurationen.

**Hur kan jag spåra förloppet för den aktuella Etableringsjobbet?**

Finns det [etablering rapporteringsguide](active-directory-saas-provisioning-reporting.md).

**Hur vet jag om användare ska kunna hämta etablerats korrekt?**

Alla fel registreras i Azure AD granskningsloggar. Mer information finns i [etablering rapporteringsguide](active-directory-saas-provisioning-reporting.md).

**Hur kan jag skapa ett program som fungerar med tjänsten etablering?**

Se [använda SCIM för att automatiskt etablera användare och grupper från Azure Active Directory till program](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

**Hur kan jag skicka feedback till teknikteamet?**

Kontakta oss via den [Azure Active Directory Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Relaterade artiklar
* [Lista över självstudier om hur du integrerar SaaS-appar](active-directory-saas-tutorial-list.md)
* [Anpassa attributmappning för Användaretablering](active-directory-saas-customizing-attribute-mappings.md)
* [Skriva uttryck för attributmappning](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Omfångsfilter för Användaretablering](active-directory-saas-scoping-filters.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](active-directory-scim-provisioning.md)
* [Översikt av Azure AD-synkronisering API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
* [Stegvisa distributionsplan för utgående användaretablering för ett program](https://aka.ms/userprovisioningdeploymentplan)

