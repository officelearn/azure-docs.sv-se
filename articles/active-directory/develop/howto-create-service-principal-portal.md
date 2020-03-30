---
title: Skapa ett Azure AD-app & tjänsthuvudnamn i portalen
titleSuffix: Microsoft identity platform
description: Skapa ett nytt Azure Active Directory-app & tjänsthuvudnamn för att hantera åtkomst till resurser med rollbaserad åtkomstkontroll i Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: c5f65adfe401f2f6e99234d08b8e8dabeff7d5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264120"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Så här: Använd portalen för att skapa ett Azure AD-program och tjänsthuvudnamn som kan komma åt resurser

Den här artikeln visar hur du skapar ett nytt Azure Active Directory -program och tjänsthuvudnamn som kan användas med den rollbaserade åtkomstkontrollen. När du har kod som behöver komma åt eller ändra resurser kan du skapa en identitet för appen. Den här identiteten kallas tjänstens huvudnamn. Du kan sedan tilldela de behörigheter som krävs till tjänstens huvudnamn. I den här artikeln visas hur du använder portalen för att skapa tjänstens huvudnamn. Den fokuserar på ett program med en klient där programmet är avsett att köras inom endast en organisation. Du använder vanligtvis program med en klient för affärsprogram som körs inom organisationen.

