---
title: Säkerhet för Azure Digital Twins-lösningar
titleSuffix: Azure Digital Twins
description: Lär dig mer om rekommenderade säkerhets metoder med Azure Digitals.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 973eeebfdf9164cb50cf98ae8edc845a80a7e080
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794507"
---
# <a name="secure-azure-digital-twins"></a>Skydda digitala Azure-dubbla

För säkerhet ger Azure Digitals dubbla, exakt åtkomst kontroll över specifika data, resurser och åtgärder i distributionen. Detta sker via en detaljerad roll och behörighets hanterings strategi som kallas **rollbaserad åtkomst kontroll (RBAC)**. Du kan läsa om de allmänna principerna för RBAC för Azure [här](../role-based-access-control/overview.md).

Azure Digitals dubbla är också stöd för kryptering av data i vila.

## <a name="granting-permissions-with-rbac"></a>Bevilja behörigheter med RBAC

RBAC tillhandahålls till Azure Digitals dubblare via integration med [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Du kan använda RBAC för att bevilja behörighet till ett *säkerhets objekt*, som kan vara en användare, en grupp eller ett huvud namn för program tjänsten. Säkerhets objekt autentiseras av Azure AD och tar emot en OAuth 2,0-token i retur. Denna token kan användas för att auktorisera en åtkomstbegäran till en Azure Digitals-instans.

### <a name="authentication-and-authorization"></a>Autentisering och auktorisering

Med Azure AD är Access en två stegs process. När ett säkerhets objekt (en användare, grupp eller ett program) försöker få åtkomst till Azures digitala dubbla, måste begäran *autentiseras* och *auktoriseras*. 

1. Först *autentiseras*säkerhets objektets identitet och en OAuth 2,0-token returneras.
2. Därefter skickas token som en del av en begäran till tjänsten Azure Digitals dubblare för att *ge* åtkomst till den angivna resursen.

Steget autentisering kräver att en program förfrågan innehåller en OAuth 2,0-åtkomsttoken vid körning. Om ett program körs i en Azure-entitet, till exempel en [Azure Functions](../azure-functions/functions-overview.md) app, kan den använda en **hanterad identitet** för att få åtkomst till resurserna. Läs mer om hanterade identiteter i nästa avsnitt.

Auktoriserings steget kräver att en Azure-roll tilldelas till säkerhets objekt. Rollerna som tilldelas ett säkerhets objekt bestämmer vilka behörigheter som huvud kontot ska ha. Azure Digitals dubbla ger Azure-roller som omfattar uppsättningar med behörigheter för Azure Digitals dubbla resurser. Dessa roller beskrivs längre fram i den här artikeln.

Mer information om roller och roll tilldelningar som stöds i Azure finns i [*förstå de olika rollerna*](../role-based-access-control/rbac-and-directory-admin-roles.md) i Azure RBAC-dokumentationen.

#### <a name="authentication-with-managed-identities"></a>Autentisering med hanterade identiteter

[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) är en funktion i Azure som gör att du kan skapa en säker identitet som är kopplad till distributionen där program koden körs. Du kan sedan associera identiteten med åtkomst kontroll roller för att bevilja anpassade behörigheter för åtkomst till specifika Azure-resurser som ditt program behöver.

Med hanterade identiteter hanterar Azure-plattformen den här körnings identiteten. Du behöver inte lagra och skydda åtkomst nycklar i din program kod eller konfiguration, antingen för själva identiteten eller för de resurser som du behöver komma åt. Ett digitalt Azure-klientprogram som körs inuti ett Azure App Service-program behöver inte hantera SAS-regler och nycklar eller andra åtkomsttoken. Klient programmet behöver bara slut punkts adressen för Azure Digitals-namnområdet. När appen ansluter binder Azure Digitals en hanterad entitets kontext till klienten. När den är kopplad till en hanterad identitet kan din Azure Digital-klient med dubbla Azure-klienter utföra alla behöriga åtgärder. Auktorisering beviljas sedan genom att associera en hanterad entitet med en Azure Digitals Azure-roll (beskrivs nedan).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Auktorisering: Azure-roller för Azure Digitals dubbla

Azure tillhandahåller de här inbyggda Azure-rollerna för att auktorisera åtkomst till en Azure Digital-resurs med dubbla resurser:
* *Azure Digitals flätat-ägare (för hands version)* – Använd den här rollen för att ge fullständig åtkomst till resurser med Azure Digitals.
* *Azure Digitals flätad läsare (för hands version)* – Använd den här rollen för att ge skrivskyddad åtkomst till Azure Digitals-resurser.

> [!TIP]
> *Azure Digitals (för hands version)* -rollen stöder nu även webb relationer.

Mer information om hur inbyggda roller definieras finns i [*förstå roll definitioner*](../role-based-access-control/role-definitions.md) i Azure RBAC-dokumentationen. Information om hur du skapar anpassade Azure-roller finns i [*Azure-anpassade roller*](../role-based-access-control/custom-roles.md).

Du kan tilldela roller på två sätt:
* via fönstret åtkomst kontroll (IAM) för Azure Digitals dubbla i Azure Portal (se [*lägga till eller ta bort roll tilldelningar med hjälp av Azure RBAC och Azure Portal*](../role-based-access-control/role-assignments-portal.md))
* via CLI-kommandon för att lägga till eller ta bort en roll

Mer detaljerad information om hur du gör detta finns i självstudien om Azure Digitals dubbla steg [*: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md).

### <a name="permission-scopes"></a>Behörighetsomfattning

Innan du tilldelar en Azure-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det är bäst att endast bevilja det snävaste möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Azure Digitals resurser.
* Modeller: åtgärderna för den här resursen dikterar kontroll över [modeller](concepts-models.md) som laddats upp i Azure Digitals.
* Fråga Digitals grafer: åtgärderna för den här resursen bestämmer möjlighet att köra [frågor](concepts-query-language.md) på digitala inne i Azure Digitals grafer.
* Digital, dubbel: åtgärder för den här resursen ger kontroll över CRUD-åtgärder på [digitala](concepts-twins-graph.md) byggen i det dubbla diagrammet.
* Digital, dubbel relation: åtgärderna för den här resursen definierar kontroll över CRUD-åtgärder på relationer mellan digitala [band](concepts-twins-graph.md) i den dubbla grafen.
* Händelse väg: åtgärder för den här resursen bestämmer behörigheter för att [dirigera händelser](concepts-route-events.md) från Azure Digitals till en slut punkts tjänst som [händelsehubben](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)eller [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Fel söknings behörigheter

Om en användare försöker utföra en åtgärd som inte tillåts av deras roll, kan de få ett fel meddelande från begäran om läsning av tjänst `403 (Forbidden)` . Mer information och fel söknings steg finns i [*fel sökning: Azure Digitals-begäran misslyckades med status: 403 (förbjuden)*](troubleshoot-error-403.md).

## <a name="encryption-of-data-at-rest"></a>Kryptering av data i vila

Azure Digitals-enheter tillhandahåller kryptering av data i vila och under överföring som de skrivs i våra data Center och dekrypterar dem åt dig när du har åtkomst till den.

## <a name="next-steps"></a>Nästa steg

* Se de här koncepten i instruktionen [*så här: Konfigurera en instans och autentisering*](how-to-set-up-instance-scripted.md).

* Se hur du interagerar med dessa begrepp från klient program kod i [*How-to: Write app authentication code*](how-to-authenticate-client.md).

* Läs mer om [RBAC för Azure](../role-based-access-control/overview.md).
