---
title: Tyst installation av Azure AD App Proxy-anslutningsapp | Microsoft-dokument
description: Beskriver hur du utför en obevakad installation av Azure AD Application Proxy Connector för att ge säker fjärråtkomst till dina lokala appar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b43d2de0a366d7e69a025b2e4e2998dccda2038e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756219"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Skapa ett obevakat installationsskript för Azure AD Application Proxy-anslutningsappen

Det här avsnittet hjälper dig att skapa ett Windows PowerShell-skript som möjliggör obevakad installation och registrering för din Azure AD Application Proxy-anslutningsapp.

Den här funktionen är användbar när du vill:

* Installera anslutningen på Windows-servrar som inte har användargränssnitt aktiverat eller som du inte kan komma åt med Fjärrskrivbord.
* Installera och registrera många anslutningar samtidigt.
* Integrera anslutningsinstallationen och registreringen som en del av en annan procedur.
* Skapa en standardserveravbildning som innehåller kopplingsbitarna men som inte är registrerad.

För att [application proxy-anslutningsappen](application-proxy-connectors.md) ska fungera måste den registreras med din Azure AD-katalog med hjälp av en programadministratör och ett lösenord. Vanligtvis anges den här informationen under installationen av Anslutning i en popup-dialogruta, men du kan använda PowerShell för att automatisera den här processen i stället.

Det finns två steg för en obevakad installation. Installera först kontakten. För det andra registrerar du kopplingen med Azure AD. 

## <a name="install-the-connector"></a>Installera kontakten
Gör så här för att installera anslutningen utan att registrera den:

1. Öppna en kommandotolk.
2. Kör följande kommando, där /q betyder tyst installation. En tyst installation uppmanar dig inte att acceptera licensavtalet för slutanvändare.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registrera kopplingen med Azure AD
Det finns två metoder som du kan använda för att registrera kopplingen:

* Registrera kopplingen med ett Windows PowerShell-autentiseringsobjekt
* Registrera kopplingen med hjälp av en token som skapats offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrera kopplingen med ett Windows PowerShell-autentiseringsobjekt
1. Skapa ett Windows PowerShell-autentiseringsobjekt `$cred` som innehåller ett administrativt användarnamn och lösenord för katalogen. Kör följande kommando och ersätt * \<\> användarnamn* och * \<lösenord:\>*
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Gå till **C:\Program Files\Microsoft AAD App Proxy** Connector `$cred` och kör följande skript med objektet som du skapade:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrera kopplingen med hjälp av en token som skapats offline
1. Skapa en offlinetoken med klassen AuthenticationContext med värdena i det här kodavsnittet eller PowerShell-cmdleterna nedan:

    **Använda C#:**

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

    **Använda PowerShell:**

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

2. När du har token skapar du en SecureString med hjälp av token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Kör följande Windows PowerShell-kommando och ersätt \<klient-GUID\> med ditt katalog-ID:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Nästa steg 
* [Publicera program med ditt domännamn](application-proxy-configure-custom-domain.md)
* [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
* [Felsöka problem med Application Proxy](application-proxy-troubleshoot.md)


