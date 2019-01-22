---
title: Skapa ett fristående Azure Automation-konto
description: Den här artikeln vägleder dig genom stegen för att skapa, testa och använda ett huvudnamn exempel säkerhetsautentisering i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1590a02503a12be870dab14a1ccffdd3cf64a282
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426615"
---
# <a name="create-a-standalone-azure-automation-account"></a>Skapa ett fristående Azure Automation-konto

Den här artikeln visar hur du skapar ett Azure Automation-konto i Azure-portalen. Du kan använda portalen Automation-kontot för att utvärdera och lär dig mer om Automation utan att använda ytterligare hanteringslösningarna eller integreringen med Azure Log Analytics. Du kan lägga till de här hanteringslösningarna eller integrera med Log Analytics för avancerad övervakning av runbook-jobb vid någon tidpunkt i framtiden.

Du kan autentisera runbooks med ett Automation-konto genom att hantera resurser i Azure Resource Manager eller den klassiska distributionsmodellen. Ett Automation-konto kan hantera resurser i alla regioner och prenumerationer för en viss klientorganisation.

Dessa konton skapas automatiskt när du skapar ett Automation-konto i Azure portal:

* **Kör som-konto**. Det här kontot har följande uppgifter:
  * Skapar ett huvudnamn för tjänsten i Azure Active Directory (AD Azure).
  * Skapar ett certifikat.
  * Tilldelar den Contributor Role-Based åtkomstkontroll (RBAC), som hanterar Azure Resource Manager-resurser med hjälp av runbooks.
* **Klassiskt kör som-konto**. Det här kontot Överför ett hanteringscertifikat. Certifikatet hanterar klassiska resurser med hjälp av runbooks.

Med dessa konton som skapats för dig kan börja du snabbt skapa och distribuera runbookflöden för dina automatiseringsbehov.

## <a name="permissions-required-to-create-an-automation-account"></a>Behörigheter som krävs för att skapa ett Automation-konto

Du måste ha följande privilegier och behörigheter att skapa eller uppdatera ett Automation-konto och utföra åtgärder som beskrivs i den här artikeln:

