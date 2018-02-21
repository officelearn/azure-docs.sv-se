---
title: "Så här konfigurerar du Azure Machine Learning-arbetsstationen för att arbeta med en IDE?  | Microsoft Docs"
description: "En guide för att konfigurera Azure Machine Learning-arbetsstationen för att arbeta med din IDE."
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: caafc626d42ea3f1514c36be04507adf31bbc5c9
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Så här konfigurerar du Azure Machine Learning-arbetsstationen för att arbeta med en IDE 

Azure Machine Learning arbetsstationen kan konfigureras för att fungera med populära Python IDEs (Integrated Development Environment). Den gör en smidig datavetenskap utvecklingsarbetet flytta mellan förberedelse av data, redigering av koden, Kör spårning och operationalization. Det finns för närvarande stöds IDEs:
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Konfigurera arbetsstationen
1. Klicka på den **filen** menyn i övre vänstra hörnet på appen. 
2. Välj den **konfigurera projektet IDE** alternativet från utfällbar 
3. Skriv i `VS Code` eller `PyCharm` i den **namn** fält (namn är valfri)
4. Ange sökvägen till den IDE körbara filen (komplett med körbara namn och filnamnstillägg) i **körning av sökväg**

### <a name="default-install-path-for-visual-studio-code"></a>Installera standardsökvägen för Visual Studio Code  

* Windows 32-bitars- `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows 64-bitars- `C:\Program Files\Microsoft VS Code\Code.exe`
* macOS - Välj .app-sökväg, till exempel `/Applications/Visual Studio Code.app`, och appen lägger till resten av sökvägen för dig. Den fullständiga sökvägen till den körbara filen som standard är `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Om du har genomfört den `Shell Command: Install 'code' command in PATH` kommandot i VS-kod, och du kan också referera skriptet VS-koden på `/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>Installera standardsökvägen för PyCharm 

* Windows 32-bitars - `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* Windows 64-bitars - `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS - Välj .app sökväg, till exempel ”/ Applications/PyCharm CE.app” och appen lägger till resten av sökvägen för dig. Den fullständiga sökvägen till den körbara filen som standard är `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. Du kan också hitta PyCharm på bin-mappen `/usr/local/bin/charm`

## <a name="open-project-in-ide"></a>Öppna projektet i IDE 
När konfigurationen är klar kan du öppna en Azure Machine Learning-projekt genom att öppna den **filen** Klicka på menyn i Azure Machine Learning arbetsstationen **öppna projektet (< IDE_Name >)**. Den här åtgärden öppnar det aktuella aktiva projektet i konfigurerade IDE. _Obs: Om du inte är i ett projekt i **öppna projekt (< IDE_Name >)** kommer att inaktiveras._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Konfigurera integrerad terminalen i Visual Studio Code

### <a name="windows"></a>Windows 
Vi har åsidosatts standardgränssnitt för cmd i stället för PowerShell. När du klickar på på **öppna projekt (< IDE_Name >)**, finns i en kommandotolk: 

_Vill du tillåta shell: `C:\windows\System32\cmd.exe` (definierat som en inställning för arbetsytan) startas i terminalen?_

Svaret `yes` för att konfigurera Windows-gränssnittet fungerar sömlöst med kommandoradsgränssnittet i Azure ML-arbetsstationen.

### <a name="mac"></a>Mac
Att köra en `az` kommandot med Visual Studio Code integrerat terminal på Mac, måste du manuellt ange den `PATH` ska vara samma värde som `PATH` i projektets `.vscode/settings.json` fil under nyckeln `terminal.integrated.env.osx`. Du kan göra det genom att köra följande kommando i en terminal: `PATH=<PATH in .vscode/settings>`
