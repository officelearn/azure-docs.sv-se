---
title: Hantera ett Kör som-konto för Azure Automation
description: Den här artikeln beskriver hur du hanterar ditt kör som-konto med PowerShell eller från Azure Portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 06/26/2020
ms.topic: conceptual
ms.openlocfilehash: 66fb5741a5ed1af9e7edf002485c959f9f2fc82f
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2020
ms.locfileid: "85507270"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Hantera ett Kör som-konto för Azure Automation

Kör som-konton i Azure Automation tillhandahålla autentisering för att hantera resurser i Azure med hjälp av Azure-cmdletar. När du skapar ett Kör som-konto skapar det en ny tjänst huvud användare i Azure Active Directory (AD) och tilldelar rollen deltagare till den här användaren på prenumerations nivå.

## <a name="types-of-run-as-accounts"></a>Typer av kör som-konton

Azure Automation använder två typer av kör som-konton:

* Kör som-konto i Azure
* Det klassiska kör som-kontot i Azure

>[!NOTE]
>Azure Cloud Solution Provider (CSP)-prenumerationer stöder endast Azure Resource Managers modellen. Icke-Azure Resource Manager tjänster är inte tillgängliga i programmet. När du använder en CSP-prenumeration skapas inte det klassiska kör som-kontot i Azure, men kör som-kontot i Azure skapas. Mer information om CSP-prenumerationer finns i [tillgängliga tjänster i CSP-prenumerationer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

Tjänstens huvud namn för ett Kör som-konto har inte behörighet att läsa Azure AD som standard. Om du vill lägga till behörigheter för att läsa eller hantera Azure AD måste du bevilja behörighet för tjänstens huvud namn under API- **behörigheter**. Läs mer i [lägga till behörigheter för åtkomst till webb-API: er](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>Kör som-konto

Kör som-kontot hanterar resurser för [distributions modeller i Resource Manager](../azure-resource-manager/management/deployment-models.md) . Den utför följande uppgifter.

* Skapar ett Azure AD-program med ett självsignerat certifikat och ett tjänstobjektskonto för programmet i Azure AD och rollen Deltagare tilldelas för kontot i din aktuella prenumeration. Du kan ändra certifikat inställningen till ägare eller en annan roll. Mer information finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
  
* Skapar en Automation-certifikat till gång med namnet `AzureRunAsCertificate` i det angivna Automation-kontot. Certifikat till gången innehåller certifikatets privata nyckel som Azure AD-programmet använder.
  
* Skapar en Automation-anslutning till gång med namnet `AzureRunAsConnection` i det angivna Automation-kontot. Anslutnings till gången innehåller program-ID, klient-ID, prenumerations-ID och tumavtryck för certifikatet.

### <a name="azure-classic-run-as-account"></a>Klassiskt Kör som-konto i Azure

Det klassiska kör som-kontot i Azure hanterar klassiska resurser för [distributions modeller](../azure-resource-manager/management/deployment-models.md) . Du måste vara en medadministratör i prenumerationen för att kunna skapa eller förnya den här typen av konto.

Det klassiska kör som-kontot i Azure utför följande uppgifter.

  * Skapar ett hanterings certifikat i prenumerationen.

  * Skapar en Automation-certifikat till gång med namnet `AzureClassicRunAsCertificate` i det angivna Automation-kontot. Certifikattillgången innehåller den privata nyckelns certifikat som används av hanteringscertifikatet.

  * Skapar en Automation-anslutning till gång med namnet `AzureClassicRunAsConnection` i det angivna Automation-kontot. Anslutnings till gången innehåller prenumerations namnet, prenumerations-ID och certifikatets till gångs namn.

>[!NOTE]
>Det klassiska kör som-kontot i Azure skapas inte som standard på samma gång när du skapar ett Automation-konto. Det här kontot skapas individuellt enligt stegen som beskrivs längre fram i den här artikeln.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Hämta behörigheter för kör som-konto

I det här avsnittet definieras behörigheter för både vanliga kör som-konton och klassiska kör som-konton.

### <a name="get-permissions-to-configure-run-as-accounts"></a>Hämta behörigheter för att konfigurera kör som-konton

Om du vill skapa eller uppdatera ett Kör som-konto måste du ha vissa behörigheter och behörigheter. En program administratör i Azure Active Directory och en ägare i en prenumeration kan slutföra alla uppgifter. I en situation där du har separering av uppgifter visar följande tabell en lista över aktiviteter, motsvarande cmdlet och behörigheter som krävs:

|Uppgift|Cmdlet  |Lägsta behörighet  |Där du anger behörigheter|
|---|---------|---------|---|
|Skapa Azure AD-program|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Programutvecklare, roll<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registreringar |
|Lägg till en autentiseringsuppgift i programmet.|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Program administratör eller global administratör<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registreringar|
|Skapa och hämta en Azure AD-tjänstens huvud namn|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Program administratör eller global administratör<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registreringar|
|Tilldela eller hämta RBAC-rollen för det angivna huvudobjektet|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administratör för användar åtkomst eller ägare eller ha följande behörigheter:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Prenumeration](../role-based-access-control/role-assignments-portal.md)</br>Start > prenumerationer > \<subscription name\> -Access Control (IAM)|
|Skapa eller ta bort ett Automation-certifikat|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Deltagare i resurs gruppen         |Resurs grupp för Automation-konto|
|Skapa eller ta bort en Automation-anslutning|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Deltagare i resurs gruppen |Resurs grupp för Automation-konto|

<sup>1</sup> användare som inte är administratörer i din Azure AD-klient kan [registrera AD-program](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) om alternativet för Azure AD-klientens **användare kan registrera program** på sidan användar inställningar är inställt på **Ja**. Om program registrerings inställningen är **Nej**måste användaren som utför den här åtgärden vara som definieras i den här tabellen.

Om du inte är medlem i prenumerationens Active Directory instans innan du lägger till den globala administratörs rollen för prenumerationen läggs du till som gäst. I så fall får du en `You do not have permissions to create…` Varning på sidan **Lägg till Automation-konto** .

Om du är medlem i prenumerationens Active Directory instans när rollen global administratör tilldelas, kan du också få en `You do not have permissions to create…` Varning på sidan **Lägg till Automation-konto** . I så fall kan du begära borttagning från prenumerationens Active Directory instans och sedan begära att du lägger till den igen, så att du blir fullständig användare i Active Directory.

För att kontrol lera att den situation som genererar fel meddelandet har åtgärd ATS:

1. I fönstret Azure Active Directory i Azure Portal väljer du **användare och grupper**.
2. Välj **alla användare**.
3. Välj ditt namn och välj sedan **profil**. 
4. Se till att värdet för attributet **användar typ** under användarens profil inte är inställt på **gäst**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Hämta behörigheter för att konfigurera klassiska kör som-konton

Om du vill konfigurera eller förnya klassiska kör som-konton måste du ha rollen medadministratör på prenumerations nivå. Mer information om klassiska prenumerations behörigheter finns i [Azures klassiska prenumerations administratörer](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Skapa ett Kör som-konto i Azure Portal

Utför följande steg för att uppdatera ditt Azure Automation-konto i Azure Portal. Skapa Kör som-och klassiska kör som-konton individuellt. Om du inte behöver hantera klassiska resurser kan du bara skapa Azure Kör som-kontot.

1. Logga in på Azure-portalen med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.

2. Sök efter och välj **Automation-konton**.

3. På sidan Automation-konton väljer du ditt Automation-konto i listan.

4. I det vänstra fönstret väljer du **Kör som-konton** i avsnittet konto inställningar.

5. Beroende på vilket konto du behöver väljer du antingen **Azures Kör som-konto** eller **Azures klassiska Kör som-konto**. 

6. Beroende på kontots intresse använder du fönstret **Lägg till Azure kör som** eller **Lägg till Azures klassiska kör som-konto** . När du har granskat översikts informationen klickar du på **skapa**.

7. Medan Azure skapar Kör-som-kontot kan du följa förloppet under **Meddelanden** på menyn. En banderoll visas också som anger att kontot skapas. Processen kan ta några minuter att slutföra.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto

I det här avsnittet beskrivs hur du tar bort ett Kör som-konto eller ett klassiskt kör som-konto. När du utför den här åtgärden behålls Automation-kontot. När du har tagit bort kontot kan du återskapa det i Azure Portal.

1. Öppna ditt Automation-konto på Azure Portal.

2. I det vänstra fönstret väljer du **Kör som-konton** i avsnittet konto inställningar.

3. På egenskapssidan för Kör som-konton väljer du antingen Kör som-kontot eller det klassiska Kör som-kontot som du vill ta bort. 

4. I rutan Egenskaper för det valda kontot klickar du på **ta bort**.

   ![Ta bort Kör som-konto](media/manage-runas-account/automation-account-delete-runas.png)

5. Medan kontot tas bort kan du följa förloppet under **Meddelanden** på menyn.

6. När kontot har tagits bort måste du återskapa det på egenskapssidan för Kör som-konton genom att välja alternativet Skapa för **Kör som-konto i Azure**.

   ![Återskapa Kör som-kontot för Automation](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Förnya ett självsignerat certifikat

Det självsignerade certifikatet som du har skapat för kör som-kontot går ut ett år från datumet då det skapades. Innan ditt kör som-konto går ut måste du förnya certifikatet. Du kan förnya det när som helst innan det upphör att gälla. 

När du förnyar det självsignerade certifikatet behålls det aktuella giltiga certifikatet för att säkerställa att alla Runbooks som köas eller aktivt körs, och som autentiserar med kör som-kontot, inte påverkas negativt. Certifikatet förblir giltigt fram till dess förfallodatum.

>[!NOTE]
>Om du tror att kör som-kontot har komprometterats kan du ta bort och återskapa det självsignerade certifikatet.

>[!NOTE]
>Om du har konfigurerat ditt kör som-konto för att använda ett certifikat som utfärdats av din företags certifikat utfärdare och du använder alternativet för att förnya ett självsignerat certifikat, ersätts företags certifikatet av ett självsignerat certifikat.

Använd följande steg för att förnya det självsignerade certifikatet.

1. Öppna ditt Automation-konto på Azure Portal.

1. Välj **Kör som-konton** i avsnittet konto inställningar.

    ![Egenskapsrutan för Automation-konto](media/manage-runas-account/automation-account-properties-pane.png)

1. På sidan Egenskaper för kör som-konton väljer du antingen kör som-kontot eller det klassiska kör som-kontot som du vill förnya certifikatet för.

1. I rutan Egenskaper för det valda kontot klickar du på **Förnya certifikat**.

    ![Förnya certifikat för Kör som-konto](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Medan certifikatet förnyas kan du följa förloppet under **Meddelanden** på menyn.

## <a name="limit-run-as-account-permissions"></a>Begränsa behörigheter för kör som-konto

Om du vill styra automatiseringen av automatisering mot resurser i Azure kan du köra [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) -skriptet. Det här skriptet ändrar ditt befintliga huvud namn för kör som-kontot för att skapa och använda en anpassad roll definition. Rollen har behörigheter för alla resurser utom [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>När du har kört **Update-AutomationRunAsAccountRoleAssignments.ps1** skriptet fungerar inte Runbooks som har åtkomst Key Vault genom användningen av kör som-konton. Innan du kör skriptet bör du granska Runbooks i ditt konto för anrop till Azure Key Vault. Om du vill ge åtkomst till Key Vault från Azure Automation runbooks måste du [lägga till kör som-kontot i Key Vault behörigheter](#add-permissions-to-key-vault).

Om du behöver begränsa ytterligare vad som kan utföras av tjänstens huvud namn kan du lägga till andra resurs typer till `NotActions` elementet i den anpassade roll definitionen. I följande exempel begränsas åtkomsten till `Microsoft.Compute/*` . Om du lägger till den här resurs typen i `NotActions` för roll definitionen kommer rollen inte att kunna komma åt någon beräknings resurs. Mer information om roll definitioner finns i [förstå roll definitioner för Azure-resurser](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Du kan kontrol lera om tjänstens huvud namn som används av ditt kör som-konto finns i roll definitionen medarbetare eller en anpassad. 

1. Gå till ditt Automation-konto och välj **Kör som-konton** i avsnittet konto inställningar.
2. Välj **Kör som-konto i Azure**. 
3. Välj **roll** för att hitta den roll definition som används.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Du kan också bestämma vilken roll definition som används av kör som-kontona för flera prenumerationer eller Automation-konton. Gör detta med hjälp av [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) -skriptet i PowerShell-galleriet.

### <a name="add-permissions-to-key-vault"></a>Lägg till behörigheter i Key Vault

Du kan låta Azure Automation verifiera om Key Vault och ditt kör som-konto-tjänstens huvud namn använder en anpassad roll definition. Du måste:

* Bevilja behörighet till Key Vault.
* Ange åtkomst principen.

Du kan använda [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) -skriptet i PowerShell-galleriet för att ge ditt kör som-konto behörighet att Key Vault. Mer information om hur du anger behörigheter för Key Vault finns i [bevilja program åtkomst till ett nyckel valv](../key-vault/general/group-permissions-for-apps.md) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Lös problem med fel konfiguration för kör som-konton

Vissa konfigurations objekt som krävs för ett Kör som-eller klassiskt kör som-konto kan ha tagits bort eller skapats felaktigt under den första installationen. Möjliga instanser av felaktig konfiguration är:

* Certifikattillgång
* Anslutningstillgång
* Kör som-konto har tagits bort från deltagar rollen
* Huvudnamn för tjänsten eller program i Azure AD

För sådana felkonfigurations instanser identifierar Automation-kontot ändringarna och visar statusen *ofullständig* i fönstret Egenskaper för kör som-konton för kontot.

![Konfigurationsstatusen Ofullständig för Kör som-konto](media/manage-runas-account/automation-account-runas-incomplete-config.png)

När du väljer Kör som-kontot visas följande fel meddelande i fönstret konto egenskaper:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Du kan snabbt lösa dessa problem med Kör som-kontot genom att ta bort och återskapa kontot.

## <a name="next-steps"></a>Nästa steg

* [Program objekt och tjänst huvud objekt](../active-directory/develop/app-objects-and-service-principals.md).
* [Översikt över certifikat för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
