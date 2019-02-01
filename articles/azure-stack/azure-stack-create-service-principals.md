---
title: Hantera ett huvudnamn för tjänsten för Azure Stack | Microsoft Docs
description: Beskriver hur du hanterar en nytt huvudnamn för tjänsten som kan användas med rollbaserad åtkomstkontroll i Azure Resource Manager för att hantera åtkomst till resurser.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: sethm
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 5ff2ee3ed271d8c32e2d41f40a56f71aa4c6c67c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245277"
---
# <a name="provide-applications-access-to-azure-stack"></a>Ge programåtkomst till Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

När ett program behöver tillgång till distribuera eller konfigurera resurser via Azure Resource Manager i Azure Stack, kan du skapa ett huvudnamn för tjänsten, vilket är en autentiseringsuppgift för ditt program. Du kan sedan delegera endast behörighet att tjänstobjektet.  

Du kan exempelvis ha ett hanteringsverktyg för konfiguration som använder Azure Resource Manager för att inventera Azure-resurser. I det här scenariot du skapar ett huvudnamn för tjänsten, ge läsarrollen till tjänstobjektet och begränsa verktyget configuration management till skrivskyddad åtkomst. 

Tjänstens huvudnamn är bättre att köra appen enligt dina autentiseringsuppgifter eftersom:

 - Du kan tilldela behörigheter till tjänstens huvudnamn som skiljer sig från dina egna kontobehörigheter. Vanligen är dessa behörigheter begränsade till exakt vad appen behöver göra.
 - Du behöver inte ändra dess autentiseringsuppgifter ändrar dina ansvarsområden.
 - Du kan använda ett certifikat för att automatisera autentisering när du kör ett oövervakat skript.  

## <a name="getting-started"></a>Komma igång

Beroende på hur du har distribuerat Azure Stack kan börja du genom att skapa ett tjänstens huvudnamn. Det här dokumentet beskrivs hur du skapar en tjänst huvudnamn för:

