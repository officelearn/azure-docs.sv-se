---
title: Konfigurera en Azure Migrate-apparat för fysiska servrar
description: Lär dig hur du konfigurerar en Azure Migrate-apparat för fysisk server-utvärdering.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 6d9cc071ad5d81a09a14b12fe2acdf564c2ea6c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84331788"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Konfigurera en installation för fysiska servrar

I den här artikeln beskrivs hur du konfigurerar Azure Migrate-enheten om du utvärderar fysiska servrar med verktyget Azure Migrate: Server bedömning.

Azure Migrate-installationen är en förenklad installation som används av Azure Migrate Server bedömning för att göra följande:

- Identifiera lokala servrar.
- Skicka metadata och prestanda data för identifierade servrar till Azure Migrate Server bedömning.

[Läs mer](migrate-appliance.md) om Azure Migrate-enheten.


## <a name="appliance-deployment-steps"></a>Distributions steg för installationen

Så här konfigurerar du den apparat som du:
- Ladda ned en zippad fil med Azure Migrate Installer-skript från Azure Portal.
- Extrahera innehållet från den zippade filen. Starta PowerShell-konsolen med administratörs behörighet.
- Kör PowerShell-skriptet för att starta webb programmet för installationen.
- Konfigurera enheten för första gången och registrera den med Azure Migrate-projektet.

## <a name="download-the-installer-script"></a>Ladda ned installations skriptet

Ladda ned den zippade filen för enheten.

1. I **mål**  >  **servrar**för migrering  >  **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2. I **identifiera datorer**  >  **är dina datorer virtualiserade?**, klicka på **inte virtualiserad/annan**.
3. Klicka på **Ladda ned** för att ladda ned den zippade filen.

    ![Hämta virtuell dator](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning för offentligt moln:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Exempel på användning av myndighets moln:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Kontrol lera den senaste versionen av installationen och hash-värden:
 
    - För det offentliga molnet:

        **Scenario** | **Hämta*** | **Hash-värde**
        --- | --- | ---
        Fysisk (63,1 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0

    - För Azure Government:

        **Scenario** | **Hämta*** | **Hash-värde**
        --- | --- | ---
        Fysisk (63,1 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1


## <a name="run-the-azure-migrate-installer-script"></a>Kör installations skriptet för Azure Migrate
Installations skriptet gör följande:

- Installerar agenter och ett webb program för identifiering och utvärdering av fysiska servrar.
- Installera Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämta och installera en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständig inställnings information för Azure Migrate.
- Skapar följande filer under sökvägen:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet på följande sätt:

1. Extrahera den zippade filen till en mapp på den server som ska vara värd för-enheten.  Kontrol lera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på servern med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp där innehållet har extraherats från den hämtade zippade filen.
4. Kör skriptet med namnet **AzureMigrateInstaller.ps1** genom att köra följande kommando:

    - För det offentliga molnet:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - För Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Skriptet startar webb programmet för installationen när det har slutförts.

Om du kommer över alla problem kan du komma åt skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log för fel sökning.



### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns virtuella datorer kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.

## <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

1. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från Skriv bordet genom att klicka på genvägen till appen.
2. I webbappen > **Konfigurera krav**gör du följande:
    - **Licens**: Godkänn licens villkoren och Läs informationen från tredje part.
    - **Anslutning**: appen kontrollerar att den virtuella datorn har Internet åtkomst. Om den virtuella datorn använder en proxyserver:
        - Klicka på **proxyinställningar**och ange proxyadress och lyssnings port i formuläret http://ProxyIPAddress eller http://ProxyFQDN .
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
    - **Tidssynkronisering**: tiden har verifierats. Tiden för installationen bör vara synkroniserad med Internet-tid för att VM-identifieringen ska fungera korrekt.
    - **Installera uppdateringar**: Azure Migrate Server Assessment kontrollerar att installations programmet har de senaste uppdateringarna installerade.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klicka på **Logga**in. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
2. På fliken nytt loggar du in med dina Azure-autentiseringsuppgifter.
    - Logga in med ditt användar namn och lösen ord.
    - Inloggning med en PIN-kod stöds inte.
3. När du har loggat in går du tillbaka till webbappen.
4. Välj den prenumeration där Azure Migrate projektet skapades. Välj sedan projektet.
5. Ange ett namn för enheten. Namnet måste vara alfanumeriskt med 14 tecken eller mindre.
6. Klicka på **Registrera**.


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut från-enheten till fysiska servrar och starta identifieringen.

1. Klicka på **Lägg till autentiseringsuppgifter** för att ange de kontoautentiseringsuppgifter som installationen ska använda för att identifiera servrar.  
2. Ange **operativ systemet**, ett eget namn för autentiseringsuppgifterna och användar namn och lösen ord. Klicka sedan på **Lägg till**.
Du kan lägga till en uppsättning autentiseringsuppgifter var för Windows-och Linux-servrar.
4. Klicka på **Lägg till Server**och ange Server information – FQDN/IP-adress och eget namn på autentiseringsuppgifter (en post per rad) för att ansluta till servern.
3. Klicka på **Validate** (Validera). Efter verifieringen visas en lista över de servrar som kan identifieras.
    - Om verifieringen Miss lyckas för en server kan du granska felet genom att hovra över ikonen i kolumnen **status** . Åtgärda problem och verifiera igen.
    - Om du vill ta bort en server väljer du > **ta bort**.
4. Efter verifieringen klickar du på **Spara och starta identifiering** för att starta identifierings processen.

Detta startar identifieringen. Det tar ungefär 15 minuter för metadata för identifierade virtuella datorer som visas i Azure Portal.

## <a name="verify-servers-in-the-portal"></a>Verifiera servrar i portalen

När identifieringen är klar kan du kontrol lera att servrarna visas i portalen.

1. Öppna instrument panelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar**  >  **Azure Migrate: Server utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.


## <a name="next-steps"></a>Nästa steg

Testa [utvärderingen av fysiska servrar](tutorial-assess-physical.md) med Azure Migrate Server-utvärdering.
