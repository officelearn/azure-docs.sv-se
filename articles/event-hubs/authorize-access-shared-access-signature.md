---
title: Auktorisera åtkomst med en signatur för delad åtkomst i Azure Event Hubs
description: Den här artikeln innehåller information om hur du godkänner åtkomst till Azure Event Hubs-resurser med hjälp av SAS (Shared Access Signatures).
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: bdb1896f8a40c6de21ae76b536bfccec316341cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992801"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Auktorisera åtkomst till eventhubbar-resurser med signaturer för delad åtkomst
En SAS -signatur (Shared Access Signature) ger dig ett sätt att bevilja begränsad åtkomst till resurser i namnområdet Event Hubs. SAS skyddar åtkomsten till Event Hubs-resurser baserat på auktoriseringsregler. Dessa regler konfigureras antingen på ett namnområde eller en entitet (händelsehubb eller ämne). Den här artikeln innehåller en översikt över SAS-modellen och granskar SAS bästa praxis.

## <a name="what-are-shared-access-signatures"></a>Vad är signaturer för delad åtkomst?
En SAS -signatur (Shared Access Signature) ger delegerad åtkomst till Event Hubs-resurser baserat på auktoriseringsregler. En auktoriseringsregel har ett namn, är associerad med specifika rättigheter och har ett par kryptografiska nycklar. Du använder regelns namn och nyckel via Event Hubs-klienterna eller i din egen kod för att generera SAS-token. En klient kan sedan skicka token till Event Hubs för att bevisa auktorisering för den begärda åtgärden.

SAS är en anspråksbaserad auktoriseringsmekanism med enkla token. Med HJÄLP AV SAS skickas aldrig nycklar på tråden. Nycklar används för att kryptografiskt signera information som senare kan verifieras av tjänsten. SAS kan användas på samma sätt som ett användarnamns- och lösenordsschema där klienten omedelbart har ett auktoriseringsregelnamn och en matchande nyckel. SAS kan användas på samma sätt som en federerad säkerhetsmodell, där klienten tar emot en tidsbegränsad och signerad åtkomsttoken från en säkerhetstokentjänst utan att någonsin komma i besittning av signeringsnyckeln.

