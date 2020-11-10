---
title: Självstudie – webbappen får åtkomst till Microsoft Graph som appen | Azure
description: I den här självstudien får du lära dig hur du kommer åt data i Microsoft Graph att använda hanterade identiteter.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: 70b180efa35d6310735f045a85103719b17c8555
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428960"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Självstudie: åtkomst Microsoft Graph från en säker app som appen

Lär dig hur du kommer åt Microsoft Graph från en webbapp som körs på Azure App Service.

:::image type="content" alt-text="Åtkomst Microsoft Graph" source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Du vill anropa Microsoft Graph för webbappens räkning.  Ett säkert sätt att ge din webbapp åtkomst till data är att använda en [systemtilldelad hanterad identitet](/azure/active-directory/managed-identities-azure-resources/overview). En hanterad identitet från Azure AD ger App Services åtkomst till resurser via Role-Based Access Control (RBAC), utan att kräva autentiseringsuppgifter för appen. När du har tilldelat en hanterad identitet till din webbapp tar Azure hand om skapandet och distributionen av ett certifikat.  Du behöver inte bekymra dig om att hantera autentiseringsuppgifter för hemligheter eller appar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en systemtilldelad hanterad identitet i en webbapp
> * Lägg till Microsoft Graph API-behörigheter till en hanterad identitet
> * Anropa Microsoft Graph från en webbapp med hanterade identiteter

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Ett webb program som körs på Azure App Service som har [modulen App Service autentisering/auktorisering aktive rad](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-app"></a>Aktivera hanterad identitet i appen

Om du skapar och publicerar din webbapp via Visual Studio har den hanterade identiteten Aktiver ATS i din app. I App Service väljer du **identitet** i det vänstra navigerings fönstret och sedan **system tilldelad**.  Kontrol lera att **status** har angetts till **på**.  Annars klickar du på **Spara** och sedan **Ja** för att aktivera systemtilldelad hanterad identitet.  När den hanterade identiteten är aktive rad är statusen inställd på *på* och objekt-ID är tillgängligt.

Anteckna **objekt-ID: t** som du behöver i nästa steg.

:::image type="content" alt-text="Tilldelad identitet" source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Bevilja åtkomst till Microsoft Graph

Vid åtkomst till Microsoft Graph måste den hanterade identiteten ha rätt behörigheter för den åtgärd som den vill utföra. För närvarande finns det inget alternativ för att tilldela sådana behörigheter via Azure Portal. Följande skript lägger till begärda Microsoft Graph API-behörigheter till det hanterade identitets tjänstens huvud objekt:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module (You need admin on the machine)
#Install-Module AzureAD 

# Your tenant id (in Azure Portal, under Azure Active Directory -> Overview )
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get ID of the managed identity for the web app
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph"

# Assign permissions to managed identity service principal
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spID/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

När skriptet har körts kan du kontrol lera i [Azure Portal](https://portal.azure.com) att de begärda API-behörigheterna har tilldelats till den hanterade identiteten.  Gå till **Azure Active Directory** och välj **företags program**.  Det här bladet visar alla tjänst huvud namn i din klient organisation.  I **alla program** väljer du tjänstens huvud namn för den hanterade identiteten.  Om du följer den här självstudien finns det två tjänst huvud namn med samma visnings namn ("SecureWebApp2020094113531").  Tjänstens huvud namn som har en *Start sidas-URL* representerar webbappen i din klient organisation.  Tjänstens huvud namn utan *Start sidans URL* representerar systemtilldelad hanterad identitet för din webbapp. Objekt-ID: t för den hanterade identiteten matchar objekt-ID: t för den hanterade identitet som du skapade tidigare.  

Välj tjänstens huvud namn för den hanterade identiteten.

:::image type="content" alt-text="Alla program" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

I **Översikt** väljer du **behörigheter** så visas ytterligare behörigheter för Microsoft Graph.

:::image type="content" alt-text="Behörigheter" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Anrops Microsoft Graph (.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) -klassen används för att hämta en token-autentiseringsuppgifter för din kod för att godkänna begär anden till Azure Storage.  Skapa en instans av klassen [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) , som använder den hanterade identiteten för att hämta tokens och bifoga dem till tjänst klienten. I följande kod exempel hämtas autentiseringsuppgifter för autentiserad token och används för att skapa ett tjänst klient objekt, som hämtar användarna i gruppen.  

### <a name="install-microsoftgraph-client-library-package"></a>Installera klient biblioteks paketet Microsoft. Graph

Installera [Microsoft. Graph NuGet-paketet](https://www.nuget.org/packages/Microsoft.Graph) i projektet med .net Core kommando rads gränssnitt eller Package Manager-konsolen i Visual Studio.

# <a name="command-line"></a>[Kommando rad](#tab/command-line)

Öppna en kommando rad och växla till den katalog som innehåller projekt filen.

Kör installations kommandona:

```dotnetcli
dotnet add package Microsoft.Graph
```

# <a name="package-manager"></a>[Paket hanterare](#tab/package-manager)

Öppna-projektet/lösningen i Visual Studio och öppna konsolen med **verktyg**  >  **NuGet Package Manager**  >  **Package Manager Console** kommando.

Kör installations kommandona:
```powershell
Install-Package Microsoft.Graph
```

---

### <a name="example"></a>Exempel

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Graph service client with a DefaultAzureCredential which gets an access token using the available Managed Identity
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med den här själv studie kursen och inte längre behöver webbappen eller tillhör ande resurser [rensar du de resurser som du har skapat](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Skapa en systemtilldelad hanterad identitet i en webbapp
> * Lägg till Microsoft Graph API-behörigheter till en hanterad identitet
> * Anropa Microsoft Graph från en webbapp med hanterade identiteter

Lär dig hur du ansluter en [.net Core-app](tutorial-dotnetcore-sqldb-app.md), [python-app](tutorial-python-postgresql-app.md), [java-app](tutorial-java-spring-cosmosdb.md)eller [Node.js app](tutorial-nodejs-mongodb-app.md) till en databas.