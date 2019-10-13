---
title: Skapa identitet för Azure-appen i portalen | Microsoft Docs
description: Beskriver hur du skapar ett nytt Azure Active Directory program och tjänstens huvud namn som kan användas med rollbaserad åtkomst kontroll i Azure Resource Manager för att hantera åtkomst till resurser.
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
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14c3f90918d246a63d50af7b3542e8e74d5fbcf1
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72295516"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Gör så här: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som har åtkomst till resurser

Den här artikeln visar hur du skapar ett nytt Azure Active Directory (Azure AD)-program och tjänstens huvud namn som kan användas med rollbaserad åtkomst kontroll. När du har kod som behöver åtkomst till eller ändra resurser kan du skapa en identitet för appen. Den här identiteten kallas tjänstens huvudnamn. Du kan sedan tilldela de behörigheter som krävs för tjänstens huvud namn. Den här artikeln visar hur du använder portalen för att skapa tjänstens huvud namn. Den fokuserar på ett program med en enda klient där programmet är avsett att köras endast inom en organisation. Du använder vanligt vis program med en enda klient för branschspecifika program som körs i din organisation.

> [!IMPORTANT]
> I stället för att skapa ett huvud namn för tjänsten bör du överväga att använda hanterade identiteter för Azure-resurser för din program identitet. Om din kod körs på en tjänst som har stöd för hanterade identiteter och åtkomst till resurser som stöder Azure AD-autentisering, är hanterade identiteter ett bättre alternativ. Mer information om hanterade identiteter för Azure-resurser, inklusive vilka tjänster som för närvarande stöder det, finns i [Vad är Managed identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory program

Nu ska vi gå direkt till att skapa identiteten. Om du stöter på ett problem kontrollerar du de [behörigheter som krävs](#required-permissions) för att se till att ditt konto kan skapa identiteten.

1. Logga in på ditt Azure-konto via [Azure Portal](https://portal.azure.com).
1. Välj **Azure Active Directory**.
1. Välj **Appregistreringar**.
1. Välj **ny registrering**.
1. Ge programmet ett namn. Välj en kontotyp som stöds, vilket avgör vem som kan använda programmet. Under **omdirigerings-URI**väljer du **webb** för den typ av program som du vill skapa. Ange URI: n där åtkomsttoken skickas till. Du kan inte skapa autentiseringsuppgifter för ett [internt program](../manage-apps/application-proxy-configure-native-client-application.md). Du kan inte använda den typen för ett automatiskt program. När du har angett värdena väljer du **Registrera**.

   ![Ange ett namn för ditt program](./media/howto-create-service-principal-portal/create-app.png)

Du har skapat ditt Azure AD-program och tjänstens huvud namn.

## <a name="assign-the-application-to-a-role"></a>Tilldela programmet till en roll

Du måste tilldela programmet till en roll för att få åtkomst till resurser i din prenumeration. Bestäm vilken roll som har rätt behörigheter för programmet. Mer information om tillgängliga roller finns i [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md).

Du kan ange omfång på nivån för prenumerationen, resurs gruppen eller resursen. Behörigheter ärvs till lägre omfattnings nivåer. Om du till exempel lägger till ett program till rollen läsare för en resurs grupp innebär det att den kan läsa resurs gruppen och alla resurser som den innehåller.

1. Navigera till den omfattnings nivå som du vill tilldela programmet till. Om du till exempel vill tilldela en roll i prenumerations omfånget väljer du **alla tjänster** och **prenumerationer**.

   ![Tilldela till exempel en roll i prenumerations omfånget](./media/howto-create-service-principal-portal/select-subscription.png)

1. Välj den specifika prenumeration som programmet ska tilldelas.

   ![Välj prenumeration för tilldelning](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Om du inte ser den prenumeration du letar efter väljer du **globala prenumerations filter**. Kontrol lera att den prenumeration du vill använda är vald för portalen.

1. Välj **Åtkomstkontroll (IAM)** .
1. Välj **Lägg till roll tilldelning**.
1. Välj den roll som du vill tilldela till programmet. Om du vill tillåta att programmet kör åtgärder som **starta om**, **Starta** och **stoppa** instanser väljer du **deltagar** rollen. Som standard visas inte Azure AD-program i de tillgängliga alternativen. Du hittar ditt program genom att söka efter namnet och välja det.

   ![Välj den roll som ska tilldelas programmet](./media/howto-create-service-principal-portal/select-role.png)

1. Välj **Spara** för att slutföra tilldelningen av rollen. Du ser ditt program i listan över användare som har tilldelats en roll för det omfånget.

Tjänstens huvud namn har kon figurer ATS. Du kan börja använda den för att köra skript eller appar. Nästa avsnitt visar hur du hämtar värden som behövs när du loggar in program mässigt.

## <a name="get-values-for-signing-in"></a>Hämta värden för inloggning

När du har loggat in via programmering måste du skicka klient-ID: t med din autentiseringsbegäran. Du behöver också ID: t för ditt program och en autentiseringsnyckel. Hämta dessa värden med följande steg:

1. Välj **Azure Active Directory**.
1. Välj ditt program från **Appregistreringar** i Azure AD.
1. Kopiera katalog-ID och lagra det i program koden.

    ![Kopiera katalogen (klient-ID) och lagra den i din app Code](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Kopiera **Program-ID:t** och lagra det i din programkod.

   ![Kopiera program-ID: t (klient)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certifikat och hemligheter
Daemon-program kan använda två typer av autentiseringsuppgifter för att autentisera med Azure AD: certifikat och program hemligheter.  Vi rekommenderar att du använder ett certifikat, men du kan också skapa en ny program hemlighet.

### <a name="upload-a-certificate"></a>Ladda upp ett certifikat

Du kan använda ett befintligt certifikat om du har ett.  Alternativt kan du skapa ett självsignerat certifikat för test ändamål. Öppna PowerShell och kör [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) med följande parametrar för att skapa ett självsignerat certifikat i användar certifikat arkivet på datorn: `$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature`.  Exportera det här certifikatet med hjälp av MMC-snapin-modulen [hantera användar certifikat](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) som är tillgänglig från kontroll panelen i Windows.

För att ladda upp certifikatet:

1. Välj **certifikat & hemligheter**.
1. Välj **överför certifikat** och välj certifikatet (ett befintligt certifikat eller det självsignerade certifikat som du exporterade).

    ![Välj överför certifikat och välj det som du vill lägga till](./media/howto-create-service-principal-portal/upload-cert.png)

1. Välj **Lägg till**.

När du har registrerat certifikatet med ditt program i program registrerings portalen måste du aktivera klient program koden för att använda certifikatet.

### <a name="create-a-new-application-secret"></a>Skapa en ny program hemlighet

Om du väljer att inte använda ett certifikat kan du skapa en ny program hemlighet.

1. Välj **certifikat & hemligheter**.
1. Välj **klient hemligheter – > ny klient hemlighet**.
1. Ange en beskrivning av hemligheten och en varaktighet. När du är färdig väljer du **Lägg till**.

   När klient hemligheten har sparats visas värdet för klient hemligheten. Kopiera det här värdet eftersom du inte kan hämta nyckeln senare. Du anger nyckel värdet med program-ID för att logga in som programmet. Lagra nyckelvärdet där programmet kan hämta det.

   ![Kopiera det hemliga värdet eftersom du inte kan hämta det senare](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="required-permissions"></a>Nödvändiga behörigheter

Du måste ha behörighet att registrera ett program med din Azure AD-klient och tilldela programmet till en roll i din Azure-prenumeration.

### <a name="check-azure-ad-permissions"></a>Kontrol lera Azure AD-behörigheter

1. Välj **Azure Active Directory**.
1. Anteckna din roll. Om du har **användar** rollen måste du se till att icke-administratörer kan registrera program.

   ![Hitta din roll. Om du är en användare ser du till att icke-administratörer kan registrera appar](./media/howto-create-service-principal-portal/view-user-info.png)

1. Välj **användar inställningar**.
1. Kontrol lera inställningen för **Appregistreringar** . Det här värdet kan bara anges av en administratör. Om det är inställt på **Ja**kan alla användare i Azure AD-klienten registrera en app.

Om inställningen för appens registrering är inställd på **Nej**kan endast användare med en administratörs roll registrera dessa typer av program. Se [tillgängliga roller](../users-groups-roles/directory-assign-admin-roles.md#available-roles) och [roll behörigheter](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) för att lära dig om tillgängliga administratörs roller och de särskilda behörigheter i Azure AD som ges till varje roll. Om ditt konto är tilldelat till användar rollen, men appens registrerings inställning är begränsad till administratörs användare, be administratören att antingen tilldela dig en av administratörs rollerna som kan skapa och hantera alla aspekter av app-registreringar, eller för att göra det möjligt för användare att registrera appar.

### <a name="check-azure-subscription-permissions"></a>Kontrol lera behörigheter för Azure-prenumeration

I din Azure-prenumeration måste ditt konto ha `Microsoft.Authorization/*/Write`-åtkomst för att tilldela en AD-App till en roll. Den här åtgärden beviljas genom rollen [Ägare](../../role-based-access-control/built-in-roles.md#owner) eller [Administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator). Om ditt konto har tilldelats rollen **deltagare** , har du inte tillräcklig behörighet. Du får ett fel meddelande när du försöker tilldela tjänstens huvud namn till en roll.

Så här kontrollerar du dina prenumerations behörigheter:

1. Välj ditt konto i det övre högra hörnet och välj **...-> mina behörigheter**.

   ![Välj ditt konto och dina användar behörigheter](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. I list rutan väljer du den prenumeration som du vill skapa tjänstens huvud namn i. Välj sedan **Klicka här om du vill visa fullständig åtkomst information för den här prenumerationen**.

   ![Välj den prenumeration som du vill skapa tjänstens huvud namn i](./media/howto-create-service-principal-portal/view-details.png)

1. Välj **roll tilldelningar** för att visa dina tilldelade roller och kontrol lera om du har tillräcklig behörighet för att tilldela en AD-App till en roll. Annars ber du prenumerations administratören att lägga till dig i rollen som administratör för användar åtkomst. I följande bild tilldelas användaren rollen ägare, vilket innebär att användaren har tillräcklig behörighet.

   ![Det här exemplet visar att användaren är tilldelad till ägar rollen](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du anger säkerhets principer finns i [Azure Role-based Access Control](../../role-based-access-control/role-assignments-portal.md).  
* En lista över tillgängliga åtgärder som kan beviljas eller nekas till användare finns i [Azure Resource Manager Resource Provider-åtgärder](../../role-based-access-control/resource-provider-operations.md).
