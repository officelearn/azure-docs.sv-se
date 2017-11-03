---
title: "Azure Active Directory villkorlig åtkomst | Microsoft Docs"
description: "Använd villkorlig åtkomstkontroll i Azure Active Directory för att söka efter särskilda villkor vid autentisering för åtkomst till program."
services: active-directory
keywords: "villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/27/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 4cf30130907151ade9eaf9db28748b8141dac8e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="conditional-access-in-azure-active-directory"></a>Villkorlig åtkomst i Azure Active Directory

I en mobile första, molnet först värld kan Azure Active Directory enkel inloggning till enheter, appar och tjänster från var som helst. Med den ökande mängden av enheter (inklusive BYOD) fungerar av företagets nätverk och 3 part SaaS-appar, IT-proffs står inför två andra mål:

- Slutanvändare att vara produktiva var och när
- Skydda företagets tillgångar när som helst

För att förbättra produktiviteten, ger Azure Active Directory användarna en mängd olika alternativ för att få åtkomst till ditt företags tillgångar. Hantering av program, Azure Active Directory, kan du se till att endast *rätt personer* kan komma åt dina program. Vad händer om du vill ha mer kontroll över hur rätt personer använder dina resurser under vissa förhållanden? Vad händer om du även har villkor som du vill blockera åtkomst till vissa appar även för den *Högerklicka personer*? Exempelvis kanske OK du om rätt personer kommer åt vissa appar från ett betrott nätverk. Däremot kanske du inte vill de kan komma åt dessa appar från ett nätverk som du inte litar på. Du kan åtgärda dessa frågor med villkorlig åtkomst.

Villkorlig åtkomst är en funktion i Azure Active Directory som gör att du kan tillämpa kontroller på åtkomst till appar i din miljö baserat på specifika villkor. Du kan antingen koppla ytterligare krav för åtkomst med kontroller, eller du kan blockera den. Implementeringen av villkorlig åtkomst baseras på principer. Principbaserad inställning förenklar konfigurationen upplevelsen eftersom följer du kanske ser om dina krav.  

Normalt kan du definiera åtkomstkraven med hjälp av rapporter som baseras på följande sätt:

![Kontrollen](./media/active-directory-conditional-access-azure-portal/10.png)

När du ersätter två förekomster av ”*detta*” verkliga information du har ett exempel på en kommentar som känner till dig du förmodligen:

*När leverantörer försöker få åtkomst till vår molnappar från nätverk som inte är betrodda, sedan blockera åtkomst.*

Princip-satsen ovan visar power av villkorlig åtkomst. Medan du kan aktivera leverantörer i princip åtkomst till dina molnappar (**som**), med villkorlig åtkomst kan du också definiera villkor under vilka åtkomst är möjligt (**hur**).

I samband med Azure Active Directory villkorlig åtkomst

- ”**När detta sker**” kallas **condition-instruktion**
- ”**Gör du så här**” kallas **kontroller**

![Kontrollen](./media/active-directory-conditional-access-azure-portal/11.png)

Kombinationen av en condition-instruktion med kontrollerna representerar en princip för villkorlig åtkomst.

