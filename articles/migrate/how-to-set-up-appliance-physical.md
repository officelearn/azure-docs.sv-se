---
title: Konfigurera en Azure Migrate-installation för fysiska servrar
description: Lär dig hur du konfigurerar en Azure Migrate-installation för fysisk serverutvärdering.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: b60a30e5e30ee81cbaca7d5e4691ccedac2462b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598178"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Konfigurera en apparat för fysiska servrar

I den hÃ¤r artikeln beskrivs hur du konfigurerar Azure Migrate-anordningen om du bedömer fysiska servrar med verktyget Azure Migrate: Server Assessment.

Azure Migrate-installationen är en lättviktsinstallation som används av Azure Migrate Server Assessment för att göra följande:

- Upptäck lokala servrar.
- Skicka metadata och prestandadata för identifierade servrar till Azure Migrate Server Assessment.

[Läs mer](migrate-appliance.md) om Azure Migrate-enheten.


## <a name="appliance-deployment-steps"></a>Åtgärder för distribution av apparater

Så här ställer du in apparaten:
- Hämta en zippad fil med Azure Migrate-installationsskript från Azure-portalen.
- Extrahera innehållet från den zippade filen. Starta PowerShell-konsolen med administratörsbehörighet.
- Kör PowerShell-skriptet för att starta programmet för apparatwebb.
- Konfigurera installationen för första gången och registrera den med Azure Migrate-projektet.

## <a name="download-the-installer-script"></a>Hämta installationsskriptet

Ladda ner den zippade filen för apparaten.

1. Klicka på **Identifiera**i **migreringsmålsservrar:** > **Servers** > **Serverutvärdering.**
2. **I** **Upptäck-datorer** > **Virtualiseras dina datorer?**
3. Klicka på **Hämta** om du vill hämta filen med dragkedja.

    ![Ladda ner vm](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrollera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hash för DEN VIRTUELLA HÅRDDISKEN
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  För den senaste installationen version, den genererade hash bör matcha dessa [inställningar](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical#verify-security).



## <a name="run-the-azure-migrate-installer-script"></a>Köra Azure Migrate-installationsskriptet
Installationsskriptet gör följande:

- Installerar agenter och ett webbprogram för identifiering och bedömning av fysiska servrar.
- Installera Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Ladda ner och installerar en IIS rewritable modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en registernyckel (HKLM) med beständig inställningsinformation för Azure Migrate.
- Skapar följande filer under sökvägen:
    - **Config-filer**: %Programdata%\Microsoft Azure\Config
    - **Loggfiler**: %Programdata%\Microsoft Azure\Loggar

Kör skriptet på följande sätt:

1. Extrahera den zippade filen till en mapp på servern som ska vara värd för apparaten.
2. Starta PowerShell på ovanstående server med administrativ (förhöjd) behörighet.
3. Ändra PowerShell-katalogen till mappen där innehållet har extraherats från den nedladdade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1** genom att köra följande kommando:
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
Skriptet startar programmet för apparat när det är klart.

Vid eventuella problem kan du komma åt skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>tidsstämpel</em>.log för felsökning.

> [!NOTE]
> Kör inte azure migrate-installationsskriptet på en befintlig Azure Migrate-installation.

### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till Azure

Se till att den virtuella datorn för den virtuella enheten kan ansluta till de [Azure-url:er](migrate-appliance.md#url-access)som krävs .

## <a name="configure-the-appliance"></a>Konfigurera apparaten

Ställ in apparaten för första gången.

1. Öppna en webbläsare på alla datorer som kan ansluta till den virtuella datorn och öppna url:en för appen för installationen: **https://*apparatens namn eller IP-adress:* 44368**.

   Alternativt kan du öppna appen från skrivbordet genom att klicka på appgenvägen.
2. Gör följande i webbappen > **Konfigurera förutsättningar:**
    - **Licens**: Acceptera licensvillkoren och läs informationen från tredje part.
    - **Anslutning**: Appen kontrollerar att den virtuella datorn har tillgång till internet. Om den virtuella datorn använder en proxy:
        - Klicka på **Proxyinställningar**och ange proxyadress och http://ProxyIPAddress lyssningsport i formuläret eller http://ProxyFQDN.
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
    - **Tidssynkronisering**: Tiden är verifierad. Tiden på apparaten ska vara synkroniserad med internettiden för att vm-identifiering ska fungera korrekt.
    - **Installera uppdateringar:** Azure Migrate Server Assessment kontrollerar att installationen har de senaste uppdateringarna installerade.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera installationen med Azure Migrate

1. Klicka på **Logga in**. Om den inte visas kontrollerar du att du har inaktiverat popup-blockeraren i webbläsaren.
2. Logga in med dina Azure-autentiseringsuppgifter på den nya fliken.
    - Logga in med ditt användarnamn och lösenord.
    - Inloggning med en PIN-kod stöds inte.
3. När du har loggat in går du tillbaka till webbappen.
4. Välj den prenumeration där Azure Migrate-projektet skapades. Välj sedan projektet.
5. Ange ett namn på apparaten. Namnet ska vara alfanumeriskt med högst 14 tecken.
6. Klicka på **Registrera**.


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut från apparaten till fysiska servrar och starta identifieringen.

1. Klicka på **Lägg till autentiseringsuppgifter** om du vill ange vilka kontouppgifter som används för att identifiera servrar.  
2. Ange **operativsystem ,** eget namn för autentiseringsuppgifter, **användarnamn** och **lösenord** och klicka på **Lägg till**.
Du kan lägga till en uppsättning autentiseringsuppgifter vardera för Windows- och Linux-servrar.
4. Klicka på **Lägg till server**och ange serverinformation- FQDN/IP-adress och eget namn på autentiseringsuppgifter (en post per rad) för att ansluta till servern.
3. Klicka på **Validate** (Validera). Efter valideringen visas listan över servrar som kan identifieras.
    - Om valideringen misslyckas för en server granskar du felet genom att hovra över ikonen i kolumnen **Status.** Åtgärda problem och verifiera igen.
    - Om du vill ta bort en server markerar du > **Ta bort**.
4. När du har validerat klickar du på **Spara och startar identifieringen** för att starta identifieringsprocessen.

Detta startar upptäckt. Det tar cirka 15 minuter innan metadata för identifierade virtuella datorer visas i Azure-portalen.

## <a name="verify-servers-in-the-portal"></a>Verifiera servrar i portalen

När identifieringen är klar kan du kontrollera att servrarna visas i portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. I **Azure Migrate - Servers** > **Azure Migrate: Server Assessment** sida, klicka på ikonen som visar antalet för identifierade **servrar**.


## <a name="next-steps"></a>Nästa steg

Prova [bedömning av fysiska servrar](tutorial-assess-physical.md) med Azure Migrate Server Assessment.
