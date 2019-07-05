---
title: Skapa för Azure-app i portalen | Microsoft Docs
description: Beskriver hur du skapar en ny Azure Active Directory-program och tjänstens huvudnamn som kan användas med rollbaserad åtkomstkontroll i Azure Resource Manager för att hantera åtkomst till resurser.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd1534b3f966051104a3f3ee389fb047ab258fc
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482809"
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
1. Välj **ny registrering**.
1. Ge programmet namnet. Välj ett stöds konto skriver, som bestämmer vem som kan använda programmet. Under **omdirigerings-URI**väljer **Web** för typ av program som du vill skapa. Ange URI: N där den åtkomst-token som skickas till. Du kan inte skapa autentiseringsuppgifter för en [programspecifik](../manage-apps/application-proxy-configure-native-client-application.md). Du kan inte använda den typen för ett automatiserat program. När du har angett värdena, Välj **registrera**.

   ![Skriv ett namn för ditt program](./media/howto-create-service-principal-portal/create-app.png)

Du har skapat din Azure AD-program och tjänstens huvudnamn.

## <a name="assign-the-application-to-a-role"></a>Tilldela programmet till en roll

För att komma åt resurser i din prenumeration, måste du tilldela programmet till en roll. Bestäm vilken roll har rätt behörigheter för programmet. Läs om tillgängliga roller i [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md).

Du kan ange omfånget för den prenumerationen, resursgruppen eller resursen. Behörigheter ärvs till lägre nivåer av omfång. Till exempel lägga till ett program till rollen Läsare för en resursgrupp innebär att den kan läsa resursgruppen och alla resurser som den innehåller.

1. Navigera till den nivå av omfång som du vill tilldela programmet till. Om du vill tilldela en roll prenumerationsområde, väljer du exempelvis **alla tjänster** och **prenumerationer**.

   ![Till exempel tilldela en roll prenumerationsområde](./media/howto-create-service-principal-portal/select-subscription.png)

