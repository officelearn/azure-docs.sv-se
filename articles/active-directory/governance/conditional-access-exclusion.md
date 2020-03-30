---
title: Hantera användare som inte omfattas av principer för villkorlig åtkomst – Azure AD
description: Lär dig hur du använder Azure Active Directory (Azure AD) åtkomstgranskningar för att hantera användare som har uteslutits från principer för villkorlig åtkomst
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d7bce940f2b614c239e8b5e5719d96da10a6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422717"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Använda Azure AD-åtkomstgranskningar för att hantera användare som är undantagna från principer för villkorlig åtkomst

I en idealisk värld följer alla användare åtkomstutpolisen för att säkra åtkomsten till organisationens resurser. Men ibland finns det affärsfall som kräver att du gör undantag. I den här artikeln beskrivs några exempel där undantag kan krävas och hur du som IT-administratör kan hantera den här uppgiften, undvika granskning av principundantag och ge revisorerna bevis på att dessa undantag granskas regelbundet med Hjälp av Azure Åtkomstgranskningar för Active Directory (Azure AD).

> [!NOTE]
> En giltig Azure AD Premium P2, Enterprise Mobility + Security E5 betald eller utvärderingslicens krävs för att använda Azure AD-åtkomstgranskningar. Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Varför utesluter du användare från principer?

Som IT-administratör kan du använda [Azure AD Conditional Access](../conditional-access/overview.md) för att kräva att användare autentiserar med multifaktorautentisering (MFA) eller loggar in från ett betrott nätverk eller en enhet. Under distributionsplaneringen får du reda på att vissa av dessa krav inte kan uppfyllas av alla användare. Det finns till exempel användare som arbetar från ett fjärrkontor som inte ingår i det interna nätverket eller så finns det en chef som använder en gammal telefon som inte stöds. Företaget kräver att dessa användare tillåts logga in och utföra sitt jobb, därför är de undantagna från principerna för villkorlig åtkomst.

Som ett annat exempel kan du använda [namngivna platser](../conditional-access/location-condition.md) i villkorlig åtkomst för att konfigurera en uppsättning län och regioner som du inte vill tillåta användare att komma åt sin klient.

![Namngivna platser i villkorlig åtkomst](./media/conditional-access-exclusion/named-locations.png)

I vissa fall kan dock användare ha en legitim anledning att logga in från dessa blockerade länder/regioner. Användare kan till exempel resa av arbetsskäl eller personliga skäl. I det här exemplet kan principen villkorlig åtkomst för att blockera dessa länder/regioner ha en särskild molnsäkerhetsgrupp för de användare som är undantagna från principen. Användare som behöver åtkomst när de reser kan lägga till sig i gruppen med hjälp av [Azure AD-självbetjäningsgrupphantering](../users-groups-roles/groups-self-service-management.md).

