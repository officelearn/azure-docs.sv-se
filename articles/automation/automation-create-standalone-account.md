---
title: Skapa ett fristående Azure Automation-konto
description: Den här artikeln vägleder dig genom stegen för att skapa, testa och använda en exempel säkerhet primär autentisering i Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: acd1ea95f4ef2717e315d9a247cd2fed54a02bf3
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-standalone-azure-automation-account"></a>Skapa ett fristående Azure Automation-konto

Den här artikeln visar hur du skapar ett Azure Automation-konto i Azure-portalen. Du kan använda portalen Automation-kontot för att utvärdera och lär dig mer om Automation utan att använda ytterligare hanteringslösningar eller integrering med Azure logganalys. Du kan lägga till dessa hanteringslösningar eller integrera med Log Analytics för avancerad övervakning av runbook-jobb när som helst i framtiden.

Du kan använda ett Automation-konto för att autentisera runbooks med hantering av resurser i Azure Resource Manager eller den klassiska distributionsmodellen.

Dessa konton skapas automatiskt när du skapar ett Automation-konto i Azure-portalen:

* **Kör som-konto**. Det här kontot har följande uppgifter:
  * Skapar ett huvudnamn för tjänsten i Azure Active Directory (AD Azure).
  * Skapar ett certifikat.
  * Tilldelar den Contributor Role-Based åtkomstkontroll (RBAC), som hanterar Azure Resource Manager-resurser med hjälp av runbooks.
* **Klassiska kör som-konto**. Det här kontot Överför ett certifikat. Certifikatet hanterar klassiska resurser med hjälp av runbooks.

Med dessa konton som skapats åt dig, kan du snabbt starta skapa och distribuera runbooks för att stödja dina behov av automatisering.

## <a name="permissions-required-to-create-an-automation-account"></a>Behörigheter som krävs för att skapa ett Automation-konto

Du måste ha följande behörigheter och behörigheter att skapa eller uppdatera ett Automation-konto och slutföra de uppgifter som beskrivs i den här artikeln:

