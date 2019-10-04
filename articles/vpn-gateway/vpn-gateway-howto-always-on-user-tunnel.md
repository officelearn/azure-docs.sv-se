---
title: Konfigurera en VPN-tunnel som alltid är på för VPN Gateway
description: Steg för att konfigurera Always on user VPN tunnel för VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846477"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Konfigurera en tunnel för Always on VPN-användare

En av de nya funktionerna i VPN-klienten (Virtual Private Network) i Windows 10 är möjligheten att underhålla en VPN-anslutning. Always On är en funktion i Windows 10 som gör det möjligt för den aktiva VPN-profilen att ansluta automatiskt och förblir ansluten baserat på utlösare – nämligen användar inloggning, ändring av nätverks tillstånd eller enhets skärm aktiv.

Virtuella Azure-nätverksgateway kan användas med Windows 10 Always on för att upprätta permanenta användar tunnlar samt enhets tunnlar till Azure. Den här artikeln hjälper dig att konfigurera en Always on VPN-användar tunnel.

Alltid på VPN-anslutningar är två typer av tunnlar:

* **Enhets tunneln** ansluter till angivna VPN-servrar innan användarna loggar in på enheten. Anslutnings scenarier för inloggning och enhets hantering använder enhets tunnel.

* **Användar tunneln** ansluter bara efter att en användare har loggat in på enheten. Användar tunnel ger användare åtkomst till organisations resurser via VPN-servrar.

Både enhets tunnel och användar tunnel körs oberoende av sina VPN-profiler. De kan vara anslutna samtidigt, och kan använda olika autentiseringsmetoder och andra inställningar för VPN-konfigurationen efter behov.

## <a name="1-configure-the-gateway"></a>1. Konfigurera gatewayen

Konfigurera VPN-gatewayen att använda IKEv2 och certifikatbaserad autentisering med hjälp av den här [punkt-till-plats-artikeln](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Konfigurera användar tunneln

1. Installera klient certifikat på Windows 10-klienten som visas i [artikeln punkt-till-plats-VPN-klient](point-to-site-how-to-vpn-client-install-azure-cert.md). Certifikatet måste finnas i det aktuella användar arkivet
2. Konfigurera Always on VPN-klienten via PowerShell, SCCM eller Intune med hjälp av [dessa instruktioner](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="configuration-example-for-user-tunnel"></a>Konfigurations exempel för användar tunnel

När du har konfigurerat den virtuella Nätverksgatewayen och installerat klient certifikatet i den lokala datorns Arkiv på Windows 10-klienten, använder du följande exempel för att konfigurera en klient enhets tunnel.

1. Kopiera följande text och spara den som ***Usercert. ps1***.

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
1. Kopiera följande text och spara den som ***VPNProfile. XML*** i samma mapp som **Usercert. ps1**. Redigera följande text för att matcha din miljö.

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

1. I PowerShell växlar du till den mapp där **Usercert. ps1** och **VPNProfile. XML** finns och kör följande kommando:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Titta under VPN-inställningar.

1. Leta efter **UserTest** -posten och klicka på **Anslut**.

1. Om anslutningen lyckas har du konfigurerat en användar tunnel som alltid är i gång.

## <a name="cleanup"></a>Rensa

Om du vill ta bort profilen kör du följande kommando:

1. Koppla från anslutningen och avmarkera "Anslut automatiskt"

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![Rensa](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Nästa steg

Information om fel sökning finns i [problem med Azure punkt-till-plats-anslutning](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
