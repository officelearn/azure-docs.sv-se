---
title: Skapa ett fristående Azure Automation-konto
description: I den här artikeln får du hjälp med stegen för att skapa, testa och använda en exempelsäkerhetsautentisering i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 01/15/2019
ms.topic: conceptual
ms.openlocfilehash: f06480767b697dca8fe41e484c02aefc58f040bf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261338"
---
# <a name="create-a-standalone-azure-automation-account"></a>Skapa ett fristående Azure Automation-konto

Den här artikeln visar hur du skapar ett Azure Automation-konto i Azure-portalen. Du kan använda portalautomationskontot för att utvärdera och lära dig mer om Automation utan att använda ytterligare hanteringslösningar eller integrering med Azure Monitor-loggar. Du kan lägga till dessa hanteringslösningar eller integrera med Azure Monitor-loggar för avancerad övervakning av runbook-jobb när som helst i framtiden.

Med ett Automation-konto kan du autentisera runbooks genom att hantera resurser i Antingen Azure Resource Manager eller den klassiska distributionsmodellen. Ett Automation-konto kan hantera resurser i alla regioner och prenumerationer för en viss klientorganisation.

När du skapar ett Automation-konto i Azure-portalen skapas dessa konton automatiskt:

* **Kör som konto**. Det här kontot utför följande uppgifter:
  * Skapar ett tjänsthuvudnamn i Azure Active Directory (Azure AD).
  * Skapar ett certifikat.
  * Tilldelar CONTRIBUTOR Role-Based Access Control (RBAC), som hanterar Azure Resource Manager-resurser med hjälp av runbooks.

Med dessa konton skapade åt dig kan du snabbt börja bygga och distribuera runbooks för att stödja dina automatiseringsbehov.

## <a name="permissions-required-to-create-an-automation-account"></a>Behörigheter som krävs för att skapa ett Automation-konto

Om du vill skapa eller uppdatera ett Automation-konto och slutföra de uppgifter som beskrivs i den här artikeln måste du ha följande behörigheter och behörigheter:

