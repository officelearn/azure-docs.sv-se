---
title: Övervaka en Linux VM med en VM-tillägg | Microsoft Docs
description: Lär dig hur du använder tillägget Linux diagnostiska för att övervaka prestanda och diagnostikdata av Linux VM i Azure.
services: virtual-machines-linux
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: f54a11c5-5a0e-40ff-af6c-e60bd464058b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: Ning
ms.openlocfilehash: f1415e2cfbe48b287db5851bb8ebef1ff9251280
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "32193068"
---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Använd diagnostiktillägget för Linux för att övervaka prestanda och diagnostikdata för en virtuell Linux-dator

Det här dokumentet beskriver versionen 2.3 av diagnostiska tillägget Linux.

> [!IMPORTANT]
> Den här versionen är föråldrad och det kan vara opublicerad helst efter 30 juni 2018. Den har ersatts av version 3.0. Mer information finns i [dokumentationen för version 3.0 för Linux diagnostiska tillägget](../diagnostic-extension.md).

## <a name="introduction"></a>Introduktion

(**Observera**: utvidgning diagnostiska Linux är öppen källkod på [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) där den mest aktuella informationen på tillägget publiceras först. Du kanske vill kontrollera den [GitHub-sidan](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) första.)

Tillägget Linux diagnostiska hjälper användare-övervakaren Linux virtuella datorer som körs på Microsoft Azure. Det har följande funktioner:

* Samlar in och överför prestandainformation från Linux VM till användarens lagringstabellen, samt information om diagnostik och syslog.
* Kan du anpassa de mätvärden för data som samlas in och överföra.
* Gör att användarna kan ladda upp angivna loggfiler till en tabell med avsedda lagring.

I den aktuella versionen 2.3 innehåller följande information:

