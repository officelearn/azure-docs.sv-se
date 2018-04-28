---
title: Med hjälp av Azure CLI på Windows | Microsoft Docs
description: Med hjälp av Azure CLI i Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: cynthn
ms.openlocfilehash: ece7806dd757ee5169a25ac83590aa4a740d6dcc
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="using-the-azure-cli-on-windows"></a>Med hjälp av Azure CLI i Windows

Azure-kommandoradsgränssnittet (CLI) tillhandahåller en kommandorad och skriptmiljö för att skapa och hantera Azure-resurser. Azure CLI är tillgänglig för macOS, Linux och Windows-operativsystem. I dessa operativsystem kan CLI-kommandona är identiska, men operativsystemet specifika skriptsyntax kan skilja sig.

Det här dokumentet beskriver sätt att Azure CLI kan installeras och körs på Windows och information för varje syntaktiska överväganden. Djupgående Azure CLI-dokumentationen finns [Azure CLI dokumentationen]( https://docs.microsoft.com/cli/azure).

## <a name="windows-subsystem-for-linux"></a>Windows-undersystem för Linux

Windows-undersystemet för Linux (WSL) tillhandahåller en Ubuntu Linux-miljö på årsdagar för Windows 10 och senare versioner. När aktiverat tillhandahåller WSL en inbyggd Bash-upplevelse, som kan användas för att skapa och köra Azure CLI-skript. Eftersom WSL tillhandahåller en inbyggd Bash, kan Azure CLI-skript delas mellan macOS, Linux och Windows utan modifiering.

Utför följande för att använda Azure CLI i WSL.

|Aktivitet | Instruktioner |
|---|---|
| Aktivera WSL | [Installera WSL dokumentation ](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) |
| Installera Azure CLI |[Installera CLI på WSL/Ubuntu 14.04](https://docs.microsoft.com/cli/azure/install-az-cli2#ubuntu)|

## <a name="powershell"></a>PowerShell

Azure CLI kan köras inbyggt i Windows. Azure CLI-paketet har installerats på Windows-operativsystemet i den här konfigurationen och kommandon kan köras från PowerShell. I den här konfigurationen, kan Azure CLI-kommandon och skript köras på någon version av Windows, men det krävs specifika skriptsyntax för plattformen. Därför kan skript inte nödvändigtvis delas mellan macOS, Linux och Windows utan modifiering.

Installera paketet med dessa instruktioner om du vill använda Azure CLI på Windows [installera CLI på Windows](https://docs.microsoft.com/cli/azure/install-az-cli2#windows).

## <a name="docker-image"></a>Docker-bild

När du använder Docker för Windows, kan en Docker-avbildning startas med Azure CLI. Den här avbildningen baseras på Linux och innehåller en inbyggd Bash-upplevelse.  När du använder Docker för Windows och Azure CLI-avbildningen skript för att delas mellan macOS, Linux och Windows. 

Se till att Docker för Windows körs och kör följande kommando för att använda Azure CLI på Docker för Windows.

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

När slutfört, ett Bash-sessionen startas som är förinstallerade med Azure CLI-verktygen.

## <a name="next-steps"></a>Nästa steg

[CLI-exempel för virtuella Azure-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[CLI prover för Azure-Webbappar](../../app-service/app-service-cli-samples.md)

[CLI prover för Azure SQL](../../sql-database/sql-database-cli-samples.md)
