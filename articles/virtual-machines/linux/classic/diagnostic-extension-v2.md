---
title: Övervakning av en Linux VM med en VM-tillägg | Microsoft Docs
description: Lär dig hur du använder Linux-Diagnostiktillägget för att övervaka prestanda och diagnostikdata för en Linux-VM i Azure.
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
ms.openlocfilehash: 13d7594c15959661f3f9c3ab2165739719beac07
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308229"
---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Använd diagnostiktillägget för Linux för att övervaka prestanda och diagnostikdata för en virtuell Linux-dator

Det här dokumentet beskrivs version 2.3 av Linux-Diagnostiktillägget.

> [!IMPORTANT]
> Den här versionen är inaktuell och det kan vara opublicerad som helst efter den 30 juni 2018. Den har ersatts av version 3.0. Mer information finns i den [dokumentationen för version 3.0 av Linux-Diagnostiktillägget](../diagnostic-extension.md).

## <a name="introduction"></a>Introduktion

(**Obs**: The Linux-Diagnostiktillägget är öppen källkod på [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) där den senaste informationen på tillägget publiceras först. Du kanske vill kontrollera den [GitHub-sidan](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) första.)

Linux-Diagnostiktillägget kan en användare Övervakare Linux-datorer som körs på Microsoft Azure. Den har följande funktioner:

* Samlar in och överför prestandainformation från Linux-VM till användarens storage-tabell, samt diagnostik- och syslog.
* Kan du anpassa mätvärden för data som samlas in och laddat upp.
* Gör att användarna kan ladda upp angivna loggfiler till en avsedda lagringstabell.

I den aktuella versionen 2.3 innehåller följande information:

