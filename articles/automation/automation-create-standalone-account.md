---
title: "Skapa ett fristående Azure Automation-konto | Microsoft Docs"
description: "Självstudie som steg för steg beskriver hur du skapar, testar och använder autentisering med säkerhetsobjekt i Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: magoedte
ms.openlocfilehash: 0397b45753ea64d1a33916d5e0dff12d6e1d80aa
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="create-a-standalone-azure-automation-account"></a>Skapa ett fristående Azure Automation-konto
I det här avsnittet beskrivs hur du skapar ett Automation-konto på Azure Portal om du vill utvärdera och lära dig mer om Azure Automation utan de ytterligare hanteringslösningarna eller integreringen med OMS Log Analytics för avancerad övervakning av runbook-jobb.  Du kan när som helst lägga till de här hanteringslösningarna eller integrera med Log Analytics senare.  Med Automation-kontot kan du autentisera runbookflöden som hanterar resurser i Azure Resource Manager eller klassiska Azure.

När du skapar ett Automation-konto på Azure Portal skapas följande automatiskt:

* Kör som-kontot som skapar ett nytt namn för tjänstobjektet i Azure Active Directory och ett certifikat samt tilldelar rollen som deltagare med rollbaserad åtkomstkontroll (RBAC), som används för att hantera Resource Manager-resurser med hjälp av runbooks.   
* Ett klassiskt Kör som-konto genom att överföra ett hanteringscertifikat, som används för att hantera klassiska resurser med hjälp av runbooks.  

Detta gör processen enklare för dig och hjälper dig att snabbt börja skapa och distribuera runbooks för dina automatiseringsbehov.  

## <a name="permissions-required-to-create-automation-account"></a>Behörighet som krävs för att skapa Automation-konton
För att kunna skapa eller uppdatera Automation-kontot och slutföra det här avsnittet måste du ha vissa behörigheter.   
 