![Kontrollen](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>Kontroller

I en princip för villkorlig åtkomst definierar kontroller vad det är som ska hända när en villkorssatsen har uppfyllts.  
Med kontroller kan du blockera åtkomst eller tillåter åtkomst med ytterligare krav.
Du måste välja minst ett krav när du konfigurerar en princip som ger åtkomst.  

Det finns två typer av kontroller: 

- **Bevilja styr** -bevilja kontroller styr om en användare kan slutföra autentisering och nå resursen som de försöker logga in på. Om du har markerat flera kontroller kan konfigurera du om alla krävs när principen har bearbetats.
Den aktuella implementationen av Azure Active Directory kan du konfigurera följande bevilja kontrollen krav:

    ![Kontrollen](./media/active-directory-conditional-access-azure-portal/73.png)

- **Sessionen kontroller** -Session styr aktivera begränsa användarupplevelse inom en molnappen. Sessionen kontroller tillämpas av molnappar och förlitar sig på ytterligare information som tillhandahålls av Azure AD App om sessionen.

    ![Kontrollen](./media/active-directory-conditional-access-azure-portal/31.png)


Mer information finns i [kontrollerna i Azure Active Directory villkorlig åtkomst](active-directory-conditional-access-controls.md).


## <a name="condition-statement"></a>Villkorssatsen

I föregående avsnitt har införts stöds alternativ att blockera eller begränsa åtkomsten till resurser i form av kontroller. I en princip för villkorlig åtkomst definierar du de kriterier som måste uppfyllas för din kontroller som ska användas i form av en condition-instruktion.  

Du kan inkludera följande tilldelningar i din villkorssatsen:

![Kontrollen](./media/active-directory-conditional-access-azure-portal/07.png)


### <a name="who"></a>Vem?

Du måste markera de användare eller grupper som principen gäller när du konfigurerar en princip för villkorlig åtkomst. I många fall vill du kontrollerna som ska tillämpas på en specifik uppsättning användare. I en villkorssatsen kan du definiera detta genom att välja nödvändiga användare och grupper som principen gäller för. Om det behövs kan du även uttryckligen undanta en uppsättning användare från din princip genom att undanta dem.  

![Kontrollen](./media/active-directory-conditional-access-azure-portal/08.png)



### <a name="what"></a>Vad?

Du måste välja molnappar som principen gäller när du konfigurerar en princip för villkorlig åtkomst.
Det finns normalt sett vissa appar i din miljö som kräver mer uppmärksamhet än andra ur skydd. Detta påverkar t.ex, appar som har åtkomst till känslig information.
Genom att välja molnappar, definiera omfattningen av molnappar som principen gäller för. Om det behövs kan du också explicit utesluta en uppsättning appar från principen.

![Kontrollen](./media/active-directory-conditional-access-azure-portal/09.png)

En fullständig lista över molnappar som du kan använda i din princip för villkorlig åtkomst finns i [Teknisk referens för Azure Active Directory villkorlig åtkomst](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).

### <a name="how"></a>Hur?

Så länge åtkomst till dina appar utförs enligt villkor som du kan styra kanske behöver för att införa ytterligare kontroller på hur dina molnappar används av användarna. Saker kan dock se annorlunda ut om åtkomst till dina molnappar utförs, till exempel från nätverk som inte är betrodda eller enheter som inte är kompatibla. Du kan definiera vissa villkor som har ytterligare krav för hur åtkomst till dina appar utförs i en condition-instruktion.

![Villkor](./media/active-directory-conditional-access-azure-portal/01.png)


## <a name="conditions"></a>Villkor

I den aktuella implementationen av Azure Active Directory kan du definiera villkor för följande områden:

- Logga in risk
- Enhetsplattformar
- Platser
- Klientprogram


![Villkor](./media/active-directory-conditional-access-azure-portal/01.png)

### <a name="sign-in-risk"></a>Logga in risk

En risk för inloggning är ett objekt som används av Azure Active Directory för att spåra sannolikheten för att en inloggning försök inte utfördes av legitima ägaren till ett användarkonto. I det här objektet sannolikheten (hög, medel eller låg) lagras i form av ett attribut som kallas [inloggning risknivå](active-directory-reporting-risk-events.md#risk-level). Det här objektet skapas under en inloggning för en användare logga in risker har upptäckts av Azure Active Directory. Mer information finns i avsnittet om [riskfyllda inloggningar](active-directory-identityprotection.md#risky-sign-ins).  
Du kan använda den beräknade inloggning risknivån som villkor i en princip för villkorlig åtkomst. 

![Villkor](./media/active-directory-conditional-access-azure-portal/22.png)

### <a name="device-platforms"></a>Enhetsplattformar

Enhetsplattformen kännetecknas av operativsystemet som körs på din enhet:

- Android
- iOS
- Windows Phone
- Windows
- macOS (förhandsversion). 

![Villkor](./media/active-directory-conditional-access-azure-portal/02.png)

Du kan definiera de enhetsplattformar som ingår samt plattformar för enheter som är undantagna från en princip.  
Om du vill använda enhetsplattformar i principen först ändra du knapparna för att konfigurera **Ja**, och markera alla eller enskilda enhetsplattformar som principen gäller för. Om du väljer enskilda enhetsplattformar påverkar principen endast på dessa plattformar. I det här fallet påverkas inloggningar till andra plattformar som stöds inte av principen.


### <a name="locations"></a>Platser

Med platser har du möjlighet att definiera villkor som är baserade på där ett anslutningsförsök initierades från. Poster i listan över platser är antingen **med namnet platser** eller **MFA tillförlitliga IP-adresser**.  

**Med namnet platser** är en funktion i Azure Active Directory som gör det möjligt att definiera etiketter för platser anslutningsförsök gjordes från. Om du vill definiera en plats kan du konfigurera en IP-adressintervall eller välj ett land / region.  

![Villkor](./media/active-directory-conditional-access-azure-portal/42.png)

Dessutom kan du markera en namngiven plats som betrodd plats. För en villkorlig åtkomstprincip betrodda platsen är ett annat filteralternativ som gör att du kan välja *alla betrodda platser* i dina platser-villkor.
Sökvägarna är också viktigt i samband med identifiering av [riskerar händelser](active-directory-reporting-risk-events.md) att minska antalet FALSKT positiva identifieringar för den omöjligt att resan till ovanliga platser risk händelse. 

Antalet namngivna platser som du kan konfigurera är begränsad av storleken på det relaterade objektet i Azure AD. Du kan konfigurera:
 
 - En namngiven plats med upp till 500 IP-adressintervall
 - Högst 60 sökvägarna (förhandsversion) med en IP-adressintervall som är tilldelade till var och en av dem. 

Mer information finns i [med namnet platser i Azure Active Directory](active-directory-named-locations.md).


**MFA tillförlitliga IP-adresser** är en funktion i Multi-Factor authentication som gör att du kan definiera tillförlitliga IP-adressintervall som motsvarar din organisations lokalt intranät. När du konfigurerar ett villkor för platsen, kan tillförlitliga IP-adresser du skilja mellan anslutningar till nätverket och alla andra platser. Mer information finns i [tillförlitliga IP-adresser](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  

I principen för villkorlig åtkomst kan du:

- Inkludera
    - Valfri plats
    - Alla betrodda platser
    - Valda platser
- Exkludera
    - Alla betrodda platser
    - Valda platser
     
![Villkor](./media/active-directory-conditional-access-azure-portal/03.png)


### <a name="client-apps"></a>Klientprogram

Klientappen kan vara antingen på en allmän nivå app (webbläsare, mobila appar klientversionen) som du har använt för att ansluta till Azure Active Directory eller mer specifikt kan du välja Exchange Active Sync.  
Äldre autentisering refererar till klienter som använder grundläggande autentisering, till exempel äldre Office-klienter som inte använder modern autentisering. Villkorlig åtkomst stöds för närvarande inte med äldre autentisering.

![Villkor](./media/active-directory-conditional-access-azure-portal/04.png)


En fullständig lista över de klientappar som du kan använda i din princip för villkorlig åtkomst finns i [Teknisk referens för Azure Active Directory villkorlig åtkomst](active-directory-conditional-access-technical-reference.md#client-apps-condition).




## <a name="common-scenarios"></a>Vanliga scenarier

### <a name="requiring-multi-factor-authentication-for-apps"></a>Att kräva multifaktorautentisering för appar

Många miljöer har appar som kräver en högre nivå av skydd än den andra.
Detta är exempelvis fallet för appar som har åtkomst till känslig information.
Om du vill lägga till ett extra skyddslager för dessa appar kan du konfigurera en princip för villkorlig åtkomst som kräver multifaktorautentisering när användarna kommer åt dessa appar.


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>Kräver multifaktorautentisering för åtkomst från nätverk som inte är betrodda

Det här scenariot liknar föregående scenario eftersom den lägger till ett krav för multifaktorautentisering.
Den största skillnaden är dock villkoret för det här kravet.  
Medan fokus i det föregående scenariot var i appar med åtkomst till sensitve data, fokuserar i det här scenariot på betrodda platser.  
Du kan med andra ord ha ett krav för multifaktorautentisering om en app öppnas av en användare från ett nätverk som du inte litar på.


### <a name="only-trusted-devices-can-access-office-365-services"></a>Endast betrodda enheter kan komma åt Office 365-tjänster

Om du använder Intune i din miljö kan du direkt börja använda gränssnittet för princip för villkorlig åtkomst i Azure-konsolen.

Många Intune-kunder använder villkorlig åtkomst för att se till att endast betrodda enheter kan komma åt Office 365-tjänster. Detta innebär att mobila enheter har registrerats med Intune och uppfyller krav på efterlevnad och att Windows-datorer är anslutna till en lokal domän. En viktiga förbättringar är att du inte behöver ange samma princip för varje Office 365-tjänster.  När du skapar en ny princip, konfigurera molnappar för att inkludera varje O365-appar som du vill skydda med villkorlig åtkomst.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 
