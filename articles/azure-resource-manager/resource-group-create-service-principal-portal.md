---
title: Skapa en identitet för Azure-app i portalen | Microsoft Docs
description: Beskriver hur du skapar en ny Azure Active Directory-program och tjänstens huvudnamn som kan användas med rollbaserad åtkomstkontroll i Azure Resource Manager för att hantera åtkomst till resurser.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: e5d93963dddb4acb1147042ae338b32cb5d7646f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Använda portalen för att skapa ett Azure Active Directory-program och tjänstens huvudnamn som har åtkomst till resurser

När du har kod som behöver komma åt eller ändra resurser måste du ställa in ett program i Azure Active Directory (AD). Tilldela behörigheterna som krävs för att AD-program. Den här metoden är bättre att köra appen enligt dina autentiseringsuppgifter eftersom du kan tilldela behörigheter till app-identitet som skiljer sig från din egen behörighet. Dessa behörigheter normalt begränsad till exakt vad appen behöver göra.

Den här artikeln visar hur du utför dessa åtgärder via portalen. Den fokuserar på en enskild klient program där programmet är avsett att köras i en enda organisation. Du använder vanligtvis stöd för en innehavare program för line-of-business-program som körs i din organisation.

> [!IMPORTANT]
> Överväg att använda Azure AD hanterade tjänstidentiteten för tillämpningsprogrammets identitet i stället för att skapa ett huvudnamn för tjänsten. Azure AD-MSI är en funktion för förhandsversion av Azure Active Directory som gör det enklare att skapa en identitet för koden. Om din kod körs på en tjänst som stöder Azure AD MSI och har åtkomst till resurser som stöder Azure Active Directory-autentisering, är ett bättre alternativ för Azure AD-MSI. Läs mer om Azure AD MSI, inklusive tjänster som stöds för närvarande, [hanterade tjänstidentiteten för Azure-resurser](../active-directory/managed-service-identity/overview.md).

## <a name="required-permissions"></a>Nödvändiga behörigheter

För att slutföra den här artikeln, måste du har behörighet att registrera ett program med Azure AD-klienten och tilldela program till en roll i din Azure-prenumeration. Vi behöver kontrollera att du har rätt behörighet för att utföra dessa steg.

### <a name="check-azure-active-directory-permissions"></a>Kontrollera behörigheter för Azure Active Directory

1. Välj **Azure Active Directory**.

   ![välj azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Välj i Azure Active Directory, **användarinställningar**.

   ![Välj användarinställningar](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Kontrollera den **App registreringar** inställningen. Om värdet **Ja**, icke-administratörer kan registrera AD-appar. Den här inställningen innebär att alla användare i Azure AD-klient kan registrera en app. Du kan fortsätta att [Kontrollera Azure-Prenumerationsbehörigheter](#check-azure-subscription-permissions).

   ![Visa appen registreringar](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Om app-registreringar inställningen är inställd på **nr**, endast [globala administratörer](../active-directory/active-directory-assign-admin-roles-azure-portal.md) kan registrera appar. Kontrollera om ditt konto är administratör för Azure AD-klient. Välj **översikt** och granska din användarinformation. Om ditt konto har tilldelats rollen, men registrering appinställningen (från föregående steg) är begränsad till administrativa användare, kan du be administratören att antingen tilldela rollen global administratör, eller så att användarna kan registrera appar.

   ![Sök efter användare](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>Kontrollera behörigheter för Azure-prenumeration

Kontot måste ha i din Azure-prenumeration `Microsoft.Authorization/*/Write` åtkomst att tilldela en AD-app till en roll. Den här åtgärden beviljas genom den [ägare](../role-based-access-control/built-in-roles.md#owner) roll eller [administratör för användaråtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator) roll. Om ditt konto är kopplat till den **deltagare** roll, du har inte tillräcklig behörighet. Du får ett fel vid försök att tilldela en roll tjänstens huvudnamn.

För att kontrollera din Prenumerationsbehörigheter:

1. Välj ditt konto i det övre högra hörnet och välj **min behörighet**.

   ![Välj användarbehörighet](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. Välj prenumerationen från den nedrullningsbara listan. Välj **Klicka här om du vill visa fullständig åtkomst information för den här prenumerationen**.

   ![Sök efter användare](./media/resource-group-create-service-principal-portal/view-details.png)

1. Visa tilldelade rollerna och kontrollera om du har tillräcklig behörighet för att tilldela en AD-app till en roll. Om inte, be administratören att lägga till dig till rollen Administratör för användaråtkomst prenumeration. I följande bild tilldelas användaren rollen som ägare, vilket innebär att användaren har tillräcklig behörighet.

   ![Visa behörighet](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com).
1. Välj **Azure Active Directory**.

   ![välj azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Välj **Appregistreringar**.

   ![välj appregistreringar](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Välj **Ny programregistrering**.

   ![lägg till app](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Ange ett namn och en URL för programmet. Välj **Webbapp/API** för den programtyp som du vill skapa. Du kan inte skapa autentiseringsuppgifter för en [programspecifika](../active-directory/manage-apps/application-proxy-configure-native-client-application.md); därför att typen inte fungerar för ett automatiserat program. När du har angett värden, Välj **skapa**.

   ![namnprogram](./media/resource-group-create-service-principal-portal/create-app.png)

Du har skapat ditt program.

## <a name="get-application-id-and-authentication-key"></a>Hämta program-ID och autentisering nyckel

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

## <a name="assign-application-to-role"></a>Tilldela program till rollen

Om du vill komma åt resurser i din prenumeration måste du tilldela program till en roll. Bestäm vilken roll representerar rätt behörigheter för programmet. Mer information om tillgängliga roller, se [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md).

Du kan ange omfånget för prenumerationen, resursgruppen eller resursen. Behörigheter ärvs på lägre nivåer i omfånget. Till exempel innebär lägga till ett program rollen Läsare för en resursgrupp att den kan läsa resursgruppen och alla resurser som den innehåller.

1. Navigera till den nivå av omfång som du vill tilldela program till. Välj till exempel om du vill tilldela en roll på prenumerationsomfattningen **prenumerationer**. Du kan i stället välja en resursgrupp eller resurs.

   ![Välj prenumeration](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Välj den vissa prenumerationen (resursgrupp eller resurs) om du vill tilldela programmet till.

   ![Välj prenumerationen för tilldelning](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Välj **åtkomstkontroll (IAM)**.

   ![Välj åtkomst](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Välj **Lägg till**.

   ![Välj Lägg till](./media/resource-group-create-service-principal-portal/select-add.png)

1. Markera den roll som du vill tilldela till programmet. Följande bild visar den **Reader** roll.

   ![Välj rollen](./media/resource-group-create-service-principal-portal/select-role.png)

1. Som standard visas inte Azure Active Directory-program i de tillgängliga alternativen. Du måste ange namnet på den i Sök-fältet för att hitta programmet. Markera den.

   ![Sök efter app](./media/resource-group-create-service-principal-portal/search-app.png)

1. Välj **spara** Slutför tilldela rollen. Du ser ditt program i listan över användare som har tilldelats en roll för detta omfång.

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett program med flera innehavare, se [Utvecklarhandbok tillstånd med Azure Resource Manager API](resource-manager-api-authentication.md).
* Läs om hur du anger säkerhetsprinciper i [Azure rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md).  
* En lista över tillgängliga åtgärder som kan beviljas eller nekas till användare finns [Azure Resource Manager Resource Provider operations](../role-based-access-control/resource-provider-operations.md).