* Alla Linux Rsyslog loggar, inklusive system-, säkerhets- och programloggarna.
* Alla systemdata som har angetts på [webbplatsen för System Center Cross Platform lösningar](https://scx.codeplex.com/wikipage?title=xplatproviders).
* Loggfiler som angetts av användaren.

Det här tillägget fungerar med både klassiskt och Resource Manager distributionsmodellerna.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>Aktuell version av tillägget och utfasningen av äldre versioner

Den senaste versionen av tillägget är **2.3**, och **eventuella gamla versioner (2.0, 2.1 och 2.2) kommer föråldrad och Opublicerat slutet av det här året (2017)**. Om du har installerat tillägget Linux diagnostik med automatisk mindre versionsuppgradering inaktiverad rekommenderas starkt att du avinstallerar tillägget och installera om den med automatiska mindre versionsuppgradering aktiverad. Du kan åstadkomma detta genom att ange '2.*' som versionen om du installerar tillägget via Azure XPLAT CLI eller Powershell på klassiska (ASM) virtuella datorer. På ARM virtuella datorer du kan åstadkomma detta genom att inkludera ”” autoUpgradeMinorVersion ”: true' i mallen för distribution av virtuell dator. En ny installation av tillägget bör också ha den lägre versionen av automatiskt uppgradera alternativet är aktiverat.

## <a name="enable-the-extension"></a>Aktivera tillägget

Du kan aktivera det här tillägget med hjälp av den [Azure-portalen](https://portal.azure.com/#), Azure PowerShell eller Azure CLI-skript.

Om du vill visa och konfigurera systemet och prestanda data direkt från Azure-portalen, Följ [de här stegen på Azure-bloggen](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

Den här artikeln fokuserar på hur du aktiverar och konfigurerar tillägget med hjälp av Azure CLI-kommandona. På så sätt kan du läsa och visa data direkt från lagringstabellen.

Observera att konfigurationen metoderna som beskrivs här inte fungerar för Azure-portalen. Om du vill visa och konfigurera systemet och prestanda data direkt från Azure-portalen, måste tillägget aktiveras via portalen.

## <a name="prerequisites"></a>Förutsättningar

* **Azure Linux-agentens version 2.0.6 eller senare**.

  Observera att de flesta Azure VM Linux galleriavbildningar inkluderar version 2.0.6 eller senare. Du kan köra **WAAgent-version** bekräfta vilken version som är installerad på den virtuella datorn. Om den virtuella datorn kör en version som är tidigare än 2.0.6, du kan följa [instruktionerna på GitHub](https://github.com/Azure/WALinuxAgent "instruktioner") att uppdatera den.
* **Azure CLI**. Följ [vägledningen för att installera CLI](../../../cli-install-nodejs.md) att konfigurera Azure CLI-miljö på din dator. När Azure CLI är installerad kan du använda den **azure** från kommandoradsgränssnittet (Bash, Terminal eller Kommandotolken) att komma åt Azure CLI-kommandona. Exempel:

  * Kör **azure vm-tillägget set--hjälp** för närmare hjälp.
  * Kör **azure-inloggning** att logga in på Azure.
  * Kör **azure vm listan** att lista alla virtuella datorer som finns på Azure.
* Ett lagringskonto för att lagra data. Du måste namnet på ett lagringskonto som skapades tidigare och snabbtangent att överföra data till din lagring.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Använda Azure CLI-kommando för att aktivera tillägget Linux diagnostik

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>Scenario 1. Aktivera tillägget med standarddatauppsättningen

I version 2.3 eller senare innehåller standarddata som samlas in:

* Alla Rsyslog information (inklusive system-, säkerhets- och programloggarna).  
* En grundläggande uppsättning bas systemdata. Observera att alla data är beskrivs på den [System Center Cross Platform lösningar plats](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Om du vill aktivera extra data fortsätter du med stegen i Scenario 2 och 3.

Steg 1. Skapa en fil med namnet PrivateConfig.json med följande innehåll:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Steg 2. Kör **azure vm-tillägget anger vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --PrivateConfig.json** privat-config-sökvägen.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>Scenario 2. Anpassa övervakaren prestandamått

Det här avsnittet beskriver hur du anpassar prestanda och diagnostikdata tabell.

Steg 1. Skapa en fil som heter PrivateConfig.json med det innehåll som beskrevs i Scenario 1. Även skapa en fil med namnet PublicConfig.json. Ange de specifika data som du vill samla in.

För alla stöds providers och variabler, referera till den [System Center Cross Platform lösningar plats](https://scx.codeplex.com/wikipage?title=xplatproviders). Du kan ha flera frågor och lagra dem på flera tabeller genom att lägga till flera frågor till skriptet.

Rsyslog data samlas alltid som standard.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


Steg 2. Kör **azure vm-tillägget anger vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*'--privat-config-sökvägen PrivateConfig.json--offentliga-config-sökvägen PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>Scenario 3. Ladda upp en egen loggfiler

Det här avsnittet beskriver hur du samlar in och överföra specifika loggfiler till ditt lagringskonto. Du måste ange både sökvägen till loggfilen och namnet på tabellen där du vill lagra loggen. Du kan skapa flera loggfiler genom att lägga till flera poster i filtabellen/skriptet.

Steg 1. Skapa en fil som heter PrivateConfig.json med det innehåll som beskrevs i Scenario 1. Skapa sedan en annan fil som heter PublicConfig.json med följande innehåll:

```json
{
    "fileCfg" :
    [
        {
            "file" : "/var/log/mysql.err",
            "table" : "mysqlerr"
            }
    ]
}
```

Steg 2. Kör `azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json`.

Observera att den här inställningen i tillägget versioner före 2.3 alla loggar skrivs till `/var/log/mysql.err` kan kopieras till `/var/log/syslog` (eller `/var/log/messages` beroende på Linux-distro) samt. Om du vill undvika dubbla loggningen kan du undanta loggning av `local6` och loggar in rsyslog konfigurationen. Det beror på Linux-distro, men på ett Ubuntu 14.04 system är filen för att ändra `/etc/rsyslog.d/50-default.conf` och du kan ersätta raden `*.*;auth,authpriv.none -/var/log/syslog` till `*.*;auth,authpriv,local6.none -/var/log/syslog`. Det här problemet löses i den senaste versionen snabbkorrigering för 2.3 (2.3.9007), så om du har version 2.3 för tillägg för det här problemet inte bör sker. Om den fortfarande finns även efter att starta om den virtuella datorn, kontakta oss och hjälper oss att felsöka anledningen till att den senaste snabbkorrigeringsversionen inte installeras automatiskt.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>Scenario 4. Stoppa filtillägget samlar in loggar

Det här avsnittet beskrivs hur du stoppar tillägget från insamling av loggar. Observera att agenten övervakningsprocessen är fortfarande aktiv och körs med den här omkonfiguration. Om du vill stoppa agent övervakningsprocessen helt, kan du göra det genom att inaktivera tillägget. Kommandot för att inaktivera tillägget är `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

Steg 1. Skapa en fil som heter PrivateConfig.json med det innehåll som beskrevs i Scenario 1. Skapa en annan fil som heter PublicConfig.json med följande innehåll:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Steg 2. Kör **azure vm-tillägget anger vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*'--privat-config-sökvägen PrivateConfig.json--offentliga-config-sökvägen PublicConfig.json**.

## <a name="review-your-data"></a>Granska dina data

Prestanda och diagnostiska data lagras i en tabell i Azure Storage. Granska [hur du använder Azure Table Storage från Ruby](../../../cosmos-db/table-storage-how-to-use-ruby.md) att lära dig hur du kommer åt data i lagringstabellen med hjälp av Azure CLI-skript.

Dessutom kan du kan du använda följande UI-verktyg för att komma åt data:

1. Visual Studio Server Explorer. Gå till ditt lagringskonto. När den virtuella datorn körs under fem minuter, visas de fyra standardtabeller: ”LinuxCpu”, ”LinuxDisk”, ”LinuxMemory” och ”Linuxsyslog”. Dubbelklicka på tabellnamn för att visa data.
1. [Azure Lagringsutforskaren](https://azurestorageexplorer.codeplex.com/ "Azure Lagringsutforskaren").

![Bild](./media/diagnostic-extension/no1.png)

Om du har aktiverat fileCfg eller perfCfg (enligt beskrivningen i Scenario 2 och 3), kan du använda Visual Studio Server Explorer och Azure Lagringsutforskaren för att visa data som inte är standard.

## <a name="known-issues"></a>Kända problem

* Rsyslog information och anges av kunden loggfilen kan endast nås via skript.
