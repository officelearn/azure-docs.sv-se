---
title: Konfigurera en Azure Migrate-apparat med ett skript
description: Lär dig hur du konfigurerar en Azure Migrate-apparat med ett skript
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337693"
---
# <a name="set-up-an-appliance-with-a-script"></a>Konfigurera en apparat med ett skript

I den hÃ¤r artikeln beskrivs hur du konfigurerar [Azure Migrate-anordningen](deploy-appliance.md) med ett PowerShell-installationsskript.

Skriptet ger:
- Ett alternativ till att ställa in apparaten med hjälp av en OVA-mall, för bedömning och agentlös migrering av virtuella datorer med videoprogram.
- Ett alternativ till att konfigurera enheten med hjälp av en VHD-mall, för bedömning och migrering av virtuella hyper-virtuella datorer.
- För bedömning av fysiska servrar (eller virtuella datorer som du vill migrera som fysiska servrar) är skriptet den enda metoden för att konfigurera installationen av installationen av den.

## <a name="prerequisites"></a>Krav

Skriptet ställer in Azure Migrate-enheten på en befintlig fysisk dator eller virtuell dator.

- Datorn som fungerar som en apparat måste köra Windows Server 2016, med 32 GB minne, åtta virtuella processorer, cirka 80 GB disklagring och en extern virtuell växel. Det kräver en statisk eller dynamisk IP-adress och tillgång till Internet.
- Innan du distribuerar apparaten bör du granska detaljerade installationskrav för [virtuella datorer med VMware,](migrate-appliance.md#appliance---vmware)virtuella datorer med [hyper-vm](migrate-appliance.md#appliance---hyper-v)och [fysiska servrar](migrate-appliance.md#appliance---physical).
- Kör inte skriptet på en befintlig Azure Migrate-installation.


## <a name="download-the-script"></a>Ladda ner skriptet

1. Leta reda på den dator/virtuella datorn som fungerar som Azure Migrate-installation.
2. Gör följande på maskinen:

    - Om du vill använda apparaten med virtuella datorer med VMware eller virtuella virtuella datorer med hyper-V [laddar du ned](https://go.microsoft.com/fwlink/?linkid=2105112) mappen med blixtlås som innehåller installationsskriptet och MSI:erna.
    - Om du vill använda installationen med fysiska servrar hämtar du skriptet från Azure Migrate-portalen enligt beskrivningen i den här [självstudien](tutorial-assess-physical.md#set-up-the-appliance).

## <a name="verify-file-security"></a>Verifiera filsäkerhet

Kontrollera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hash för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Kontrollera att de genererade hash-värdena matchar dessa inställningar (för den senaste installationen):

    **Algoritm** | **Hash-värde**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cdd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>Kör skriptet

Så här gör skriptet:

- Installerar agenter och ett webbprogram.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämtar och installerar en IIS rewritable modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en registernyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg- och konfigurationsfiler enligt följande:
    - **Config-filer**: %ProgramData%\Microsoft Azure\Config
    - **Loggfiler**: %ProgramData%\Microsoft Azure\Loggar

Kör skriptet så här:

1. Extrahera den zippade filen till en mapp på datorn som är värd för apparaten.
2. Starta PowerShell på datorn med administratörsbehörighet (förhöjd).
3. Ändra PowerShell-katalogen till mappen som innehåller innehållet som extraherats från den nedladdade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1** enligt följande:

    - För VMware: 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - För Hyper-V:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - För fysiska servrar:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. När skriptet har körts startar det programmet för apparatwebb så att du kan konfigurera apparaten. Om du stöter på några problem kan du visa skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Tidsstämpel</em>.log.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat apparaten med skriptet följer du dessa instruktioner:

- Ställ in apparaten för [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Ställ in apparaten för [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Konfigurera apparaten för [fysiska servrar](how-to-set-up-appliance-physical.md).