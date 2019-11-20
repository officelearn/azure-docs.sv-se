---
title: Konfigurera en Azure Migrate-apparat för fysiska servrar
description: Lär dig hur du konfigurerar en Azure Migrate-apparat för fysisk server-utvärdering.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ade2a76c168002e573a6504e31769b83f0c6eb37
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185897"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Konfigurera en installation för fysiska servrar

I den här artikeln beskrivs hur du konfigurerar Azure Migrate-enheten om du utvärderar fysiska servrar med verktyget Azure Migrate: Server bedömning.

> [!NOTE]
> Om funktionerna nämns här som du ännu inte ser i Azure Migrate-portalen, låser du. De kommer att visas under nästa vecka eller så.

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

1. I **mål för migrering** > **servrar** > **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2. I **identifiera datorer** > **dina datorer virtualiserade?** , klicka på **inte virtualiserad/annan**.
3. Klicka på **Ladda ned** för att ladda ned den zippade filen.

    ![Hämta virtuell dator](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den virtuella hård disken
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  För den senaste versionen av produkten ska den genererade hashen matcha de här inställningarna.

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | 5b98cf68dad495696c789bdad8f0d91b
  SHA256 | c2364887738875a31634eb0cf5defd0398f813d41069875976ca076297071e6f



## <a name="run-the-azure-migrate-installer-script"></a>Kör installations skriptet för Azure Migrate
= Installations skriptet gör följande:

- Installerar agenter och ett webb program för identifiering och utvärdering av fysiska servrar.
- Installera Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämta och installera en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständig inställnings information för Azure Migrate.
- Skapar följande filer under sökvägen:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet på följande sätt:

1. Extrahera den zippade filen till en mapp på den server som ska vara värd för-enheten.
2. Starta PowerShell på servern med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp där innehållet har extraherats från den hämtade zippade filen.
4. Kör skriptet genom att köra följande kommando:
    ```
    PS C:\Users\Administrators\Desktop> AzureMigrateInstaller-physical.ps1
    ```
Skriptet startar webb programmet för installationen när det har slutförts.



### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns dator kan ansluta till de [Azure-webbadresser](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)som krävs.

## <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

1. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från Skriv bordet genom att klicka på genvägen till appen.
2. I webbappen > **Konfigurera krav**gör du följande:
    - **Licens**: Godkänn licens villkoren och Läs informationen från tredje part.
    - **Anslutning**: appen kontrollerar att den virtuella datorn har Internet åtkomst. Om den virtuella datorn använder en proxyserver:
        - Klicka på **proxyinställningar**och ange proxyadress och lyssnings port, i formatet http://ProxyIPAddress eller http://ProxyFQDN.
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
2. Ange **operativ system**, eget namn för autentiseringsuppgifterna, **användar** namn och **lösen ord** och klicka på **Lägg till**.
Du kan lägga till en uppsättning autentiseringsuppgifter var för Windows-och Linux-servrar.
4. Klicka på **Lägg till Server**och ange Server information – FQDN/IP-adress och eget namn på autentiseringsuppgifter (en post per rad) för att ansluta till servern.
3. Klicka på **validera**. Efter verifieringen visas en lista över de servrar som kan identifieras.
    - Om verifieringen Miss lyckas för en server kan du granska felet genom att hovra över ikonen i kolumnen **status** . Åtgärda problem och verifiera igen.
    - Om du vill ta bort en server väljer du > **ta bort**.
4. Efter verifieringen klickar du på **Spara och starta identifiering** för att starta identifierings processen.

Detta startar identifieringen. Det tar ungefär 15 minuter för metadata för identifierade virtuella datorer som visas i Azure Portal.

## <a name="verify-servers-in-the-portal"></a>Verifiera servrar i portalen

När identifieringen är klar kan du kontrol lera att servrarna visas i portalen.

1. Öppna instrument panelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar** > **Azure Migrate: Server utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.


## <a name="next-steps"></a>Nästa steg

Testa [utvärderingen av fysiska servrar](tutorial-assess-physical.md) med Azure Migrate Server-utvärdering.
