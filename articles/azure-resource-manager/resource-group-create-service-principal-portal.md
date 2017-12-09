---
title: "Skapa en identitet för Azure-app i portalen | Microsoft Docs"
description: "Beskriver hur du skapar en ny Azure Active Directory-program och tjänstens huvudnamn som kan användas med rollbaserad åtkomstkontroll i Azure Resource Manager för att hantera åtkomst till resurser."
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
ms.date: 11/16/2017
ms.author: tomfitz
ms.openlocfilehash: 9b5b33f61021bf4b0ae238e88c2926c0d17b4929
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>Använda portalen för att skapa ett Azure Active Directory-program och tjänstens huvudnamn som har åtkomst till resurser

När du har ett program som behöver komma åt eller ändra resurser måste du konfigurera ett program för Azure Active Directory (AD) och tilldela behörigheterna som krävs. Den här metoden är bättre att köra appen enligt dina autentiseringsuppgifter eftersom:

* Du kan tilldela behörigheter till app-identitet som skiljer sig från din egen behörighet. Dessa behörigheter normalt begränsad till exakt vad appen behöver göra.
* Du behöver inte ändra appens autentiseringsuppgifterna ändrar dina ansvarsområden. 
* Du kan använda ett certifikat för att automatisera autentisering när du kör ett oövervakat skript.

Den här artikeln visar hur du utför dessa åtgärder via portalen. Den fokuserar på en enskild klient program där programmet är avsett att köras i en enda organisation. Du använder vanligtvis stöd för en innehavare program för line-of-business-program som körs i din organisation.

## <a name="required-permissions"></a>Nödvändiga behörigheter

För att slutföra den här artikeln, måste du har behörighet att registrera ett program med Azure AD-klienten och tilldela program till en roll i din Azure-prenumeration. Vi behöver kontrollera att du har rätt behörighet för att utföra dessa steg.

### <a name="check-azure-active-directory-permissions"></a>Kontrollera behörigheter för Azure Active Directory

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com).