1. Välj den specifika prenumerationen att tilldela programmet till.

   ![Välj prenumeration för tilldelning](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Om du inte ser den prenumeration som du letar efter, väljer **globala prenumerationer filter**. Kontrollera att den prenumeration du vill har valts för portalen.

1. Välj **åtkomstkontroll (IAM)** .
1. Välj **Lägg till rolltilldelning**.
1. Välj den roll som du vill tilldela till programmet. Att tillåta program att köra åtgärder som att **omstart**, **starta** och **stoppa** instanser, väljer den **deltagare** roll. Som standard visas inte Azure AD-program i de tillgängliga alternativen. Sök efter namnet för att hitta ditt program, och markera den.

   ![Välj rollen för att tilldela till programmet](./media/howto-create-service-principal-portal/select-role.png)

1. Välj **spara** Slutför tilldela rollen. Du ser ditt program i listan över användare som har tilldelats en roll för detta omfång.

Tjänstens huvudnamn har ställts in. Du kan börja använda det för att köra dina skript eller appar. Nästa avsnitt visar hur du hämtar värden som behövs när du loggar in via programmering.

## <a name="get-values-for-signing-in"></a>Hämta värden för att logga in

När du programmässigt loggar in, måste du skicka klientorganisations-ID med din autentiseringsbegäran. Du måste också ID: T för ditt program och en autentiseringsnyckel. Hämta dessa värden med följande steg:

1. Välj **Azure Active Directory**.
1. Från **appregistreringar** i Azure AD, Välj ditt program.
1. Kopiera katalog (klient)-ID och lagra den i din programkod.

    ![Kopiera katalogen (klient-ID) och lagra den i din kod](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Kopiera **Program-ID:t** och lagra det i din programkod.

   ![Kopiera program (klient)-ID](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certifikat och hemligheter
Daemon för program kan använda två typer av autentiseringsuppgifter för att autentisera med Azure AD: certifikat och hemligheter.  Vi rekommenderar att du använder ett certifikat, men du kan också skapa en ny hemlighet för programmet.

### <a name="upload-a-certificate"></a>Ladda upp ett certifikat

Du kan använda ett befintligt certifikat om du har en.  Du kan också skapa ett självsignerat certifikat för testning. Öppna PowerShell och kör [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) med följande parametrar för att skapa ett självsignerat certifikat i användarens certifikatarkiv på datorn: `$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature`.  Exportera det här certifikatet med hjälp av den [Hantera användarcertifikat](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) MMC-snapin-modulen kan nås från Kontrollpanelen i Windows.

Ladda upp certifikatet:

1. Välj **certifikat och hemligheter**.
1. Välj **överför certifikat** och välj certifikatet (ett befintligt certifikat eller det självsignerade certifikatet du exporterade).

    ![Välj överför certifikat och välj den som du vill lägga till](./media/howto-create-service-principal-portal/upload-cert.png)

1. Välj **Lägg till**.

När du har registrerat certifikatet med ditt program i portalen för registrering av programmet, måste du aktivera programkod för klienten att använda certifikatet.

### <a name="create-a-new-application-secret"></a>Skapa en ny programhemlighet

Om du väljer att inte använda ett certifikat, kan du skapa en ny hemlighet för programmet.

1. Välj **certifikat och hemligheter**.
1. Välj **klienten hemligheter -> nya klienthemligheten**.
1. Ange en beskrivning av hemligheten och varaktighet. När du är klar, Välj **Lägg till**.

   När du har sparat klienthemligheten visas värdet för klienthemligheten. Kopiera det här värdet eftersom du inte kan komma att hämta nyckeln senare. Du kan ange nyckelvärdet med program-ID för inloggning som programmet. Lagra nyckelvärdet där programmet kan hämta det.

   ![Kopiera värdet för hemligheten eftersom du inte kan hämta det senare](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="required-permissions"></a>Nödvändiga behörigheter

Du måste ha tillräcklig behörighet för att registrera ett program med Azure AD-klienten och tilldela programmet till en roll i Azure-prenumerationen.

### <a name="check-azure-ad-permissions"></a>Kontrollera Azure AD-behörigheter

1. Välj **Azure Active Directory**.
1. Observera din roll. Om du har den **användaren** roll, måste du se till att icke-administratörer kan registrera program.

   ![Hitta din roll. Om du är en användare kan se till att icke-administratörer kan registrera appar](./media/howto-create-service-principal-portal/view-user-info.png)

1. Välj **användarinställningar**.
1. Kontrollera den **appregistreringar** inställningen. Det här värdet kan bara anges av en administratör. Om inställd **Ja**, någon användare i Azure AD-klient kan registrera en app.

Om inställningen appregistreringar anges till **nr**, endast användare med administratörsroller registrera dessa typer av program. Se [tillgängliga roller](../users-groups-roles/directory-assign-admin-roles.md#available-roles) och [rollbehörighet](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) vill veta mer om tillgängliga administratörsroller och särskilda behörigheter i Azure AD som har angetts för varje roll. Om ditt konto har tilldelats till rollen, men appinställningen för registreringen är begränsat till administratörer, be din administratör att antingen tilldela dig till någon av rollerna Administratör, som kan skapa och hantera alla aspekter av appregistreringar eller för att ge användare registrera appar.

### <a name="check-azure-subscription-permissions"></a>Kontrollera behörigheter för Azure-prenumeration

I Azure-prenumerationen, måste kontot ha `Microsoft.Authorization/*/Write` åtkomst till tilldelar en AD-app till en roll. Den här åtgärden beviljas genom rollen [Ägare](../../role-based-access-control/built-in-roles.md#owner) eller [Administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator). Om ditt konto tilldelas den **deltagare** roll, du har inte tillräcklig behörighet. Du får ett fel vid försök att tilldela en roll med tjänstens huvudnamn.

Att kontrollera dina Prenumerationsbehörigheter:

1. Välj ditt konto i det övre högra hörnet och välj **... -> Mina behörigheter**.

   ![Välj ditt konto och din användarbehörigheter](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Välj den prenumeration som du vill skapa tjänsten huvudnamn i från den nedrullningsbara listan. Välj **Klicka här om du vill visa fullständig åtkomst information för den här prenumerationen**.

   ![Välj den prenumeration som du vill skapa tjänsten huvudnamn i](./media/howto-create-service-principal-portal/view-details.png)

1. Välj **rolltilldelningar** att visa dina tilldelade roller och avgöra om du har tillräcklig behörighet för att tilldela en AD-app till en roll. Annars kan du be systemadministratören att lägga till dig till rollen Administratör för användaråtkomst. I följande bild tilldelas användaren rollen ägare, vilket innebär att användaren har tillräcklig behörighet.

   ![Det här exemplet visar att användaren har tilldelats rollen ägare](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill konfigurera ett program med flera innehavare, se [utvecklarguiden för auktorisering med Azure Resource Manager API](../../azure-resource-manager/resource-manager-api-authentication.md).
* Läs om hur du anger säkerhetsprinciper i [Azure rollbaserad åtkomstkontroll](../../role-based-access-control/role-assignments-portal.md).  
* En lista över tillgängliga åtgärder som kan beviljas eller nekas till användare finns i [Azure Resource Manager Resource Provider operations](../../role-based-access-control/resource-provider-operations.md).