> [!NOTE]
> Azure Event Hubs stöder auktorisera till eventhubbar resurser med Azure Active Directory (Azure AD). Att auktorisera användare eller program med OAuth 2.0-token som returneras av Azure AD ger överlägsen säkerhet och användarvänlighet över SIGNATURER för delad åtkomst (SAS). Med Azure AD behöver du inte lagra token i koden och riskera potentiella säkerhetsproblem.
>
> Microsoft rekommenderar att du använder Azure AD med dina Azure Event Hubs-program när det är möjligt. Mer information finns i [Auktorisera åtkomst till Azure Event Hubs-resurs med Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> SAS-token (Shared Access Signatures) är avgörande för att skydda dina resurser. Samtidigt som SAS tillhandahåller granularitet ger sas klienter åtkomst till dina eventhubbar-resurser. De bör inte delas offentligt. När du delar, om det behövs av felsökningsskäl, bör du överväga att använda en reducerad version av loggfiler eller ta bort SAS-token (om sådana finns) från loggfilerna och se till att skärmbilderna inte heller innehåller SAS-informationen.

## <a name="shared-access-authorization-policies"></a>Behörighetsprinciper för delad åtkomst
Varje händelsehubbarnamnområde och varje eventhubbar-entitet (en händelsenavinstans eller ett Kafka-ämne) har en princip för auktorisering för delad åtkomst som består av regler. Principen på namnområdesnivå gäller för alla entiteter i namnområdet, oavsett deras individuella principkonfiguration.
För varje behörighetsprincipregel bestämmer du dig för tre informationsdelar: namn, omfattning och rättigheter. Namnet är ett unikt namn i det omfånget. Omfattningen är URI för resursen i fråga. För ett namnområde för händelsehubbar är scopet det fullständigt kvalificerade `https://<yournamespace>.servicebus.windows.net/`domännamnet (FQDN), till exempel .

De rättigheter som föreskrivs i policyregeln kan vara en kombination av:
- **Skicka** – Ger rätt att skicka meddelanden till entiteten
- **Lyssna** – Ger rätt att lyssna eller ta emot till entiteten
- **Hantera** – Ger rätt att hantera topologin för namnområdet, inklusive skapande och radering av entiteter

> [!NOTE]
> **Rättigheten Hantera** innehåller rättigheterna **Skicka** och **lyssna.**

En namnområdes- eller entitetsprincip kan innehålla upp till 12 behörighetsregler för delad åtkomst, vilket ger utrymme för de tre uppsättningarna regler, som var och en täcker de grundläggande rättigheterna och kombinationen av Skicka och lyssna. Den här gränsen understryker att SAS-principarkivet inte är avsett att vara ett användarkonto eller en tjänstkontobutik. Om ditt program behöver bevilja åtkomst till Event Hubs-resurser baserat på användar- eller tjänstidentiteter bör det implementera en säkerhetstokentjänst som utfärdar SAS-token efter en autentiserings- och åtkomstkontroll.

En auktoriseringsregel tilldelas en **primärnyckel** och en **sekundär nyckel**. Dessa nycklar är kryptografiskt starka nycklar. Tappa inte bort dem eller läcka dem. De är alltid tillgängliga i Azure-portalen. Du kan använda någon av de genererade nycklarna och du kan återskapa dem när som helst. Om du återskapar eller ändrar en nyckel i principen blir alla tidigare utfärdade token baserat på nyckeln omedelbart ogiltiga. Pågående anslutningar som skapas baserat på sådana token kommer dock att fortsätta att fungera tills token upphör att gälla.

När du skapar ett namnområde för händelsehubbar skapas automatiskt en principregel med namnet **RootManageSharedAccessKey** för namnområdet. Den här principen har **hantera** behörigheter för hela namnområdet. Vi rekommenderar att du behandlar den här regeln som ett administrativt rotkonto och inte använder den i ditt program. Du kan skapa ytterligare principregler på fliken **Konfigurera** för namnområdet i portalen, via PowerShell eller Azure CLI.

## <a name="best-practices-when-using-sas"></a>Metodtips när du använder SAS
När du använder signaturer med delad åtkomst i dina program måste du vara medveten om två potentiella risker:

- Om en SAS läcker kan den användas av alla som skaffar den, vilket kan äventyra dina Event Hubs-resurser.
- Om en SAS som tillhandahålls ett klientprogram upphör att gälla och programmet inte kan hämta en ny SAS från din tjänst, kan programmets funktioner hindras.

Följande rekommendationer för användning av signaturer för delad åtkomst kan bidra till att minska dessa risker:

- **Låt klienter automatiskt förnya SAS om det behövs:** Klienter bör förnya SAS i god tid före förfallodatum, för att ge tid för återförsök om tjänsten som tillhandahåller SAS inte är tillgänglig. Om din SAS är avsedd att användas för ett litet antal omedelbara, kortlivade åtgärder som förväntas slutföras inom utgångsperioden, kan det vara onödigt eftersom SAS inte förväntas förnyas. Men om du har klient som rutinmässigt gör förfrågningar via SAS, då möjligheten att upphöra kommer in i bilden. Det viktigaste är att balansera behovet av att SAS blir kortlivade (som tidigare nämnts) med behovet av att se till att kunden begär förnyelse tillräckligt tidigt (för att undvika störningar på grund av att SAS löper ut innan en lyckad förnyelse).
- **Var försiktig med SAS starttid:** Om du ställer in starttiden för SAS till **nu**, sedan på grund av klocksnedställning (skillnader i aktuell tid enligt olika maskiner), fel kan observeras periodvis för de första minuterna. I allmänhet ställer du in starttiden till minst 15 minuter tidigare. Eller, inte ställa in det alls, vilket kommer att göra det giltigt omedelbart i alla fall. Detsamma gäller i allmänhet för utgångstiden också. Kom ihåg att du kan bedöma upp till 15 minuters klocka skeva i båda riktningar på någon begäran. 
- **Var specifik med den resurs som ska nås:** En säkerhetspraxis är att ge användaren de lägsta behörigheterna som krävs. Om en användare bara behöver läsbehörighet till en enda entitet, ge dem sedan läsbehörighet till den enda entiteten och inte läs/skriv/ta bort åtkomst till alla entiteter. Det bidrar också till att minska skadan om en SAS äventyras eftersom SAS har mindre ström i händerna på en angripare.
- **Använd inte alltid SAS:** Ibland överväger riskerna med en viss åtgärd mot dina eventhubbar fördelarna med SAS. För sådana åtgärder skapar du en tjänst på mellannivå som skriver till dina eventhubbar efter validering, autentisering och granskning av affärsregeln.
- **Använd alltid HTTPs:** Använd alltid Https för att skapa eller distribuera en SAS. Om en SAS skickas över HTTP och fångas upp, en angripare som utför en man-in-the-middle bifoga kan läsa SAS och sedan använda den precis som den avsedda användaren kan ha, potentiellt äventyra känsliga data eller möjliggör data korruption av den illvilliga användaren.

## <a name="conclusion"></a>Slutsats
Signaturer för delningsåtkomst är användbara för att ge begränsade behörigheter till Event Hubs-resurser till dina klienter. De är en viktig del av säkerhetsmodellen för alla program som använder Azure Event Hubs. Om du följer de bästa metoderna i den här artikeln kan du använda SAS för att ge större flexibilitet i åtkomsten till dina resurser, utan att äventyra säkerheten för ditt program.

## <a name="next-steps"></a>Nästa steg
Se följande relaterade artiklar: 

- [Autentisera begäranden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
- [Autentisera en hanterad identitet med Azure Active Directory för att komma åt eventhubbarresurser](authenticate-managed-identity.md)
- [Autentisera begäranden till Azure Event Hubs med signaturer för delad åtkomst](authenticate-shared-access-signature.md)
- [Auktorisera åtkomst till eventhubbar-resurser med Azure Active Directory](authorize-access-azure-active-directory.md)


