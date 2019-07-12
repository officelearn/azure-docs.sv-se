---
title: Konfigurera en AlwaysOn VPN-tunnel för VPN-Gateway
description: Steg för att konfigurera alltid på VPN-tunnel för VPN-Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 81822297dcf9370fc8ce7f7ce0285689c31606ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695836"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurera en VPN-enhetstunnel för AlwaysOn

En av de nya funktionerna i Windows 10 virtuella privata nätverk (VPN)-klient är möjligheten att ha en VPN-anslutning. Always On är en Windows 10-funktion som gör det möjligt för aktiva VPN-profilen att ansluta automatiskt och vara anslutna baserat på utlösare – nämligen användarinloggning tillståndsändring för nätverk eller enhetens skärm som är aktiva.

Azures virtuella nätverksgatewayar kan användas med Windows 10 alltid på att upprätta beständiga användaren tunnlar samt enheten tunnlar till Azure. Den här artikeln beskriver hur du konfigurerar en enhet alltid på VPN-tunnel.

Alltid på VPN-anslutningar innehåller två typer av tunnlar:

* **Enheten tunnel** ansluter till den angivna VPN-servrar innan användarna logga in på enheten. Scenarier för före inloggningen anslutning och hanteringsändamål för enheten du använder enheten tunnlar.

* **Användaren tunnel** ansluter bara när en användare loggar i enheten. Användaren tunnel låter användare åtkomst till organisationens resurser via VPN-servrar.

Både tunnel för enheten och användaren tunnel är oberoende av varandra med sina VPN-profiler. De kan anslutas samtidigt och kan använda olika autentiseringsmetoder och andra konfigurationsinställningar för VPN-efter behov.

## <a name="1-configure-the-gateway"></a>1. Konfigurera gatewayen

Konfigurera VPN-gateway för att använda IKEv2 och certifikatbaserad autentisering som använder det här [punkt-till-plats-artikeln](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Konfigurera tunneln användare

1. Installera klientcertifikat på Windows 10-klient som visas i den här [punkt-till-plats VPN-klienten artikeln](point-to-site-how-to-vpn-client-install-azure-cert.md). Certifikatet måste finnas i den aktuella användaren Store
2. Konfigurera AlwaysOn VPN-klienten med hjälp av PowerShell, SCCM eller Intune [instruktionerna](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

## <a name="3-configure-the-device-tunnel"></a>3. Konfigurera enheten-tunnel

Följande krav måste uppfyllas för att skapa en tunnel för enheten:

* Enheten måste vara en domänansluten dator som kör Windows 10 Enterprise eller Education version 1709 eller senare.
* Tunneln kan bara konfigureras för den inbyggda VPN-lösningen för Windows och upprättas med hjälp av IKEv2 med autentisering med datorcertifikat. 
* Endast en enhet tunnel kan konfigureras per enhet.

1. Installera klientcertifikat på Windows 10-klient som visas i den här [punkt-till-plats VPN-klienten artikeln](point-to-site-how-to-vpn-client-install-azure-cert.md). Certifikatet måste finnas i lokala datorns Arkiv.
1. Använd [instruktionerna](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) du skapar en VPN-profil och konfigurerar enheten tunnel i samband med det lokala systemkontot.

### <a name="configuration-example-for-device-tunnel"></a>Konfigurationsexempel för enhet-tunnel

När du har konfigurerat den virtuella nätverksgatewayen och har installerat klientcertifikatet i det lokala datorarkivet på Windows 10-klienten, kan du använda följande exempel för att konfigurera en tunnel för klient-enhet.

1. Kopiera följande text och spara den som ***devicecert.ps1***.

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
1. Kopiera följande text och spara den som ***VPNProfile.xml*** i samma mapp som **devicecert.ps1**. Redigera följande text som matchar din miljö.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
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
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Ladda ned **PsExec** från [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) och extrahera filerna till **C:\PSTools**.
1. Starta PowerShell genom att köra från en administratör CMD-kommandotolk:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![PowerShell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. I PowerShell, växla till mappen där **devicecert.ps1** och **VPNProfile.xml** finns och kör följande kommando:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Kör **rasphone**.

   ![RASPHONE](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Leta efter den **MachineCertTest** posten och klicka på **Connect**.

   ![Anslut](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Om anslutningen lyckas, kan du starta om datorn. Tunneln ansluter automatiskt.

## <a name="cleanup"></a>Rensa

Ta bort profilen genom att köra följande kommando:

![Rensa](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Nästa steg

Om felsökning finns [Azure punkt-till-plats-anslutningsproblem](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
