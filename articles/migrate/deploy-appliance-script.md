---
title: Konfigurera en Azure Migrate-apparat med ett skript
description: Lär dig hur du konfigurerar en Azure Migrate-apparat med ett skript
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: faed7f96ea8c1850af5523d35f9f891011a48df8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537720"
---
# <a name="set-up-an-appliance-with-a-script"></a>Konfigurera en apparat med ett skript

I den hÃ¤r artikeln beskrivs hur du konfigurerar [Azure Migrate-anordningen](deploy-appliance.md) med ett PowerShell-installationsskript, vmware-virtuella datorer och virtuella virtuella hyper-V-datorer. Om du vill konfigurera apparaten för fysiska servrar [läser du den här artikeln](how-to-set-up-appliance-physical.md).


Du kan distribuera apparaten med ett par metoder:


- Använda en mall för virtuella datorer (VMware) (OVA) eller virtuella datorer med hyper-V (VHD).
- Använda ett skript. Detta är den metod som beskrivs i den här artikeln. Skriptet ger:
    - Ett alternativ till att ställa in apparaten med hjälp av en OVA-mall, för bedömning och agentlös migrering av virtuella datorer med videoprogram.
    - Ett alternativ till att konfigurera enheten med hjälp av en VHD-mall, för bedömning och migrering av virtuella hyper-virtuella datorer.
    - För bedömning av fysiska servrar (eller virtuella datorer som du vill migrera som fysiska servrar) är skriptet den enda metoden för att konfigurera installationen av installationen av den.
    - Ett sätt att distribuera installationen i Azure Government.


När du har skapat installationen kontrollerar du att den kan ansluta till Azure Migrate. Du konfigurerar sedan installationen för första gången och registrerar den med Azure Migrate-projektet.


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
    - Exempel på användning för offentligt moln:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Exempel på användning för myndighetsmoln:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip```

3. Verifiera de genererade hash-värdena:

    - För det offentliga molnet (för den senaste installationen):

        **Algoritm** | **Hash-värde**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - För Azure-myndighet (för den senaste installationen):

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
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande:
    - För det offentliga molnet:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
    - För Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
   
5. När skriptet har körts startar det programmet för apparatwebb så att du kan konfigurera apparaten. Om du stöter på några problem läser du skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Tidsstämpel</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Se till att installationen kan ansluta till Azure-URL:er för [offentliga](migrate-appliance.md#public-cloud-urls) moln och [myndighetsmoln](migrera-appliance.md#government-cloud-urls.


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
    - Exempel på användning för offentligt moln:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Exempel på användning för myndighetsmoln:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Verifiera de genererade hash-värdena:

    - För det offentliga molnet (för den senaste installationen):

        **Algoritm** | **Hash-värde**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - För Azure-myndighet (för den senaste installationen):

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
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande:
    - För det offentliga molnet:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
    - För Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ```
   
5. När skriptet har körts startar det programmet för apparatwebb så att du kan konfigurera apparaten. Om du stöter på några problem läser du skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Tidsstämpel</em>.log.

### <a name="verify-access"></a>Verifiera åtkomst

Se till att installationen kan ansluta till Azure-URL:er för [offentliga](migrate-appliance.md#public-cloud-urls) moln och [myndighetsmoln](migrera-appliance.md#government-cloud-urls.



## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar en mall med en mall eller för fysiska servrar finns i följande artiklar:

- Ställ in apparaten för [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Ställ in apparaten för [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Konfigurera apparaten för [fysiska servrar](how-to-set-up-appliance-physical.md).