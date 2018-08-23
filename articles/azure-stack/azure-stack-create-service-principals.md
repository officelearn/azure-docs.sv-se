---
title: Skapa ett tjänstobjekt för Azure Stack | Microsoft Docs
description: Beskriver hur du skapar ett nytt huvudnamn för tjänsten som kan användas med rollbaserad åtkomstkontroll i Azure Resource Manager för att hantera åtkomst till resurser.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: sethm
ms.openlocfilehash: f7233d6a27b9ec3d58f33f7032bbec7a646d24f7
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42366127"
---
# <a name="provide-applications-access-to-azure-stack"></a>Ge programåtkomst till Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

När ett program behöver tillgång till distribuera eller konfigurera resurser via Azure Resource Manager i Azure Stack, kan du skapa ett huvudnamn för tjänsten, vilket är en autentiseringsuppgift för ditt program.  Du kan sedan delegera endast behörighet att tjänstobjektet.  

Du kan exempelvis ha ett hanteringsverktyg för konfiguration som använder Azure Resource Manager för att inventera Azure-resurser.  I det här scenariot du skapar ett huvudnamn för tjänsten, ge läsarrollen till tjänstobjektet och begränsa verktyget configuration management till skrivskyddad åtkomst. 

Tjänstens huvudnamn är bättre att köra appen enligt dina autentiseringsuppgifter eftersom:

* Du kan tilldela behörigheter till tjänstens huvudnamn som skiljer sig från dina egna kontobehörigheter. Vanligen är dessa behörigheter begränsade till exakt vad appen behöver göra.
* Du behöver inte ändra dess autentiseringsuppgifter ändrar dina ansvarsområden.
* Du kan använda ett certifikat för att automatisera autentisering när du kör ett oövervakat skript.  

## <a name="getting-started"></a>Komma igång

