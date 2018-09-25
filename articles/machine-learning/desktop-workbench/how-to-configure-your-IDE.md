---
title: Så här konfigurerar du Azure Machine Learning Workbench för att arbeta med en IDE-miljö?  | Microsoft Docs
description: En guide för att konfigurera Azure Machine Learning Workbench och fungerar med din IDE.
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 8158d6faee5ec4d28f0c7e16963fc3d78392b857
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978972"
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Så här konfigurerar du Azure Machine Learning Workbench för att arbeta med en IDE-miljö 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Azure Machine Learning Workbench kan konfigureras för att fungera med populära Python IDE (Integrated Development Environment). Det gör det möjligt för en smidig dataforskningsutveckling flytta mellan förberedelse av data, redigering av kod, Kör spårning och driftsättning. Det finns för närvarande stöds IDE: er:
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Konfigurera workbench
1. Klicka på den **filen** menyn i övre vänstra hörnet på appen. 
2. Välj den **konfigurera projektet IDE** alternativet från utfällt 
3. Skriv i `VS Code` eller `PyCharm` i den **namn** fält (namnet är godtyckligt)
4. Ange platsen till den IDE körbara filen (slutförd med filnamn och tillägg) i **Exekveringsväg**

### <a name="default-install-path-for-visual-studio-code"></a>Standardinstallationssökväg för Visual Studio Code  

* Windows 32-bitars- `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows 64-bitars `C:\Program Files\Microsoft VS Code\Code.exe`
* macOS – Välj .app-sökväg, till exempel `/Applications/Visual Studio Code.app`, och appen lägger till resten av sökvägen för dig. Den fullständiga sökvägen till den körbara filen som standard är `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Om du har genomfört den `Shell Command: Install 'code' command in PATH` kommandot i VS Code, och du kan också referera till VS Code-skriptet i `/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>Standardinstallationssökväg för PyCharm 

* Windows 32-bitars - `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* Windows 64-bitars - `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS – Välj den .app-sökvägen, till exempel ”/ program/PyCharm CE.app” och appen lägger till resten av sökvägen för dig. Den fullständiga sökvägen till den körbara filen som standard är `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. Du kan också hitta PyCharm på bin-mappen `/usr/local/bin/charm`

## <a name="open-project-in-ide"></a>Öppna projektet i IDE 
När konfigurationen är klar kan du öppna ett Azure Machine Learning-projekt genom att öppna den **filen** menyn i Azure Machine Learning Workbench, klicka sedan på **öppna projekt (< IDE_Name >)**. Den här åtgärden öppnar det aktuella aktiva projektet i konfigurerade IDE. _Obs: Om du inte är i ett projekt i **öppna projektet (< IDE_Name >)** kommer att inaktiveras._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Konfigurera den integrerade terminalen i Visual Studio Code

### <a name="windows"></a>Windows 
Vi har åsidosatts standardgränssnitt för att vara cmd i stället för PowerShell. När du klickar på på **öppna projektet (< IDE_Name >)**, du ser en uppmaning: 

_Tillåter du att shell: `C:\windows\System32\cmd.exe` (definieras som en arbetsyta) startas i terminalen?_

Svar `yes` att konfigurera gränssnittet för att fungera smidigt med kommandoradsgränssnittet för Azure ML Workbench.

### <a name="mac"></a>Mac
Att köra en `az` kommandot med Visual Studio Code-integrerade terminalen på Mac, måste du manuellt ange den `PATH` ska vara samma värde som `PATH` i projektets `.vscode/settings.json` fil under nyckeln `terminal.integrated.env.osx`. Du kan göra det genom att köra följande kommando i terminalen: `PATH=<PATH in .vscode/settings>`