* Om du vill skapa ett Automation-konto, Azure AD-användarkonto måste du lägga till en roll med behörigheter motsvarande ägarrollen för **Microsoft. Automation** resurser. Mer information finns i [rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
* I Azure-portalen under **Azure Active Directory** > **hantera** > **appregistreringar**om **appregistreringar**  är inställd på **Ja**, icke-administratörer i din Azure AD-klient kan [registrera Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Om **appregistreringar** är inställd på **nr**, den användare som utför den här åtgärden måste vara en global administratör i Azure AD.

Om du inte är medlem i prenumerationens Active Directory-instans innan du läggs till i prenumerationens global administratör/medadministratör roll läggs du till Active Directory som gäst. I det här scenariot kan du se det här meddelandet på den **Lägg till Automation-konto** sidan: ”Du har inte behörighet att skapa”.

Om en användare läggs till rollen global administratör/medadministratör först, kan du ta bort dem från prenumerationens Active Directory-instans och readd dem till fullständiga användarrollen i Active Directory.

För att verifiera användarroller:

1. I Azure-portalen går du till den **Azure Active Directory** fönstret.
1. Välj **Användare och grupper**.
1. Välj **alla användare**.
1. När du väljer en viss användare, väljer **profil**. Värdet för den **användartyp** attributet under användarens profil bör inte vara **gäst**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Skapa ett nytt Automation-konto i Azure portal

Om du vill skapa ett Azure Automation-konto i Azure-portalen gör du följande:

1. Logga in på Azure portal med ett konto som är medlem i rollen administratörer för prenumeration och en medadministratör för prenumerationen.
1. Välj **+ skapa en resurs**.
1. Sök efter **Automation**. I sökresultaten väljer **Automation**.

   ![Sök efter och välj automatisering och kontroll på Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. På nästa skärm väljer **skapa**.

  ![Lägg till Automation-konto](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

  > [!NOTE]
  > Om du ser följande meddelande i den **Lägg till Automation-konto** fönstret ditt konto är inte medlem i rollen administratörer för prenumeration och en medadministratör för prenumerationen.
  >
  > ![Lägg till Automation-konto varning](media/automation-create-standalone-account/create-account-without-perms.png)

1. I den **Lägg till Automation-konto** fönstret i den **namn** anger du ett namn för ditt Automation-konto. Det här namnet kan inte ändras när det är valt. *Namn är unikt för varje region och resursgrupp. Namn för Automation-konton som har tagits bort är kanske inte omedelbart tillgängligt.*
1. Om du har mer än en prenumeration i den **prenumeration** anger du den prenumeration som du vill använda för det nya kontot.
1. För **resursgrupp**anger eller väljer en ny eller befintlig resursgrupp.
1. För **plats**, Välj en plats i Azure-datacenter.
1. För den **skapa kör som-konto** alternativet, se till att **Ja** är markerat och välj sedan **skapa**.

  > [!NOTE]
  > Om du inte väljer att skapa kör som-kontot genom att välja **nr** för **skapa kör som-konto**, visas ett meddelande i den **Lägg till Automation-konto** fönstret. Även om kontot har skapats i Azure-portalen, har den inte motsvarande autentiseringsidentitet i prenumerationen klassiska modellen eller i katalogtjänsten för Azure Resource Manager-prenumeration. Därför har Automation-kontot inte åtkomst till resurser i din prenumeration. Detta förhindrar att runbookflöden som refererar till det här kontot kan autentisera och utföra åtgärder mot resurser i dessa distributionsmodeller.
  >
  > ![Lägg till Automation-konto varning](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
  >
  > Om tjänstobjektnamnet inte skapas tilldelas inte rollen deltagare.
  >

1. Om du vill spåra förloppet för Automation-kontot skapades, i menyn, Välj **meddelanden**.

### <a name="resources-included"></a>Resurser som ingår

När Automation-kontot har skapats skapas flera resurser automatiskt. När du har skapat, kan dessa runbooks tas bort om du inte vill att de. Det kör som-konton kan användas för att autentisera till ditt konto i en runbook och ska fortsätta om du inte skapar en ny eller behöver dem inte. I följande tabell sammanfattas resurserna för Kör som-kontot.

| Resurs | Beskrivning |
| --- | --- |
| AzureAutomationTutorial-runbook |Ett exempel grafisk runbook som visar hur du autentiserar med hjälp av kör som-kontot. Runbook hämtar alla Resource Manager-resurser. |
| AzureAutomationTutorialScript-runbook |Ett exempel PowerShell-runbook som visar hur du autentiserar med hjälp av kör som-kontot. Runbook hämtar alla Resource Manager-resurser. |
| AzureAutomationTutorialPython2 Runbook |En Python-exempelrunbook som visar hur du autentiserar med hjälp av kör som-kontot. Runbook visar en lista över alla resursgrupper i prenumerationen. |
| AzureRunAsCertificate |Certifikattillgång som skapas automatiskt när Automation-kontot har skapats eller genom att använda ett PowerShell-skript för ett befintligt konto. Certifikatet autentiserar med Azure så att du kan hantera Azure Resource Manager-resurser från runbooks. Det här certifikatet har en livslängd på ett år. |
| AzureRunAsConnection |Anslutningstillgång som skapas automatiskt när Automation-kontot har skapats eller genom att använda ett PowerShell-skript för ett befintligt konto. |

I följande tabell sammanfattas resurserna för det klassiska Kör som-kontot.

| Resurs | Beskrivning |
| --- | --- |
| AzureClassicAutomationTutorial-runbook |Ett exempel på en grafisk runbook. Runbook hämtar alla klassiska virtuella datorer i en prenumeration med hjälp av det klassiska kör som-kontot (certifikat). Den visar sedan namn på virtuella datorer och status. |
| AzureClassicAutomationTutorial Script-runbook |Ett exempel PowerShell-runbook. Runbook hämtar alla klassiska virtuella datorer i en prenumeration med hjälp av det klassiska kör som-kontot (certifikat). Den visar sedan namn på virtuella datorer och status. |
| AzureClassicRunAsCertificate |Certifikattillgång som skapas automatiskt. Certifikatet autentiserar med Azure så att du kan hantera klassiska Azure-resurser från runbooks. Det här certifikatet har en livslängd på ett år. |
| AzureClassicRunAsConnection |Anslutningstillgång som skapas automatiskt. Tillgången autentiserar med Azure så att du kan hantera klassiska Azure-resurser från runbooks. |

## <a name="next-steps"></a>Nästa steg

* Läs mer om grafisk redigering i [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).
* Information om hur du kommer igång med PowerShell-runbooks finns i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md).
* Läs [My first Python2 runbook](automation-first-runbook-textual-python2.md) (Min första Python2-runbook) för att komma igång med Python2-runbooks.

