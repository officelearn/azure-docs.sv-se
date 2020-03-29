---
title: Hybrididentitetsdesign – strategi för livscykelanvändning Azure | Microsoft-dokument
description: Hjälper till att definiera hybrididentitetshanteringsuppgifterna enligt de alternativ som är tillgängliga för varje livscykelfas.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85f600c8bd46e699e80bf7b596574dc01467ef79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109320"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Ta reda på strategi för antagande av hybrididentitetslivscykel
I den här uppgiften definierar du identitetshanteringsstrategin för hybrididentitetslösningen för att uppfylla de affärskrav som du definierade i [Bestäm hybrididentitetshanteringsuppgifter](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Om du vill definiera hybrididentitetshanteringsuppgifterna enligt den dagliga identitetslivscykel som presenterades tidigare i det här steget måste du överväga vilka alternativ som är tillgängliga för varje livscykelfas.

## <a name="access-management-and-provisioning"></a>Åtkomsthantering och etablering
Med en bra lösning för hantering av kontoåtkomst kan din organisation spåra exakt vem som har åtkomst till vilken information i hela organisationen.

Åtkomstkontroll är en kritisk funktion i ett centraliserat system för etablering av en punkt. Förutom att skydda känslig information exponerar åtkomstkontroller befintliga konton som inte har godkända auktoriseringar eller inte längre är nödvändiga. Om du vill kontrollera föråldrade konton länkar etableringssystemet samman kontoinformation med auktoritär information om de användare som äger kontona. Auktoritär användaridentitetsinformation finns vanligtvis i databaser och kataloger av mänskliga resurser.

Konton i sofistikerade IT-företag innehåller hundratals parametrar som definierar myndigheterna, och dessa uppgifter kan kontrolleras av ditt etableringssystem. Nya användare kan identifieras med de data som du tillhandahåller från den auktoritära källan. Åtkomstbegäran godkännande kapacitet initierar de processer som godkänner (eller avvisa) resursetablering för dem.

| Livscykelhanteringsfas | På plats | Molnet | Hybrid |
| --- | --- | --- | --- |
| Kontohantering och etablering |Med hjälp av serverrollen Active Directory® Domain Services (AD DS) kan du skapa en skalbar, säker och hanterbar infrastruktur för användare och resurshantering och erbjuda stöd för katalogaktiverade applikationer som Microsoft® Exchange Server. <br><br> [Du kan etablera grupper i AD DS via en identitetshanterare](https://technet.microsoft.com/library/ff686261.aspx) <br>[Du kan etablera användare i AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Administratörer kan använda åtkomstkontroll för att hantera användaråtkomst till delade resurser av säkerhetsskäl. I Active Directory administreras åtkomstkontrollen på objektnivå genom att olika åtkomstnivåer eller behörigheter ställs in på objekt, till exempel Fullständig kontroll, Skriv, Läsa eller Ingen åtkomst. Åtkomstkontroll i Active Directory definierar hur olika användare kan använda Active Directory-objekt. Som standard är behörigheterna för objekt i Active Directory inställda på den säkraste inställningen. |Du måste skapa ett konto för varje användare som kommer åt en Microsoft-molntjänst. Du kan också ändra användarkonton eller ta bort dem när de inte längre behövs. Som standard har användarna inte administratörsbehörighet, men du kan också tilldela dem. <br><br> I Azure Active Directory är en av de viktigaste funktionerna möjligheten att hantera åtkomst till resurser. Dessa resurser kan vara en del av katalogen, som i fallet med behörigheter för att hantera objekt via roller i katalogen, eller resurser som är externa för katalogen såsom SaaS-program, Azure-tjänster och SharePoint-webbplatser eller lokala resurser. <br><br> I mitten av Azure Active Directorys åtkomsthanteringslösning finns säkerhetsgruppen. Resursägaren (eller administratören för katalogen) kan tilldela en grupp för att ge en viss åtkomsträtt till resurser som ägaren äger. Medlemmarna i gruppen får åtkomst och resursägaren kan delegera rätten att hantera medlemslistan för en grupp till någon annan, till exempel en avdelningschef eller en helpdesk-administratör<br> <br> Avsnittet Hantera grupper i Azure AD innehåller mer information om hur du hanterar åtkomst via grupper. |Utöka Active Directory-identiteter till molnet genom synkronisering och federation |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
Rollbaserad åtkomstkontroll (RBAC) använder roller och etableringsprinciper för att utvärdera, testa och tillämpa dina affärsprocesser och regler för att bevilja åtkomst till användare. Nyckeladministratörer skapar etableringsprinciper och tilldelar användare till roller och som definierar uppsättningar med berättiganden till resurser för dessa roller. RBAC utökar identitetshanteringslösningen för att använda programvarubaserade processer och minska användarhandbokens interaktion i etableringsprocessen.
Azure AD RBAC gör det möjligt för företaget att begränsa antalet åtgärder som en individ kan göra när de har åtkomst till Azure-portalen. Genom att använda RBAC för att kontrollera åtkomsten till portalen delegerar IT-administratörer åtkomst med hjälp av följande åtkomsthanteringsmetoder:

* **Gruppbaserad rolltilldelning:** Du kan tilldela åtkomst till Azure AD-grupper som kan synkroniseras från din lokala Active Directory. På så sätt kan du utnyttja de befintliga investeringar som din organisation har gjort i verktyg och processer för att hantera grupper. Du kan också använda den delegerade grupphanteringsfunktionen i Azure AD Premium.
* **Utnyttja inbyggda roller i Azure**: Du kan använda tre roller – Ägare, Deltagare och Läsare, för att säkerställa att användare och grupper har behörighet att bara utföra de uppgifter de behöver för att utföra sina jobb.
* **Detaljerad åtkomst till resurser**: Du kan tilldela roller till användare och grupper för en viss prenumeration, resursgrupp eller en enskild Azure-resurs, till exempel en webbplats eller databas. På så sätt kan du se till att användarna har åtkomst till alla resurser de behöver och ingen åtkomst till resurser som de inte behöver hantera.

## <a name="provisioning-and-other-customization-options"></a>Etablering och andra anpassningsalternativ
Ditt team kan använda affärsplaner och krav för att bestämma hur mycket som ska anpassa identitetslösningen. Ett stort företag kan till exempel kräva en stegvis utrullningsplan för arbetsflöden och anpassade kort som baseras på en tidsrad för stegvis etablering av program som används i stor utsträckning över geografiska områden. En annan anpassningsplan kan innehålla två eller flera program som ska etableras i en hel organisation, efter lyckad testning. Interaktion mellan användare och program kan anpassas och procedurer för etablering av resurser kan ändras för automatisk etablering.

Du kan avetablera för att ta bort en tjänst eller komponent. Om du till exempel avetableras av ett konto innebär det att kontot tas bort från en resurs.

Hybridmodellen för etableringsresurser kombinerar begäranden och rollbaserade metoder, som båda stöds av Azure AD. För en delmängd av medarbetare eller hanterade system kanske ett företag vill automatisera åtkomst med rollbaserad tilldelning. Ett företag kan också hantera alla andra åtkomstbegäranden eller undantag via en begäran-baserad modell. Vissa företag kan börja med manuell tilldelning och utvecklas mot en hybridmodell, med avsikt att en helt rollbaserad distribution i framtiden.

Andra företag kan tycka att det är opraktiskt av affärsmässiga skäl att uppnå fullständig rollbaserad etablering, och rikta en hybrid strategi som ett önskat mål. Ytterligare andra företag kan vara nöjda med endast begäran-baserad etablering, och inte vill investera ytterligare ansträngningar för att definiera och hantera rollbaserade, automatiserade etableringsprinciper.

## <a name="license-management"></a>Licenshantering
Gruppbaserad licenshantering i Azure AD gör det möjligt för administratörer att tilldela användare till en säkerhetsgrupp och Azure AD automatiskt tilldelar licenser till alla medlemmar i gruppen. Om en användare senare läggs till eller tas bort från gruppen tilldelas eller tas en licens bort automatiskt.

Du kan använda grupper som du synkroniserar från lokalt AD eller hantera i Azure AD. Para ihop detta med Azure AD premium Självbetjäning Grupphantering kan du enkelt delegera licenstilldelning till lämpliga beslutsfattare. Du kan vara säker på att problem som licenskonflikter och saknade platsdata sorteras automatiskt.

## <a name="self-regulating-user-administration"></a>Självreglerande användaradministration
När din organisation börjar etablera resurser i alla interna organisationer implementerar du den självreglerande användarens administrationskapacitet. Du kan inse fördelarna och fördelarna med att etablera användare över organisationsgränser. I den här miljön återspeglas en ändring av en användares status automatiskt i åtkomsträttigheter över organisationsgränser och geografiska områden. Du kan minska etableringskostnaderna och effektivisera åtkomst- och godkännandeprocesserna. Implementeringen förverkligar den fulla potentialen av att implementera rollbaserad åtkomstkontroll för hantering av åtkomst från till på tid i organisationen. Du kan minska de administrativa kostnaderna genom automatiska procedurer för styrning av användaretablering. Du kan förbättra säkerheten genom att automatisera efterlevnad av säkerhetsprinciper och effektivisera och centralisera användarlivscykelhantering och resursetablering för stora användargrupper.

> [!NOTE]
> Mer information finns i Konfigurera Azure AD för självbetjäningshantering av program
> 
> 

Licensbaserade (berättigandebaserade) Azure AD-tjänster fungerar genom att aktivera en prenumeration i din Azure AD-katalog/tjänstklient. När prenumerationen är aktiv kan tjänstfunktionerna hanteras av katalog-/tjänstadministratörer och användas av licensierade användare. 

## <a name="integration-with-other-3rd-party-providers"></a>Integration med andra tredjepartsleverantörer

Azure Active Directory ger enkel inloggning och förbättrad programåtkomstsäkerhet till tusentals SaaS-program och lokala webbprogram. Mer information finns i [Integrera program med Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

## <a name="define-synchronization-management"></a>Definiera synkroniseringshantering
Om du integrerar dina lokala kataloger med Azure AD kan du hjälpa dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Med den här integrationen kan användare och organisationer dra nytta av följande:

* Organisationer kan ge användare en gemensam hybrididentitet för lokala eller molnbaserade tjänster som utnyttjar Windows Server Active Directory och sedan ansluter till Azure Active Directory.
* Administratörer kan ange villkorlig åtkomst baserat på programresurs, enhets- och användaridentitet, nätverksplats och multifaktorautentisering.
* Användare kan använda sin gemensamma identitet via konton i Azure AD till Office 365-, Intune-, SaaS-appar och program från tredje part.
* Utvecklare kan skapa program som utnyttjar den gemensamma identitetsmodellen och integrerar program i Active Directory lokalt eller Azure för molnbaserade program

Följande bild har ett exempel på en vy på hög nivå för identitetssynkroniseringsprocessen.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Identitetssynkroniseringsprocess

Gå igenom följande tabell för att jämföra synkroniseringsalternativen:

| Alternativ för synkroniseringshantering | Fördelar | Nackdelar |
| --- | --- | --- |
| Synkroniseringsbaserad (via DirSync eller AADConnect) |Användare och grupper synkroniserade från lokalt och moln <br>  **Principkontroll**: Kontoprinciper kan ställas in via Active Directory, vilket ger administratören möjlighet att hantera lösenordsprinciper, arbetsstation, begränsningar, lockoutkontroller med mera, utan att behöva utföra ytterligare uppgifter i molnet.  <br>  **Åtkomstkontroll:** Kan begränsa åtkomsten till molntjänsten så att tjänsterna kan nås via företagsmiljön, via onlineservrar eller båda. <br>  Minskade supportsamtal: Om användarna har färre lösenord att komma ihåg är det mindre troligt att de glömmer dem. <br>  Säkerhet: Användaridentiteter och information skyddas eftersom alla servrar och tjänster som används i enkel inloggning behärskas och kontrolleras lokalt. <br>  Stöd för stark autentisering: Du kan använda stark autentisering (kallas även tvåfaktorsautentisering) med molntjänsten. Om du använder stark autentisering måste du dock använda enkel inloggning. | |
| Federationsbaserad (via AD FS) |Aktiverad av Security Token Service (STS). När du konfigurerar en STS för att ge enkel inloggningsåtkomst med en Microsoft-molntjänst skapar du ett federerat förtroende mellan din lokala STS och den federerade domän som du har angett i din Azure AD-klientorganisation. <br> Gör det möjligt för slutanvändare att använda samma uppsättning autentiseringsuppgifter för att få åtkomst till flera resurser <br>slutanvändarna behöver inte underhålla flera uppsättningar autentiseringsuppgifter. Ändå måste användarna ange sina autentiseringsuppgifter till var och en av de deltagande resurserna.,B2B- och B2C-scenarier som stöds. |Kräver specialiserad personal för distribution och underhåll av dedikerade på lokala AD FS-servrar. Det finns begränsningar för användning av stark autentisering om du planerar att använda AD FS för din STS. Mer information finns i [Konfigurera avancerade alternativ för AD FS 2.0](https://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Mer information finns [i Integrering av lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Se även
[Översikt över designöverväganden](plan-hybrid-identity-design-considerations-overview.md)