* Om du vill skapa ett Automation-konto måste ditt Azure AD-användarkonto läggas till i en roll med behörigheter som motsvarar ägarrollen för **Microsoft. Automatiseringsresurser.** Mer information finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
* I Azure-portalen, under **Azure Active Directory** > **MANAGE-användarinställningar,****MANAGE** > om **appregistreringar** är inställda på **Ja**kan användare som inte är administratörer i din Azure AD-klient [registrera Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Om **Appregistreringar** är inställt på **Nej**måste användaren som utför den här åtgärden vara en global administratör i Azure AD.

Om du inte är medlem i prenumerationens Active Directory-instans innan du läggs till i prenumerationens globala administratörs-/coadministratorroll läggs du till i Active Directory som gäst. I det här fallet visas det här meddelandet på sidan **Lägg till automationskonto:** "Du har inte behörighet att skapa."

Om en användare läggs till i rollen global administratör/coadministrator först kan du ta bort dem från prenumerationens Active Directory-instans och sedan läsa dem till den fullständiga användarrollen i Active Directory.

Så här verifierar du användarroller:

1. Gå till Azure Active **Directory** i Azure-portalen.
1. Välj **Användare och grupper**.
1. Välj **Alla användare**.
1. När du har valt en viss användare väljer du **Profil**. Värdet för attributet **Användartyp** under användarens profil ska inte vara **Gäst**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Skapa ett nytt Automation-konto i Azure-portalen

Så här skapar du ett Azure Automation-konto i Azure-portalen:

1. Logga in på Azure-portalen med ett konto som är medlem i rollen administratörer för prenumerationen och en medadministratör för prenumerationen.
1. Välj **+ Skapa en resurs**.
1. Sök efter **Automation**. Välj **Automatisering**i sökresultaten .

   ![Sök efter och välj Automation &-kontroll på Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. På nästa skärm väljer du **Skapa**.

   ![Lägg till automationskonto](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Om följande meddelande visas i fönstret **Lägg till automationskonto** är ditt konto inte medlem i rollen administratörer för prenumerationen och en medadministrator för prenumerationen.
   >
   > ![Lägg till varning för automationskonto](media/automation-create-standalone-account/create-account-without-perms.png)

1. Ange ett namn på ditt nya Automation-konto i rutan **Namn** i fönstret **Lägg till automationskonto.** Det går inte att ändra namnet när det har valts. *Namn på automationskonto är unika per region och resursgrupp. Namn för automationskonton som har tagits bort kanske inte är omedelbart tillgängliga.*
1. Om du har mer än en prenumeration anger du den prenumeration du vill använda för det nya kontot i rutan **Prenumeration.**
1. För **resursgrupp**anger eller väljer du en ny eller befintlig resursgrupp.
1. För **Plats**väljer du en Azure datacenterplats.
1. För alternativet **Skapa Azure Kör som konto,** se till att **Ja** är markerat och välj sedan **Skapa**.

   > [!NOTE]
   > Om du väljer att inte skapa kontot Kör som genom att välja **Nej** för **Skapa Azure Kör som konto**visas ett meddelande i fönstret Lägg till **automationskonto.** Även om kontot skapas i Azure-portalen har kontot inte en motsvarande autentiseringsidentitet i din klassiska distributionsmodellprenumeration eller i prenumerationskatalogtjänsten för Azure Resource Manager. Därför har Automation-kontot inte åtkomst till resurser i din prenumeration. Detta förhindrar att alla runbooks som refererar till det här kontot kan autentisera och utföra uppgifter mot resurser i dessa distributionsmodeller.
   >
   > ![Lägg till varning för automationskonto](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > När tjänstens huvudnamn inte skapas tilldelas inte rollen Deltagare.
   >

1. Om du vill spåra hur du skapar automationskontot väljer du **Meddelanden**på menyn .

### <a name="resources-included"></a>Resurser som ingår

När Automation-kontot har skapats skapas flera resurser automatiskt. När dessa runbooks har skapats kan de tas bort på ett säkert sätt om du inte vill behålla dem. Kör som-konton, kan användas för att autentisera till ditt konto i en runbook, och bör lämnas om du inte skapar en annan eller inte kräver dem. I följande tabell sammanfattas resurserna för Kör som-kontot.

| Resurs | Beskrivning |
| --- | --- |
| AzureAutomationTutorial-runbook |En grafisk runbook som exempel på hur du autentiserar med hjälp av kontot Kör som. Runbook hämtar alla Resource Manager-resurser. |
| AzureAutomationTutorialScript-runbook |Ett exempel på PowerShell-runbook som visar hur du autentiserar med hjälp av kontot Kör som. Runbook hämtar alla Resource Manager-resurser. |
| AzureAutomationTutorialPython2 Runbook |Ett exempel på Python-runbook som visar hur du autentiserar med hjälp av kontot Kör som. Runbook listar alla resursgrupper som finns i prenumerationen. |
| AzureRunAsCertificate |En certifikattillgång som skapas automatiskt när Automation-kontot skapas eller med hjälp av ett PowerShell-skript för ett befintligt konto. Certifikatet autentiserar med Azure så att du kan hantera Azure Resource Manager-resurser från runbooks. Det här certifikatet har en livslängd på ett år. |
| AzureRunAsConnection |En anslutningstillgång som skapas automatiskt när Automation-kontot skapas eller med hjälp av ett PowerShell-skript för ett befintligt konto. |

## <a name="create-a-classic-run-as-account"></a>Skapa ett klassiskt run-as-konto

Klassiska run-as-konton skapas inte längre, som standard, när du skapar ett Azure Automation-konto. Om du fortfarande behöver ett klassiskt körkonto utför du följande steg.

1. På sidan **Automation-konto** väljer du **Kör som konton** under **Kontoinställningar**.
2. Välj **Azure Classic-körning som konto**.
3. Klicka på **Skapa** om du vill fortsätta med skapandet av klassiska kör som konto.

## <a name="next-steps"></a>Nästa steg

* Mer information om grafisk redigering finns [i Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).
* Information om hur du kommer igång med PowerShell-runbooks finns i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).
* Information om hur du kommer igång med PowerShell-arbetsflödeskörningsböcker finns i [Min första PowerShell-arbetsflödeskörningsbok](automation-first-runbook-textual.md).
* Läs [My first Python2 runbook](automation-first-runbook-textual-python2.md) (Min första Python2-runbook) för att komma igång med Python2-runbooks.

