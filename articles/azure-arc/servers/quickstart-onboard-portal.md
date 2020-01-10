---
title: Snabb start – ansluta datorer till Azure med Azure-båge för servrar – Portal
description: I den här snabb starten får du lära dig hur du ansluter datorer till Azure med hjälp av Azure Arc för servrar från portalen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, önskad tillstånds konfiguration, uppdaterings hantering, ändrings spårning, inventering, Runbooks, python, grafisk, hybrid och internt
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 26c79db956b2703bf037fc6f7790d4ee13874410
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834252"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>Snabb start: ansluta datorer till Azure med Azure-båge för servrar – Portal

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Granska de klienter som stöds och nödvändig nätverks konfiguration i [översikten över Azure-bågen för servrar](overview.md).

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Generera agent installations skriptet med hjälp av Azure Portal

1. Starta [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)
1. Klicka på **+ Lägg till**
1. Följ guiden för att slutföra
1. På den sista sidan finns ett skript som du kan kopiera (eller ladda ned).

Skriptet måste köras på mål datorn som du vill ansluta. Den laddar ned agenten, installerar den och ansluter datorn som en enda åtgärd.

På de icke-Azure-servrar som du vill hantera:

1. Logga in på servern (med SSH-, RDP-eller PowerShell-fjärrkommunikation)
1. Starta ett gränssnitt: bash på Linux, PowerShell som administratör i Windows
1. Klistra in skriptet från portalen och kör det på servern för att ansluta till Azure.
1. Standardautentiseringen för onboarding av en enskild server är *interaktiv* med Azures enhets inloggning ". När du kör skriptet visas ett meddelande som liknar:

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   Öppna en webbläsare och ange den kod som ska autentiseras. Webbläsaren behöver inte köras på den server som du registrerar, det kan finnas på en annan dator, till exempel din bärbara dator.

1. Om du vill autentisera icke-interaktivt följer du stegen i [skapa ett huvud namn för tjänsten](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale) och ändrar skriptet som genereras från portalen.

> [!NOTE]
> Om du använder Internet Explorer på servern första gången du loggar in, kommer den att vara fel. Du kan bara öppna webbläsaren igen och göra det igen.

## <a name="execute-the-script-on-target-nodes"></a>Kör skriptet på målnoden

Logga in på varje nod och kör skriptet som du skapade från portalen. När skriptet har slutförts går du till Azure Portal verifiera att servern har anslutits.

![Lyckad onboarding](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Rensa

Om du vill koppla bort en dator från Azure-bågen för servrar måste du utföra två steg.

1. Välj datorn i [portalen](https://aka.ms/hybridmachineportal), klicka på ellipsen (`...`) och välj **ta bort**.
1. Avinstallera agenten från datorn.

   I Windows kan du använda "apparna & funktioner" på kontroll panelen för att avinstallera agenten.
  
  ![Appar & funktioner](./media/quickstart-onboard/apps-and-features.png)

   Om du vill skripta avinstallationen kan du använda följande exempel som hämtar **PackageId** och avinstallerar agenten med hjälp av `msiexec /X`.

   Titta under register nyckeln `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` och hitta **PackageId**. Sedan kan du avinstallera agenten med hjälp av `msiexec`.

   Exemplet nedan visar hur du avinstallerar agenten.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   Kör följande kommando i Linux för att avinstallera agenten.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Tilldela en princip till anslutna datorer](../../governance/policy/assign-policy-portal.md)
