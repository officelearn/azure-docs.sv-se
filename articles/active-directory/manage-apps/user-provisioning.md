---
title: Automatisk SaaS app användaretablering i Azure AD | Microsoft Docs
description: En introduktion till hur du kan använda Azure AD för att automatiskt etablera avetablera och kontinuerligt uppdatera användarkonton i flera SaaS-program från tredje part.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/30/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 935fef5ea988908787ae04688985606acec41bfd
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49387285"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatisera etablering och avetablering för SaaS-program med Azure Active Directory

## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>Vad är automatisk användaretablering för SaaS-appar?
Azure Active Directory (Azure AD) kan du automatisera den genereringen, underhållet och borttagningen av användaridentiteter i molnet ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) program, till exempel Dropbox, Salesforce, ServiceNow med mera.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**Nedan visas några exempel på vad den här funktionen kan du göra:**

* Automatiskt skapa nya konton i de högra system för nya personer när de ansluter till ditt team eller din organisation.
* Inaktivera automatiskt konton i de högra system när personer som lämnar gruppen eller organisationen.
* Se till att identiteter i dina appar och system hålls uppdaterade baserat på ändringar i katalogen, eller HR-system.
* Etablera icke-användare objekt, till exempel grupper, program som stöder dem.

**Automatisk användaretablering innehåller också följande funktioner:**

* Möjlighet att matcha befintliga identiteter mellan käll- och system.
* Anpassningsbara attributmappningar som definierar vilka användardata som ska flöda från källsystemet till målsystemet.
* Valfri e-postaviseringar för att etablera fel
* Rapportering och aktivitet loggar för bättre övervakning och felsökning.

## <a name="why-use-automated-provisioning"></a>Varför använda automatiserad etablering?

Vissa vanliga motiveringarna till att använda den här funktionen är:

* Undvika de kostnader och ineffektivitet handhavarfel vid manuell etablering processer.
* Undvika kostnader i samband med som är värd för och underhålla anpassade lösningar för etablering och skript
* Att skydda din organisation genom att direkt ta bort användarnas identiteter från viktiga SaaS-appar när de lämnar organisationen.
* Att enkelt importera ett stort antal användare till ett visst SaaS-program eller system.
* Att få med en enda uppsättning principer för att fastställa vem som har etablerats och vem som kan logga in till en app.

## <a name="how-does-automatic-provisioning-work"></a>Hur fungerar automatisk etablering?
    
Den **Provisioning-tjänsten för Azure AD** etablerar användare i SaaS-appar och andra system, genom att ansluta till användaren hanterings-API-slutpunkter som tillhandahålls av varje programleverantören. Dessa användare management API-slutpunkter kan Azure AD för att skapa, uppdatera och ta bort användare. För valda program etableringstjänsten kan också skapa, uppdatera och ta bort ytterligare identitetsrelaterade objekt, till exempel grupper och roller. 

![Etablering](./media/user-provisioning/provisioning0.PNG)
*bild 1: den Azure AD-Etableringstjänsten*

![Utgående etablering](./media/user-provisioning/provisioning1.PNG)
*bild 2: ”utgående” användaretablering arbetsflöde från Azure AD till populära SaaS-program*

![Inkommande etablering](./media/user-provisioning/provisioning2.PNG)
*bild 3: ”Inbound” arbetsflödet från populära Human Capital Management (HCM)-program till Azure Active Directory och Windows Server Active Directory för användaretablering*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Vilka program och system kan jag använda med Azure AD automatisk användaretablering?

Azure AD-funktioner förintegrerade stöd för en mängd olika populära SaaS-appar och HR-system, samt allmänna stöd för appar som implementerar vissa delar av den SCIM 2.0-standarden.

### <a name="pre-integrated-applications"></a>Redan integrerade program

En lista över alla program som stöder Azure AD en förintegrerade etablering anslutningen finns i den [lista över självstudier för program för användaretablering](../saas-apps/tutorial-list.md).