- [Azure Active Directory (Azure AD)](#create-service-principal-for-azure-ad). Azure AD är en flera innehavare, molnbaserad katalog och identity management-tjänsten. Du kan använda Azure AD med en ansluten Azure Stack.
- [Active Directory Federation Services (AD FS)](#create-service-principal-for-ad-fs). AD FS tillhandahåller förenklad, säkrad identitetsfederering och funktioner för webb-enkel inloggning (SSO). Du kan använda AD FS med både ansluten och frånkopplade Azure Stack-instanser.

När du har skapat tjänstens huvudnamn, en uppsättning steg som är gemensamma för både AD FS och Azure Active Directory är vana vid [delegera behörigheter](#assign-role-to-service-principal) till rollen.

## <a name="manage-service-principal-for-azure-ad"></a>Hantera tjänstens huvudnamn för Azure AD

Om du har distribuerat Azure Stack med Azure Active Directory (Azure AD) som identity management-tjänsten, kan du skapa tjänstens huvudnamn precis som du gör för Azure. Det här avsnittet visar hur du utför stegen via portalen. Kontrollera att du har den [Azure AD-behörigheter som krävs för](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) innan du börjar.

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

I det här avsnittet skapar du ett program (tjänstens huvudnamn) i Azure AD som representerar ditt program.

1. Logga in på ditt Azure-konto via den [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory** > **appregistreringar** > **ny programregistrering**
3. Ange ett namn och en URL för programmet. Välj antingen **webbapp / API** eller **interna** för typ av program som du vill skapa. När du har angett värdena, Välj **skapa**.

Du har skapat ett huvudnamn för tjänsten för ditt program.

### <a name="get-credentials"></a>Hämta autentiseringsuppgifter

När du loggar in, använder ID: T för ditt program och för en webbapp / API, en autentiseringsnyckel. Hämta dessa värden med följande steg:

1. Från **appregistreringar** i Active Directory, Välj ditt program.

2. Kopiera **Program-ID:t** och lagra det i din programkod. Program i den [programexempel](#sample-applications) avsnittet avser det här värdet som klient-ID.

     ![Klient-ID](./media/azure-stack-create-service-principal/image12.png)
3. Generera en autentiseringsnyckel för en webbapp / API, Välj **inställningar** > **nycklar**. 

4. Tillhandahåll beskrivning av och varaktighet för nyckeln. Välj **Spara** när du är klar.

När du har sparat nyckeln visas nyckelns värde. Kopiera det här värdet i anteckningar eller på en tillfällig plats, eftersom du inte kan hämta nyckeln senare. Du kan ange nyckelvärdet med program-ID för att logga in som programmet. Store nyckelvärdet på en plats där programmet kan hämta den.

![sparad nyckel](./media/azure-stack-create-service-principal/image15.png)

När du är klar kan du [tilldela en roll för ditt program](#assign-role-to-service-principal).

## <a name="manage-service-principal-for-ad-fs"></a>Hantera tjänstens huvudnamn för AD FS

Om du har distribuerat Azure Stack med Active Directory Federation Services (AD FS) som identity management-tjänsten, kan du använda PowerShell för att skapa ett huvudnamn för tjänsten, tilldela en roll för åtkomst och logga in med den identiteten.

Du kan använda någon av två metoder för att skapa din tjänstens huvudnamn med AD FS. Du kan:
 - [Skapa ett huvudnamn för tjänsten som använder ett certifikat](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [Skapa ett huvudnamn för tjänsten med hjälp av en klienthemlighet](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

Åtgärder för att hantera AD FS-tjänsthuvudnamn.

| Type | Åtgärd |
| --- | --- |
| AD FS-certifikat | [Skapa](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| AD FS-certifikat | [Uppdatering](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-AD-FS) |
| AD FS-certifikat | [ta bort](azure-stack-create-service-principals.md#remove-a-service-principal-for-AD-FS) |
| Klienthemlighet för AD FS | [Skapa](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Klienthemlighet för AD FS | [Uppdatering](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Klienthemlighet för AD FS | [ta bort](azure-stack-create-service-principals.md##remove-a-service-principal-for-AD-FS) |

### <a name="create-a-service-principal-using-a-certificate"></a>Skapa ett huvudnamn för tjänsten som använder ett certifikat

När du skapar ett huvudnamn för tjänsten när du använder AD FS för identitet, kan du använda ett certifikat.

#### <a name="certificate"></a>Certifikat

Det krävs ett certifikat.

**Certifikatkrav**

 - Den kryptografiprovider (CSP) måste vara äldre nyckelleverantören.
 - Certifikatformatet måste vara i PFX-fil som krävs för både offentliga och privata nycklar. Windows-servrar använda PFX-filer som innehåller den offentliga nyckelfilen (SSL-certifikatfil) och den associera privata nyckelfilen.
 - För produktion, måste certifikatet utfärdas från en intern certifikatutfärdare eller en offentlig certifikatutfärdare. Om du använder en offentlig certifikatutfärdare, måste du med behörighet i grundläggande systemavbildningen som en del av Microsoft Trusted Root utfärdare Program. Du hittar en fullständig lista på [Microsoft Trusted Root Certificate Program: Deltagare](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - Azure Stack-infrastruktur måste ha nätverksåtkomst till certifikatutfärdarens certifikat listan över Återkallade plats publiceras i certifikatet. Den här listan över återkallade certifikat måste vara en HTTP-slutpunkt.

#### <a name="parameters"></a>Parametrar

Följande information måste anges som indata för automation-parametrar:

|Parameter|Beskrivning|Exempel|
|---------|---------|---------|
|Name|Namnet på kontot SPN|MyAPP|
|ClientCertificates|Matris med objekt för certifikat|X509 certifikat|
|ClientRedirectUris<br>(Valfritt)|Programmet omdirigerings-URI|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>Använd PowerShell för att skapa ett huvudnamn för tjänsten

1. Öppna en upphöjd Windows PowerShell-session och kör följande cmdlets:

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
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

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > För ett självsignerat certifikat kan skapas med verifiering i exemplet nedan:

   ```PowerShell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. När automation är klar visas informationen som krävs för att använda tjänstens Huvudnamn. Vi rekommenderar att lagra utdata för senare användning.

   Exempel:

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>Uppdatera certifikat för tjänstens huvudnamn för AD FS

Om du har distribuerat Azure Stack med AD FS, kan du använda PowerShell för att uppdatera hemligheten för tjänstens huvudnamn.

Skriptet körs från den privilegierade slutpunkten på en ERCS-dator.

#### <a name="parameters"></a>Parametrar

Följande information måste anges som indata för automation-parametrar:

|Parameter|Beskrivning|Exempel|
|---------|---------|---------|
|Name|Namnet på kontot SPN|MyAPP|
|ApplicationIdentifier|Unik identifierare|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|Matris med objekt för certifikat|X509 certifikat|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>Exempel för att uppdatera tjänstens huvudnamn för AD FS

I exemplet skapas ett självsignerat certifikat. Använd när du kör cmdletarna i en Produktionsdistribution [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) att hämta certifikatobjekt för det certifikatet som du vill använda.

1. Öppna en upphöjd Windows PowerShell-session och kör följande cmdlets:

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. När automation är klar visas den uppdaterade tumavtrycksvärde krävs för SPN-autentisering.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>Skapa ett huvudnamn för tjänsten med hjälp av en klienthemlighet

När du skapar ett huvudnamn för tjänsten när du använder AD FS för identitet, kan du använda ett certifikat. Du använder privilegierad slutpunkt för att köra cmdlets.

Dessa skript körs från den privilegierade slutpunkten på en ERCS-dator. Läs mer om privilegierad slutpunkt [med hjälp av privilegierad slutpunkt i Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

#### <a name="parameters"></a>Parametrar

Följande information måste anges som indata för automation-parametrar:

| Parameter | Beskrivning | Exempel |
|----------------------|--------------------------|---------|
| Name | Namnet på kontot SPN | MyAPP |
| GenerateClientSecret | Skapa hemliga |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>Använd ERCS PrivilegedEndpoint för att skapa tjänstens huvudnamn

1. Öppna en upphöjd Windows PowerShell-session och kör följande cmdlets:

     ```PowerShell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. När du kör cmdlet: ar, visas informationen som krävs för att använda tjänstens Huvudnamn. Kontrollera att du lagrar klienthemligheten.

     ```PowerShell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>Uppdatera klienthemlighet för tjänstens huvudnamn för AD FS

En ny klienthemlighet är auto som genererats av PowerShell-cmdleten.

Skriptet körs från den privilegierade slutpunkten på en ERCS-dator.

##### <a name="parameters"></a>Parametrar

Följande information måste anges som indata för automation-parametrar:

| Parameter | Beskrivning | Exempel |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | Unik identifierare. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | Ändrar klienthemlighet vid en övergångsperioden 2880 minuter där den gamla hemligheten är fortfarande giltig. |  |
| ResetClientSecret | Ändra klienthemligheten omedelbart |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>Exempel för att uppdatera en klienthemlighet för AD FS

I exemplet används den **ResetClientSecret** parameter, vilket genast ändrar klienthemligheten.

1. Öppna en upphöjd Windows PowerShell-session och kör följande cmdlets:

     ```PowerShell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. När automation är klar visas den nyligen skapade hemligheten som krävs för SPN-autentisering. Se till att spara den nya klienthemligheten.

     ```PowerShell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>Ta bort ett huvudnamn för tjänsten för AD FS

Om du har distribuerat Azure Stack med AD FS, kan du använda PowerShell för att ta bort ett huvudnamn för tjänsten.

Skriptet körs från den privilegierade slutpunkten på en ERCS-dator.

#### <a name="parameters"></a>Parametrar

Följande information måste anges som indata för automation-parametrar:

|Parameter|Beskrivning|Exempel|
|---------|---------|---------|
| Parameter | Beskrivning | Exempel |
| ApplicationIdentifier | Unik identifierare | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> Om du vill visa en lista över alla befintliga huvudmän för tjänsten och deras program-ID, kan du använda get-graphapplication-kommandot.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>Exempel på att ta bort tjänstens huvudnamn för AD FS

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>Tilldela en roll

För att komma åt resurser i din prenumeration, måste du tilldela programmet till en roll. Bestäm vilken roll representerar rätt behörigheter för programmet. Läs om tillgängliga roller i [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md).

Du kan ange omfånget för den prenumerationen, resursgruppen eller resursen. Behörigheter ärvs till lägre nivåer av omfång. Till exempel lägga till ett program till rollen Läsare för en resursgrupp innebär att den kan läsa resursgruppen och alla resurser som den innehåller.

1. Navigera till den nivå av omfång som du vill tilldela programmet till i Azure Stack-portalen. Om du vill tilldela en roll prenumerationsområde, väljer du exempelvis **prenumerationer**. Du kan i stället välja en resursgrupp eller resurs.

2. Välj den specifika prenumerationen (resursgrupp eller resurs) för att tilldela programmet till.

     ![Välj prenumeration för tilldelning](./media/azure-stack-create-service-principal/image16.png)

3. Välj **åtkomstkontroll (IAM)**.

     ![Välj åtkomst](./media/azure-stack-create-service-principal/image17.png)

4. Välj **Lägg till rolltilldelning**.

5. Välj den roll som du vill tilldela till programmet.

6. Sök efter ditt program och markera den.

7. Välj **OK** Slutför tilldela rollen. Du ser ditt program i listan över användare som har tilldelats en roll för detta omfång.

Nu när du har skapat ett tjänstobjekt och tilldelas en roll, kan du börja använda det i ditt program till Azure Stack-resurser.  

## <a name="next-steps"></a>Nästa steg

[Lägga till användare för AD FS](azure-stack-add-users-adfs.md)  
[Hantera användarbehörigheter](azure-stack-manage-permissions.md)  
[Dokumentation om Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
