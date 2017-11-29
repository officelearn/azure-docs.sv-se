---
title: "Azure Cloud Shell-felsökning | Microsoft Docs"
description: "Felsöka Azure-molnet Shell"
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: damaerte
ms.openlocfilehash: 233569303ea3651192aafe9681f58a9582625d29
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshooting-azure-cloud-shell"></a>Felsöka Azure-molnet Shell

Kända lösningar på problem i Azure Cloud Shell inkluderar:

## <a name="general-resolutions"></a>Allmän lösningar

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Lagring dialogruta - fel: 403 RequestDisallowedByPolicy
- **Information om**: när du skapar ett lagringskonto via molnet Shell, är det misslyckas på grund av en Azure-princip placeras av din administratör. Felmeddelande innehåller:`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Lösning**: Kontakta Azure-administratören om du vill ta bort eller uppdatera Azure principen neka lagring skapas.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Lagring dialogruta - fel: 400 DisallowedOperation
 - **Information om**: när du använder en prenumeration på Azure Active Directory, du kan inte skapa lagring.
 - **Lösning**: använda en Azure-prenumeration kan skapa lagringsresurser. Azure AD-prenumerationer går inte att skapa Azure-resurser.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal utdata - fel: Det gick inte att ansluta en terminal: websocket kan inte upprättas. Tryck på `Enter` återansluta.
 - **Information om**: molnet Shell kräver möjlighet att upprätta en websocket-anslutning till molnet Shell-infrastruktur.
 - **Lösning**: Kontrollera att du har konfigurerat inställningarna för att aktivera skicka https-förfrågningar och websocket-begäranden till domäner på *. console.azure.com.

## <a name="bash-resolutions"></a>Bash lösningar

### <a name="cannot-run-az-login"></a>Det går inte att köra az inloggning

- **Information om**: kör `az login` fungerar inte som du redan har autentiserats under kontot som används för att logga in på molnet Shell eller Azure-portalen.
- **Lösning**: använda ditt konto som används för att logga in eller logga ut och autentiseras med ditt avsedda Azure-konto.

### <a name="cannot-run-the-docker-daemon"></a>Det går inte att köra docker-daemon

- **Information om**: molnet Shell använder en behållare som värd för din miljö för shell, kör därför daemon är inte tillåtet.
- **Lösning**: använda [docker-datorn](https://docs.docker.com/machine/overview/), som installeras som standard för att hantera behållare med docker från en fjärrvärd med Docker.

## <a name="powershell-resolutions"></a>PowerShell-lösningar

### <a name="no-home-directory-persistence"></a>No $Home directory beständiga

- **Information**: data programmet (exempel: git vim och andra) skriver till `$Home` inte beständig i PowerShell-sessioner.
- **Lösning**: skapa en symbolisk länk till specifika program-mappen i i profilen för PowerShell `clouddrive` till $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>CTRL + C Avsluta inte Kommandotolken Cmdlet

- **Information**: när du försöker avsluta Kommandotolken Cmdlet `Ctrl+C` inte avslutas i prompten.
- **Lösning**: Om du vill avsluta Kommandotolken, tryck på `Ctrl+C` sedan `Enter`.

### <a name="gui-applications-are-not-supported"></a>GUI-program stöds inte

- **Information om**: om en användare startar en GUI-app, frågan inte returnerar. När en användare klonar en privat GitHub-repo-som är aktiverad för tvåfaktorsautentisering, till exempel visas en dialogruta för att slutföra två faktorautentisering.  
- **Lösning**: Stäng och öppna gränssnittet.


### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help - online går inte att öppna hjälpsidan

- **Information om**: om en användare anger `Get-Help Find-Module -online`, något som ser ett felmeddelande visas:`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Lösning**: Kopiera webbadressen och öppna den manuellt i din webbläsare.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Felsökning av fjärrhantering av virtuella Azure-datorer

- **Information om**: på grund av Windows-brandväggen standardinställningarna för WinRM kan användaren se följande fel:`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Lösning**: se till att den virtuella datorn körs. Du kan köra `Get-AzureRmVM -Status` ta reda på VM-statusen.  Lägg sedan till en ny brandväggsregel på fjärranslutna VM att tillåta WinRM anslutningar från alla undernät, t.ex.

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Du kan använda [tillägget för anpassat skript för Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript) att undvika att logga in på din fjärranslutna VM för att lägga till nya brandväggsregeln.
 Du kan spara skriptet till en fil, säg `addfirerule.ps1`, och överföra den till Azure storage-behållare.
 Försök sedan följande kommando:

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir`cachelagrar resultatet i Azure-enhet

- **Information om**: resultatet av `dir` cachelagras i Azure-enheten.
- **Lösning**: när du skapar eller ta bort en resurs i vyn Azure enhet kör `dir -force` att uppdatera.