* Alla Linux Rsyslog loggar, inklusive system-, säkerhets- och programloggarna.
* Alla systemdata som har angetts på [mellan Plattformslösningar i System Center-webbplatsen](https://scx.codeplex.com/wikipage?title=xplatproviders).
* Användardefinierade loggfiler.

Det här tillägget fungerar med både klassiska och Resource Manager-distributionsmodellen.

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>Aktuell version av tillägget och utfasningen av äldre versioner

Den senaste versionen av tillägget är **2.3**, och **eventuella gamla versioner (2.0, 2.1 och 2.2) kommer inaktuell och Opublicerat slutet av det här året (2017)**. Om du har installerat Linux-diagnostiktillägget med automatisk mindre versionsuppgradering inaktiverad rekommenderas starkt att du avinstallerar tillägget och installerar om den med automatiska mindre versionsuppgradering aktiverat. Du kan åstadkomma detta genom att ange ”2.*” som versionen om du installerar tillägget via Azure XPLAT CLI eller Powershell på klassiska (ASM) virtuella datorer. På ARM virtuella datorer, du kan åstadkomma detta genom att inkludera ' ”autoUpgradeMinorVersion”: true' i mallen för distribution av virtuell dator. En ny installation av tillägget bör också ha den lägre versionen av automatisk uppgradera alternativet är aktiverat.

## <a name="enable-the-extension"></a>Aktivera tillägget

Du kan aktivera det här tillägget med hjälp av den [Azure-portalen](https://portal.azure.com/#), Azure PowerShell eller Azure CLI-skript.

Om du vill visa och konfigurera system- och prestanda data direkt från Azure-portalen, Följ [de här stegen på Azure-bloggen](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/).

Den här artikeln handlar om hur du aktiverar och konfigurerar tillägget med hjälp av Azure CLI-kommandon. På så sätt kan du läsa och visa data direkt från storage-tabell.

Observera att konfigurationsmetoder som beskrivs här inte fungerar för Azure-portalen. Om du vill visa och konfigurera system- och prestanda data direkt från Azure-portalen, måste tillägget aktiveras via portalen.

## <a name="prerequisites"></a>Förutsättningar

* **Azure Linux-Agent version 2.0.6 eller senare**.

  Observera att de flesta Azure VM Linux galleriavbildningar inkluderar version 2.0.6 eller senare. Du kan köra **WAAgent-version** att kontrollera vilken version som är installerad på den virtuella datorn. Om den virtuella datorn kör en version som är tidigare än 2.0.6, kan du följa [de här instruktionerna om GitHub](https://github.com/Azure/WALinuxAgent "instruktioner") att uppdatera den.
* **Azure CLI**. Följ [den här vägledningen för att installera CLI](../../../cli-install-nodejs.md) att konfigurera Azure CLI-miljö på din dator. När Azure CLI är installerat, kan du använda den **azure** från ditt kommandoradsgränssnitt (Bash, Terminal eller kommandotolk) att komma åt Azure CLI-kommandon. Exempel:

  * Kör **azure vm-tillägget set--help** för detaljerad hjälpinformation.
  * Kör **azure-inloggning** att logga in på Azure.
  * Kör **azure vm-lista** att lista alla virtuella datorer som du har i Azure.
* Ett lagringskonto för att lagra data. Du behöver namnet på ett lagringskonto som skapades tidigare och en åtkomstnyckel för att ladda upp data till din lagring.

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Använd Azure CLI-kommando för att aktivera Linux-Diagnostiktillägget

### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>Scenario 1. Aktivera tillägget med standarddatauppsättningen

I version 2.3 eller senare innehåller de standarddata som samlas in:

* Alla Rsyslog information (inklusive system-, säkerhets- och programloggarna).  
* En grundläggande uppsättning basis systemdata. Observera att en fullständig uppsättning data är beskrivningen på den [mellan Plattformslösningar i System Center-webbplatsen](https://scx.codeplex.com/wikipage?title=xplatproviders).
  Om du vill aktivera extra data fortsätter du med stegen i Scenario 2 och 3.

Steg 1. Skapa en fil med namnet PrivateConfig.json med följande innehåll:

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

Steg 2. Kör **azure vm-tillägget anger vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.\* --private-config-path PrivateConfig.json**.

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>Scenario 2. Anpassa övervaka prestandamått

Det här avsnittet beskrivs hur du anpassar den prestanda och diagnostikdata tabellen.

Steg 1. Skapa en fil med namnet PrivateConfig.json med det innehåll som beskrevs i Scenario 1. Också skapa en fil med namnet PublicConfig.json. Ange vilka data som du vill samla in.

För alla leverantörer och variabler kan referera till den [mellan Plattformslösningar i System Center-webbplatsen](https://scx.codeplex.com/wikipage?title=xplatproviders). Du kan ha flera frågor och lagra dem i flera tabeller genom att lägga till fler frågor till skriptet.

Rsyslog-data samlas alltid som standard.

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


Steg 2. Kör **azure vm-tillägget anger vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*'--private-config-path PrivateConfig.json--public-config-path PublicConfig.json**.

### <a name="scenario-3-upload-your-own-log-files"></a>Scenario 3. Ladda upp en egen loggfiler

Det här avsnittet beskriver hur du samlar in och ladda upp specifika loggfiler till ditt lagringskonto. Du måste ange både sökvägen till loggfilen och namnet på tabellen där du vill lagra loggen. Du kan skapa flera loggfiler genom att lägga till flera poster i filtabellen/skriptet.

Steg 1. Skapa en fil med namnet PrivateConfig.json med det innehåll som beskrevs i Scenario 1. Skapa sedan en annan fil som heter PublicConfig.json med följande innehåll:

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

Observera att den här inställningen i tillägget versioner före 2.3 alla loggar skrivs till `/var/log/mysql.err` kan kopieras till `/var/log/syslog` (eller `/var/log/messages` beroende på Linux-distribution) samt. Om du vill undvika dubbla loggning kan du utesluta loggning av `local6` anläggning loggar i rsyslog konfigurationen. Det beror på Linux-distribution, men på ett Ubuntu 14.04 filen för att ändra är `/etc/rsyslog.d/50-default.conf` och du kan ersätta raden `*.*;auth,authpriv.none -/var/log/syslog` till `*.*;auth,authpriv,local6.none -/var/log/syslog`. Det här problemet löses i den senaste versionen snabbkorrigering av 2.3 (2.3.9007), så om du har tilläggsversion 2.3 kan det här problemet inte bör inträffa. Kontakta oss och hjälper oss att felsöka anledningen till att den senaste snabbkorrigeringsversionen inte installeras automatiskt om den fortfarande finns även efter att starta om den virtuella datorn.

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>Scenario 4. Stoppa tillägget samlar in loggar

Det här avsnittet beskrivs hur du stoppar tillägget från att samla in loggar. Observera att agenten övervakningsprocessen fortfarande igång även med den här omkonfiguration. Om du vill stoppa agenten övervakningsprocessen helt kan göra du det genom att inaktivera tillägget. Kommandot för att inaktivera tillägget är `azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'`.

Steg 1. Skapa en fil med namnet PrivateConfig.json med det innehåll som beskrevs i Scenario 1. Skapa en annan fil med namnet PublicConfig.json med följande innehåll:

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


Steg 2. Kör **azure vm-tillägget anger vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*'--private-config-path PrivateConfig.json--public-config-path PublicConfig.json**.

## <a name="review-your-data"></a>Granska dina data

Prestanda och diagnostiska data lagras i en Azure Storage-tabell. Granska [hur du använder Azure Table Storage från Ruby](../../../cosmos-db/table-storage-how-to-use-ruby.md) att lära dig hur du kommer åt data i storage-tabell med hjälp av Azure CLI-skript.

Du kan dessutom använda följande UI-verktyg för att komma åt data:

1. Visual Studio Server Explorer. Gå till ditt lagringskonto. När den virtuella datorn körs i ungefär fem minuter, visas de fyra standardtabeller: ”LinuxCpu”, ”LinuxDisk”, ”LinuxMemory” och ”Linuxsyslog”. Dubbelklicka på tabellnamn för att visa data.
1. [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

![image](./media/diagnostic-extension/no1.png)

Om du har aktiverat fileCfg eller perfCfg (enligt Scenario 2 och 3) kan använda du Visual Studio Server Explorer och Azure Storage Explorer för att visa data för icke-standard.

## <a name="known-issues"></a>Kända problem

* Rsyslog information och kunden angivna loggfilen kan bara kommas åt via skript.
