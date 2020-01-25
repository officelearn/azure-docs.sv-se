---
title: Tyst installation Azure AD App proxy Connector | Microsoft Docs
description: Beskriver hur du utför en obevakad installation av Azure AD-programproxy Connector för att ge säker fjärråtkomst till dina lokala appar.
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
ms.date: 01/23/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca40e9717394690374f5ca289a69e5c22551eb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712004"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Skapa ett obevakat installations skript för Azure AD-programproxy-anslutningen

Det här avsnittet hjälper dig att skapa ett Windows PowerShell-skript som möjliggör obevakad installation och registrering för din Azure AD-programproxy-anslutning.

Den här funktionen är användbar när du vill:

* Installera anslutningen på Windows-servrar som inte har användar gränssnitt aktiverat eller som du inte kan komma åt med fjärr skrivbord.
* Installera och registrera många kopplingar samtidigt.
* Integrera anslutnings installationen och registreringen som en del av en annan procedur.
* Skapa en standard Server avbildning som innehåller kopplings bitarna men som inte är registrerad.

För att [Application Proxy Connector](application-proxy-connectors.md) ska fungera måste det registreras med Azure AD-katalogen med hjälp av en program administratör och ett lösen ord. Vanligt vis anges den här informationen under anslutnings installationen i en dialog ruta i ett popup-fönster, men du kan använda PowerShell för att automatisera processen i stället.

Det finns två steg för en obevakad installation. Installera först anslutningen. Sedan registrerar du anslutningen med Azure AD. 

## <a name="install-the-connector"></a>Installera anslutningen
Använd följande steg för att installera anslutningen utan att registrera den:

1. Öppna en kommandotolk.
2. Kör följande kommando, där/q innebär tyst installation. En tyst installation kräver inte att du accepterar licens avtalet för slutanvändare.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registrera anslutningen med Azure AD
Det finns två metoder som du kan använda för att registrera anslutningen:

* Registrera anslutningen med hjälp av ett Windows PowerShell-objekt för autentiseringsuppgifter
* Registrera anslutningen med en token som skapats offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrera anslutningen med hjälp av ett Windows PowerShell-objekt för autentiseringsuppgifter
1. Skapa ett objekt för Windows PowerShell-autentiseringsuppgifter `$cred` som innehåller ett administrativt användar namn och lösen ord för din katalog. Kör följande kommando och Ersätt *\<användar namn\>* och *\<lösen ord\>* :
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Gå till **C:\Program FILES\MICROSOFT AAD App proxy Connector** och kör följande skript med `$cred`-objektet som du skapade:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrera anslutningen med en token som skapats offline
1. Skapa en offline-token med AuthenticationContext-klassen med hjälp av värdena i det här kodfragmentet eller PowerShell-cmdletarna nedan:

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
        static readonly Uri ConnectorRedirectAddress = new Uri("https://login.microsoftonline.com/common/oauth2/nativeclient");

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
        [uri]$ConnectorRedirectAddress = "https://login.microsoftonline.com/common/oauth2/nativeclient" 
        
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


