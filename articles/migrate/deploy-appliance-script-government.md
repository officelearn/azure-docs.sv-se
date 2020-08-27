---
title: Konfigurera en Azure Migrate-apparat i Azure Government
description: Lär dig hur du konfigurerar en Azure Migrate-apparat i Azure Government
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: ffea966c58909ecaab0da13a4204295ecb193895
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936800"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Konfigurera en installation i Azure Government 

Följ den här artikeln för att distribuera en [Azure Migrate-apparat](./migrate-appliance-architecture.md) för virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar i ett Azure Government moln. Du kan köra ett skript för att skapa installationen och kontrol lera att den kan ansluta till Azure. Om du vill konfigurera en installation i det offentliga molnet följer du [den här artikeln](deploy-appliance-script.md).


> [!NOTE]
> Alternativet för att distribuera en installation med hjälp av en mall (för virtuella VMware-datorer och virtuella Hyper-V-datorer) stöds inte i Azure Government.


## <a name="prerequisites"></a>Förutsättningar

Skriptet konfigurerar Azure Migrate-installationen på en befintlig fysisk eller virtuell dator.

- Datorn som fungerar som installations program måste köra Windows Server 2016 med 32 GB minne, åtta virtuella processorer, runt 80 GB disk lagring och en extern virtuell växel. Den kräver en statisk eller dynamisk IP-adress och till gång till Internet.
- Innan du distribuerar installationen bör du gå igenom detaljerade installations krav för [virtuella VMware-datorer](migrate-appliance.md#appliance---vmware), [virtuella Hyper-V-datorer](migrate-appliance.md#appliance---hyper-v)och [fysiska servrar](migrate-appliance.md#appliance---physical).
- Kör inte skriptet på en befintlig Azure Migrate-apparat.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurera enheten för VMware

Om du vill konfigurera en installation för VMware laddar du ned en zippad fil från Azure Portal och extraherar innehållet. Du kör PowerShell-skriptet för att starta installations programmets webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du enheten med Azure Migrate-projektet.

### <a name="download-the-script"></a>Hämta skriptet

1.  I **mål**  >  **servrar**för migrering  >  **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2.  I **identifiera datorer**  >  **är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere hypervisor**.
3.  Klicka på **Ladda ned**för att ladda ned den zippade filen. 


### <a name="verify-file-security"></a>Verifiera fil säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Kontrol lera den senaste versionen av produkten och hash-värdet:

    **Integritetsalgoritm** | **Ladda ned** | **SHA256**
    --- | --- | ---
    VMware (85 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140337) | 31b1bfdd4fc29b3eb923c7c6e7a898af79b7cac0404426bea18809def2284188


### <a name="run-the-script"></a>Kör skriptet

Så här fungerar skriptet:

- Installerar agenter och ett webb program.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämtar och installerar en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg-och konfigurationsfiler enligt följande:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den zippade filen till en mapp på den dator som ska vara värd för-enheten. Kontrol lera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på datorn med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp som innehåller innehållet som extraheras från den hämtade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande: 
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. När skriptet har körts startar det program webb programmet så att du kan konfigurera installationen. Om det uppstår några problem granskar du skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrol lera att enheten kan ansluta till Azure-URL: er för [myndighets moln](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurera enheten för Hyper-V

Om du vill konfigurera enheten för Hyper-V laddar du ned en zippad fil från Azure Portal och extraherar innehållet. Du kör PowerShell-skriptet för att starta installations programmets webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du enheten med Azure Migrate-projektet.

### <a name="download-the-script"></a>Hämta skriptet

1.  I **mål**  >  **servrar**för migrering  >  **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2.  I **identifiera datorer**  >  **är dina datorer virtualiserade?** väljer du **Ja, med Hyper-V**.
3.  Klicka på **Ladda ned**för att ladda ned den zippade filen. 


### <a name="verify-file-security"></a>Verifiera fil säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Kontrol lera den senaste versionen av produkten och hash-värdet:

    **Scenario** | **Ladda ned** | **SHA256**
    --- | --- | ---
    Hyper-V (85 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>Kör skriptet

Så här fungerar skriptet:

- Installerar agenter och ett webb program.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämtar och installerar en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg-och konfigurationsfiler enligt följande:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den zippade filen till en mapp på den dator som ska vara värd för-enheten. Kontrol lera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på datorn med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp som innehåller innehållet som extraheras från den hämtade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. När skriptet har körts startar det program webb programmet så att du kan konfigurera installationen. Om det uppstår några problem granskar du skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrol lera att enheten kan ansluta till Azure-URL: er för [myndighets moln](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Konfigurera enheten för fysiska servrar

Om du vill konfigurera en installation för VMware laddar du ned en zippad fil från Azure Portal och extraherar innehållet. Du kör PowerShell-skriptet för att starta installations programmets webbapp. Du konfigurerar installationen och konfigurerar den för första gången. Sedan registrerar du enheten med Azure Migrate-projektet.

### <a name="download-the-script"></a>Hämta skriptet

1.  I **mål**  >  **servrar**för migrering  >  **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2.  I **identifiera datorer**  >  **är dina datorer virtualiserade?**, Välj **inte virtualiserad/övrigt**.
3.  Klicka på **Ladda ned**för att ladda ned den zippade filen. 


### <a name="verify-file-security"></a>Verifiera fil säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Kontrol lera den senaste versionen av produkten och hash-värdet:

    **Scenario** | **Hämta*** | **Hash-värde**
    --- | --- | ---
    Fysisk (85 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140338) | 1545f9ce8874cedef6347c1a1332f8b5eabd6811a017440a2382525fb0430309
          

### <a name="run-the-script"></a>Kör skriptet

Så här fungerar skriptet:

- Installerar agenter och ett webb program.
- Installerar Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämtar och installerar en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständiga inställningar för Azure Migrate.
- Skapar logg-och konfigurationsfiler enligt följande:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet så här:

1. Extrahera den zippade filen till en mapp på den dator som ska vara värd för-enheten. Kontrol lera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på datorn med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp som innehåller innehållet som extraheras från den hämtade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1**enligt följande: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. När skriptet har körts startar det program webb programmet så att du kan konfigurera installationen. Om det uppstår några problem granskar du skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verifiera åtkomst

Kontrol lera att enheten kan ansluta till Azure-URL: er för [myndighets moln](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Nästa steg

När du har distribuerat enheten måste du konfigurera den för första gången och registrera den med Azure Migrate projektet.

- Konfigurera enheten för [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Konfigurera enheten för [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Konfigurera enheten för [fysiska servrar](how-to-set-up-appliance-physical.md).
