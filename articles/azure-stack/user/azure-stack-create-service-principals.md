---
title: Skapa ett tjänstobjekt för Azure Stack | Microsoft Docs
description: Beskriver hur du skapar ett huvudnamn för tjänsten som kan användas med rollbaserad åtkomstkontroll i Azure Resource Manager för att hantera åtkomst till resurser.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: da75d829ccb74f7be675501b601ad3e6375bfb70
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813168"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Ge program åtkomst till Azure Stack-resurser genom att skapa tjänstens huvudnamn

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan ge ett programåtkomst till Azure Stack-resurser genom att skapa ett tjänstens huvudnamn som använder Azure Resource Manager. Ett huvudnamn för tjänsten kan du delegera specifika behörigheter med hjälp av [rollbaserad åtkomstkontroll](azure-stack-manage-permissions.md).

Som bästa praxis bör du använda tjänstens huvudnamn för dina program. Tjänstens huvudnamn är bättre att köra en app med dina autentiseringsuppgifter för följande:

* Du kan tilldela behörigheter till tjänstens huvudnamn som skiljer sig från dina egna kontobehörigheter. Vanligtvis är ett tjänstens huvudnamn begränsade till exakt vad app måste göra.
* Du behöver inte ändra dess autentiseringsuppgifter om din roll eller ansvarsområden ändrar.
* Du kan använda ett certifikat för att automatisera autentisering när du kör ett oövervakat skript.

## <a name="example-scenario"></a>Exempel på ett scenario

Du har en configuration management-app som behöver inventera Azure-resurser med Azure Resource Manager. Du kan skapa ett tjänstobjekt och tilldela den till rollen läsare. Den här rollen ger appen skrivskyddad åtkomst till Azure-resurser.

## <a name="getting-started"></a>Komma igång

Använd stegen i den här artikeln som vägledning för:

* Skapa ett huvudnamn för tjänsten för din app.
* Registrera din app och skapa en autentiseringsnyckel.
* Tilldela din app till en roll.

Konfiguration av Active Directory för Azure Stack avgör hur du skapar ett huvudnamn för tjänsten. Välj något av följande alternativ:

* Skapa ett tjänstobjekt för [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Skapa ett tjänstobjekt för [Active Directory Federation Services (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

Stegen för att tilldela ett huvudnamn för tjänsten till en roll samma för Azure AD och AD FS. När du skapar tjänstens huvudnamn, kan du [delegera behörigheter](azure-stack-create-service-principals.md#assign-role-to-service-principal) genom att tilldela den till en roll.

## <a name="create-service-principal-for-azure-ad"></a>Skapa tjänstens huvudnamn för Azure AD

Om Azure Stack använder Azure AD som Identitetslagret, kan du skapa en tjänst huvudnamn med hjälp av samma steg som Azure, med hjälp av Azure-portalen.

>[!NOTE]
Kontrollera att du har den [Azure AD-behörigheter som krävs för](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) innan du börjar skapa ett huvudnamn för tjänsten.

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

Skapa ett huvudnamn för tjänsten för ditt program:

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory** > **appregistreringar** > **Lägg till**.
3. Ange ett namn och en URL för programmet. Välj antingen **webbapp / API** eller **interna** för typ av program som du vill skapa. När du har angett värdena, Välj **skapa**.

### <a name="get-credentials"></a>Hämta autentiseringsuppgifter

När du loggar in programmässigt kan du använda det ID: T för ditt program och en autentiseringsnyckel. Hämta dessa värden:

1. Från **appregistreringar** i Active Directory, Välj ditt program.

2. Kopiera **Program-ID:t** och lagra det i din programkod. Använda program i exempelprogrammen **klient-id** när det gäller den **program-ID**.

     ![Program-ID för programmet](./media/azure-stack-create-service-principal/image12.png)
3. Välj **Nycklar** om du vill generera en autentiseringsnyckel.

4. Tillhandahåll beskrivning av och varaktighet för nyckeln. Välj **Spara** när du är klar.

>[!IMPORTANT]
När du har sparat nyckeln, nyckeln **värdet** visas. Anteckna det här värdet eftersom det går inte att hämta nyckeln senare. Lagra nyckelvärdet där programmet kan hämta det.

![Nyckelvärdet varning för sparade nyckeln.](./media/azure-stack-create-service-principal/image15.png)

Det sista steget är [tilldela en roll för ditt program](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Skapa tjänstens huvudnamn för AD FS

Om du har distribuerat Azure Stack med hjälp av AD FS som Identitetslagret kan du använda PowerShell för följande uppgifter:

* Skapa ett huvudnamn för tjänsten.
* Tilldela en roll tjänstens huvudnamn.
* Logga in med tjänstens huvudnamn identitet.

Mer information om hur du skapar tjänstens huvudnamn finns i [skapa tjänstens huvudnamn för AD FS](../azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Tilldela en roll med tjänstens huvudnamn

För att komma åt resurser i din prenumeration, måste du tilldela programmet till en roll. Bestäm vilken roll representerar rätt behörigheter för programmet. Läs om tillgängliga roller i [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md).

>[!NOTE]
Du kan ange en Användarrollens omfång på nivån för en prenumeration, en resursgrupp eller en resurs. Behörigheter ärvs till lägre nivåer av omfång. Till exempel innebär en app med rollen Läsare för en resursgrupp att appen kan läsa någon av resurserna i resursgruppen.

Använd följande steg som en vägledning för att tilldela en roll till tjänstens huvudnamn.

1. Navigera till den nivå av omfång du vill tilldela programmet till i Azure Stack-portalen. Om du vill tilldela en roll prenumerationsområde, väljer du exempelvis **prenumerationer**.

2. Välj prenumeration att tilldela programmet till. I det här exemplet är prenumerationen som Visual Studio Enterprise.

     ![Välj Visual Studio Enterprise-prenumeration för tilldelning](./media/azure-stack-create-service-principal/image16.png)

3. Välj **åtkomstkontroll (IAM)** för prenumerationen.

4. Välj **Lägg till rolltilldelning**.

5. Välj den roll som du vill tilldela till programmet.

6. Sök efter ditt program och markera den.

7. Välj **OK** Slutför tilldela rollen. Du kan se ditt program i listan över användare som har tilldelats en roll för detta omfång.

Nu när du har skapat ett tjänstobjekt och tilldelas en roll, ditt program kan komma åt Azure Stack-resurser.

## <a name="next-steps"></a>Nästa steg

[Hantera användarbehörigheter](azure-stack-manage-permissions.md)
