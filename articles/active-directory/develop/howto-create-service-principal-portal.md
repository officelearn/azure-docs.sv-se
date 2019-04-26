---
title: Skapa för Azure-app i portalen | Microsoft Docs
description: Beskriver hur du skapar en ny Azure Active Directory-program och tjänstens huvudnamn som kan användas med rollbaserad åtkomstkontroll i Azure Resource Manager för att hantera åtkomst till resurser.
services: active-directory
documentationcenter: na
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: tomfitz
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9affec9ccc1b87f36d6f30aff4795d85532be8c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300996"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Anvisningar: Använda portalen för att skapa en Azure AD-program och tjänstens huvudnamn som kan komma åt resurser

Den här artikeln visar hur du skapar en ny Azure Active Directory (Azure AD)-program och tjänstens huvudnamn som kan användas med rollbaserad åtkomstkontroll. När du har kod som behöver åtkomst till eller ändra resurser kan skapa du en identitet för appen. Den här identiteten kallas tjänstens huvudnamn. Du kan sedan tilldela behörighet till tjänstens huvudnamn. Den här artikeln visar hur du använder portalen för att skapa tjänstens huvudnamn. Den fokuserar på en enda klient program där programmet är avsett att köras i endast en organisation. Du använder vanligtvis enda klient program för line-of-business-program som körs i din organisation.

