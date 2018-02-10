---
title: "Azure Active Directory villkorlig åtkomst | Microsoft Docs"
description: "Lär dig hur villkorlig åtkomst i Azure Active Directory kan hjälpa dig att hantera åtkomstkontroll från en central plats."
services: active-directory
keywords: "villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 156ec054c36e6ad5bd9011954e96fe1d4afa05aa
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="conditional-access-in-azure-active-directory"></a>Villkorlig åtkomst i Azure Active Directory

Säkerhet är ett viktigt mål för organisationer som använder molnet. En viktig aspekt av molnet säkerhet är identitets- och när det gäller att hantera dina molnresurser. Med mobile första, molnet först världen, kan användare åtkomst till organisationens resurser med olika enheter och appar från var som helst. Det innebär räcker bara fokusera på vem som kan komma åt en resurs inte längre. För att kunna master balansen mellan säkerhet och produktivitet måste IT-proffs också factor hur en resurser används i ett access control beslut. Med villkorlig åtkomst i Azure AD, kan du lösa det här kravet. Villkorlig åtkomst är en funktion i Azure Active Directory som gör att du kan använda reglagen på åtkomst till appar i din miljö baserat på specifika villkor från en central plats. 


![Kontroll](./media/active-directory-conditional-access-azure-portal/81.png)

Den här artikeln innehåller en översikt av villkorlig åtkomst i Azure AD.


## <a name="common-scenarios"></a>Vanliga scenarier

I en mobile första, molnet först värld kan Azure Active Directory enkel inloggning till enheter, appar och tjänster från var som helst. Med den ökande mängden av enheter (inklusive BYOD) fungerar av företagets nätverk och 3 part SaaS-appar, IT-proffs står inför två andra mål:

- Ge användarna att vara produktiva var och när
- Skydda företagets tillgångar när som helst

Du kan använda rätt åtkomstkontroller på villkor som krävs med hjälp av principer för villkorlig åtkomst. Azure AD villkorlig åtkomst ger ökad säkerhet vid behov och förblir utanför dina användares sätt när den inte. 

Nedan visas några vanliga problem för åtkomst som villkorlig åtkomst kan hjälpa dig med:



- **Logga in risk**: Azure AD Identity Protection identifierar inloggning risker. Hur du begränsa åtkomsten om identifierade risk inloggning anger en felaktig aktören? Vad händer om du vill hämta en starkare bevis att en inloggning verkligen har utförts av legitim användare eller din tvivel är starkt att även blockera specifika användare från att komma åt en app?

- **Nätverksplats**: Azure AD kan nås från var som helst. Vad händer om en åtkomstförsök utförs från en nätverksplats som inte kontrolleras av IT-avdelningen? Med hjälp av en kombination av användarnamn och lösenord kan vara bra tillräckligt som bevis på identitet för åtkomst försöker dina resurser från företagsnätverket. Vad händer om du behovet av en starkare identitetsbevis åtkomst försöker som initieras från andra oväntat länder eller regioner världen? Vad händer om du även vill blockera åtkomst försöker från vissa platser?  

- **Enhetshantering**: I Azure AD användare har åtkomst till molnappar från en mängd olika enheter, inklusive mobila och personliga enheter. Vad händer om du behöver den åtkomstförsök bara ska utföras användare som använder enheter som hanteras av IT-avdelningen? Vad händer om du vill även blockera vissa typer av formuläret Enhetsåtkomst i molnappar i din miljö? 

- **Klientprogrammet**: idag, du kan komma åt många molnappar med hjälp av olika apptyper som till exempel webbaserade appar, mobila appar eller skrivbordsprogram. Vad händer om en åtkomstförsök utförs med hjälp av en typ av klient app som orsakar kända problem? Vad händer om du behöver en enhet som hanteras av IT-avdelningen för vissa typer av appen? 

Dessa och relaterade svaren representerar vanliga scenarier för åtkomst för villkorlig åtkomst i Azure AD. Villkorlig åtkomst är en funktion i Azure Active Directory som gör att du kan hantera åtkomst scenarier med hjälp av en principbaserad metod.


## <a name="conditional-access-policies"></a>Principer för villkorlig åtkomst

En princip för villkorlig åtkomst är definition av ett scenario för åtkomst med hjälp av följande mönster:

![Kontroll](./media/active-directory-conditional-access-azure-portal/10.png)

**Gör detta** definierar svar i principen. Det är viktigt att notera att målet för en villkorlig åtkomstprincip inte att bevilja åtkomst till en molnapp. I Azure AD är bevilja åtkomst till molnappar föremål för användare. Med en princip för villkorlig åtkomst som du styr hur behöriga användare (användare som har beviljats åtkomst till en molnapp) har åtkomst till molnappar vissa villkor. I ditt svar tillämpa du ytterligare krav som multifaktorautentisering, en hanterad enhet och andra. I samband med villkorlig åtkomst i Azure AD kallas de krav som din princip tillämpar åtkomstkontroller. I formuläret mest restriktiva kan principen blockera åtkomst. Mer information finns i [åtkomstkontroller i Azure Active Directory för villkorlig åtkomst](active-directory-conditional-access-controls.md).
     

**När detta inträffar** definierar orsak för att utlösa principen. Därför kännetecknas av en grupp av villkor är uppfyllda. Spela upp en särskild roll i Azure AD villkorlig åtkomst, tilldelning av två villkor:

- **Användare**: de användare som utför ett åtkomstförsök (**som**). 

- **Molnappar**: ett åtkomstförsök mål (**vad**).    

Dessa två villkor är obligatoriskt i en princip för villkorlig åtkomst. Du kan även inkludera ytterligare villkor som beskriver hur försöket utförs utöver de två obligatoriska villkor. Vanliga exempel använder mobila enheter eller platser utanför företagets nätverk. Mer information finns i [villkor i Azure Active Directory för villkorlig åtkomst](active-directory-conditional-access-conditions.md).   

Kombinationen av villkor med din åtkomstkontroller representerar en princip för villkorlig åtkomst. 

![Kontroll](./media/active-directory-conditional-access-azure-portal/51.png)

Med villkorlig åtkomst till Azure AD, kan du styra hur behöriga användare kan komma åt dina molnappar. Målet med en princip för villkorlig åtkomst är att tillämpa ytterligare kontroller i ett åtkomstförsök till en molnapp som drivs av hur ett åtkomstförsök utförs.

En fördel med att använda en principbaserad metod för att skydda åtkomst till dina molnappar är att du kan starta utformning principkraven för din miljö med strukturen som beskrivs i den här artikeln utan att oroa teknisk implementering. 



## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om villkor finns [villkor i Azure Active Directory för villkorlig åtkomst](active-directory-conditional-access-conditions.md).

- Om du vill veta mer om åtkomstkontroller, se [åtkomstkontroller i Azure Active Directory för villkorlig åtkomst](active-directory-conditional-access-controls.md).

- Om du vill hämta viss erfarenhet av att konfigurera principer för villkorlig åtkomst, se [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 
