---
title: Skapa ett huvudnamn för tjänsten för Azure-Stack | Microsoft Docs
description: Beskriver hur du skapar ett huvudnamn för tjänsten som kan användas med rollbaserad åtkomstkontroll i Azure Resource Manager för att hantera åtkomst till resurser.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 3c9f114c2844021d515765888aa19f18a0adc10b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320760"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Ge program åtkomst till resurser i Azure-stacken genom att skapa tjänstens huvudnamn

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan ge åtkomst till ett program till Azure-stacken resurser genom att skapa en tjänst huvudnamn som använder Azure Resource Manager. Ett huvudnamn för tjänsten kan du delegera specifika behörigheter med [rollbaserad åtkomstkontroll](azure-stack-manage-permissions.md).

Som bästa praxis bör du använda tjänstens huvudnamn för dina program. Tjänstens huvudnamn är bättre att köra en app med dina autentiseringsuppgifter av följande skäl:

* Du kan tilldela behörigheter till tjänsten huvudnamn som skiljer sig från din egen behörighet. Tjänstens huvudnamn normalt begränsad till exakt vad appen behöver göra.
* Du behöver inte ändra appens autentiseringsuppgifterna ändrar din roll eller ansvarsområden.
* Du kan använda ett certifikat för att automatisera autentisering när du kör ett oövervakat skript.

## <a name="example-scenario"></a>Exempel på ett scenario

Du har en configuration management-app som behöver inventera Azure-resurser med Azure Resource Manager. Du kan skapa en tjänstens huvudnamn och tilldela rollen läsare. Den här rollen ger app skrivskyddad åtkomst till Azure-resurser.

## <a name="getting-started"></a>Komma igång

Använd stegen i den här artikeln som vägledning för:

* Skapa ett huvudnamn för tjänsten för din app.
* Registrera din app och skapa en autentiseringsnyckel.
* Koppla appen till en roll.

Konfiguration av Active Directory för Azure-Stack avgör hur du skapar ett huvudnamn för tjänsten. Välj något av följande alternativ:

* Skapa en tjänstens huvudnamn för [Azure Active Directory (AD Azure)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Skapa en tjänstens huvudnamn för [Active Directory Federation Services (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

Stegen för att tilldela ett huvudnamn för tjänsten till en roll samma för Azure AD och AD FS. När du skapar huvudnamn för tjänsten, kan du [delegera behörigheter](azure-stack-create-service-principals.md#assign-role-to-service-principal) genom att tilldela en roll.

## <a name="create-a-service-principal-for-azure-ad"></a>Skapa ett huvudnamn för tjänsten för Azure AD

Om Azure-stacken använder Azure AD som Identitetslagret, kan du skapa en tjänst huvudnamn med samma metod som i Azure med Azure-portalen.

>[!NOTE]
Se till att du har den [nödvändiga behörigheter för Azure AD](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) innan du börjar skapa ett huvudnamn för tjänsten.

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

Skapa ett huvudnamn för tjänsten för ditt program:

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory** > **App registreringar** > **Lägg till**.
3. Ange ett namn och en URL för programmet. Välj antingen **webbapp / API** eller **interna** för typ av program som du vill skapa. När du har angett värden, Välj **skapa**.

### <a name="get-credentials"></a>Hämta autentiseringsuppgifter

När du loggar in via programmering använda ID: T för ditt program och en autentiseringsnyckel. Hämta dessa värden:

1. Från **App registreringar** Markera programmet i Active Directory.

2. Kopiera **Program-ID:t** och lagra det i din programkod. Program i den [programexempel](#sample-applications) använder **klient-id** när du refererar till den **program-ID**.

     ![Program-ID för programmet](./media/azure-stack-create-service-principal/image12.png)
3. Välj **Nycklar** om du vill generera en autentiseringsnyckel.

4. Tillhandahåll beskrivning av och varaktighet för nyckeln. Välj **Spara** när du är klar.

>[!IMPORTANT]
När du har sparat nyckeln nyckeln **värdet** visas. Skriv ned det här värdet eftersom du inte kan hämta nyckeln senare. Lagra nyckelvärdet där programmet kan hämta det.

![Nyckel/värde-varning för sparade nyckeln.](./media/azure-stack-create-service-principal/image15.png)

Det sista steget är [tilldela en roll för ditt program](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Skapa tjänstens huvudnamn för AD FS

Om du har distribuerat Azure stacken använder AD FS som Identitetslagret använder du PowerShell för följande uppgifter:

* Skapa ett huvudnamn för tjänsten.
* Tilldela en roll tjänstens huvudnamn.
* Logga in med tjänstens huvudnamn identitet.

Mer information om hur du skapar huvudnamn för tjänsten finns [skapa tjänstens huvudnamn för AD FS](../azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Tilldela en roll tjänstens huvudnamn

Om du vill komma åt resurser i din prenumeration måste du tilldela program till en roll. Bestäm vilken roll representerar rätt behörigheter för programmet. Mer information om tillgängliga roller, se [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md).

>[!NOTE]
Du kan ange en rollens omfattning för en prenumeration, resursgrupp eller en resurs. Behörigheter ärvs på lägre nivåer i omfånget. Till exempel innebär en app med rollen Läsare för en resursgrupp att appen kan läsa alla resurser i resursgruppen.

Använd följande steg som en vägledning för att tilldela en roll till ett huvudnamn för tjänsten.

1. Navigera till den nivå för omfång som du vill tilldela program till i Azure Stack-portalen. Välj till exempel om du vill tilldela en roll på prenumerationsomfattningen **prenumerationer**.

2. Välj den prenumeration för att tilldela program till. I det här exemplet är prenumerationen Visual Studio Enterprise.

     ![Välj Visual Studio Enterprise-prenumeration för tilldelning](./media/azure-stack-create-service-principal/image16.png)

3. Välj **Access Control (IAM)** för prenumerationen.

     ![Välj åtkomstkontroll](./media/azure-stack-create-service-principal/image17.png)

4. Välj **Lägg till**.

5. Markera den roll som du vill tilldela till programmet.

6. Sök efter programmet och markera den.

7. Välj **OK** Slutför tilldela rollen. Du kan se ditt program i listan över användare som har tilldelats en roll för detta omfång.

Nu när du har skapat ett huvudnamn för tjänsten och tilldelats en roll, ditt program kan komma åt resurser i Azure-stacken.

## <a name="next-steps"></a>Nästa steg

[Hantera användarbehörigheter](azure-stack-manage-permissions.md)
