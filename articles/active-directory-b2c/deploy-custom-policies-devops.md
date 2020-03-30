---
title: Distribuera anpassade principer med Azure Pipelines
titleSuffix: Azure AD B2C
description: Lär dig hur du distribuerar azure AD B2C-anpassade principer i en CI/CD-pipeline med hjälp av Azure Pipelines i Azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188757"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Distribuera anpassade principer med Azure Pipelines

Genom att använda en pipeline för kontinuerlig integrering och leverans (CI/CD) som du konfigurerar i [Azure Pipelines][devops-pipelines]kan du inkludera dina anpassade Azure AD B2C-principer i automatiseringen av programvaruleverans och kodkontroll. När du distribuerar till olika Azure AD B2C-miljöer, till exempel utveckling, testning och produktion, rekommenderar vi att du tar bort manuella processer och utför automatiserade tester med hjälp av Azure Pipelines.

Det finns tre primära steg som krävs för att aktivera Azure Pipelines för att hantera anpassade principer i Azure AD B2C:

1. Skapa en webbprogramregistrering i din Azure AD B2C-klient
1. Konfigurera en Azure Repo
1. Konfigurera en Azure Pipeline

> [!IMPORTANT]
> Hantera Azure AD B2C-anpassade principer med en Azure Pipeline `/beta` använder för närvarande **förhandsgranskningsåtgärder** som är tillgängliga på slutpunkten för Microsoft Graph API. Användning av dessa API:er i produktionsprogram stöds inte. Mer information finns i [betaslutpunktsreferensen för Microsoft Graph REST API](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Krav

* [Azure AD B2C-klient och](tutorial-create-tenant.md)autentiseringsuppgifter för en användare i katalogen med rollen [B2C IEF-principadministratör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Anpassade principer](custom-policy-get-started.md) som överförs till din klientorganisation
* [Hanteringsapp](microsoft-graph-get-started.md) registrerad i din klientorganisation med microsoft graph *API-behörighetsprincipen.ReadWrite.TrustFramework*
* [Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/)och åtkomst till ett [Azure DevOps Services-projekt][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Tilldelningsflöde för klientuppgifter

Scenariot som beskrivs här använder sig av service-to-service-anrop mellan Azure Pipelines och Azure AD B2C med hjälp av [tilldelningsflödet](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)för OAuth 2.0-klientautentiseringsuppgifter . Med det här beviljandeflödet kan en webbtjänst som Azure Pipelines (den konfidentiella klienten) använda sina egna autentiseringsuppgifter i stället för att personifiera en användare för att autentisera när en annan webbtjänst anropas (Microsoft Graph API, i det här fallet). Azure Pipelines hämtar en token som inte är interaktivt och gör sedan begäranden till Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Registrera ett program för hanteringsuppgifter

Som [nämnts](#prerequisites)i Förutsättningar behöver du en programregistrering som dina PowerShell-skript - som körs av Azure Pipelines - kan använda för att komma åt resurserna i din klientorganisation.

Om du redan har en programregistrering som du använder för automatiseringsuppgifter kontrollerar du att den har beviljats behörigheten **Microsoft Graph** > **Policy.ReadWrite.TrustFramework** **Policy** > i **API-behörigheterna** för appregistreringen.

Instruktioner om hur du registrerar ett hanteringsprogram finns i [Hantera Azure AD B2C med Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Konfigurera en Azure Repo

När ett hanteringsprogram har registrerats är du redo att konfigurera en databas för dina principfiler.

1. Logga in på din Azure DevOps Services-organisation.
1. [Skapa ett nytt projekt][devops-create-project] eller välj ett befintligt projekt.
1. I projektet navigerar du till **Repos** och väljer sidan **Filer.** Välj en befintlig databas eller skapa en för den här övningen.
1. Skapa en mapp med namnet *B2CAssets*. Ge den platshållarfil som krävs *README.md* och **arkivera** filen. Du kan ta bort den här filen senare, om du vill.
1. Lägg till dina Azure AD B2C-principfiler i *mappen B2CAssets.* Detta inkluderar *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*och alla andra principer som du har skapat. Registrera filnamnet för varje Azure AD B2C-principfil för användning i ett senare steg (de används som PowerShell-skriptargument).
1. Skapa en mapp med namnet *Skript* i rotkatalogen i databasen, namnge platshållarfilen *DeployToB2c.ps1*. Gör inte filen i det här läget, du gör det i ett senare steg.
1. Klistra in följande PowerShell-skript i *DeployToB2c.ps1*och **beskydda** sedan filen. Skriptet hämtar en token från Azure AD och anropar Microsoft Graph API för att överföra principerna i *mappen B2CAssets* till din Azure AD B2C-klient.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Konfigurera din Azure-pipeline

När databasen har initierats och fyllts i med dina anpassade principfiler är du redo att konfigurera versionspipelinen.

### <a name="create-pipeline"></a>Skapa pipeline

1. Logga in på din Azure DevOps Services-organisation och navigera till ditt projekt.
1. Välj **Pipelines** > **Releases** > **New pipeline**i projektet .
1. Under **Välj en mall**väljer du Tomt **jobb**.
1. Ange ett **scennamn**, till exempel *DeployCustomPolicies*och stäng sedan fönstret.
1. Välj **Lägg till en artefakt**och välj Azure **Repository**under **Källtyp**.
    1. Välj källdatabasen som innehåller mappen *Skript* som du har fyllt i med PowerShell-skriptet.
    1. Välj en **standardgren**. Om du har skapat en ny databas i föregående avsnitt är standardgrenen *huvud.*
    1. Lämna **standardversionsinställningen** *för Senaste från standardgrenen*.
    1. Ange ett **källalias** för databasen. Till exempel *policyRepo*. Ta inte med några blanksteg i aliasnamnet.
1. Välj **Lägg till**
1. Byt namn på pipelinen så att den återspeglar dess avsikt. Distribuera till exempel *anpassad princippipeline*.
1. Välj **Spara** om du vill spara pipelinekonfigurationen.

### <a name="configure-pipeline-variables"></a>Konfigurera pipelinevariabler

1. Välj fliken **Variabler.**
1. Lägg till följande variabler under **Pipeline-variabler** och ange deras värden enligt angiven:

    | Namn | Värde |
    | ---- | ----- |
    | `clientId` | **Ansökan (klient) ID** för det program som du registrerade tidigare. |
    | `clientSecret` | Värdet på **klienthemligheten** som du skapade tidigare. <br /> Ändra variabeltypen till **hemlig** (välj låsikonen). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, där *din-b2c-klient är* namnet på din Azure AD B2C-klientorganisation. |

1. Välj **Spara** om du vill spara variablerna.

### <a name="add-pipeline-tasks"></a>Lägga till pipeline-aktiviteter

Lägg sedan till en uppgift för att distribuera en principfil.

1. Markera fliken **Uppgifter.**
1. Välj **Agent-jobb**och välj sedan**+** plustecknet ( ) om du vill lägga till en aktivitet i agentjobbet.
1. Sök efter och välj **PowerShell**. Välj inte "Azure PowerShell", "PowerShell på måldatorer" eller en annan PowerShell-post.
1. Välj nyligen tillagd **PowerShell-skriptuppgift.**
1. Ange följande värden för PowerShell-skriptaktiviteten:
    * **Uppgiftsversion**: 2.*
    * **Visningsnamn**: Namnet på den princip som den här uppgiften ska överföra. Till exempel *B2C_1A_TrustFrameworkBase*.
    * **Typ**: Sökväg till fil
    * **Skriptsökväg:** Markera ellipsen (***...***), navigera till *mappen Skript* och välj sedan filen *DeployToB2C.ps1.*
    * **Argument:**

        Ange följande värden för **Argument**. Ersätt `{alias-name}` med det alias som du angav i föregående avsnitt.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Om det alias du angav till exempel är *policyRepo*ska argumentraden vara:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Välj **Spara** om du vill spara agentjobbet.

Uppgiften som du just har lagt till laddar upp *en* principfil i Azure AD B2C. Innan du fortsätter utlöser du jobbet manuellt (**Skapa frisättning**) för att säkerställa att det slutförs innan du skapar ytterligare uppgifter.

Om uppgiften har slutförts lägger du till distributionsuppgifter genom att utföra föregående steg för var och en av de anpassade principfilerna. Ändra `-PolicyId` argumentvärdena och `-PathToFile` argumenten för varje princip.

Värdet `PolicyId` finns i början av en XML-principfil i trustframeworkpolicy-noden. I följande `PolicyId` princip-XML finns till exempel *B2C_1A_TrustFrameworkBase:*

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

När du kör agenterna och laddar upp principfilerna ska du se till att de laddas upp i den här ordningen:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *Registrera Dig Själv tillSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Identity Experience Framework tillämpar den här ordningen eftersom filstrukturen bygger på en hierarkisk kedja.

## <a name="test-your-pipeline"></a>Testa din pipeline

Så här testar du versionspipelinen:

1. Välj **Pipelines** och **sedan Släpper**.
1. Välj den pipeline som du skapade tidigare, till exempel *DeployCustomPolicies*.
1. Välj **Skapa version**och välj sedan **Skapa** för att köa versionen.

Du bör se en meddelande banner som säger att en release har köats. Om du vill visa dess status markerar du länken i meddelandebanderollen eller markerar den i listan på fliken **Versioner.**

## <a name="next-steps"></a>Nästa steg

Läs mer om:

* [Service-till-tjänst-samtal med hjälp av klientuppgifter](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