> [!IMPORTANT]
> I stället för att skapa ett tjänsthuvudnamn bör du överväga att använda hanterade identiteter för Azure-resurser för din programidentitet. Om din kod körs på en tjänst som stöder hanterade identiteter och har åtkomst till resurser som stöder Azure AD-autentisering, är hanterade identiteter ett bättre alternativ för dig. Mer information om hanterade identiteter för Azure-resurser, inklusive vilka tjänster som för närvarande stöder det, finns i [Vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

Låt oss hoppa rakt in i att skapa identiteten. Om du stöter på ett problem kontrollerar du [vilka behörigheter som krävs](#required-permissions) för att ditt konto ska kunna skapa identiteten.

1. Logga in på ditt Azure-konto via [Azure-portalen](https://portal.azure.com).
1. Välj **Azure Active Directory**.
1. Välj **Appregistreringar**.
1. Välj **Ny registrering**.
1. Ge programmet ett namn. Välj en kontotyp som stöds, som avgör vem som kan använda programmet. Under **Omdirigera URI**väljer du **Webb** för den typ av program som du vill skapa. Ange den URI där åtkomsttoken skickas till. Du kan inte skapa autentiseringsuppgifter för ett [inbyggt program](../manage-apps/application-proxy-configure-native-client-application.md). Du kan inte använda den typen för ett automatiserat program. När du har ställt in värdena väljer du **Registrera**.

   ![Ange ett namn på ditt program](./media/howto-create-service-principal-portal/create-app.png)

Du har skapat ditt Azure AD-program och tjänsthuvudnamn.

## <a name="assign-a-role-to-the-application"></a>Tilldela programmet en roll

Om du vill komma åt resurser i prenumerationen måste du tilldela programmet en roll. Bestäm vilken roll som ger rätt behörighet för programmet. Mer information om tillgängliga roller finns [i RBAC: Inbyggd roller](../../role-based-access-control/built-in-roles.md).

Du kan ange omfånget på nivån för prenumerationen, resursgruppen eller resursen. Behörigheter ärvs till lägre omfattningsnivåer. Om du till exempel lägger till ett program i rollen Läsare för en resursgrupp kan den läsa resursgruppen och alla resurser som den innehåller.

1. I Azure-portalen väljer du den omfattningsnivå som du vill tilldela programmet till. Om du till exempel vill tilldela en roll i prenumerationsomfånget söker du efter och väljer **Prenumerationer**eller väljer **Prenumerationer** på **startsidan.**

   ![Tilldela till exempel en roll i prenumerationsomfånget](./media/howto-create-service-principal-portal/select-subscription.png)

1. Välj den prenumeration som programmet ska tilldelas.

   ![Välj prenumeration för tilldelning](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Om du inte ser den prenumeration du letar efter väljer du **filter för globala prenumerationer**. Kontrollera att den prenumeration du vill ha är markerad för portalen.

1. Välj **Åtkomstkontroll (IAM)**.
1. Välj **Lägg till rolltilldelning**.
1. Välj den roll som du vill tilldela till programmet. Om du till exempel vill att programmet ska kunna utföra åtgärder som **omstart**, **starta** och **stoppa** instanser väljer du rollen **Deltagare.**  Läs mer om de [tillgängliga rollerna](../../role-based-access-control/built-in-roles.md) Som standard visas inte Azure AD-program i de tillgängliga alternativen. Om du vill hitta programmet söker du efter namnet och väljer det.

   ![Välj den roll som programmet ska tilldelas](./media/howto-create-service-principal-portal/select-role.png)

1. Välj **Spara** om du vill slutföra tilldelningen av rollen. Du ser ditt program i listan över användare med en roll för det omfånget.

Ditt huvudnamn är inställt. Du kan börja använda den för att köra skript eller appar. I nästa avsnitt visas hur du hämtar värden som behövs när du loggar in programmässigt.

## <a name="get-values-for-signing-in"></a>Hämta värden för inloggning

När du loggar in programmässigt måste du skicka klient-ID:n med din autentiseringsbegäran. Du behöver också ID för ditt program och en autentiseringsnyckel. Hämta dessa värden med följande steg:

1. Välj **Azure Active Directory**.
1. Välj ditt program från **Appregistreringar** i Azure AD.
1. Kopiera katalog-ID:t (klienten) och lagra det i programkoden.

    ![Kopiera katalogen (klient-ID) och lagra den i appkoden](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Kopiera **Program-ID:t** och lagra det i din programkod.

   ![Kopiera programmets (klient)-ID](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certifikat och hemligheter
Daemon-program kan använda två former av autentiseringsuppgifter för att autentisera med Azure AD: certifikat och programhemligheter.  Vi rekommenderar att du använder ett certifikat, men du kan också skapa en ny programhemlighet.

### <a name="upload-a-certificate"></a>Ladda upp ett certifikat

Du kan använda ett befintligt certifikat om du har ett.  Du kan också skapa ett självsignerat certifikat endast för *testning*. Öppna PowerShell och kör [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) med följande parametrar för att skapa ett självsignerat certifikat i användarcertifikatarkivet på datorn: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exportera det här certifikatet till en fil med mmc-snapin-modulen [Hantera användarcertifikat](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) som är tillgängligt från Kontrollpanelen i Windows.

1. Välj **Kör** på **Start-menyn** och ange sedan **certmgr.msc**.

   Verktyget Certifikathanteraren för den aktuella användaren visas.

1. Om du vill visa dina certifikat expanderar du den **personliga** katalogen under **Certifikat - Aktuell användare** i den vänstra rutan.
1. Högerklicka på det certifikat du skapade, välj **Alla uppgifter >Exportera**.
1. Följ guiden Exportera certifikat.  Exportera den privata nyckeln, ange ett lösenord för cert-filen och exportera till en fil.

Så här laddar du upp certifikatet:

1. Välj **Azure Active Directory**.
1. Välj ditt program från **Appregistreringar** i Azure AD.
1. Välj **Certifikat & hemligheter**.
1. Välj **Ladda upp certifikat** och välj certifikatet (ett befintligt certifikat eller det självsignerade certifikat som du exporterade).

    ![Välj Ladda upp certifikat och välj det du vill lägga till](./media/howto-create-service-principal-portal/upload-cert.png)

1. Välj **Lägg till**.

När du har registrerat certifikatet med ditt program i programregistreringsportalen måste du aktivera klientprogramkoden för att använda certifikatet.

### <a name="create-a-new-application-secret"></a>Skapa en ny programhemlighet

Om du väljer att inte använda ett certifikat kan du skapa en ny programhemlighet.

1. Välj **Certifikat & hemligheter**.
1. Välj **Klienthemligheter -> Ny klienthemlighet**.
1. Ange en beskrivning av hemligheten och en varaktighet. När du är klar väljer du **Lägg till**.

   När du har sparat klienthemligheten visas värdet för klienthemligheten. Kopiera det här värdet eftersom du inte kan hämta nyckeln senare. Du anger nyckelvärdet med program-ID:et för att logga in som program. Lagra nyckelvärdet där programmet kan hämta det.

   ![Kopiera det hemliga värdet eftersom du inte kan hämta det senare](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Konfigurera åtkomstprinciper för resurser
Tänk på att du kan behöva konfigurera tilläggsbehörigheter för resurser som programmet behöver komma åt. Du måste till exempel också [uppdatera en nyckelvalvs åtkomstprinciper](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) för att ge ditt program åtkomst till nycklar, hemligheter eller certifikat.  

1. I [Azure-portalen](https://portal.azure.com)navigerar du till nyckelvalvet och väljer **Åtkomstprinciper**.  
1. Välj **Lägg till åtkomstprincip**och välj sedan de nyckel-, hemlighets- och certifikatbehörigheter som du vill bevilja ditt program.  Välj det tjänsthuvudnamn som du skapade tidigare.
1. Välj **Lägg till** om du vill lägga till åtkomstprincipen och sedan **Spara** för att genomföra ändringarna.
    ![Lägg till åtkomstprincip](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>Nödvändiga behörigheter

Du måste ha tillräcklig behörighet för att registrera ett program med din Azure AD-klient och tilldela programmet en roll i din Azure-prenumeration till programmet.

### <a name="check-azure-ad-permissions"></a>Kontrollera Azure AD-behörigheter

1. Välj **Azure Active Directory**.
1. Notera din roll. Om du har rollen **Användare** måste du se till att icke-administratörer kan registrera program.

   ![Hitta din roll. Om du är användare, se till att icke-administratörer kan registrera appar](./media/howto-create-service-principal-portal/view-user-info.png)

1. Välj **Användarinställningar**i den vänstra rutan .
1. Kontrollera inställningen **för appregistreringar.** Det här värdet kan bara anges av en administratör. Om den är inställd på **Ja**kan alla användare i Azure AD-klienten registrera en app.

Om inställningen för appregistreringar är inställd på **Nej**kan endast användare med administratörsroll registrera dessa typer av program. Se [tillgängliga roller](../users-groups-roles/directory-assign-admin-roles.md#available-roles) och [rollbehörigheter](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) för att lära dig mer om tillgängliga administratörsroller och de specifika behörigheter i Azure AD som ges till varje roll. Om ditt konto har tilldelats användarrollen, men appregistreringsinställningen är begränsad till administratörsanvändare, ber du administratören att antingen tilldela dig en av administratörsrollerna som kan skapa och hantera alla aspekter av appregistreringar, eller för att göra det möjligt för användare att registrera Apps.

### <a name="check-azure-subscription-permissions"></a>Kontrollera Azure-prenumerationsbehörigheter

I din Azure-prenumeration måste `Microsoft.Authorization/*/Write` ditt konto ha åtkomst för att tilldela en roll till en AD-app. Den här åtgärden beviljas genom rollen [Ägare](../../role-based-access-control/built-in-roles.md#owner) eller [Administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator). Om ditt konto tilldelas rollen **Deltagare** har du inte tillräcklig behörighet. Du får ett felmeddelande när du försöker tilldela tjänstens huvudnamn en roll.

Så här kontrollerar du dina prenumerationsbehörigheter:

1. Sök efter och välj **Prenumerationer**eller välj **Prenumerationer** på **startsidan.**

   ![Search](./media/howto-create-service-principal-portal/select-subscription.png)

1. Välj den prenumeration som du vill skapa tjänstens huvudnamn i.

   ![Välj prenumeration för tilldelning](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Om du inte ser den prenumeration du letar efter väljer du **filter för globala prenumerationer**. Kontrollera att den prenumeration du vill ha är markerad för portalen.

1. Välj **Mina behörigheter**. Välj sedan **Klicka här om du vill visa fullständig åtkomstinformation för den här prenumerationen**.

   ![Välj den prenumeration som du vill skapa tjänstens huvudnamn i](./media/howto-create-service-principal-portal/view-details.png)

1. Välj **Visa** i **rolltilldelningar** om du vill visa dina tilldelade roller och ta reda på om du har tillräcklig behörighet för att tilldela en roll till en AD-app. Om inte, ber prenumerationsadministratören att lägga till dig i rollen Administratör för användaråtkomst. I följande bild tilldelas användaren rollen Ägare, vilket innebär att användaren har tillräcklig behörighet.

   ![I det här exemplet visas att användaren har tilldelats rollen Ägare](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du anger säkerhetsprinciper finns i [Azure Role-based Access Control](../../role-based-access-control/role-assignments-portal.md).  
* En lista över tillgängliga åtgärder som kan beviljas eller nekas användare finns i [Azure Resource Manager Resource Provider- åtgärder](../../role-based-access-control/resource-provider-operations.md).