* Om du ska kunna skapa ett Automation-konto måste ditt AD-användarkonto tilldelas en roll med behörigheter motsvarande ägarrollen för Microsoft.Automation-resurser enligt beskrivningen i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).  
* Om **Ja** har angetts för inställningen Appregistreringar kan användare som inte är administratörer i din Azure AD-klient [registrera AD-program](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Om **Nej** har angetts för inställningen Appregistreringar måste användaren som utför den här åtgärden vara global administratör i Azure AD. 

Om du inte är medlem i prenumerationens Active Directory-instans innan du läggs till i rollen som global administratör/medadministratör för prenumerationen läggs du till i Active Directory som gäst. I så fall visas varningen ”Du har inte behörighet att skapa ...” på bladet **Lägg till Automation-konto**. Användare som har tilldelats rollen som global administratör/medadministratör kan tas bort från prenumerationens Active Directory-instans och sedan läggas till igen så att de blir fullständiga användare i Active Directory. Du kan kontrollera detta i rutan **Azure Active Directory** på Azure Portal genom att välja **Användare och grupper**, välja **Alla användare**, välja den specifika användaren och sedan välja **Profil**. Värdet för attributet **Användartyp** under användarens profil bör inte vara lika med **Gäst**.

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Skapa ett nytt Automation-konto från Azure Portal
I det här avsnittet ska du utföra följande steg för att skapa ett Azure Automation-konto från Azure Portal.    

1. Logga in på Azure Portal med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
2. Klicka på **Ny**.<br><br> ![Välj alternativet Ny på Azure Portal](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Sök efter **Automation** och välj sedan **Automatisering och kontroll*** i sökresultaten.<br><br> ![Sök efter och välj Automation från Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. Klicka på **Lägg till** på bladet Automation-konton.<br><br>![Lägga till ett Automation-konto](media/automation-create-standalone-account/automation-create-automationacct-properties.png)


   > [!NOTE]
   > Om du ser följande varning i bladet **Lägg till Automation-konto** beror det på att ditt konto inte är medlem i rollerna administratör eller medadministratör för prenumerationen.<br><br>![Varningsmeddelande för Lägga till ett Automation-konto](media/automation-create-standalone-account/create-account-without-perms.png)
   > 
   > 
4. På bladet **Lägg till Automation-konto** skriver du namnet på det nya Automation-kontot i rutan **Namn**.
5. Om du har mer än en prenumeration anger du den prenumeration som du vill använda för det nya kontot, en ny eller befintlig **resursgrupp** och en **plats** för Azure-datacentret.
6. Kontrollera att värdet **Ja** har valts för **Skapa Kör som-konto i Azure** och klicka på knappen **Skapa**.  
   
   > [!NOTE]
   > Om du väljer att inte skapa ”Kör som”-kontot genom att välja alternativet **Nej** visas ett varningsmeddelande på bladet **Lägg till Automation-konto**.  Kontot skapas på Azure-portalen, men har inte motsvarande autentiseringsidentitet i den klassiska prenumerationskatalogtjänsten eller Resource Manager-prenumerationskatalogtjänsten och har därför inte åtkomst till resurser i din prenumeration.  Det här innebär att runbooks som refererar till det här kontot inte kan autentisera och utföra åtgärder mot resurser i dessa distributionsmodeller.
   > 
   > ![Varningsmeddelande för Lägga till ett Automation-konto](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)<br>
   > Om tjänstobjektnamnet inte skapas tilldelas inte rollen Deltagare.
   > 

7. Medan Azure skapar Automation-kontot kan du följa förloppet under **Meddelanden** på menyn.

### <a name="resources-included"></a>Resurser som ingår
När Automation-kontot har skapats skapas flera resurser automatiskt.  I följande tabell sammanfattas resurserna för Kör som-kontot.<br>

| Resurs | Beskrivning |
| --- | --- |
| AzureAutomationTutorial-runbook |Ett exempel på en grafisk runbook som visar hur du autentiserar med hjälp av Kör som-kontot och hur du hämtar alla Resource Manager-resurser. |
| AzureAutomationTutorialScript-runbook |Ett exempel på en PowerShell-runbook som visar hur du autentiserar med hjälp av Kör som-kontot och hur du hämtar alla Resource Manager-resurser. |
| AzureAutomationTutorialPython2 Runbook |En python-exempelrunbook som visar hur man autentiserar med Kör som-konto och anger sedan de resursgrupper som finns i den angivna prenumerationen. |
| AzureRunAsCertificate |Certifikattillgång som skapas automatiskt när Automation-kontot genereras eller med hjälp av PowerShell-skriptet nedan för ett befintligt konto.  Den gör att du kan autentisera med Azure så att du kan hantera Azure Resource Manager-resurser från runbooks.  Det här certifikatet har en livslängd på ett år. |
| AzureRunAsConnection |Anslutningstillgång som skapas automatiskt när Automation-kontot genereras eller med hjälp av PowerShell-skriptet nedan för ett befintligt konto. |

I följande tabell sammanfattas resurserna för det klassiska Kör som-kontot.<br>

| Resurs | Beskrivning |
| --- | --- |
| AzureClassicAutomationTutorial-runbook |Ett exempel på en grafisk runbook som hämtar alla klassiska virtuella datorer i en prenumeration med hjälp av det klassiska Kör som-kontot (certifikat) och som sedan returnerar den virtuella datorns namn och status. |
| AzureClassicAutomationTutorial Script-runbook |Ett exempel på en PowerShell-runbook som hämtar alla klassiska virtuella datorer i en prenumeration med hjälp av det klassiska Kör som-kontot (certifikat) och som sedan returnerar den virtuella datorns namn och status. |
| AzureClassicRunAsCertificate |Certifikattillgång som skapas automatiskt och som används för att autentisera med Azure så att du kan hantera klassiska Azure-resurser från runbooks.  Det här certifikatet har en livslängd på ett år. |
| AzureClassicRunAsConnection |Anslutningstillgång som skapas automatiskt och som används för att autentisera med Azure så att du kan hantera klassiska Azure-resurser från runbooks. |


## <a name="next-steps"></a>Nästa steg
* Läs mer om grafisk redigering i [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).
* Information om hur du kommer igång med PowerShell-runbooks finns i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md).
* Läs [My first Python2 runbook](automation-first-runbook-textual-python2.md) (Min första Python2-runbook) för att komma igång med Python2-runbooks.
