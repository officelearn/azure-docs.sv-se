---
title: Exempel på Azure-program för användning med DMZ-miljöer
titlesuffix: Azure Virtual Network
description: Distribuera det här enkla webbprogrammet när du har skapat ett perimeternätverk för att testa scenarier för flödet av trafik
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 52058469b531383b32974ccc5bc77b31783583bd
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339955"
---
# <a name="sample-application-for-use-with-dmzs"></a>Exempelprogram för användning med DMZ-miljöer
[Gå tillbaka till gränsen bästa praxis sidan][HOME]

Dessa PowerShell-skript kan köras lokalt på IIS01 och AppVM01 att installera och konfigurera en enkel webbapp som visar en HTML-sida från servern IIS01 med innehåll från backend-AppVM01 servern.

Det här programmet innehåller en enkel testmiljö för många av de exempel som DMZ och hur ändringar på slutpunkter, NSG, UDR och brandväggen kan påverka trafikflöden.

## <a name="firewall-rule-to-allow-icmp"></a>Brandväggsregel för att tillåta ICMP
Den här enkla PowerShell-instruktionen kan köras på alla Windows virtuella datorer för att tillåta ICMP (Ping)-trafik. Uppdateringen brandvägg tillåter för enklare testning och felsökning genom att låta ping-protokollet att gå igenom windows-brandväggen (för de flesta Linux-distributioner ICMP är aktiverad som standard).

```PowerShell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

Om du använder följande skript, är den här brandväggen regeln lägga den första instruktionen.

## <a name="iis01---web-application-installation-script"></a>IIS01 - skript för Web application installation
Det här skriptet kommer att:

1. Öppna IMCPv4 (Ping) på lokal server windows-brandväggen för enklare testning
2. Installera IIS och .NET Framework 4.5
3. Skapa en ASP.NET-webbsida och en Web.config-fil
4. Ändra standardprogrampoolen för att underlätta filåtkomst
5. Ange anonym användare till ditt administratörskonto och lösenord

Det här PowerShell-skriptet ska köras lokalt medan RDP hade till IIS01.

```PowerShell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isn''t cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesn''t really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Classic Pipeline to remote file access will work easier
    Write-Host "Updating IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 - skript för installation
Det här skriptet ställer in backend-server för den här enkla programmet. Det här skriptet kommer att:

1. Öppna IMCPv4 (Ping) i brandväggen för enklare testning
2. Skapa en katalog för webbplatsen
3. Skapa en textfil för att vara via en fjärranslutning åtkomst av webbsidan
4. Ange behörigheter för katalogen och filen att anonym åtkomst ska tillåtas
5. Inaktivera Förbättrad säkerhet i Internet Explorer så att enklare surfning från den här servern

> [!IMPORTANT]
> **Bästa praxis**: Inaktivera Förbättrad säkerhetskonfiguration aldrig på en produktionsserver, samt det är vanligtvis en felaktig idé att surfa på Internet från servrar i produktionsmiljö. Öppnar upp filresurser för anonym åtkomst är också en felaktig idé, men klar här för enkelhetens skull.
>
>

Det här PowerShell-skriptet ska köras lokalt medan RDP hade till AppVM01. PowerShell krävs för att köras som administratör för att se till att åtgärden har körts.

```PowerShell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 - installationsskriptet för DNS-server
Det finns inga skript som ingår i det här exempelprogrammet för att ställa in DNS-servern. Om testning av brandväggsregler, NSG och UDR måste innehålla DNS-trafik, måste DNS01-servern konfigureras manuellt. Xml-filen nätverkskonfiguration och Resource Manager-mall för båda exemplen innehåller DNS01 som den primära DNS-servern och den offentliga DNS-server med nivå 3 som säkerhetskopiering DNS-server. Nivå 3 DNS-servern skulle den faktiska DNS-servern som används för icke-lokala trafik och med DNS01 inte konfigurera något lokala nätverk som DNS skulle inträffa.

## <a name="next-steps"></a>Nästa steg
* Köra skriptet IIS01 på en IIS-server
* Kör skriptet för filserver på AppVM01
* Bläddra till den offentliga IP-adresser på IIS01 att verifiera din version

<!--Link References-->
[HOME]: ../best-practices-network-security.md