Beroende på hur du har distribuerat Azure Stack kan börja du genom att skapa ett tjänstens huvudnamn.  Det här dokumentet hjälper dig att skapa ett huvudnamn för tjänsten för både [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) och [Active Directory Federation Services(AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  När du har skapat tjänstens huvudnamn, en uppsättning steg som är gemensamma för både AD FS och Azure Active Directory är vana vid [delegera behörigheter](azure-stack-create-service-principals.md#assign-role-to-service-principal) till rollen.     

## <a name="create-service-principal-for-azure-ad"></a>Skapa tjänstens huvudnamn för Azure AD

Om du har distribuerat Azure Stack med Azure AD som Identitetslagret kan skapa du tjänstens huvudnamn precis som du gör för Azure.  Det här avsnittet visar hur du utför stegen via portalen.  Kontrollera att du har den [Azure AD-behörigheter som krävs för](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) innan du börjar.

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn
I det här avsnittet skapar du ett program (tjänstens huvudnamn) i Azure AD som representerar ditt program.

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory** > **appregistreringar** > **Lägg till**   
3. Ange ett namn och en URL för programmet. Välj antingen **webbapp / API** eller **interna** för typ av program som du vill skapa. När du har angett värdena, Välj **skapa**.

Du har skapat ett huvudnamn för tjänsten för ditt program.

### <a name="get-credentials"></a>Hämta autentiseringsuppgifter
När du loggar in, använder ID: T för ditt program och för en webbapp / API, en autentiseringsnyckel. Hämta dessa värden med följande steg:

1. Från **appregistreringar** i Active Directory, Välj ditt program.

2. Kopiera **Program-ID:t** och lagra det i din programkod. Program i den [programexempel](#sample-applications) avsnittet avser det här värdet som klient-ID.

     ![klient-ID](./media/azure-stack-create-service-principal/image12.png)
3. Generera en autentiseringsnyckel för en webbapp / API, Välj **inställningar** > **nycklar**. 

4. Tillhandahåll beskrivning av och varaktighet för nyckeln. Välj **Spara** när du är klar.

När du har sparat nyckeln visas nyckelns värde. Kopiera det här värdet eftersom det inte går att hämta nyckeln senare. Du kan ange nyckelvärdet med program-ID för att logga in som programmet. Lagra nyckelvärdet där programmet kan hämta det.

![sparad nyckel](./media/azure-stack-create-service-principal/image15.png)


När du är klar fortsätter du till [tilldela en roll för ditt program](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Skapa tjänstens huvudnamn för AD FS
Om du har distribuerat Azure Stack med AD FS, kan du använda PowerShell för att skapa ett huvudnamn för tjänsten, tilldela en roll för åtkomst och logga in från PowerShell med hjälp av den identiteten.

Skriptet körs från den privilegierade slutpunkten på en ERCS-dator.


Krav:
- Det krävs ett certifikat.

**Parametrar**

Följande information måste anges som indata för automation-parametrar:


|Parameter|Beskrivning|Exempel|
|---------|---------|---------|
|Namn|Namnet på kontot SPN|MyAPP|
|ClientCertificates|Matris med objekt för certifikat|X509 certifikat|
|ClientRedirectUris<br>(Valfritt)|Programmet omdirigerings-URI|         |

**Exempel**

1. Öppna en upphöjd Windows PowerShell-session och kör följande kommandon:

   > [!NOTE]
   > Det här exemplet skapar ett självsignerat certifikat. När du kör dessa kommandon i en Produktionsdistribution, använder du Get-certifikat för att hämta certifikatobjektet för det certifikatet som du vill använda.

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint typically domain\cloudadmin
    $creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

    # This produces a self signed cert for testing purposes.  It is prefered to use a managed certificate for this.
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

    $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name '<yourappname>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }
    $session|remove-pssession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "azurestackuser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```

2. När automation är klar visas informationen som krävs för att använda tjänstens Huvudnamn. 

   Exempel:

   ```
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="assign-a-role"></a>Tilldela en roll
När tjänstens huvudnamn har skapats måste du [tilldela den till en roll](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>Logga in via PowerShell
När du har tilldelat en roll, kan du logga in på Azure Stack med tjänstens huvudnamn med följande kommando:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ClientId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Tilldela roll till tjänstens huvudnamn
För att komma åt resurser i din prenumeration, måste du tilldela programmet till en roll. Bestäm vilken roll representerar rätt behörigheter för programmet. Läs om tillgängliga roller i [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md).

Du kan ange omfånget för den prenumerationen, resursgruppen eller resursen. Behörigheter ärvs till lägre nivåer av omfång. Till exempel lägga till ett program till rollen Läsare för en resursgrupp innebär att den kan läsa resursgruppen och alla resurser som den innehåller.

1. Navigera till den nivå av omfång som du vill tilldela programmet till i Azure Stack-portalen. Om du vill tilldela en roll prenumerationsområde, väljer du exempelvis **prenumerationer**. Du kan i stället välja en resursgrupp eller resurs.

2. Välj den specifika prenumerationen (resursgrupp eller resurs) för att tilldela programmet till.

     ![Välj prenumeration för tilldelning](./media/azure-stack-create-service-principal/image16.png)

3. Välj **åtkomstkontroll (IAM)**.

     ![Välj åtkomst](./media/azure-stack-create-service-principal/image17.png)

4. Välj **Lägg till**.

5. Välj den roll som du vill tilldela till programmet.

6. Sök efter ditt program och markera den.

7. Välj **OK** Slutför tilldela rollen. Du ser ditt program i listan över användare som har tilldelats en roll för detta omfång.

Nu när du har skapat ett tjänstobjekt och tilldelas en roll, kan du börja använda det i ditt program till Azure Stack-resurser.  

## <a name="next-steps"></a>Nästa steg

[Lägga till användare för AD FS](azure-stack-add-users-adfs.md)
[Hantera användarbehörigheter](azure-stack-manage-permissions.md)
