---
title: Installera ansluten datoragent med Windows PowerShell DSC
description: I den här artikeln får du lära dig hur du ansluter datorer till Azure med Azure Arc för servrar (förhandsversion) med Windows PowerShell DSC.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164688"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Installera agenten ansluten dator med Windows PowerShell DSC

Med Hjälp av [DSC (Windows PowerShell Desired State Configuration)](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) kan du automatisera installation och konfiguration av programvara för en Windows-dator. I den här artikeln beskrivs hur du använder DSC för att installera Azure Arc för servrar Connected Machine agent på hybrid Windows-datorer.

## <a name="requirements"></a>Krav

- Windows PowerShell version 4.0 eller senare

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC-modulen

- Ett tjänsthuvudnamn för att ansluta datorerna till Azure Arc för servrar som inte är interaktivt. Följ stegen under avsnittet [Skapa ett tjänsthuvudnamn för introduktion i stor skala](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) om du inte redan har skapat ett huvudnamn för Tjänsten för Arc för servrar.

## <a name="install-the-connectedmachine-dsc-module"></a>Installera ConnectedMachine DSC-modulen

1. Om du vill installera modulen manuellt hämtar du källkoden och hämtar `$env:ProgramFiles\WindowsPowerShell\Modules folder`innehållet i projektkatalogen till . Eller kör följande kommando för att installera från PowerShell-galleriet med PowerShellGet (i PowerShell 5.0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. För att bekräfta installationen, kör följande kommando och se till att du ser de Azure Connected Machine DSC-resurser som är tillgängliga.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   I utdata bör du se något som liknar följande:

   ![Bekräftelse på exempel på installation av ansluten maskin DSC-modul](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Installera agenten och ansluta till Azure

Resurserna i den här modulen är utformade för att hantera Azure Connected Machine Agent-konfigurationen. Dessutom ingår ett PowerShell-skript `AzureConnectedMachineAgent.ps1` `AzureConnectedMachineDsc\examples` , som finns i mappen. Den använder community-resurser för att automatisera hämtning och installation och upprätta en anslutning till Azure Arc. Det här skriptet utför liknande steg som beskrivs i [Connect-hybriddatorerna till Azure från Azure-portalartikeln.](onboard-portal.md)

Om datorn behöver kommunicera via en proxyserver till tjänsten måste du köra ett kommando som beskrivs [här](onboard-portal.md#configure-the-agent-proxy-setting)när du har installerat agenten . Detta anger proxyserversystemets `https_proxy`miljövariabel . I stället för att köra kommandot manuellt kan du utföra det här steget med DSC med hjälp av modulen [ComputeManagementDsc.](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)

>[!NOTE]
>För att DSC ska kunna köras måste Windows konfigureras för att ta emot PowerShell-fjärrkommandon även när du kör en localhost-konfiguration. Om du enkelt vill konfigurera `Set-WsManQuickConfig -Force` din miljö korrekt kan du bara köra i en upphöjd PowerShell-terminal.
>

Konfigurationsdokument (MOF-filer) kan användas `Start-DscConfiguration` på datorn med hjälp av cmdlet.

Följande är de parametrar som du skickar till PowerShell-skriptet som ska användas.

- `TenantId`: Den unika identifierare (GUID) som representerar din dedikerade instans av Azure AD.

- `SubscriptionId`: Prenumerations-ID (GUID) för din Azure-prenumeration som du vill ha datorerna i.

- `ResourceGroup`: Resursgruppsnamnet där du vill att dina anslutna datorer ska tillhöra.

- `Location`: Se [Azure-regioner som stöds](overview.md#supported-regions). Den här platsen kan vara samma eller annorlunda, som resursgruppens plats.

- `Tags`: Strängmatris med taggar som ska tillämpas på den anslutna datorresursen.

- `Credential`: Ett PowerShell-autentiseringsobjekt med **ApplicationId** och **lösenord** som används för att registrera datorer i stor skala med hjälp av ett [huvudnamn](onboard-service-principal.md)för tjänsten . 

1. I en PowerShell-konsol navigerar du `.ps1` till mappen där du sparade filen.

2. Kör följande PowerShell-kommandon för kompilering av MOF-dokumentet (mer information om kompilering av DSC-konfigurationer finns i [DSC-konfigurationer:](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7)

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Detta skapar `localhost.mof file` en i en `C:\dsc`ny mapp med namnet .

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc för servrar (förhandsversion) går du till Azure-portalen för att kontrollera att servern har anslutits. Visa dina datorer i [Azure-portalen](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Lägga till befintliga konfigurationer

Den här resursen kan läggas till i befintliga DSC-konfigurationer för att representera en end-to-end-konfiguration för en dator. Du kanske till exempel vill lägga till den här resursen i en konfiguration som anger inställningar för säkert operativsystem.

[Modulen CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) från PowerShell-galleriet kan användas för att skapa en [sammansatt resurs](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) för exempelkonfigurationen, för att ytterligare förenkla kombinationen av konfigurationer.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du hanterar din dator med [Azure Policy](../../governance/policy/overview.md), för till exempel [VM-gästkonfiguration,](../../governance/policy/concepts/guest-configuration.md)verifierar att datorn rapporterar till den förväntade Log Analytics-arbetsytan, aktiverar övervakning med [Azure Monitor med virtuella datorer](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)och mycket mer.

- Läs mer om [Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics-agenten för Windows och Linux krävs när du proaktivt vill övervaka operativsystemet och arbetsbelastningar som körs på datorn, hantera det med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-intro.md).