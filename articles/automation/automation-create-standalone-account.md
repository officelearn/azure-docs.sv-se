---
title: "Skapa ett fristående Azure Automation-konto | Microsoft Docs"
description: "Självstudie som steg för steg beskriver hur du skapar, testar och använder autentisering med säkerhetsobjekt i Azure Automation."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 97b863748dd726e19217e360645b8e6189c010b3
ms.lasthandoff: 04/15/2017

---

# <a name="create-a-standalone-azure-automation-account"></a>Skapa ett fristående Azure Automation-konto
I det här avsnittet beskrivs hur du skapar ett Automation-konto på Azure Portal om du vill utvärdera och lära dig mer om Azure Automation utan de ytterligare hanteringslösningarna eller integreringen med OMS Log Analytics för avancerad övervakning av runbook-jobb.  Du kan när som helst lägga till de här hanteringslösningarna eller integrera med Log Analytics senare.  Med Automation-kontot kan du autentisera runbookflöden som hanterar resurser i Azure Resource Manager eller klassiska Azure.

När du skapar ett Automation-konto på Azure Portal skapas följande automatiskt:

* Kör som-kontot som skapar ett nytt namn för tjänstobjektet i Azure Active Directory och ett certifikat samt tilldelar rollen som deltagare med rollbaserad åtkomstkontroll (RBAC), som används för att hantera Resource Manager-resurser med hjälp av runbooks.   
* Ett klassiskt Kör som-konto genom att överföra ett hanteringscertifikat, som används för att hantera klassiska resurser med hjälp av runbooks.  

Detta gör processen enklare för dig och hjälper dig att snabbt börja skapa och distribuera runbooks för dina automatiseringsbehov.  

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Skapa ett nytt Automation-konto från Azure Portal
I det här avsnittet ska du utföra följande steg för att skapa ett Azure Automation-konto från Azure Portal.    

>[!NOTE]
>För att kunna skapa ett Automation-konto måste du vara medlem i rollen Tjänstadministratörer eller vara medadministratör för den prenumeration som ger åtkomst till prenumerationen. Du måste också läggas till som användare i prenumerationens Active Directory-standardinstans. Kontot behöver inte tilldelas en privilegierad roll.
>
>Om du inte är medlem i prenumerationens Active Directory-instans innan du läggs till i rollen som medadministratör för prenumerationen läggs du till i Active Directory som gäst. I så fall visas varningen ”Du har inte behörighet att skapa ...” på bladet **Lägg till Automation-konto**.
>
>Användare som har lagts till i rollen som medadministratör kan först tas bort från prenumerationens Active Directory-instans och sedan läggas till igen så att de blir fullständiga användare i Active Directory. Du kan kontrollera detta i rutan **Azure Active Directory** på Azure Portal genom att välja **Användare och grupper**, välja **Alla användare**, välja den specifika användaren och sedan välja **Profil**. Värdet för attributet **Användartyp** under användarens profil bör inte vara lika med **Gäst**.

1. Logga in på Azure Portal med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
2. Klicka på **Ny**.<br><br> ![Välj alternativet Ny på Azure Portal](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Sök efter **Automation** och välj sedan **Automatisering och kontroll*** i sökresultaten.<br><br> ![Sök efter och välj Automation från Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. Klicka på **Lägg till** på bladet Automation-konton.<br><br>![Lägga till ett Automation-konto](media/automation-create-standalone-account/automation-create-automationacct-properties.png)
   
   > [!NOTE]
   > Om du ser i följande varning i bladet **Lägga till ett Automation-konto** beror det på att ditt konto inte är medlem i rollerna administratör eller medadministratör för prenumerationen.<br><br>![Varningsmeddelande för Lägga till ett Automation-konto](media/automation-create-standalone-account/create-account-without-perms.png)
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
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i Min första PowerShell-arbetsflödesbaserade runbook (automation-first-runbook-textual.md).
