---
title: Använd Azure AD-åtkomstgranskningar att hantera användare som har undantagits från principer för villkorlig åtkomst | Microsoft Docs
description: Använd Azure Active Directory (Azure AD)-åtkomstgranskningar för att hantera användare som har undantagits från principer för villkorlig åtkomst
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a82fdfe598e11415130950443fb0d3e27f5d9d01
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168548"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-that-have-been-excluded-from-conditional-access-policies"></a>Använd Azure AD-åtkomstgranskningar att hantera användare som har undantagits från principer för villkorlig åtkomst

I en perfekt värld följer alla användare åtkomst principer för säker åtkomst till organisationens resurser. Men finns ibland företagsfall som uppmanar dig att göra undantag. Den här artikeln beskrivs några exempel där undantag kan krävas och hur du som IT-administratören kan hantera den här uppgiften, undvika översyn av rutinundantag och ge granskare bevis sådana undantag granskas regelbundet med hjälp av Azure Active Directory (Azure AD)-åtkomstgranskningar.

> [!NOTE]
> En giltig Azure AD Premium P2, Enterprise Mobility + Security E5 betald eller utvärderingslicens krävs för att använda Azure AD-åtkomstgranskningar. Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Varför skulle du exkludera användare från principer?

Som IT-administratör kan du använda [Azure AD villkorsstyrd åtkomst](../conditional-access/overview.md) användare måste autentisera med hjälp av Multi-Factor authentication (MFA) eller logga in från ett betrott nätverk eller en enhet. Vid distributionsplanering veta du att vissa av dessa krav kan uppfyllas av alla användare. Exempel: det finns användare som arbetar från ett fjärrkontor som inte är en del av det interna nätverket eller det finns en chef som använder en gammal telefon som inte stöds. Företaget kräver att dessa användare att kunna logga in och utföra sitt arbete, därför undantas från principer för villkorlig åtkomst.

Du kan använda ett annat exempel är [namngivna platser](../conditional-access/location-condition.md) i villkorlig åtkomst för att konfigurera en uppsättning regioner och regioner som du inte vill ge användare åtkomst till deras klientorganisationer.

![Namngivna platser](./media/conditional-access-exclusion/named-locations.png)

Användare kan dock i vissa fall kan ha en bra anledning till att logga in från dessa blockerade länder. Användare kan till exempel resa för arbete eller personligt orsaker. I det här exemplet har principen för villkorlig åtkomst för att blockera dessa länder en dedikerad molnet säkerhetsgrupp för användare som är undantagna från principen. Användare som behöver åtkomst under resa kan lägga till sig själva till en grupp med [grupphantering för Azure AD-självbetjäning](../users-groups-roles/groups-self-service-management.md).