Ett annat exempel kan vara att du har en princip för villkorlig åtkomst som [blockerar äldre autentisering för de allra flesta användare](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Microsoft rekommenderar starkt att du blockerar användningen av äldre protokoll i din klientorganisation för att förbättra din säkerhetsposition. Men om du har några användare som absolut behöver använda äldre autentiseringsmetoder för att komma åt dina resurser via Office 2010 eller IMAP/SMTP/POP-baserade klienter, kan du utesluta dessa användare från principen som blockerar äldre autentiseringsmetoder.

## <a name="why-are-exclusions-challenging"></a>Varför är undantag utmanande?

I Azure AD kan du begränsa en princip för villkorlig åtkomst till en uppsättning användare. Du kan också utesluta vissa av dessa användare genom att välja Azure AD-roller, enskilda användare eller gäster till användare. Det är viktigt att komma ihåg att när dessa undantag har konfigurerats kan principavsikten inte tillämpas för dessa användare. Om dessa undantag har konfigurerats som antingen en lista över enskilda användare eller via en äldre lokal säkerhetsgrupp, begränsar den synligheten för den här undantagslistan (användarna kanske inte känner till dess existens) och IT-administratörens kontroll över den (användare kan ansluta sig till säkerhetsgrupp för att förbigå principen). Dessutom kan användare som är kvalificerade för uteslutningen vid ett tillfälle inte längre behöver det eller vara berättigade till det.

I början av ett undantag finns det en kort lista över användare som kringgår principen. Med tiden utesluts allt fler användare och listan växer. Vid något tillfälle finns det ett behov av att granska listan och bekräfta att var och en av dessa användare fortfarande bör uteslutas. Hantera listan ur teknisk synvinkel, kan vara relativt lätt, men vem gör affärsbeslut och hur ser du till att det är alla granskningsbara?

Men om du konfigurerar uteslutningen till principen villkorlig åtkomst med hjälp av en Azure AD-grupp kan du använda åtkomstgranskningar som en kompenserande kontroll, för att öka synligheten och minska antalet användare som har ett undantag.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Så här skapar du en undantagsgrupp i en princip för villkorlig åtkomst

Följ dessa steg för att skapa en ny Azure AD-grupp och en policy för villkorlig åtkomst som inte gäller för den gruppen.

### <a name="create-an-exclusion-group"></a>Skapa en undantagsgrupp

1. Logga in på Azure Portal.

1. I den vänstra navigeringen klickar du på **Azure Active Directory** och sedan på **Grupper**.

1. **Öppna** gruppfönstret på den övre menyn.

1. Välj **Säkerhet**i listan **Grupptyp** . Ange ett namn och en beskrivning.

1. Se till att ange **medlemstypen** till **Tilldelad**.

1. Markera de användare som ska ingå i den här undantagsgruppen och klicka sedan på **Skapa**.

    ![Nytt gruppfönster i Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Skapa en princip för villkorlig åtkomst som utesluter gruppen

Nu kan du skapa en princip för villkorlig åtkomst som använder den här undantagsgruppen.

1. I den vänstra navigeringen klickar du på **Azure Active Directory** och sedan på Villkorlig **åtkomst** för att öppna bladet **Principer.**

1. Klicka på **Ny princip** för att öppna fönstret **Nytt.**

1. Ange ett namn.

1. Klicka på **Användare och grupper**under Tilldelningar.

1. Välj **Alla användare**på fliken **Inkludera** .

1. Lägg till en bock i **Användare och grupper** på fliken **Uteslut** och klicka sedan på **Markera uteslutna användare**.

1. Välj den uteslutningsgrupp som du skapade.

    > [!NOTE]
    > Det rekommenderas att du utesluter minst ett administratörskonto från principen när du testar för att se till att du inte är utelåst från din klientorganisation.

1. Fortsätt med att ställa in principen villkorlig åtkomst baserat på dina organisationskrav.

    ![Fönstret Markera uteslutna användare i villkorlig åtkomst](./media/conditional-access-exclusion/select-excluded-users.png)

Låt oss ta upp två exempel där du kan använda åtkomstgranskningar för att hantera undantag i principer för villkorlig åtkomst.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Exempel 1: Åtkomstgranskning för användare som har åtkomst från blockerade länder/regioner

Anta att du har en princip för villkorlig åtkomst som blockerar åtkomst från vissa länder/regioner. Den innehåller en grupp som är utesluten från principen. Här är en rekommenderad åtkomstgranskning där medlemmar i gruppen granskas.

> [!NOTE]
> En global administratörs- eller användaradministratörsroll krävs för att skapa åtkomstgranskningar.

1. Översynen kommer att återkomma varje vecka.

2. Kommer aldrig att sluta för att se till att du håller denna uteslutningsgrupp den mest uppdaterade.

3. Alla medlemmar i den här gruppen kommer att vara i utrymme för granskningen.

4. Varje användare måste själv intyga att de fortfarande måste ha tillgång från dessa blockerade länder /regioner, därför måste de fortfarande vara medlem i gruppen.

5. Om användaren inte svarar på granskningsbegäran tas de automatiskt bort från gruppen och kan därför inte längre komma åt klienten när han reser till dessa länder/regioner.

6. Aktivera e-postmeddelanden så att användarna får ett meddelande om att åtkomstgranskningen har påbörjats och slutförts.

    ![Skapa ett åtkomstgranskningsfönster till exempel 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exempel 2: Åtkomstgranskning för användare som har åtkomst med äldre autentisering

Anta att du har en princip för villkorlig åtkomst som blockerar åtkomst för användare som använder äldre autentisering och äldre klientversioner. Den innehåller en grupp som är utesluten från principen. Här är en rekommenderad åtkomstgranskning där medlemmar i gruppen granskas.

1. Denna översyn skulle behöva vara en återkommande översyn.

2. Alla i gruppen skulle behöva ses över.

3. Den kan konfigureras för att lista affärsenhetsägarna som de valda granskarna.

4. Tillämpa resultaten automatiskt och ta bort användare som inte har godkänts för att fortsätta använda äldre autentiseringsmetoder.

5. Det kan vara fördelaktigt att möjliggöra rekommendationer så att granskare av stora grupper lätt kan fatta sina beslut.

6. Aktivera e-postmeddelanden så att användarna får ett meddelande om att åtkomstgranskningen har påbörjats och slutförts.

    ![Skapa ett åtkomstgranskningsfönster till exempel 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Pro Tips:** Om du har många undantagsgrupper och därför behöver skapa flera åtkomst recensioner, har vi nu ett API i Microsoft Graph beta slutpunkt som låter dig skapa och hantera dem programmässigt. För att komma igång, se [Azure AD-åtkomstrecensioner API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) och [Exempel på hämtning av Azure AD-åtkomstgranskningar via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Resultat och granskningsloggar för åtkomst

Nu när du har allt på plats, grupp, villkorlig åtkomstprincip och åtkomstgranskningar är det dags att övervaka och spåra resultaten av dessa granskningar.

1. Öppna bladet Access **Reviews** i Azure-portalen.

1. Öppna kontrollen och programmet som du har skapat för att hantera uteslutningsgruppen.

1. Klicka på **Resultat** för att se vem som godkändes för att stanna kvar i listan och vem som togs bort.

    ![Resultaten för accessrecensioner visar vem som godkändes](./media/conditional-access-exclusion/access-reviews-results.png)

1. Klicka sedan på **Granskningsloggar** för att se vilka åtgärder som vidtogs under den här granskningen.

    ![Granskningsloggar för åtkomstgranskningar](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Som IT-administratör vet du att det ibland är oundvikligt att hantera uteslutningsgrupper till dina principer. Men att underhålla dessa grupper, granska dem regelbundet av företagets ägare eller användarna själva, och granska dessa ändringar kan göra det enklare med Azure AD-åtkomstgranskningar.

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md)
- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)
