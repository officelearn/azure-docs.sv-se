---
title: Konfigurera en Azure Migrate-installation i Azure Government
description: Lär dig hur du konfigurerar en Azure Migrate-installation i Azure Government
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: fd97161ffa075a6165ea963ef80bfabf8904576e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726740"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Konfigurera en apparat i Azure Government 

Följ den här artikeln om du vill distribuera en [Azure Migrate-installation för](deploy-appliance.md) virtuella datorer med VMware, virtuella datorer med hyper-vm och fysiska servrar i ett Azure Government-moln. Du kör ett skript för att skapa installationen och verifiera att den kan ansluta till Azure. Om du vill konfigurera en apparat i det offentliga molnet följer du den [här artikeln](deploy-appliance-script.md).


> [!NOTE]
> Alternativet att distribuera en installation med hjälp av en mall (för virtuella datorer med VMware och virtuella datorer med hyper-vm) stöds inte i Azure Government.


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
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip MD5```

3. Verifiera de genererade hash-värdena. För den senaste apparatversionen:

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | 6316bcc8bc932204295bfe33f4be3949
          

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
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. När skriptet har körts startar det programmet för apparatwebb så att du kan konfigurera apparaten. Om du stöter på några problem läser du skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Tidsstämpel</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Se till att installationen kan ansluta till Azure-url:er för [myndighetsmoln](migrate-appliance.md#government-cloud-urls).


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
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Verifiera de genererade hash-värdena. För den senaste apparatversionen:

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | 717f8b9185f565006b5aff0215ecadac
          

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
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. När skriptet har körts startar det programmet för apparatwebb så att du kan konfigurera apparaten. Om du stöter på några problem läser du skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Tidsstämpel</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Se till att installationen kan ansluta till Azure-url:er för [myndighetsmoln](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Konfigurera apparaten för fysiska servrar

Om du vill konfigurera installationen av installationen av VMware hämtar du en zippad fil från Azure-portalen och extraherar innehållet. Du kör PowerShell-skriptet för att starta appen för apparatwebb. Du ställer in apparaten och konfigurerar den för första gången. Sedan registrerar du installationen med Azure Migrate-projektet.

### <a name="download-the-script"></a>Ladda ner skriptet

1.  Klicka på **Identifiera**i **migreringsmålsservrar:** > **Servers** > **Serverutvärdering.**
2.  I **Upptäck-datorer** >  **Not virtualized/Other****Virtualiseras dina datorer?**
3.  Klicka på **Ladda ner**, för att ladda ner zippade filen. 


### <a name="verify-file-security"></a>Verifiera filsäkerhet

Kontrollera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hash för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5```

3. Verifiera de genererade hash-värdena. För den senaste apparatversionen:

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | f81c155fc4a1409901caea948713913f
          

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
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. När skriptet har körts startar det programmet för apparatwebb så att du kan konfigurera apparaten. Om du stöter på några problem läser du skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Tidsstämpel</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Se till att installationen kan ansluta till Azure-url:er för [myndighetsmoln](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Nästa steg

När du har distribuerat installationen måste du konfigurera den för första gången och registrera den med Azure Migrate-projektet.

- Ställ in apparaten för [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Ställ in apparaten för [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Konfigurera apparaten för [fysiska servrar](how-to-set-up-appliance-physical.md).