Ett annat exempel kanske du har en princip för villkorlig åtkomst som [block äldre autentisering för flesta användare](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Microsoft rekommenderar att du blockera användningen av äldre protokoll i din klient för att förbättra din säkerhetsposition. Men om du har några användare som är absolut behöver använda äldre autentiseringsmetoder för att komma åt resurser via Office 2010 eller IMAP-SMTP-POP baserade klienter, kan du undanta dessa användare från den princip som blockerar äldre autentiseringsmetoder.

## <a name="why-are-exclusions-challenging"></a>Varför är undantag utmanande?

I Azure AD kan du begränsa principer för villkorlig åtkomst till en uppsättning användare. Du kan också utesluta vissa av dessa användare genom att välja katalogroller, enskilda användare eller gäster av användare. Det är viktigt att komma ihåg att när dessa undantag har konfigurerats, principens avsikt inte kan tillämpas på de aktuella användarna. Om dessa undantag har konfigurerats som antingen en lista med enskilda användare eller via en äldre lokala säkerhetsgrupp så det begränsar synligheten för den här undantagslistan (användare inte kanske vet av dess finns) och IT-administratörens kontroll över den (användare kan ansluta till den säkerhetsgrupp för att hoppa över principen). Användare som är kvalificerade för undantaget i taget kan dessutom inte längre behövs eller vara berättigad till den.

Det finns en kort lista med användare som kringgår principen i början av ett undantag. Framöver kommer fler och fler användare som är undantagna och listan växer. Det finns ett behov av att granska listan och bekräfta att var och en av dessa användare fortfarande ska undantas vid en viss tidpunkt. Hantera listan från en teknisk synvinkel kan vara relativt enkelt, men vem som gör affärsbeslut och hur du ser till att det är allt granskningsbara?

Om du konfigurerar undantag till principen för villkorlig åtkomst med hjälp av en Azure AD-grupp, kan sedan du dock använda åtkomstgranskningar som en kompenserande kontroll att öka synligheten och minska antalet användare som har ett undantag.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Så här skapar du en undantagsgrupp i en princip för villkorlig åtkomst

Följ stegen nedan för att skapa en ny Azure AD-grupp och en princip för villkorlig åtkomst som inte gäller för den gruppen.

### <a name="create-an-exclusion-group"></a>Skapa en undantagsgrupp

1. Logga in på Azure Portal.

1. I det vänstra navigeringsfönstret klickar du på **Azure Active Directory** och klicka sedan på **grupper**.

1. Klicka på den översta menyn **ny grupp** att öppna fönstret grupp.

1. I den **grupptyp** väljer **Security**. Ange ett namn och beskrivning.

1. Se till att ange den **medlemskap** skriver till **tilldelad**.

1. Markera de användare som ska ingå i den här undantagsgrupp och klicka sedan på **skapa**.

    ![Den nya gruppen rutan](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Skapa en princip för villkorlig åtkomst som inte omfattar gruppen

Nu kan du skapa en princip för villkorlig åtkomst som använder den här undantagsgrupp.

1. I det vänstra navigeringsfönstret klickar du på **Azure Active Directory** och klicka sedan på **villkorlig åtkomst** att öppna den **principer** bladet.

1. Klicka på **ny princip** att öppna den **New** fönstret.

1. Ange ett namn.

1. Klicka på under tilldelningar **användare och grupper**.

1. På den **inkludera** fliken **alla användare**.

1. På den **undanta** fliken, lägga till en bockmarkering till **användare och grupper** och klicka sedan på **Välj exkluderade användare**.

1. Välj en undantagsgrupp som du skapade.

    > [!NOTE]
    > Som bästa praxis rekommenderar vi att minst ett administratörskonto i principen när du testar om du vill kontrollera att du inte har låsts ute från din klient.

1. Fortsätt med att konfigurera principen för villkorlig åtkomst baserat på din organisations krav.

    ![Välj exkluderade användare](./media/conditional-access-exclusion/select-excluded-users.png)

Vi beskriver två exempel där du kan använda åtkomstgranskningar för att hantera undantag i principer för villkorlig åtkomst.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countries"></a>Exempel 1: Åtkomstgranskning för användare som kommer åt från blockerade länder

Vi antar att du har en villkorlig åtkomstprincip som blockerar åtkomsten från vissa länder. Den innehåller en grupp som är undantagna från principen. Här är en rekommenderad åtkomstgranskning där medlemmar i gruppen granskas.

> [!NOTE]
> Rollen Global administratör eller Användaradministratör konto krävs för att skapa åtkomstgranskningar.

1. Granskningen ska återkomma varje vecka.

2. Upphör aldrig för att se till att du håller den här undantagsgrupp den senaste.

3. Alla medlemmar i den här gruppen kommer att inom omfånget för granskningen.

4. Varje användare har själva intyga att de fortfarande behöver åtkomst från dessa blockerade länder, därför de fortfarande behöver vara medlem i gruppen.

5. Om användaren inte svarar på begäran för granskning kan de tas automatiskt bort från gruppen och därför kan inte längre komma åt klienten resande till dessa länder.

6. Aktivera e-postaviseringar så att användarna meddelas om start och slutförande av åtkomstgranskningen.

    ![Skapa en åtkomstgranskning](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exempel 2: Åtkomstgranskning för användare som kommer åt med äldre autentisering

Vi antar att du har en villkorlig åtkomstprincip som blockerar åtkomsten för användare som använder äldre autentisering och äldre klientversioner. Den innehåller en grupp som är undantagna från principen. Här är en rekommenderad åtkomstgranskning där medlemmar i gruppen granskas.

1. Den här granskningen måste vara en återkommande granskning.

2. Alla i gruppen måste granskas.

3. Det gick att konfigurera om du vill visa företagsägare för enhet som de valda granskarna.

4. Tillämpa automatiskt resultaten och ta bort användare som inte har godkänts för att fortsätta att använda äldre autentiseringsmetoder.

5. Det kan vara bra att aktivera rekommendationer så att granskare av stora grupper kan enkelt göra sina beslut.

6. Aktivera e-postaviseringar så att användarna meddelas om start och slutförande av åtkomstgranskningen.

    ![Skapa en åtkomstgranskning](./media/conditional-access-exclusion/create-access-review-2.png)

**Pro-tipset**: Om du har många undantagsgrupper och därför måste du skapa flera åtkomstgranskningar, nu har vi ett API i Microsoft Graph beta-slutpunkt som gör det möjligt att skapa och hantera dem via programmering. Kom igång genom att se den [API-referens för Azure AD-åtkomstgranskningar](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) och [exempel för att hämta Azure AD-åtkomst går igenom via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Åtkomstgranskningsresultaten och granskningsloggar

Nu när du har allt på plats, grupp, princip för villkorlig åtkomst och åtkomstgranskningar, är det dags att övervaka och spåra resultatet av dessa granskningar.

1. I Azure-portalen öppnar du den **Åtkomstgranskningar** bladet.

1. Öppna kontroll och program som du har skapat för att hantera en undantagsgrupp.

1. Klicka på **resultat** att se vem som har godkänts kvar på listan och som har tagits bort.

    ![Åtkomstgranskningar resultat](./media/conditional-access-exclusion/access-reviews-results.png)

1. Klicka sedan på **granskningsloggar** att se de åtgärder som vidtagits under den här granskningen.

    ![Åtkomstgranskningar granskningsloggar](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Som IT-administratör kan vet du att hantera undantagsgrupper i dina principer ibland är ofrånkomligt. Dock granskar underhålla dessa grupper, granska dem regelbundet av företagsägaren eller användarna själva och granskning dessa ändringar kan enklare med Azure AD åtkomst.

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomstgranskning för medlemmar i gruppen eller programmet åtkomst med Azure AD](create-access-review.md)
- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](../conditional-access/overview.md)
