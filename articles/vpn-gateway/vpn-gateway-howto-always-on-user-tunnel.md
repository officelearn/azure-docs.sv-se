---
title: Konfigurera en tunnel för alltid-on VPN-användare
titleSuffix: Azure VPN Gateway
description: Den här artikeln beskriver hur du konfigurerar en Always on VPN-tunnel för din VPN-gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: 099ed3c3c0ac2abe034388849385a45b44b32b34
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145949"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurera en tunnel för Always On VPN-användare

En ny funktion i VPN-klienten för Windows 10, Always on, är möjligheten att underhålla en VPN-anslutning. Med Always on kan den aktiva VPN-profilen ansluta automatiskt och förbli ansluten baserat på utlösare, till exempel användar inloggning, ändring av nätverks tillstånd eller enhets skärmen aktiv.

Du kan använda Azures virtuella nätverks-gatewayer med Windows 10 Always on för att upprätta permanenta användar tunnlar och enhets tunnlar till Azure. Den här artikeln hjälper dig att konfigurera en Always on VPN-användar tunnel.

Always on VPN-anslutningar omfattar endera av två typer av tunnlar:

* **Enhets tunnel**: ansluter till angivna VPN-servrar innan användarna loggar in på enheten. I anslutnings scenarier för inloggning och enhets hantering används en enhets tunnel.

* **Användar tunnel**: ansluter bara när användare loggar in på enheten. Med hjälp av användar tunnlar kan du få åtkomst till organisations resurser via VPN-servrar.

Enhets tunnlar och användar tunnlar drivs oberoende av deras VPN-profiler. De kan vara anslutna samtidigt, och de kan använda olika autentiseringsmetoder och andra inställningar för VPN-konfigurationen.

I följande avsnitt konfigurerar du en VPN-gateway och en användar tunnel.

## <a name="step-1-configure-a-vpn-gateway"></a>Steg 1: Konfigurera en VPN-gateway

Du konfigurerar VPN-gatewayen att använda IKEv2 och certifikatbaserad autentisering genom att följa anvisningarna i den här [punkt-till-plats-](vpn-gateway-howto-point-to-site-resource-manager-portal.md) artikeln.

## <a name="step-2-configure-a-user-tunnel"></a>Steg 2: Konfigurera en användar tunnel

1. Installera klient certifikat på Windows 10-klienten, som du ser i den här artikeln [punkt-till-plats-VPN-klient](point-to-site-how-to-vpn-client-install-azure-cert.md) . Certifikatet måste finnas i det aktuella användar arkivet.

1. Konfigurera Always on VPN-klienten via PowerShell, System Center Configuration Manager eller Intune genom att följa anvisningarna i [Konfigurera Windows 10-klienten alltid på VPN-anslutningar](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="example-configuration-for-the-user-tunnel"></a>Exempel på konfiguration av användar tunneln

När du har konfigurerat den virtuella Nätverksgatewayen och installerat klient certifikatet i den lokala datorns Arkiv på Windows 10-klienten, konfigurerar du en tunnel för klient enheter med hjälp av följande exempel:

1. Kopiera följande text och spara den som *Usercert. ps1*:

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Kopiera följande text och spara den som *VPNProfile. XML* i samma mapp som *Usercert. ps1*. Redigera följande text för att matcha din miljö:

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
    </Authentication>  
    <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
     <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
    <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
      </NativeProfile> 
      <!-- use host routes(/32) to prevent routing conflicts -->  
      <Route>  
    <Address>192.168.3.5</Address>  
    <PrefixSize>32</PrefixSize>  
      </Route>  
      <Route>  
    <Address>192.168.3.4</Address>  
    <PrefixSize>32</PrefixSize>  
      </Route>  
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. Kör PowerShell som administratör.

1. I PowerShell växlar du till den mapp där *Usercert. ps1* och *VPNProfile. XML* finns och kör följande kommando:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Leta upp posten **UserTest** under **VPN-inställningar**och välj sedan **Anslut**.

1. Om anslutningen lyckas har du konfigurerat en användar tunnel som Always.

## <a name="clean-up-your-resources"></a>Rensa dina resurser

Gör så här om du vill ta bort profilen:

1. Kör följande kommando:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Koppla från anslutningen och avmarkera kryss rutan **Anslut automatiskt** .

![Rensa](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Nästa steg

Information om hur du felsöker anslutnings problem som kan uppstå finns i [problem med Azure punkt-till-plats-anslutning](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