1. Välj **Azure Active Directory**.

   ![Välj azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Välj i Azure Active Directory, **användarinställningar**.

   ![Välj användarinställningar](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Kontrollera den **App registreringar** inställningen. Om värdet **Ja**, icke-administratörer kan registrera AD-appar. Den här inställningen innebär att alla användare i Azure AD-klient kan registrera en app. Du kan fortsätta att [Kontrollera Azure-Prenumerationsbehörigheter](#check-azure-subscription-permissions).

   ![Visa appen registreringar](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. Om app-registreringar inställningen är inställd på **nr**, men endast administrativa användare kan registrera appar. Kontrollera om ditt konto är administratör för Azure AD-klient. Välj **översikt** och **söka efter en användare** från snabb uppgifter.

   ![Sök efter användare](./media/resource-group-create-service-principal-portal/find-user.png)

1. Sök efter ditt konto och markera den när du har hittat.

   ![Sök användare](./media/resource-group-create-service-principal-portal/show-user.png)

1. Ditt konto, Välj **Directory rollen**.

   ![Directory roll](./media/resource-group-create-service-principal-portal/select-directory-role.png)

1. Visa tilldelade directory-rollen i Azure AD. Om ditt konto har tilldelats rollen, men registrering appinställningen (från föregående steg) är begränsad till administrativa användare, kan du be administratören att antingen tilldela dig en administratör eller så att användarna kan registrera appar.

   ![Visa roll](./media/resource-group-create-service-principal-portal/view-role.png)

### <a name="check-azure-subscription-permissions"></a>Kontrollera behörigheter för Azure-prenumeration

Kontot måste ha i din Azure-prenumeration `Microsoft.Authorization/*/Write` åtkomst att tilldela en AD-app till en roll. Den här åtgärden beviljas genom den [ägare](../active-directory/role-based-access-built-in-roles.md#owner) roll eller [administratör för användaråtkomst](../active-directory/role-based-access-built-in-roles.md#user-access-administrator) roll. Om ditt konto är kopplat till den **deltagare** roll, du har inte tillräcklig behörighet. Du får ett fel vid försök att tilldela en roll tjänstens huvudnamn.

För att kontrollera din Prenumerationsbehörigheter:

1. Om du inte redan tittar på Azure AD-kontot från föregående steg, väljer **Azure Active Directory** i den vänstra rutan.

1. Hitta din Azure AD-kontot. Välj **översikt** och **söka efter en användare** från snabb uppgifter.

   ![Sök efter användare](./media/resource-group-create-service-principal-portal/find-user.png)

1. Sök efter ditt konto och markera den när du har hittat.

   ![Sök användare](./media/resource-group-create-service-principal-portal/show-user.png)

1. Välj **Azure-resurser**.

   ![Välj resurser](./media/resource-group-create-service-principal-portal/select-azure-resources.png)

1. Visa tilldelade rollerna och kontrollera om du har tillräcklig behörighet för att tilldela en AD-app till en roll. Om inte, be administratören att lägga till dig till rollen Administratör för användaråtkomst prenumeration. I följande bild tilldelas användaren rollen som ägare för två prenumerationer, vilket innebär att användaren har tillräcklig behörighet.

   ![Visa behörighet](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com).
1. Välj **Azure Active Directory**.

   ![Välj azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Välj **App registreringar**.

   ![Välj app-registreringar](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Välj **nya appregistrering**.

   ![Lägg till app](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Ange ett namn och URL: en för programmet. Välj **webbapp / API** för typ av program som du vill skapa. Du kan inte skapa autentiseringsuppgifter för en **interna** programmet, därför fungerar inte den typen för ett automatiserat program. När du har angett värden, Välj **skapa**.

   ![namn på program](./media/resource-group-create-service-principal-portal/create-app.png)

Du har skapat ditt program.

## <a name="get-application-id-and-authentication-key"></a>Hämta program-ID och autentisering nyckel

När du programmässigt loggar in, behöver du ID för ditt program och en autentiseringsnyckel. Använd följande steg för att få dessa värden:

1. Från **App registreringar** i Azure Active Directory, väljer du ditt program.

   ![Välj program](./media/resource-group-create-service-principal-portal/select-app.png)

1. Kopiera den **program-ID** och lagra den i din programkod. Vissa [programexempel](#log-in-as-the-application) referera till det här värdet som klient-ID.

   ![Klient-ID](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. Om du vill generera en autentiseringsnyckel, Välj **nycklar**.

   ![Välj nycklar](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Ange en beskrivning av nyckeln och varaktighet för nyckeln. När du är klar väljer **spara**.

   ![Spara nyckel](./media/resource-group-create-service-principal-portal/save-key.png)

   När du har sparat nyckeln visas värdet för nyckeln. Kopiera det här värdet eftersom det inte går att hämta nyckeln senare. Du kan ange värdet för nyckeln med program-ID för att logga in som programmet. Lagra värdet för nyckeln där programmet kan hämta.

   ![Spara nyckel](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Hämta klient-ID

När programmässigt inloggningen måste du skicka klient-ID med din autentiseringsbegäran.

1. Välj **Azure Active Directory**.

   ![Välj azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Om du vill hämta klient-ID, Välj **egenskaper** för din Azure AD-klient.

   ![Välj Azure AD-egenskaper](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Kopiera den **katalog-ID**. Det här värdet är klient-ID.

   ![Klient-ID](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Tilldela program till rollen

Om du vill komma åt resurser i din prenumeration måste du tilldela program till en roll. Bestäm vilken roll representerar rätt behörigheter för programmet. Mer information om tillgängliga roller, se [RBAC: inbyggda roller](../active-directory/role-based-access-built-in-roles.md).

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

1. Sök efter programmet och markera den.

   ![Sök efter app](./media/resource-group-create-service-principal-portal/search-app.png)

1. Välj **spara** Slutför tilldela rollen. Du ser ditt program i listan över användare som har tilldelats en roll för detta omfång.

## <a name="log-in-as-the-application"></a>Logga in som programmet

Programmet har konfigurerats i Azure Active Directory. Du har ett ID och nyckel ska användas för att logga in som programmet. Programmet har tilldelats en roll som ger den vissa åtgärder kan utföras. För information om att logga in som programmet via olika plattformar, se:

* [PowerShell](resource-group-authenticate-service-principal.md#provide-credentials-through-powershell)
* [Azure CLI](resource-group-authenticate-service-principal-cli.md)
* [REST](/rest/api/#create-the-request)
* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett program med flera innehavare, se [Utvecklarhandbok tillstånd med Azure Resource Manager API](resource-manager-api-authentication.md).
* Läs om hur du anger säkerhetsprinciper i [Azure rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md).  
* En lista över tillgängliga åtgärder som kan beviljas eller nekas till användare finns [Azure Resource Manager Resource Provider operations](../active-directory/role-based-access-control-resource-provider-operations.md).
