---
title: Konfigurera en Azure Migrate-apparat med ett skript
description: Lär dig hur du konfigurerar en Azure Migrate-apparat med ett skript
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 0c4d85909bbfa623b5ad8590e973250474d9d95a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676300"
---
# <a name="set-up-an-appliance-with-a-script"></a>Konfigurera en apparat med ett skript

Följ den här artikeln om du vill skapa en [Azure Migrate-apparat](deploy-appliance.md) för utvärdering/migrering av virtuella datorer och virtuella datorer med hyper-v.Follow this article to create an Azure Migrate appliance for the assessment/migration of VMware VMs, and Hyper-VMs. Du kör ett skript för att skapa en installation och verifiera att den kan ansluta till Azure. 

Du kan distribuera installationen för virtuella och virtuella datorer med virtuella datorer med hjälp av ett skript eller använda en mall som du hämtar från Azure-portalen. Det är användbart att använda ett skript om du inte kan skapa en virtuell dator med den hämtade mallen.

- Om du vill använda en mall följer du självstudierna för [VMware](tutorial-prepare-vmware.md) eller [Hyper-V](tutorial-prepare-hyper-v.md).
- Om du vill konfigurera en installation för fysiska servrar kan du bara använda ett skript. Följ [den här artikeln](how-to-set-up-appliance-physical.md).
- Om du vill konfigurera en installation i ett Azure Government-moln följer du [den här artikeln](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Krav

Skriptet ställer in Azure Migrate-enheten på en befintlig fysisk dator eller virtuell dator.

- Datorn som fungerar som en apparat måste köra Windows Server 2016, med 32 GB minne, åtta virtuella processorer, cirka 80 GB disklagring och en extern virtuell växel. Det kräver en statisk eller dynamisk IP-adress och tillgång till Internet.
- Innan du distribuerar apparaten bör du granska detaljerade installationskrav för [virtuella datorer med VMware,](migrate-appliance.md#appliance---vmware)virtuella datorer med [hyper-vm](migrate-appliance.md#appliance---hyper-v)och [fysiska servrar](migrate-appliance.md#appliance---physical).
- Kör inte skriptet på en befintlig Azure Migrate-installation.

## <a name="set-up-the-appliance-for-vmware"></a>Ställ in apparaten för VMware

Om du vill konfigurera installationen av installationen av VMware hämtar du en zippad fil från Azure-portalen och extraherar innehållet. Du kör PowerShell-skriptet för att starta appen för apparatwebb. Du ställer in apparaten och konfigurerar den för första gången. Sedan registrerar du installationen med Azure Migrate-projektet.

### <a name="download-the-script"></a>Ladda ner skriptet

1.  Klicka på **Identifiera**i **migreringsmålsservrar:** > **Servers** > **Serverutvärdering.**
2.  I **Upptäck-datorer** >  **Yes, with VMWare vSphere hypervisor****Virtualiseras dina datorer?**
3.  Klicka på **Ladda ner**, för att ladda ner zippade filen. 


### <a name="verify-file-security"></a>Verifiera filsäkerhet

Kontrollera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hash för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Verifiera det genererade hash-värdet. För den senaste apparatversionen:

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c



### <a name="run-the-script"></a>Kör skriptet

Så här gör skriptet:

- Installerar agenter och ett webbprogram.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämtar och installerar en IIS rewritable modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en registernyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg- och konfigurationsfiler enligt följande:
    - **Config-filer**: %ProgramData%\Microsoft Azure\Config
    - **Loggfiler**: %ProgramData%\Microsoft Azure\Loggar

Kör skriptet så här:

1. Extrahera den zippade filen till en mapp på datorn som är värd för apparaten. Kontrollera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på datorn med administratörsbehörighet (förhöjd).
3. Ändra PowerShell-katalogen till mappen som innehåller innehållet som extraherats från den nedladdade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. När skriptet har körts startar det programmet för apparatwebb så att du kan konfigurera apparaten. Om du stöter på några problem läser du skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Tidsstämpel</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Se till att installationen kan ansluta till Azure-url:er för det [offentliga](migrate-appliance.md#public-cloud-urls) molnet.

## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurera apparaten för Hyper-V

Om du vill konfigurera installationen av installationen av Hyper-V hämtar du en zippad fil från Azure-portalen och extraherar innehållet. Du kör PowerShell-skriptet för att starta appen för apparatwebb. Du ställer in apparaten och konfigurerar den för första gången. Sedan registrerar du installationen med Azure Migrate-projektet.

### <a name="download-the-script"></a>Ladda ner skriptet

1.  Klicka på **Identifiera**i **migreringsmålsservrar:** > **Servers** > **Serverutvärdering.**
2.  I **Upptäck-datorer** >  **Yes, with Hyper-V****Virtualiseras dina datorer?**
3.  Klicka på **Ladda ner**, för att ladda ner zippade filen. 


### <a name="verify-file-security"></a>Verifiera filsäkerhet

Kontrollera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hash för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Verifiera de genererade hash-värdena. För den senaste apparatversionen:

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cdd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-script"></a>Kör skriptet

Så här gör skriptet:

- Installerar agenter och ett webbprogram.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämtar och installerar en IIS rewritable modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en registernyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg- och konfigurationsfiler enligt följande:
    - **Config-filer**: %ProgramData%\Microsoft Azure\Config
    - **Loggfiler**: %ProgramData%\Microsoft Azure\Loggar

Kör skriptet så här:

1. Extrahera den zippade filen till en mapp på datorn som är värd för apparaten. Kontrollera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på datorn med administratörsbehörighet (förhöjd).
3. Ändra PowerShell-katalogen till mappen som innehåller innehållet som extraherats från den nedladdade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. När skriptet har körts startar det programmet för apparatwebb så att du kan konfigurera apparaten. Om du stöter på några problem läser du skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Tidsstämpel</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Se till att installationen kan ansluta till Azure-url:er för det [offentliga](migrate-appliance.md#public-cloud-urls) molnet.

## <a name="next-steps"></a>Nästa steg

När du har distribuerat installationen måste du konfigurera den för första gången och registrera den med Azure Migrate-projektet.

- Ställ in apparaten för [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Ställ in apparaten för [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
