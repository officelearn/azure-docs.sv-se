---
title: Identitet hybridutformning - livscykeln införandestrategi Azure | Microsoft Docs
description: Hjälper till att definiera hybrid identitetshanteringsaktiviteter enligt alternativ som är tillgängliga för varje fas i livscykeln.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 5590f1fc3716582da090b8429f8bcf4fc7911dbe
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251771"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Fastställa hybrid införande för identitetslivscykel
I det här steget ska du definiera identity management strategin för din hybrididentitetslösning att uppfylla affärskrav som du definierade i [fastställa hybrid identitetshanteringsaktiviteter](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Om du vill definiera hybrid identitetshanteringsaktiviteter enligt den identitetslivscykel för slutpunkt till slutpunkt som visas tidigare i det här steget måste du beaktas vilka alternativ som är tillgängliga för varje fas livscykel.

## <a name="access-management-and-provisioning"></a>Åtkomsthantering och driftsättning
Med en bra konto åtkomsthanteringslösning kan din organisation spåra exakt vem som har åtkomst till vilka i organisationen.

Access control är en viktig funktion av en central, en enda punkt etablering system. Förutom skyddar känslig information, exponera åtkomstkontroller befintliga konton som har icke-godkända auktoriseringar eller är inte längre behövs. Kontrollera föråldrade konton, etablering system länkar tillsammans uppgifterna för ett konto med auktoritär information om användarna som äger konton. Auktoritativ användaren identitetsinformation tillhandahålls vanligtvis i databaser och kataloger för personalavdelningen.

Konton i avancerade IT-företag omfattar hundratals olika parametrar som definierar utfärdare och informationen kan styras av systemet etablering. Nya användare kan identifieras med de data som du anger från den auktoritativa källan. Begäran om godkännande-funktion för åtkomst initierar de processer som godkänna (eller avvisa) resursetablering för dem.

| Livscykeln för hantering av fas | Lokalt | Molnet | Hybrid |
| --- | --- | --- | --- |
| Kontohantering och etablering |Med hjälp av serverrollen Active Directory® Domain Services (AD DS), kan du skapa en skalbar, säker och hanterbar infrastruktur för användar- och resurshantering och ger stöd för katalogaktiverade program, till exempel Microsoft® Exchange Server. <br><br> [Du kan etablera grupper i AD DS via en Identity manager](https://technet.microsoft.com/library/ff686261.aspx) <br>[ Du kan etablera användare i AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Administratörer kan använda åtkomstkontroll för att hantera användarnas åtkomst till delade resurser av säkerhetsskäl. I Active Directory administreras åtkomstkontroll på objektnivå genom att ställa in olika nivåer av åtkomst och behörighet till objekt, till exempel fullständig behörighet, Skriv, läs eller ingen åtkomst. Åtkomstkontroll i Active Directory definierar hur olika användare kan använda Active Directory-objekt. Behörigheter för objekt i Active Directory är som standard den säkraste inställningen. |Du måste skapa ett konto för varje användare som behöver komma åt en Microsoft-molntjänst. Du kan också ändra användarkonton eller ta bort dem när de inte längre behövs. Användare har inte administratörsbehörighet som standard, men du kan tilldela dem (valfritt). <br><br> I Azure Active Directory är en av de viktigaste funktionerna möjligheten att hantera åtkomst till resurser. Dessa resurser kan vara en del av katalogen, som i fallet med behörigheter för att hantera objekt via roller i katalogen, eller resurser som är externa för katalogen såsom SaaS-program, Azure-tjänster och SharePoint-webbplatser eller lokala resurser. <br><br> Center för Azure Active Directorys åtkomst finns lösning i säkerhetsgruppen. Resursägaren (eller administratören för katalogen) kan tilldela en grupp för att ge en viss åtkomsträtt till resurser som ägaren äger. Medlemmar i gruppen ges åtkomst och resursägaren kan delegera behörighet att hantera listan över medlemmar i en grupp till någon annan – till exempel en avdelningschef eller en supportavdelningsadministratör<br> <br> Hantera grupper i Azure AD-avsnitt innehåller mer information om hur du hanterar åtkomst via grupper. |Utöka Active Directory-identiteter till molnet via synkronisering och federation |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
Rollbaserad åtkomst kontroll (RBAC) använder roller och etablera principer för att utvärdera, testa och tillämpa dina affärsprocesser och regler för att bevilja åtkomst till användare. Viktiga administratörer skapa principer för etablering och tilldela användare till roller och som definierar uppsättningar av rättigheter till resurser för dessa roller. RBAC utökar identitetshanteringslösning för att använda programvarubaserade processer och minska manuell användaråtgärder i etableringsprocessen.
RBAC i Azure AD gör det möjligt för företaget att begränsa antalet åtgärder som en person kan göra när han har åtkomst till Azure-portalen. Genom att använda RBAC för att styra åtkomst till portalen kan IT-administratörer ca delegera åtkomst med hjälp av följande metoder för hantering av åtkomst:

* **Gruppbaserad rolltilldelning**: du kan tilldela åtkomst till Azure AD-grupper som kan synkroniseras från din lokala Active Directory. På så sätt kan du utnyttja befintliga investeringar som din organisation har gjort i verktyg och processer för att hantera grupper. Du kan också använda delegerad grupp-funktionen för hantering av Azure AD Premium.
* **Utnyttja inbyggda roller i Azure**: du kan använda tre roller, ägare, deltagare och läsare, så att användare och grupper har behörighet till endast de aktiviteter som de behöver för att göra sina jobb.
* **Detaljerad åtkomst till resurser**: du kan tilldela roller till användare och grupper för en viss prenumeration, resursgrupp eller en enskild Azure-resurs, till exempel en webbplats eller i databasen. På så sätt kan du se till att användarna har åtkomst till alla resurser som de behöver och ingen åtkomst till resurser som de inte behöver hantera.

## <a name="provisioning-and-other-customization-options"></a>Etablering och andra anpassningsalternativ för
Ditt team kan använda affärsplaner och krav för att avgöra hur mycket du anpassar ID-lösning. Ett stort företag kan till exempel kräva en fasad lansering plan för arbetsflöden och anpassade kort som baseras på en tidslinje för stegvis etablering program som används mycket i geografiska områden. En annan anpassning planen kan innehålla två eller flera program som ska etableras i hela företaget, efter lyckad testning. Användarprogram interaktion kan anpassas och procedurer för att etablera resurser kan ändras för att hantera Automatisk etablering.

Du kan ta bort etableringen för att ta bort en tjänst eller en komponent. Avetablering av ett konto betyder exempelvis att kontot tas bort från en resurs.

Hybridmodell för att etablera resurser kombinerar begäran och rollbaserad metoder som stöds både av Azure AD. För en delmängd av anställda eller hanterade system kan ett företag vill automatisera åtkomst med rollbaserad tilldelning. Ett företag kan också hantera alla andra förfrågningar eller undantag via en begäran-baserade modellen. Vissa företag kan börja med manuell tilldelning och utvecklas mot en hybridmodell, med en avsikt är en fullständigt rollbaserad distribution vid ett senare tillfälle.

Andra företag kan hitta det opraktiskt för affärs-skäl att uppnå fullständig rollbaserad etablering och rikta en hybridlösning som önskade mål. Fortfarande andra företag kan uppfyllas med endast begäran-baserad etablering och vill inte investera extra arbete för att definiera och hantera principer för rollbaserad, automatiserad etablering.

## <a name="license-management"></a>Licenshantering
Licenshantering av gruppbaserad i Azure AD kan administratörer tilldela användare till en säkerhetsgrupp och Azure AD tilldelar automatiskt licenser till alla medlemmar i gruppen. Om en användare är därefter läggs till eller tas bort från gruppen, tilldelas en licens automatiskt eller tas bort efter behov.

Du kan använda grupper som du synkroniserar från lokala AD eller hantera i Azure AD. Para ihop detta med Azure AD premium Self-Service Group Management kan du enkelt delegera licenstilldelning för rätt beslutsfattare. Du kan vara säker på att problem som licens konflikter och saknas platsdata sorteras automatiskt.

## <a name="self-regulating-user-administration"></a>Lokal reglerande Användaradministration
När din organisation börjar att etablera resurser i alla interna organisationer, kan du implementera funktionen för administration av lokal reglerande användare. Du kan spara fördelarna och fördelarna med etablering användare över organisationens gränser. I den här miljön återspeglas automatiskt ändras användarens status i åtkomsträttigheter i organisationen gränser och geografiska områden. Du kan minska kostnaderna för etablering och effektivisera processer för åtkomst och godkännande. Implementeringen inser hela potentialen för att implementera rollbaserad åtkomstkontroll för hantering av slutpunkt till slutpunkt i din organisation. Du kan minska administrativa kostnader med automatiserade procedurer för att styra etableringen av användare. Du kan förbättra säkerheten genom att automatisera tvingande säkerhetsprinciper, och effektivisera och centralisera hantering och etablering av för många av användarna.

> [!NOTE]
> Mer information finns i hur du konfigurerar Azure AD för hantering av programåtkomst via självbetjäning
> 
> 

Licens-baserade (rättigheten-baserad) Azure AD tjänsterna fungerar genom att aktivera en prenumeration i katalogtjänsten/Azure AD-klienten. När prenumerationen är aktiv service-funktionerna hanteras av katalogtjänsten/administratörer och används av licensierade användare. 

## <a name="integration-with-other-3rd-party-providers"></a>Integrering med andra 3 part-leverantörer

Azure Active Directory tillhandahåller enkel inloggning och förbättrad säkerhet för åtkomst till tusentals SaaS-program och lokala webbprogram. Mer information finns i [integrera program med Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

## <a name="define-synchronization-management"></a>Definiera hantering av datasynkronisering
Om du integrerar dina lokala kataloger med Azure AD kan du hjälpa dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Med den här integrationen kan användare och organisationer att dra nytta av följande:

* Organisationer kan ge användarna en gemensam hybrididentitet i den lokala eller molnbaserade tjänster utnyttjar Windows Server Active Directory och sedan ansluta till Azure Active Directory.
* Administratörer kan förse villkorlig åtkomst baserat på programresursen, enheten och användarens identitet, nätverksplats och multifaktorautentisering.
* Användare kan utnyttja deras gemensam identitet via konton i Azure AD till Office 365, Intune, SaaS-appar och program från tredje part.
* Utvecklare kan bygga program som använder den gemensamma identitetsmodellen, integrering av program i Active Directory lokalt eller Azure för molnbaserade program

Följande bild innehåller ett exempel på en översikt över processen för synkronisering av identitet.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Processen för synkronisering av identitet

Kontrollera i följande tabell för att jämföra synkroniseringsåtgärder som:

| Synkronisering av Management-alternativ | Fördelar | Nackdelar |
| --- | --- | --- |
| Synkroniseringsbaserade (via DirSync eller AADConnect) |Användare och grupper som synkroniserats från lokal plats och molnet <br>  **Principkontroll**: Kontoprinciper kan ställas in via Active Directory, som ger administratören möjlighet att hantera principer för lösenord, arbetsstation, begränsningar, lås rapporteras kontroller, och mer, utan att behöva utföra ytterligare aktiviteter i molnet.  <br>  **Åtkomstkontroll**: kan begränsa åtkomsten till Molntjänsten så att tjänsterna som kan nås via en företagsmiljö via online servrar eller båda. <br>  Minskar supportsamtalen: om användarna har färre lösenord för att komma ihåg, är det mindre troligt att glömma dem. <br>  Säkerhet: Användaridentiteter och informationen skyddas eftersom alla servrar och tjänster som används i enkel inloggning, administreras och kontrolleras den lokala. <br>  Stöd för stark autentisering: du kan använda stark autentisering (kallas även för tvåfaktorsautentisering) med Molntjänsten. Men om du använder stark autentisering, måste du använda enkel inloggning. | |
| Federationsbaserat (via AD FS) |Aktiverat som säkerhetstokentjänst (STS). När du konfigurerar en STS att tillhandahålla enkel inloggning med ett Microsoft-molntjänst, skapar du ett federerat förtroende mellan din lokala STS och federerad domän du har angett i Azure AD-klienten. <br> Gör att du kan använda samma uppsättning autentiseringsuppgifter för att få åtkomst till flera resurser <br>slutanvändare behöver inte underhålla flera uppsättningar med autentiseringsuppgifter. Ännu, användare måste ange sina autentiseringsuppgifter för var och en av de deltagande resurserna., B2B och B2C-scenarier som stöds. |Kräver särskild personal för distribution och underhåll av dedikerad lokal AD FS-servrar. Det finns vissa restriktioner gällande användningen av stark autentisering om du planerar att använda AD FS för din STS. Mer information finns i [konfigurera avancerade alternativ för AD FS 2.0](https://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Mer information finns i [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](plan-hybrid-identity-design-considerations-overview.md)