> [!IMPORTANT]
> Överväg att använda hanterade identiteter för Azure-resurser för din Programidentitet istället för att skapa ett huvudnamn för tjänsten. Om din kod körs på en tjänst som stöder hanterade identiteter och åtkomst till resurser som har stöd för Azure AD-autentisering, är hanterade identiteter ett bättre alternativ för dig. Läs mer om hanterade identiteter för Azure-resurser, inklusive vilka tjänster för närvarande stöd för den i [vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

Låt oss sätta igång direkt i att skapa identiteten. Om du stöter på problem, kontrollerar du den [nödvändiga behörigheter](#required-permissions) att kontrollera att ditt konto kan skapa identiteten.

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com).
1. Välj **Azure Active Directory**.
1. Välj **Appregistreringar**.

   ![Välj appregistreringar](./media/howto-create-service-principal-portal/select-app-registrations.png)

1. Välj **Ny programregistrering**.

   ![Lägg till app](./media/howto-create-service-principal-portal/select-add-app.png)

1. Ange ett namn och en URL för programmet. Välj **Webbapp/API** för den programtyp som du vill skapa. Du kan inte skapa autentiseringsuppgifter för en [programspecifik](../manage-apps/application-proxy-configure-native-client-application.md). Du kan inte använda den typen för ett automatiserat program. När du har angett värdena, Välj **skapa**.

   ![namnprogram](./media/howto-create-service-principal-portal/create-app.png)

Du har skapat din Azure AD-program och tjänstens huvudnamn.

## <a name="assign-the-application-to-a-role"></a>Tilldela programmet till en roll

För att komma åt resurser i din prenumeration, måste du tilldela programmet till en roll. Bestäm vilken roll har rätt behörigheter för programmet. Läs om tillgängliga roller i [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md).

Du kan ange omfånget för den prenumerationen, resursgruppen eller resursen. Behörigheter ärvs till lägre nivåer av omfång. Till exempel lägga till ett program till rollen Läsare för en resursgrupp innebär att den kan läsa resursgruppen och alla resurser som den innehåller.

1. Navigera till den nivå av omfång som du vill tilldela programmet till. Om du vill tilldela en roll prenumerationsområde, väljer du exempelvis **alla tjänster** och **prenumerationer**.

   ![Välj en prenumeration](./media/howto-create-service-principal-portal/select-subscription.png)

1. Välj den specifika prenumerationen att tilldela programmet till.

   ![Välj prenumeration för tilldelning](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Om du inte ser den prenumeration som du letar efter, väljer **globala prenumerationer filter**. Kontrollera att den prenumeration du vill har valts för portalen. 

1. Välj **Åtkomstkontroll (IAM)**.
1. Välj **Lägg till rolltilldelning**.

   ![Välj Lägg till rolltilldelning](./media/howto-create-service-principal-portal/select-add.png)

1. Välj den roll som du vill tilldela till programmet. Att tillåta program att köra åtgärder som att **omstart**, **starta** och **stoppa** instanser, väljer den **deltagare** roll. Som standard visas inte Azure AD-program i de tillgängliga alternativen. Sök efter namnet för att hitta ditt program, och markera den.

   ![Välja en roll](./media/howto-create-service-principal-portal/select-role.png)

1. Välj **spara** Slutför tilldela rollen. Du ser ditt program i listan över användare som har tilldelats en roll för detta omfång.

Tjänstens huvudnamn har ställts in. Du kan börja använda det för att köra dina skript eller appar. Nästa avsnitt visar hur du hämtar värden som behövs när du loggar in via programmering.

## <a name="get-values-for-signing-in"></a>Hämta värden för att logga in

### <a name="get-tenant-id"></a>Hämta klientorganisations-ID

När du programmässigt loggar in, måste du skicka klientorganisations-ID med din autentiseringsbegäran.

1. Välj **Azure Active Directory**.
1. Välj **egenskaper**.

   ![Välj Azure AD-egenskaper](./media/howto-create-service-principal-portal/select-ad-properties.png)

1. Kopiera den **katalog-ID** att få ditt klient-ID.

   ![Klient-ID:t](./media/howto-create-service-principal-portal/copy-directory-id.png)

### <a name="get-application-id-and-authentication-key"></a>Hämta nyckel för program-ID och autentiseringsnyckel

Du måste också ID: T för ditt program och en autentiseringsnyckel. Hämta dessa värden med följande steg:

1. Från **appregistreringar** i Azure AD, Välj ditt program.

   ![Välj program](./media/howto-create-service-principal-portal/select-app.png)

1. Kopiera **Program-ID:t** och lagra det i din programkod.

   ![Klientorganisations-ID](./media/howto-create-service-principal-portal/copy-app-id.png)

1. Välj **inställningar**.

   ![Välj inställningar](./media/howto-create-service-principal-portal/select-settings.png)

1. Välj **Nycklar**.
1. Tillhandahåll beskrivning av och varaktighet för nyckeln. Välj **Spara** när du är klar.

   ![Spara nyckel](./media/howto-create-service-principal-portal/save-key.png)

   När du har sparat nyckeln visas nyckelns värde. Kopiera det här värdet eftersom du inte kan komma att hämta nyckeln senare. Du kan ange nyckelvärdet med program-ID för inloggning som programmet. Lagra nyckelvärdet där programmet kan hämta det.

   ![sparad nyckel](./media/howto-create-service-principal-portal/copy-key.png)

## <a name="required-permissions"></a>Nödvändiga behörigheter

Du måste ha tillräcklig behörighet för att registrera ett program med Azure AD-klienten och tilldela programmet till en roll i Azure-prenumerationen.

### <a name="check-azure-ad-permissions"></a>Kontrollera Azure AD-behörigheter

1. Välj **Azure Active Directory**.
1. Observera din roll. Om du har den **användaren** roll, måste du se till att icke-administratörer kan registrera program.

   ![Sök efter användare](./media/howto-create-service-principal-portal/view-user-info.png)

1. Välj **användarinställningar**.

   ![Välj användarinställningar](./media/howto-create-service-principal-portal/select-user-settings.png)

1. Kontrollera den **appregistreringar** inställningen. Det här värdet kan bara anges av en administratör. Om inställd **Ja**, någon användare i Azure AD-klient kan registrera en app.

   ![Visa app-registreringar](./media/howto-create-service-principal-portal/view-app-registrations.png)

Om inställningen appregistreringar anges till **nr**, endast användare med administratörsroller registrera dessa typer av program. Se [tillgängliga roller](../users-groups-roles/directory-assign-admin-roles.md#available-roles) och [rollbehörighet](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) vill veta mer om tillgängliga administratörsroller och särskilda behörigheter i Azure AD som har angetts för varje roll. Om ditt konto har tilldelats till rollen, men appinställningen för registreringen är begränsat till administratörer, be din administratör att antingen tilldela dig till någon av rollerna Administratör, som kan skapa och hantera alla aspekter av appregistreringar eller för att ge användare registrera appar.

### <a name="check-azure-subscription-permissions"></a>Kontrollera behörigheter för Azure-prenumeration

I Azure-prenumerationen, måste kontot ha `Microsoft.Authorization/*/Write` åtkomst till tilldelar en AD-app till en roll. Den här åtgärden beviljas genom rollen [Ägare](../../role-based-access-control/built-in-roles.md#owner) eller [Administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator). Om ditt konto tilldelas den **deltagare** roll, du har inte tillräcklig behörighet. Du får ett fel vid försök att tilldela en roll med tjänstens huvudnamn.

Att kontrollera dina Prenumerationsbehörigheter:

1. Välj ditt konto i det övre högra hörnet och välj **Mina behörigheter**.

   ![Välj användarbehörigheter](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Välj den prenumeration som du vill skapa tjänsten huvudnamn i från den nedrullningsbara listan. Välj **Klicka här om du vill visa fullständig åtkomst information för den här prenumerationen**.

   ![Sök efter användare](./media/howto-create-service-principal-portal/view-details.png)

1. Visa dina tilldelade roller och avgör om du har tillräcklig behörighet för att tilldela en AD-app till en roll. Annars kan du be systemadministratören att lägga till dig till rollen Administratör för användaråtkomst. I följande bild tilldelas användaren rollen ägare, vilket innebär att användaren har tillräcklig behörighet.

   ![Visa behörigheter](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill konfigurera ett program med flera innehavare, se [utvecklarguiden för auktorisering med Azure Resource Manager API](../../azure-resource-manager/resource-manager-api-authentication.md).
* Läs om hur du anger säkerhetsprinciper i [Azure rollbaserad åtkomstkontroll](../../role-based-access-control/role-assignments-portal.md).  
* En lista över tillgängliga åtgärder som kan beviljas eller nekas till användare finns i [Azure Resource Manager Resource Provider operations](../../role-based-access-control/resource-provider-operations.md).
