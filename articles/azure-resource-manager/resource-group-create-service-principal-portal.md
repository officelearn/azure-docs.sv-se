---
title: Skapa för Azure-app i portalen | Microsoft Docs
description: Beskriver hur du skapar en ny Azure Active Directory-program och tjänstens huvudnamn som kan användas med rollbaserad åtkomstkontroll i Azure Resource Manager för att hantera åtkomst till resurser.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: fc0ccd84f493fd69c84515331386592ec11a887e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025301"
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Använda portalen för att skapa ett Azure Active Directory-program och tjänstens huvudnamn som kan komma åt resurser

När du har kod som behöver åtkomst till eller ändra resurser kan ställa du in ett Azure Active Directory (AD)-program. Du kan sedan tilldela behörigheterna som krävs för AD-programmet. Den här metoden är bättre att köra appen enligt dina autentiseringsuppgifter eftersom du kan tilldela behörigheter till appen identiteten som skiljer sig från dina egna behörigheter. Vanligen är dessa behörigheter begränsade till exakt vad appen behöver göra.

Den här artikeln visar hur du utför de här stegen via portalen. Den fokuserar på en enda klient program där programmet är avsett att köras i endast en organisation. Du använder vanligtvis enda klient program för line-of-business-program som körs i din organisation.

> [!IMPORTANT]
> Överväg att använda Azure AD hanterad tjänstidentitet som tillämpningsprogrammets identitet i stället för att skapa ett huvudnamn för tjänsten. Azure AD MSI är en funktion för förhandsversion av Azure Active Directory som gör det enklare att skapa en identitet för koden. Om din kod körs på en tjänst som stöder Azure AD MSI och har åtkomst till resurser som stöder Azure Active Directory-autentisering, är Azure AD MSI ett bättre alternativ för dig. Läs mer om Azure AD MSI, inklusive tjänster som stöds för närvarande under avsnittet om [hanterade tjänstidentiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="required-permissions"></a>Nödvändiga behörigheter

För att slutföra den här artikeln, måste du har behörighet att registrera ett program med Azure AD-klienten och tilldela programmet till en roll i Azure-prenumerationen. Nu ska vi Kontrollera att du har rätt behörighet för att utföra de här stegen.

### <a name="check-azure-active-directory-permissions"></a>Kontrollera Azure Active Directory-behörigheter

1. Välj **Azure Active Directory**.

   ![välj azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Välj **Användarinställningar** i Azure Active Directory.

   ![Välj användarinställningar](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Kontrollera den **appregistreringar** inställningen. Om inställd **Ja**, icke-administratörer kan registrera AD-appar. Den här inställningen innebär att alla användare i Azure AD-klientorganisationen kan registrera en app. Du kan fortsätta att [Kontrollera Azure-Prenumerationsbehörighet](#check-azure-subscription-permissions).

   ![Visa app-registreringar](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Om inställningen appregistreringar anges till **nr**, endast [globala administratörer](../active-directory/users-groups-roles/directory-assign-admin-roles.md) kan registrera appar. Kontrollera om ditt konto är en administratör för Azure AD-klient. Välj **översikt** och titta på din användarinformation. Om ditt konto har tilldelats till rollen, men registreringen appinställningen (från föregående steg) är begränsad till administratörer, kan du be din administratör att antingen tilldela dig till rollen som global administratör eller så att användarna kan registrera appar.

   ![Sök efter användare](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Kontrollera behörigheter för Azure-prenumeration

I Azure-prenumerationen, måste kontot ha `Microsoft.Authorization/*/Write` åtkomst till tilldelar en AD-app till en roll. Den här åtgärden beviljas genom rollen [Ägare](../role-based-access-control/built-in-roles.md#owner) eller [Administratör för användaråtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator). Om ditt konto tilldelas den **deltagare** roll, du har inte tillräcklig behörighet. Du får ett fel vid försök att tilldela en roll med tjänstens huvudnamn.

Att kontrollera dina Prenumerationsbehörigheter:

1. Välj ditt konto i det övre högra hörnet och välj **Mina behörigheter**.

   ![Välj användarbehörigheter](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. Välj prenumerationen från den nedrullningsbara listan. Välj **Klicka här om du vill visa fullständig åtkomst information för den här prenumerationen**.

   ![Sök efter användare](./media/resource-group-create-service-principal-portal/view-details.png)

1. Visa dina tilldelade roller och avgör om du har tillräcklig behörighet för att tilldela en AD-app till en roll. Annars kan du be systemadministratören att lägga till dig till rollen Administratör för användaråtkomst. I följande bild tilldelas användaren rollen ägare, vilket innebär att användaren har tillräcklig behörighet.

   ![Visa behörigheter](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com).
1. Välj **Azure Active Directory**.

   ![välj azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Välj **Appregistreringar**.

   ![välj appregistreringar](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Välj **Ny programregistrering**.

   ![lägg till app](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Ange ett namn och en URL för programmet. Välj **Webbapp/API** för den programtyp som du vill skapa. Du kan inte skapa autentiseringsuppgifter för en [programspecifik](../active-directory/manage-apps/application-proxy-configure-native-client-application.md)därför att typen inte fungerar för ett automatiserat program. När du har angett värdena, Välj **skapa**.

   ![namnprogram](./media/resource-group-create-service-principal-portal/create-app.png)

Du har skapat ditt program.

## <a name="get-application-id-and-authentication-key"></a>Hämta nyckel för program-ID och autentiseringsnyckel

När du loggar in med programmet behöver du programmets ID och en autentiseringsnyckel. Hämta dessa värden med följande steg:

1. Välj ditt program i **Appregistreringar** i Azure Active Directory.

   ![välj program](./media/resource-group-create-service-principal-portal/select-app.png)

1. Kopiera **Program-ID:t** och lagra det i din programkod. Vissa [programexempel](#log-in-as-the-application) refererar till det här värdet som klient-ID:t.

   ![klient-ID](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Välj **Inställningar** om du vill generera en autentiseringsnyckel.

   ![välj inställningar](./media/resource-group-create-service-principal-portal/select-settings.png)

1. Välj **Nycklar** om du vill generera en autentiseringsnyckel.

   ![välj nycklar](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Tillhandahåll beskrivning av och varaktighet för nyckeln. Välj **Spara** när du är klar.

   ![spara nyckel](./media/resource-group-create-service-principal-portal/save-key.png)

   När du har sparat nyckeln visas nyckelns värde. Kopiera det här värdet eftersom det inte går att hämta nyckeln senare. Logga in som programmet genom att ange nyckelvärdet med program-ID:t. Lagra nyckelvärdet där programmet kan hämta det.

   ![sparad nyckel](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Hämta klientorganisations-ID

När du loggar in med programmet måste du skicka klientorganisations-ID:t med din autentiseringsbegäran.

1. Välj **Azure Active Directory**.

   ![välj azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Om du vill hämta klientorganisations-ID:t väljer du **Egenskaper** för din Microsoft Azure Active Directory-klientorganisation.

   ![välj Azure AD-egenskaper](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Kopiera **katalog-ID:t**. Det här värdet är ditt klientorganisations-ID.

   ![klientorganisations-ID](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Tilldela program till roll

För att komma åt resurser i din prenumeration, måste du tilldela programmet till en roll. Bestäm vilken roll representerar rätt behörigheter för programmet. Läs om tillgängliga roller i [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md).

Du kan ange omfånget för den prenumerationen, resursgruppen eller resursen. Behörigheter ärvs till lägre nivåer av omfång. Till exempel lägga till ett program till rollen Läsare för en resursgrupp innebär att den kan läsa resursgruppen och alla resurser som den innehåller.

1. Navigera till den nivå av omfång som du vill tilldela programmet till. Om du vill tilldela en roll prenumerationsområde, väljer du exempelvis **prenumerationer**. Du kan i stället välja en resursgrupp eller resurs.

   ![Välj prenumeration](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Välj den specifika prenumerationen (resursgrupp eller resurs) för att tilldela programmet till.

   ![Välj prenumeration för tilldelning](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Välj **åtkomstkontroll (IAM)**.

   ![Välj åtkomst](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Välj **Lägg till**.

   ![Välj Lägg till](./media/resource-group-create-service-principal-portal/select-add.png)

1. Välj den roll som du vill tilldela till programmet. För att köra programmet åtgärder som att **omstart**, **starta** och **stoppa** instanser, du måste ha att välja rollen **deltagare**. Följande bild visar den **läsare** roll.

   ![Välj roll](./media/resource-group-create-service-principal-portal/select-role.png)

1. Som standard visas inte Azure Active Directory-program i de tillgängliga alternativen. Du måste ange namnet på den i sökfältet för att hitta ditt program. Markera den.

   ![Sök efter app](./media/resource-group-create-service-principal-portal/search-app.png)

1. Välj **spara** Slutför tilldela rollen. Du ser ditt program i listan över användare som har tilldelats en roll för detta omfång.

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett program med flera innehavare, se [utvecklarguiden för auktorisering med Azure Resource Manager API](resource-manager-api-authentication.md).
* Läs om hur du anger säkerhetsprinciper i [Azure rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md).  
* En lista över tillgängliga åtgärder som kan beviljas eller nekas till användare finns i [Azure Resource Manager Resource Provider operations](../role-based-access-control/resource-provider-operations.md).
