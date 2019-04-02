---
title: Azure AD App Proxy connector för tyst installation | Microsoft Docs
description: Beskriver hur du utför en obevakad installation av Azure AD Application Proxy Connector att tillhandahålla säker fjärråtkomst till lokala appar.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb193119186c2cf9e758f8c74f99f18c5fb389b8
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58792526"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Skapa ett obevakat installationsskript för Azure AD Application Proxy connector

Det här avsnittet hjälper dig att skapa ett Windows PowerShell-skript som gör det möjligt för obevakad installation och registrering för din Azure AD Application Proxy connector.

Den här funktionen är användbar när du vill:

* Installera anslutningen på Windows-servrar som inte har aktiverat användargränssnittet eller som du inte kommer åt via ett fjärrskrivbord.
* Installera och registrera många anslutningsappar på samma gång.
* Integrera kopplingsinstallationen och registrering som en del av en annan procedur.
* Skapa en standard server-avbildning som innehåller connector-bitar, men har inte registrerats.

För den [Application Proxy connector](application-proxy-connectors.md) för att fungera, så måste den vara registrerad med Azure AD-katalogen med hjälp av en programadministratör och ett lösenord. Den här informationen anges normalt under kopplingsinstallationen i en popup-dialogruta, men du kan använda PowerShell för att automatisera den processen i stället.

Det finns två steg för en obevakad installation. Installera först anslutningen. Dessutom registrera anslutningsverktyget med Azure AD. 

## <a name="install-the-connector"></a>Installera connector
Använd följande steg för att installera connector utan att registrera den:

1. Öppna en kommandotolk.
2. Kör följande kommando, där /q innebär tyst installation. En tyst installation ombedd inte att godkänna licensavtalet för slutanvändare.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registrera anslutningsverktyget med Azure AD
Det finns två metoder som du kan använda för att registrera anslutningsverktyget:

* Registrera anslutningsverktyget med hjälp av en Windows PowerShell-objektet för autentiseringsuppgifter
* Registrera anslutningsverktyget med hjälp av en token som skapats offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrera anslutningsverktyget med hjälp av en Windows PowerShell-objektet för autentiseringsuppgifter
1. Skapa en Windows PowerShell med huvudautentiseringsuppgifter `$cred` som innehåller ett administratörsanvändarnamn och lösenord för din katalog. Kör följande kommando ersätter *\<användarnamn\>* och  *\<lösenord\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Gå till **C:\Program Files\Microsoft AAD App Proxy Connector** och kör följande skript med den `$cred` objektet som du skapade:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrera anslutningsverktyget med hjälp av en token som skapats offline
1. Skapa en offline-token med hjälp av klassen AuthenticationContext med hjälp av värdena i det här kodfragmentet eller PowerShell-cmdletar nedan:

    **Med C#:**

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }

    **Med hjälp av PowerShell:**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1
        
        # Add ADAL Helper Library
        Add-Type -Path $ADALPath
        
        #region constants
        
        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/" 
        
        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"
        
        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "urn:ietf:wg:oauth:2.0:oob" 
        
        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"
        
        #endregion
        
        #region GetAuthenticationToken
        
        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint
        
        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior
        
        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result
        
        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }
        
        #endregion

2. När du har en token kan skapa en SecureString med token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Kör följande Windows PowerShell-kommando ersätter \<klient-GUID\> med din katalog-ID:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Nästa steg 
* [Publicera program med ditt domännamn](application-proxy-configure-custom-domain.md)
* [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
* [Felsöka problem med Application Proxy](application-proxy-troubleshoot.md)


