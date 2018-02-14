---
title: "Aktivera fjärrfelsökning med kontinuerlig leverans | Microsoft Docs"
description: "Lär dig att aktivera fjärrfelsökning när kontinuerlig leverans för att distribuera till Azure"
services: cloud-services
documentationcenter: .net
author: mikejo
manager: ghogen
editor: 
ms.assetid: 7d423639-3b2f-4ca5-ac5a-9ac19a217c29
ms.service: cloud-services
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 1a30b42e6e84edf9a7cef861aaf6a60e87c473d0
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Aktivera fjärrfelsökning när du använder kontinuerlig leverans för att publicera till Azure.
Du kan aktivera fjärrfelsökning i Azure, för molntjänster eller virtuella datorer när du använder [kontinuerlig leverans](cloud-services-dotnet-continuous-delivery.md) att publicera till Azure genom att följa dessa steg.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Aktivera fjärrfelsökning för molntjänster
1. Build-agenten, konfigurera inledande miljön för Azure som beskrivs i [kommandoradsverktyget skapa för Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Eftersom remote debug-körning (msvsmon.exe) krävs för paketet, installera den **fjärrverktyg för Visual Studio**.

   * [Fjärrverktyg för Visual Studio 2017](https://go.microsoft.com/fwlink/?LinkId=746570)
   * [Fjärrverktyg för Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=615470)
   * [Fjärrverktyg för Visual Studio 2013 Update 5](https://www.microsoft.com/download/details.aspx?id=48156)
    
   Alternativt kan kopiera du remote debug-binärfiler från ett system som har Visual Studio installerat.

3. Skapa ett certifikat som beskrivs i [certifikat översikt för Azure Cloud Services](cloud-services-certs-create.md). Håll .pfx och RDP certifikatets tumavtryck och överför certifikatet till molnet Måltjänsten.
4. Med hjälp av följande alternativ på kommandoraden MSBuild att bygga och paketet med fjärråtkomst felsökning aktiverat. (Ersätt faktiska sökvägarna till system- och filerna för vinkeln omges-objekt).
   
   ```cmd
   msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"
   ```
   
   `VSX64RemoteDebuggerPath`är sökvägen till mappen som innehåller msvsmon.exe i fjärranslutna verktyg för Visual Studio.
   `RemoteDebuggerConnectorVersion`är Azure SDK-version i Molntjänsten. Det bör också matcha den version som installeras med Visual Studio.

5. Publicera till Molntjänsten mål med hjälp av paket- och .cscfg-filen som skapas i föregående steg.
6. Importera certifikatet (.pfx-filen) till den dator som har Visual Studio med Azure SDK för .NET installerats. Se till att importera till den `CurrentUser\My` certifikatarkiv, annars kopplar till felsökning i Visual Studio misslyckas.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Aktivera fjärrfelsökning för virtuella datorer
1. Skapa en virtuell Azure-dator. Se [skapa en virtuell dator som kör Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [skapa och hantera virtuella Azure-datorer i Visual Studio](../virtual-machines/windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
2. På Azure-portalen] (http://go.microsoft.com/fwlink/p/?LinkID=269851), navigera till den virtuella datorn till den virtuella datorn **RDP CERTIFIKATETS TUMAVTRYCK**. Det här värdet används för den `ServerThumbprint` värde i konfigurationen för tillägget.
3. Skapa ett klientcertifikat som beskrivs i [certifikat översikt för Azure Cloud Services](cloud-services-certs-create.md) (håll .pfx och tumavtrycket för RDP).
4. Installera Azure Powershell (version 0.7.4 eller senare) som beskrivs i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
5. Kör följande skript för att aktivera tillägget RemoteDebug. Ersätt sökvägar och personliga data med ditt eget, till exempel din prenumerationsnamn, tjänstnamn och tumavtryck.
   
   > [!NOTE]
   > Det här skriptet har konfigurerats för Visual Studio 2015. Om du använder Visual Studio 2013 eller Visual Studio 2017 kan ändra den `$referenceName` och `$extensionName` tilldelningar nedan till `RemoteDebugVS2013` eller `RemoteDebugVS2017`.

    ```powershell   
    Add-AzureAccount

    Select-AzureSubscription "My Microsoft Subscription"

    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

    $endpoints = @(
                    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
                    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
                )

    foreach($endpoint in $endpoints)
    {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2015"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Set-AzureVMExtension -ReferenceName $referenceName -Publisher $publisher -ExtensionName $extensionName -Version $version -PublicConfiguration $publicConfiguration

    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
            $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
            break
        }
    }

    $vm | Update-AzureVM
    ```

6. Importera certifikatet (.pfx) till den dator som har Visual Studio med Azure SDK för .NET installerats.

