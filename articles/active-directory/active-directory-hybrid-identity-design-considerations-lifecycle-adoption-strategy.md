---
title: "Identitet hybridutformning - livscykel införandestrategin Azure | Microsoft Docs"
description: "Bidrar till att definiera hanteringsuppgifter för hybrid identity enligt alternativen för varje fas i livscykeln."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: bfa74c7557819bbef334fc94eb42e5ba83cf3fee
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Fastställa införandestrategin för hybrid identity livscykel
I det här steget ska du definiera identity management strategin för din hybrididentitetslösning att uppfylla affärskrav som du definierade i [fastställa hanteringsuppgifter för hybrid identity](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Om du vill definiera hanteringsuppgifter för hybrid identity enligt livscykeln för slutpunkt till slutpunkt-identitet som presenteras tidigare i det här steget kommer du måste beaktas vilka alternativ som är tillgängliga för varje fas i livscykeln.

## <a name="access-management-and-provisioning"></a>Hantering och etablering
Med en bra konto åtkomstlösning, kan din organisation spåra exakt som har åtkomst till vilken information i organisationen.

Åtkomstkontroll är en viktig funktion av en central, enda punkt etablering system. Förutom att skydda känslig information åtkomstkontroller för att visa befintliga konton som har icke-godkända tillstånd eller är inte längre behövs. Du kan styra föråldrade konton, etablering system länkar tillsammans kontoinformationen med auktoritär information om användarna som äger konton. Auktoritativa användaren identitetsinformation underhålls vanligtvis i databaser och kataloger på personalavdelningen.

Konton i avancerade IT-företag med hundratals olika parametrar som definierar utfärdare och dessa uppgifter kan styras av systemet etablering. Nya användare kan identifieras med de data som du anger auktoritativ datakälla. Godkännande-möjlighet till begäran initierar godkänna (eller avvisa) resurs etablering för dessa processer.

| Livscykeln för hantering av fas | Lokalt | Molnet | Hybrid |
| --- | --- | --- | --- |
| Kontohantering och etablering |Med hjälp av serverrollen Active Directory® Domain Services (AD DS) kan du skapa en skalbar, säker och hanterbar infrastruktur för användare och resurshantering och erbjuda stöd för katalogaktiverade applikationer som Microsoft® Exchange Server. <br><br> [Du kan etablera grupper i AD DS via en Identity manager](https://technet.microsoft.com/library/ff686261.aspx) <br>[Du kan etablera användare i AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Administratörer kan använda åtkomstkontroll för att hantera användarnas åtkomst till delade resurser av säkerhetsskäl. I Active Directory administreras åtkomstkontroll på objektnivå genom att ange olika nivåer av åtkomst eller behörighet till objekt, till exempel fullständig behörighet, Skriv-, läs- eller ingen åtkomst. Åtkomstkontroll i Active Directory definierar hur olika användare kan använda Active Directory-objekt. Behörigheter för objekt i Active Directory är som standard den säkraste inställningen. |Du måste skapa ett konto för varje användare som kommer åt en Microsoft-molntjänst. Du kan också ändra användarkonton eller ta bort dem när de inte längre behövs. Användare har inte administratörsbehörighet som standard, men du kan tilldela dem (valfritt). Mer information finns i [hantera användare i Azure AD](active-directory-create-users.md). <br><br> En av de viktigaste funktionerna är möjligheten att hantera åtkomst till resurser i Azure Active Directory. Dessa resurser kan inte ingå i katalogen, som i fallet med behörigheter för att hantera objekt genom roller i katalogen eller resurser som är externa för katalogen som SaaS-program, Azure-tjänster, och SharePoint-webbplatser eller lokala resurser. <br><br> I mitten av Azure Active Directorys åtkomst är hanteringslösning säkerhetsgruppen. Resursägare (eller administratör för katalogen) kan tilldela en grupp som ger en viss åtkomst till resurser som de äger. Medlemmar i gruppen kommer att tillhandahållas åtkomst och resursägaren kan delegera behörighet för att hantera listan över medlemmar i en grupp till någon annan – till exempel en avdelningschef eller administratör supportavdelning<br> <br> Hantera grupper i Azure AD-avsnittet finns mer information om hur du hanterar åtkomst via grupper. |Utöka Active Directory identiteter till molnet via synkronisering och federation |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
Rollbaserad åtkomst styr (RBAC) använder roller och provisioning principer för att utvärdera, testa och tillämpa dina affärsprocesser och regler för att bevilja åtkomst till användare. Viktiga administratörer skapa principer för etablering och tilldela användare till roller och som definierar uppsättningar av rättigheter till resurser för dessa roller. RBAC utökar identitetshanteringslösning för att använda programvarubaserade processer och minska manuella användaråtgärder i etableringsprocessen.
Azure AD-RBAC kan företag att begränsa mängden operationer som en enskild person kan göra när han har åtkomst till Azure-hanteringsportalen. Genom att använda RBAC för att styra åtkomsten till portalen kan IT-administratörer certifikatutfärdare delegera åtkomst med hjälp av följande metoder för hantering av åtkomst:

* **Gruppbaserade rolltilldelning**: du kan tilldela åtkomst till Azure AD-grupper som kan synkroniseras från din lokala Active Directory. På så sätt kan du utnyttja befintliga investeringar som organisationen har gjort i verktygsuppsättning och processer för att hantera grupper. Du kan också använda funktionen för hantering av delegerad grupp i Azure AD Premium.
* **Utnyttjar inbyggda roller i Azure**: du kan använda tre roller, ägare, deltagare och läsare, så att användare och grupper har behörighet att utföra de uppgifter som de behöver för att utföra sitt arbete.
* **Detaljerade åtkomst till resurser**: du kan tilldela roller till användare och grupper för en viss prenumeration, resursgrupp eller en enskild Azure resurs, till exempel en webbplats eller i databasen. På så sätt kan du se till att användarna har åtkomst till alla resurser som de behöver och ingen åtkomst till resurser som de inte behöver hantera.

## <a name="provisioning-and-other-customization-options"></a>Etablering och andra alternativ för anpassning
Ditt team kan använda affärsplaner och krav för att avgöra hur mycket du vill anpassa identitetslösning. Stora företag kan till exempel kräva en stegvis distributionsplan för arbetsflöden och anpassade kort som baseras på en tidslinje för att etablera inkrementellt program som används ofta i geografiska områden. En annan anpassning planen kan innehålla två eller flera program som ska etableras i hela företaget, efter lyckad testning. Interaktion från användaren program kan anpassas och procedurer för att etablera resurser kan ändras för automatisk etablering.

Du kan ta bort etableringen för att ta bort en tjänst eller en komponent. Till exempel innebär avetablering ett konto att kontot tas bort från en resurs.

Hybrid-modell för etablering av resurser kombinerar begäran och rollbaserad metoder som stöds både av Azure AD. För en delmängd av anställda eller hanterade system kan ett företag vill automatisera åtkomst med rollbaserad tilldelning. Ett företag kan också hantera alla andra förfrågningar eller undantag via en frågebaserad modell. Vissa företag kan börja med manuell tilldelning och utvecklas mot en hybrid-modell med en avsikt är att ett fullständigt rollbaserad distributionen vid ett senare tillfälle.

Andra företag kan hitta den opraktiska för affärs-skäl att uppnå fullständig rollbaserad etablering och rikta en hybrid-metod som önskat mål. Fortfarande andra företag kan vara nöjd med endast begäran-baserad etablering och inte vill att investera extra arbete att definiera och hantera rollbaserad, automatiserad etablering principer.

## <a name="license-management"></a>Licenshantering
Gruppbaserade licenshantering i Azure AD kan administratörer tilldela användare till en säkerhetsgrupp och Azure AD tilldelar automatiskt licenser till alla medlemmar i gruppen. Om en användare tas bort från gruppen eller senare lägga till tilldelats en licens automatiskt eller tas bort efter behov.

Du kan använda grupper som du synkroniserar från lokala AD eller hantera i Azure AD. Koppla ihop detta med Azure AD premium Self-Service Group Management delegera du enkelt licenstilldelning till lämplig beslutsfattare. Du kan vara säker på att problem som licens konflikter och saknas lokaliseringsuppgifter sorteras automatiskt.

## <a name="self-regulating-user-administration"></a>Automatisk reglerande Användaradministration
När din organisation startar att etablera resurser över alla interna organisationer implementera själv reglerande funktionen för administration av användare. Du kan spara fördelarna och fördelarna med etablering användare över organisationens gränser. I den här miljön återspeglas automatiskt en ändring i användarens status i åtkomsträttigheter över organisationens gränser och geografiska områden. Du kan minska kostnaderna för etablering och förenkla åtkomst och godkännande processer. Implementeringen inser med Implementera rollbaserad åtkomstkontroll för slutpunkt till slutpunkt access management i din organisation. Du kan minska administrativa kostnader genom automatisk förfaranden som reglerar användaretablering. Du kan förbättra säkerheten genom att automatisera tvingande säkerhetsprinciper, och förenkla och centralisera Livscykelhantering för användaren och resursetablering för stora användare.

> [!NOTE]
> Mer information finns i Konfigurera Azure AD för självbetjäningsportalen programhantering åtkomst
> 
> 

Licens-baserade (rätt-baserat) Azure AD-tjänster fungerar genom att aktivera en prenumeration i Azure AD directory/service-klienten. När prenumerationen är aktiv service-funktionerna hanteras av administratörer för directory-tjänsten och används av licensierade användare. Mer information finns i hur fungerar Azure AD-licensiering arbete?
Integrering med andra 3 part-leverantörer

Azure Active Directory tillhandahåller enkel inloggning på och förbättrad programsäkerhet åtkomst till tusentals SaaS-program och lokala webbprogram. En detaljerad lista över Azure Active Directory-programgalleriet för SaaS-program som stöds finns i kompatibilitetslistan för Azure Active Directory federation: tredjeparts identitetsleverantörer som kan användas för att implementera enkel inloggning

## <a name="define-synchronization-management"></a>Definiera hantering av datasynkronisering
Om du integrerar dina lokala kataloger med Azure AD kan du hjälpa dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Med den här integreringen kan användare och organisationer dra nytta av följande:

* Organisationer kan ge användarna en gemensam hybrididentitet över lokala eller molnbaserade tjänster utnyttja Windows Server Active Directory och sedan ansluta till Azure Active Directory.
* Administratörer kan ge villkorad tillgång baserat på programresursen, enheten och användarens identitet, nätverksplats och multifaktorautentisering.
* Användare kan använda sina gemensam identitet via konton i Azure AD till Office 365, Intune, SaaS-appar och program från tredje part.
* Utvecklare kan bygga program som använder den gemensamma identitetsmodellen, integrera program i Active Directory lokalt eller Azure för molnbaserade program

Följande bild innehåller ett exempel på en övergripande bild av processen för synkronisering av identitet.

![](./media/hybrid-id-design-considerations/identitysync.png)

Processen för synkronisering av identitet

Kontrollera i följande tabell om du vill jämföra alternativen synkronisering:

| Alternativ för synkronisering av Management | Fördelar | Nackdelar |
| --- | --- | --- |
| Sync-baserade (via DirSync eller AADConnect) |Användare och grupper som synkroniseras från molnet och lokalt <br>  **Princip för värdegruppen**: Kontoprinciper kan anges via Active Directory, som ger administratören möjlighet att hantera principer för lösenord, arbetsstation, begränsningar, lås ut kontroller, och mer, utan att behöva utföra ytterligare uppgifter i molnet.  <br>  **Åtkomstkontroll**: kan begränsa åtkomsten till Molntjänsten så att tjänsterna som kan nås via företagsmiljö via online servrar eller båda. <br>  Minskad supportsamtal: om användarna har färre lösenord att komma ihåg, de är mindre troligt att glömma dem. <br>  Säkerhet: Användaridentiteter och information skyddas eftersom alla servrar och tjänster som används i enkel inloggning, hanterat och kontrollerade lokalt. <br>  Stöd för stark autentisering: du kan använda stark autentisering (även kallat tvåfaktorsautentisering) med Molntjänsten. Om du använder stark autentisering, måste du använda enkel inloggning. | |
| Federationsbaserat (via AD FS) |Aktiverad som en säkerhetstokentjänst (STS). När du konfigurerar en STS att ge åtkomst för enkel inloggning med ett Microsoft-Molntjänsten, kommer du att skapa ett federerat förtroende mellan lokala STS och den federerade domänen som du har angett i Azure AD-klienten. <br> Gör att du kan använda samma uppsättning autentiseringsuppgifter för att få åtkomst till flera resurser <br>slutanvändare behöver inte underhålla flera uppsättningar med autentiseringsuppgifter. Ännu, användare måste ange sina autentiseringsuppgifter till var och en av deltagande resurserna., B2B och B2C-scenarier som stöds. |Kräver särskild personal för distribution och underhåll av dedikerade lokal AD FS-servrarna. Det finns begränsningar för användning av stark autentisering om du planerar att använda AD FS för din STS. Mer information finns i [konfigurera avancerade alternativ för AD FS 2.0](http://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Mer information finns i [integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
> 
> 

## <a name="see-also"></a>Se även
[Översikt över design-överväganden](active-directory-hybrid-identity-design-considerations-overview.md)