Till kontakten med Azure AD Utvecklingsteamet för att begära etablering stöd för ytterligare program kan skicka ett meddelande via den [Azure Active Directory-Feedbackforum](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> För ett program som stöder automatisk användaretablering, måste den först ange den nödvändiga användarhanteringen API: er som gör att externa program att automatisera genereringen, underhållet och borttagningen användare. Därför kan är inte alla SaaS-appar kompatibla med den här funktionen. För appar som har stöd för användarhantering API: er kan det tekniska teamet för Azure AD kommer sedan att kunna skapa en allokering anslutning till de apparna och arbetet är prioriterad av aktuella och framtida kunders behov. 

### <a name="connecting-applications-that-support-scim-20"></a>Ansluta appar som stöder SCIM 2.0

Information om hur du ansluter Allmänt program som implementerar SCIM 2.0 - baserat Användarhantering API: er, se [med SCIM för att automatiskt etablera användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md).

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hur ställer jag in automatisk etablering till ett program?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Konfigurationen av den Azure AD-etableringstjänsten för ett valt program startar i den  **[Azure-portalen](https://portal.azure.com)**. I den **Azure Active Directory > företagsprogram** väljer **Lägg till**, sedan **alla**, och sedan lägga till något av följande beroende på ditt scenario:

* Alla program i den **aktuellt program** avsnittet stöd för automatisk etablering. Se den [lista över självstudier för program för användaretablering](../saas-apps/tutorial-list.md) för fler håller på att.

* Använd alternativet ”icke-galleriprogram” för anpassade SCIM-integreringar

![Galleri](./media/user-provisioning/gallery.png)

På skärmen application management etablering har konfigurerats i den **etablering** fliken.

![Inställningar](./media/user-provisioning/provisioning_settings0.PNG)


* **Administratörsautentiseringsuppgifter** måste anges med Azure AD provisioning-tjänst som gör att den kan ansluta till API som tillhandahålls av programmet för användarhantering. Det här avsnittet kan du aktivera e-postmeddelanden om autentiseringsuppgifterna misslyckas eller Etableringsjobbet hamnar i [karantän](#quarantine).

* **Attributmappningar** kan konfigureras som anger vilka fält i källsystemet (exempel: Azure AD) kommer har innehållet synkroniseras på vilka fält i målsystemet (exempel: ServiceNow). Om målprogrammet stöder det kan kan det här avsnittet du också konfigurera etablering av grupper förutom användarkonton. ”Matchande egenskaper” kan du välja vilka fält som används för att matcha konton mellan systemen. ”[Uttryck](functions-for-customizing-application-data.md)” kan du ändra och omvandla värden som hämtats från källsystemet innan de skrivs till målsystemet. Mer information finns i [anpassa attributmappningar](customize-application-attributes.md).

![Inställningar](./media/user-provisioning/provisioning_settings1.PNG)

* **Omfångsfilter** berätta etableringstjänsten för vilka användare och grupp i källsystemet bör vara etablerade och/eller avetableras till målsystemet. Det finns två aspekter att Omfångsfilter som utvärderas ihop som bestämmer vem som är inom omfånget för etablering:

    * **Filtret på attributvärden** -menyn ”omfång för källobjekt” i attributmappningarna kan filtrera efter specifika attributvärden. Du kan till exempel ange att endast användare med attributet ”avdelning” med ”försäljning” ska vara i omfånget för etablering. Mer information finns i [med Omfångsfilter](define-conditional-rules-for-provisioning-user-accounts.md).

    * **Filtret på tilldelningar** -menyn ”omfång” i etablering > Inställningar-avsnittet i portalen kan du ange om endast ”tilldelade” användare och grupper bör vara i omfånget för etablering eller om alla användare i Azure AD-katalogen ska vara etableras. Läs om hur du ”tilldelar” användare och grupper [tilldela en användare eller grupp till en enterprise-app i Azure Active Directory](assign-user-or-group-access-portal.md).
    
* **Inställningar för** kontroll över etableringstjänsten för ett program, inklusive om den körs eller inte.

* **Granskningsloggar** ger dokumentation över av varje åtgärd som utförs av den Azure AD-etableringstjänsten. Mer information finns i den [etablering rapporteringsguide](check-status-user-account-provisioning.md).

![Inställningar](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Azure AD-tjänst för användaretablering kan också konfigureras och hanteras med hjälp av den [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>Vad händer under etableringen?

När Azure AD är källsystemet, etableringstjänsten använder det [differentiell fråga funktion i Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) att övervaka användare och grupper. Etableringstjänsten körs en initial synkronisering mot källsystemet och målsystemet, följt av periodiska inkrementella synkroniseringar. 

### <a name="initial-sync"></a>Den inledande synkroniseringen

När etableringstjänsten startas, kommer den första synkroniseringen har någonsin tidigare utfört:

1. Fråga efter alla användare och grupper från källsystemet, hämtas alla attribut som definierats i den [attributmappningar](customize-application-attributes.md).
2. Filtrera användare och grupper som returneras med hjälp av något konfigurerat [tilldelningar](assign-user-or-group-access-portal.md) eller [attributbaserade Omfångsfilter](define-conditional-rules-for-provisioning-user-accounts.md).
3. När en användare hittas som ska tilldelas eller inom omfånget för etablering tjänsten frågor målsystemet för en matchande användare som använder den angivna [matchar attribut](customize-application-attributes.md#understanding-attribute-mapping-properties). Exempel: Om userPrincipal namnet i källsystemet är attributet matchande och mappar till användarnamnet i målsystemet och sedan etableringstjänsten frågar målsystemet användarnamn som matchar de userPrincipal värdena i källsystemet.
4. Om en matchande användare inte finns i målsystemet, skapas den med hjälp av de attribut som returnerades från källsystemet. När användarkontot har skapats, identifierar etableringstjänsten och cachelagrar målsystemets-ID för den nya användaren, som används för att utföra alla framtida åtgärder på den användaren.
5. Om en matchande användare hittas, uppdateras den med hjälp av de attribut som tillhandahålls av källsystemet. När användarkontot matchas etableringstjänsten identifierar och cachelagrar målsystemets-ID för den nya användaren, som används för att utföra alla framtida åtgärder på den användaren.
6. Om attributmappningarna innehåller ”referens” attribut, utför tjänsten ytterligare uppdateringar på målsystemet och skapa och länka de refererade objekt. En användare kan till exempel ha en ”Manager”-attributet i målsystemet, som är länkad till en annan användare som har skapats i målsystemet.
7. Spara en vattenstämpel i slutet av den första synkroniseringen, vilket ger en startpunkt för efterföljande inkrementella synkroniseringar.

Vissa program, till exempel Box, ServiceNow och Google Apps stöd inte bara etablera användare, utan också etablering grupper och deras medlemmar. I sådana fall om gruppetablering har aktiverats i den [mappningar](customize-application-attributes.md), Etablerar tjänsten synkroniseras användarna och grupper och därefter synkroniserar gruppmedlemskap. 

### <a name="incremental-syncs"></a>Inkrementella synkroniseringar

Efter den första synkroniseringen kommer alla efterföljande synkroniseringar

1. Fråga källsystemet för alla användare och grupper som har uppdaterats sedan den senaste vattenstämpeln lagrades.
2. Filtrera användare och grupper som returneras med hjälp av något konfigurerat [tilldelningar](assign-user-or-group-access-portal.md) eller [attributbaserade Omfångsfilter](define-conditional-rules-for-provisioning-user-accounts.md).
3. När en användare hittas som ska tilldelas eller inom omfånget för etablering tjänsten frågor målsystemet för en matchande användare som använder den angivna [matchar attribut](customize-application-attributes.md#understanding-attribute-mapping-properties).
4. Om en matchande användare inte finns i målsystemet, skapas den med hjälp av de attribut som returnerades från källsystemet. När användarkontot har skapats, identifierar etableringstjänsten och cachelagrar målsystemets-ID för den nya användaren, som används för att utföra alla framtida åtgärder på den användaren.
5. Om en matchande användare hittas, uppdateras den med hjälp av de attribut som tillhandahålls av källsystemet. Om kontot är ett nyligen tilldelade som matchas, identifierar etableringstjänsten och cachelagrar målsystemets-ID för den nya användaren, som används för att utföra alla framtida åtgärder på den användaren.
6. Om attributmappningarna innehåller ”referens” attribut, utför tjänsten ytterligare uppdateringar på målsystemet och skapa och länka de refererade objekt. En användare kan till exempel ha en ”Manager”-attributet i målsystemet, som är länkad till en annan användare som har skapats i målsystemet.
7. Om en användare som tidigare ingick i omfånget för etablering tas bort från omfång (inklusive som otilldelade), inaktiveras tjänsten användaren i målsystemet via en uppdatering.
8. Om en användare som tidigare ingick i omfånget för etablering är inaktiverad eller ej permanent borttagna i källsystemet, inaktiverar tjänsten användaren i målsystemet via en uppdatering.
9. Om en användare som tidigare ingick i omfånget för etablering har tagits bort i källsystemet, tas tjänsten bort användaren i målsystemet. Användarna är i Azure AD-bort 30 dagar efter att de är ej permanent borttagna.
10. Spara en ny vattenstämpel i slutet av inkrementell synkronisering, vilket ger en startpunkt för efterföljande inkrementella synkroniseringar.

>[!NOTE]
> Du kan också inaktivera de skapa, uppdatera eller ta borttagningsåtgärder med hjälp av den **rikta åtgärder** kryssrutor i den [attributmappningar](customize-application-attributes.md) avsnittet. Logik för att inaktivera en användare under en uppdatering kontrolleras även via en attributmappning från ett fält, till exempel ”accountEnabled”.

Etableringstjänsten fortsätter att köras back-to-back inkrementella synkroniseringar under obestämd tid, med intervall som definieras i den [självstudien som är specifika för varje program](../saas-apps/tutorial-list.md), tills någon av följande händelser inträffar:

* Tjänsten stoppas manuellt med hjälp av Azure portal eller med hjälp av kommandot Graph API 
* En ny inledande synkronisering aktiveras med hjälp av den **rensa tillstånd och starta om** i Azure-portalen, eller med hjälp av kommandot Graph API. Detta tar bort alla lagrade vattenstämpel och gör att alla källobjekt som ska utvärderas igen.
* En ny inledande synkronisering aktiveras på grund av en ändring i attributmappningar eller Omfångsfilter. Detta tar bort alla lagrade vattenstämpel och gör att alla källobjekt som ska utvärderas igen.
* Etableringen går i karantän (se nedan) på grund av en hög Felfrekvens och ligger i karantän i mer än fyra veckor. I detta fall kan inaktiveras tjänsten automatiskt.

### <a name="errors-and-retries"></a>Fel och återförsök

Om en enskild användare inte kan har lagts till, uppdateras eller tas bort i målsystemet pga ett fel i målsystemet, kommer åtgärden upprepas under nästa synkroniseringscykel. Om du fortsätter att misslyckas, påbörjas nya försök ska ske en mindre ofta gradvis skala tillbaka till bara ett försök per dag. För att lösa felet kan administratörerna kontrollera de [granskningsloggar](check-status-user-account-provisioning.md) för ”processdeponering” händelser för att avgöra roten orsaka och vidta lämplig åtgärd. Vanliga fel kan innefatta:

* Användare som inte har ett attribut som fylls i källsystemet som krävs i målsystemet
* Användare med ett attributvärde i källsystemet som har en unik begränsning i målsystemet, samma värde finns i en annan användarpost

De här felen kan lösas genom att justera attributvärden för den berörda användaren i källsystemet, eller genom att justera attributmappningar för att inte orsakar konflikter.   

### <a name="quarantine"></a>Karantän

Om de flesta eller alla anrop görs mot målsystemet konsekvent misslyckas på grund av ett fel (till exempel som i fallet med ogiltig administratörsautentiseringsuppgifter), sedan Etableringsjobbet försätts i ”karantän” tillstånd. Detta anges i den [etablering sammanfattningsrapport](check-status-user-account-provisioning.md), och via e-post om e-postaviseringar har konfigurerats i Azure-portalen. 

När du är i karantän, minskar frekvensen av inkrementella synkroniseringar gradvis till en gång per dag. 

Etablering jobbet tas bort från karantän efter alla felaktiga fel åtgärdas och nästa synkroniseringscykel startar. Om Etableringsjobbet ligger i karantän fler än fyra veckor, är etablering jobbet inaktiverad.


## <a name="how-long-will-it-take-to-provision-users"></a>Hur lång tid tar det för att etablera användare?

Prestanda beror på om din Etableringsjobbet genomför en inledande synkronisering eller en inkrementell synkronisering, enligt beskrivningen i föregående avsnitt.

För **inledande synkroniseringar**, jobbtiden beror på en mängd olika faktorer, bland annat hur många användare och grupper i omfånget för etablering och det totala antalet användare och grupper i källsystemet. En omfattande lista över faktorer som påverkar prestanda för den inledande synkroniseringen sammanfattas senare i det här avsnittet.

För **inkrementella synkroniseringar**, jobbtiden beror på antalet ändringar som har identifierats i den synkroniseringscykel. Om det finns färre än 5 000 användare eller ändringar i gruppmedlemskap, kan jobbet slutförs inom en enda inkrementell synkroniseringscykel. 

I följande tabell sammanfattas synkroniseringstider för vanliga scenarier för etablering. I dessa scenarier källsystemet är Azure AD och målsystemet är ett SaaS-program. Synkroniseringstiderna härleds från en statistiska analyser av Synkroniseringsjobb för SaaS-program, ServiceNow, arbetsplats, Salesforce och Google Apps.


| Omfattningskonfigurationen | Användare, grupper och medlemmar i omfånget | Den inledande synkroniseringstiden | Inkrementell synkronisering |
| -------- | -------- | -------- | -------- |
| Synkronisera tilldelade användare och grupper bara |  < 1 000 |  < 30 minuter | < 30 minuter |
| Synkronisera tilldelade användare och grupper bara |  1 000 – 10 000 | 142 - 708 minuter | < 30 minuter |
| Synkronisera tilldelade användare och grupper bara |   10 000 – 100 000 | 1,170 - 2,340 minuter | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  < 1 000 | < 30 minuter  | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  1 000 – 10 000 | < 30-120 minuter | < 30 minuter |
| Synkronisera alla användare och grupper i Azure AD |  10 000 – 100 000  | 713 - 1,425 minuter | < 30 minuter |
| Synkronisera alla användare i Azure AD|  < 1 000  | < 30 minuter | < 30 minuter |
| Synkronisera alla användare i Azure AD | 1 000 – 10 000  | 43 - 86 minuter | < 30 minuter |


För konfigurationen **synkronisering tilldelade användare och grupper bara**, du kan använda följande formler för att fastställa ungefärlig minimum och maximum som förväntat **inledande synkronisering** gånger:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Sammanfattning av faktorer som påverkar den tid det tar för att slutföra en **inledande synkronisering**:

* Det totala antalet användare och grupper i omfånget för etablering

* Det totala antalet användare, grupper och medlemmar i gruppen finns i källsystemet (Azure AD)

* Huruvida användare inom omfånget för etablering matchas mot befintliga användare i målprogrammet eller måste skapas för första gången. Synkroniseringsjobb som alla användare skapas för första gången tar cirka *dubbelt så länge* som Synkronisera jobb som alla användare matchas mot befintliga användare.

* Antal fel i den [granskningsloggar](check-status-user-account-provisioning.md). Prestanda är långsammare om det finns många fel och etableringstjänsten är i ett tillstånd i karantän 

* Begära hastighetsbegränsningar och begränsning implementeras av målsystemet. Vissa målsystem implementerar begäran hastighetsbegränsningar och begränsning vilket kan påverka prestanda under stora synkroniseringsåtgärder. Under dessa förhållanden kan en app som tar emot för många begäranden för snabbt långsam dess konverteringsfrekvensen eller stänga anslutningen. Anslutningen måste justera genom att inte skicka appförfrågningar snabbare än appen kan bearbeta dem för att förbättra prestanda. Etablering anslutningsverktyg som bygger Microsoft göra den här justering. 

* Antalet och storleken på tilldelade grupper. Det tar längre tid än synkronisera användare att synkronisera tilldelade grupper. Prestanda kan påverkas av både antalet och storleken på de tilldelade grupperna. Om ett program har [mappningar som aktiverats för gruppen objektsynkronisering](customize-application-attributes.md#editing-group-attribute-mappings), egenskaper som gruppnamn och medlemskap har synkroniserats och användare. Dessa ytterligare synkroniseringar tar längre tid än att bara synkronisera användarobjekt.


## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Hur vet jag om användare har etablerats korrekt?

Alla åtgärder som utförs av tjänst för användaretablering registreras i Azure AD granskningsloggar. Detta inkluderar alla Läs- och skrivåtgärder som gjorts i käll-och mål, samt vilka användardata som har lästs eller skrivits under varje åtgärd.

Information om hur läsningen granskningsloggarna i Azure-portalen finns i den [etablering rapporteringsguide](check-status-user-account-provisioning.md).


## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Hur felsöker jag problem med etableringen av användare?

Scenariobaserade vägledning om hur du felsöker automatisk användaretablering finns i [problem med att konfigurera och etablera användare till ett program](application-provisioning-config-problem.md).


## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Vilka är rekommenderade metoder för att lansera automatisk användaretablering?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Ett exempel stegvisa distributionsplan för utgående användaretablering för ett program, finns det [identitet Distributionsguide för Användaretablering](https://aka.ms/userprovisioningdeploymentplan).

## <a name="more-frequently-asked-questions"></a>Fler vanliga frågor och svar

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>Stöder automatisk användaretablering till SaaS-appar fungerar med B2B-användare i Azure AD?

Ja, det är möjligt att använda Azure AD-användare etablera tjänsten för att etablera B2B (eller gäst) användare i Azure AD SaaS-program.

SaaS-program måste dock för B2B-användare för att kunna logga in på SaaS-program med hjälp av Azure AD kan ha sin SAML-baserad enkel inloggning förmåga konfigurerats i ett visst sätt. Mer information om hur du konfigurerar SaaS-program för att stödja inloggningar från B2B-användare finns i [konfigurera SaaS-appar för B2B-samarbete]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>Stöder automatisk användaretablering till SaaS-appar fungerar med dynamiska grupper i Azure AD?

Ja. När konfigurerad att ”synkronisera enbart tilldelade användare och grupper”, Azure AD-tjänst för användaretablering kan etablera eller användares användare i ett SaaS-program baserat på huruvida de är medlemmar i en [dynamisk grupp](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule]). Dynamiska grupper kan även användas med alternativet ”Synkronisera alla användare och grupper”.

Användning av dynamiska grupper kan dock påverka prestandan för slutpunkt till slutpunkt-användare från Azure AD-etablering till SaaS-program. När du använder dynamiska grupper Tänk på dessa varningar och rekommendationer:

* Hur snabbt en användare i en dynamisk grupp etablerats eller avetableras i en SaaS-programmet beror på hur snabbt den dynamiska gruppen kan utvärdera ändringar i medlemskap. Information om hur du kontrollerar Bearbetningsstatus för av en dynamisk grupp finns i [Kontrollera Bearbetningsstatus för för en medlemskapsregel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule#check-processing-status-for-a-membership-rule).

* När du använder dynamiska grupper, måste reglerna noggrant beaktas med användaren etablering och avetablering i åtanke, eftersom en förlust av medlemskap resulterar i en avställningsskript händelse.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>Stöder automatisk användaretablering till SaaS-appar fungerar med kapslade grupper i Azure AD?

Nej. När konfigurerad att ”synkronisera enbart tilldelade användare och grupper”, kan Azure AD-tjänst för användaretablering inte läsa eller etablera användare som finns i kapslade grupper. Det är bara kunna läsa och etablera användare som är direkta medlemmar av gruppen uttryckligen tilldelad.

Detta är en begränsning av ”gruppbaserad tilldelningar till program”, som också påverkar enkel inloggning och beskrivs i [hantera åtkomst till SaaS-program med hjälp av en grupp](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Som en lösning kan du måste uttryckligen tilldela (eller på annat sätt [omfång i](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) de grupper som innehåller de användare som behöver etableras.

## <a name="related-articles"></a>Relaterade artiklar

* [Lista över guider om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
* [Anpassa attributmappningar för etableringen av användare](customize-application-attributes.md)
* [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md)
* [Omfångsfilter för etableringen av användare](define-conditional-rules-for-provisioning-user-accounts.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md)
* [Översikt över synkronisering API i Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