* Om du vill skapa ett Automation-konto, Azure AD-användarkontot måste läggas till en roll med behörigheter som motsvarar rollen ägare för **Microsoft. Automation** resurser. Mer information finns i [rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
* I Azure-portalen under **Azure Active Directory** > **hantera** > **App registreringar**om **App registreringar**  är inställd på **Ja**, icke-administratörer i din Azure AD-klient kan [registrera Active Directory-program](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Om **App registreringar** är inställd på **nr**, användaren som utför den här åtgärden måste vara en global administratör i Azure AD.

Om du inte är medlem i Active Directory-instans för prenumerationens innan du lade till rollen som global administratör/coadministrator prenumerationens läggs du till Active Directory som en gäst. I det här scenariot kan du se det här meddelandet på det **lägga till Automation-konto** sida: ”du har inte behörighet att skapa”.

Om en användare läggs till rollen global administratör/coadministrator först, kan du ta bort dem från prenumerationens Active Directory-instans och nytt läggs till i användarrollen fullständig i Active Directory.

Att verifiera användarroller:

1. I Azure-portalen går du till den **Azure Active Directory** fönstret.
1. Välj **användare och grupper**.
1. Välj **alla användare**.
1. När du väljer en viss användare, markerar du **profil**. Värdet för den **användartyp** attribut under användarens profil får inte vara **gäst**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Skapa ett nytt Automation-konto i Azure-portalen

Om du vill skapa ett Azure Automation-konto i Azure-portalen, gör du följande:

1. Logga in på Azure-portalen med ett konto som är medlem i rollen administratörer för prenumeration och en coadministrator för prenumerationen.
1. Välj **+ skapa en resurs för**.
1. Sök efter **Automation**. I sökresultaten väljer **Automation**.

   ![Sök efter och välj kontrollen & Automation i Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. På nästa skärm markerar **skapa**.
  ![Lägg till Automation-konto](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

  > [!NOTE]
  > Om du ser följande meddelande i den **lägga till Automation-konto** rutan ditt konto är inte medlem i rollen administratörer för prenumeration och en coadministrator för prenumerationen.
  >
  > ![Lägg till varning för Automation-konto](media/automation-create-standalone-account/create-account-without-perms.png)
  >
1. I den **lägga till Automation-konto** rutan i den **namn** anger du ett namn för det nya Automation-kontot.
1. Om du har mer än en prenumeration i den **prenumeration** anger du den prenumeration som du vill använda för det nya kontot.
1. För **resursgruppen**, ange eller välj en ny eller befintlig resursgrupp.
1. För **plats**, Välj en plats för Azure-datacenter.
1. För den **skapa kör som-kontot Azure** alternativet, se till att **Ja** är markerad och välj sedan **skapa**.

  > [!NOTE]
  > Om inte du skapa kör som-kontot genom att välja **nr** för **skapa kör som-kontot Azure**, visas ett meddelande i den **lägga till Automation-konto** fönstret. Även om kontot har skapats i Azure-portalen, har den inte en motsvarande autentiseringsidentitet i prenumerationen klassisk distribution modell eller i Azure Resource Manager prenumeration katalogtjänsten. Därför har Automation-kontot inte åtkomst till resurser i din prenumeration. Detta förhindrar alla runbooks som refererar till det här kontot från att kunna autentisera och utföra åtgärder mot resurser i dessa distributionsmodeller.
  >
  > ![Lägg till varning för Automation-konto](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
  >
  > Deltagarrollen tilldelas när tjänstens huvudnamn inte har skapats.
  >

1. Om du vill spåra förloppet för Automation-konto skapas i menyn, Välj **meddelanden**.

### <a name="resources-included"></a>Resurser som ingår

När Automation-kontot har skapats skapas flera resurser automatiskt. När den har skapats dessa runbooks kan vara på ett säkert sätt tas bort om du inte vill hålla dem. Det kör som-konton kan användas för att autentisera till ditt konto i en runbook och ska lämnas om du inte skapar en ny eller behöver inte ha. I följande tabell sammanfattas resurserna för Kör som-kontot.

| Resurs | Beskrivning |
| --- | --- |
| AzureAutomationTutorial-runbook |Ett exempel grafisk runbook som visar hur du autentiserar med hjälp av kör som-kontot. Runbook hämtar alla resurser i hanteraren för filserverresurser. |
| AzureAutomationTutorialScript-runbook |Ett exempel PowerShell-runbook som visar hur du autentiserar med hjälp av kör som-kontot. Runbook hämtar alla resurser i hanteraren för filserverresurser. |
| AzureAutomationTutorialPython2 Runbook |Ett exempel Python-runbook som visar hur du autentiserar med hjälp av kör som-kontot. Runbook visar alla resursgrupper i prenumerationen. |
| AzureRunAsCertificate |En certifikattillgång som skapas automatiskt när Automation-kontot har skapats eller genom att använda ett PowerShell-skript för ett befintligt konto. Certifikatet autentiserar med Azure så att du kan hantera Azure Resource Manager-resurser från runbooks. Det här certifikatet har en livslängd på ett år. |
| AzureRunAsConnection |En anslutningstillgång som skapas automatiskt när Automation-kontot har skapats eller genom att använda ett PowerShell-skript för ett befintligt konto. |

I följande tabell sammanfattas resurserna för det klassiska Kör som-kontot.

| Resurs | Beskrivning |
| --- | --- |
| AzureClassicAutomationTutorial-runbook |Ett exempel grafisk runbook. Runbook hämtar alla klassiska virtuella datorer i en prenumeration med hjälp av den klassiska kör som-konto (certifikat). Sedan visas VM-namn och status. |
| AzureClassicAutomationTutorial Script-runbook |Ett exempel PowerShell-runbook. Runbook hämtar alla klassiska virtuella datorer i en prenumeration med hjälp av den klassiska kör som-konto (certifikat). Sedan visas VM-namn och status. |
| AzureClassicRunAsCertificate |En certifikattillgång som skapas automatiskt. Certifikatet autentiserar med Azure så att du kan hantera Azure klassiska resurser från runbooks. Det här certifikatet har en livslängd på ett år. |
| AzureClassicRunAsConnection |En anslutningstillgång som skapas automatiskt. Tillgången autentiserar med Azure så att du kan hantera Azure klassiska resurser från runbooks. |

## <a name="next-steps"></a>Nästa steg

* Mer information om redigering av grafiska finns [grafiska redigering i Azure Automation](automation-graphical-authoring-intro.md).
* Information om hur du kommer igång med PowerShell-runbooks finns i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md).
* Läs [My first Python2 runbook](automation-first-runbook-textual-python2.md) (Min första Python2-runbook) för att komma igång med Python2-runbooks.
