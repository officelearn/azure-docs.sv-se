---
title: Konfigurera en Azure Migrate-apparat för Hyper-V
description: Lär dig hur du konfigurerar en Azure Migrate-apparat för att utvärdera och migrera virtuella Hyper-V-datorer.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: a94d11d48728b03dd978af85db4b6c2af4887938
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534495"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Konfigurera en installation för virtuella Hyper-V-datorer

I den här artikeln beskrivs hur du konfigurerar Azure Migrate-installationen om du utvärderar virtuella Hyper-V-datorer med verktyget för att utvärdera Azure Migrate Server eller migrerar virtuella VMware-datorer till Azure med hjälp av verktyget Azure Migrate Migreringsverktyg för Server.

VM-installationen för Hyper-V är en förenklad installation som används av Azure Migrate Server bedömning/migrering för att göra följande:

- Identifiera lokala virtuella Hyper-V-datorer.
- Skicka metadata och prestanda data för identifierade virtuella datorer till Azure Migrate Server bedömning/migrering.

[Läs mer](migrate-appliance.md) om Azure Migrate-enheten.


## <a name="appliance-deployment-steps"></a>Distributions steg för installationen

Så här konfigurerar du den apparat som du:
- Ladda ned en komprimerad virtuell Hyper-V-hårddisk från Azure Portal.
- Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering.
- Konfigurera enheten för första gången och registrera den med Azure Migrate-projektet.

## <a name="download-the-vhd"></a>Ladda ned den virtuella hård disken

Hämta den zippade VHD-mallen för enheten.

1. I **mål för migrering** > **servrar** > **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2. I **identifiera datorer** > **dina datorer virtualiserade?** , klicka på **Ja, med Hyper-V**.
3. Klicka på **Ladda ned** för att ladda ned VHD-filen.

    ![Hämta virtuell dator](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den virtuella hård disken
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  För version 2.19.11.12 ska den genererade hashen matcha de här inställningarna.

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31



## <a name="create-the-appliance-vm"></a>Skapa VM-enheten

Importera den hämtade filen och skapa den virtuella datorn.

1. Extrahera den zippade VHD-filen till en mapp på Hyper-V-värden som ska vara värd för den virtuella dator enheten. Tre mappar extraheras.
2. Öppna Hyper-V Manager. Klicka på **Importera virtuell dator**i **åtgärder**.

    ![Distribuera virtuell hård disk](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. I guiden Importera virtuell dator > **innan du börjar klickar du**på **Nästa**.
3. I **hitta mapp**anger du den mapp som innehåller den extraherade virtuella hård disken. Klicka sedan på **Nästa**.
1. I **Välj virtuell dator**klickar du på **Nästa**.
2. I **Välj import typ**klickar **du på Kopiera den virtuella datorn (skapa ett nytt unikt ID)** . Klicka sedan på **Nästa**.
3. I **Välj mål**låter du standardvärdet vara kvar. Klicka på **Nästa**.
4. Lämna standardinställningen i **Storage Folders**. Klicka på **Nästa**.
5. I **Välj nätverk**anger du den virtuella växel som den virtuella datorn ska använda. Växeln behöver Internet anslutning för att skicka data till Azure.
6. Granska inställningarna i **Sammanfattning**. Klicka sedan på **Slutför**.
7. Starta den virtuella datorn i Hyper-V Manager > **Virtual Machines**.


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns dator kan ansluta till [Azure-URL: er](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

1. I Hyper-V Manager > **Virtual Machines**högerklickar du på den virtuella datorn > **Anslut**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att klicka på genvägen till appen.
1. I webbappen > **Konfigurera krav**gör du följande:
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


### <a name="delegate-credentials-for-smb-vhds"></a>Delegera autentiseringsuppgifter för SMB-VHD: er

Om du kör virtuella hård diskar på SMB: er måste du aktivera delegering av autentiseringsuppgifter från installationen till Hyper-V-värdarna. Så här gör du detta från enheten:

1. Kör det här kommandot på den virtuella datorn. HyperVHost1/HyperVHost2 är exempel värd namn.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Du kan också göra detta i redigerare för lokalt grupprincipobjekt på enheten:
    - I **princip för den lokala datorn** > **dator konfiguration**klickar du på **administrativa mallar** > **system** > **delegering av autentiseringsuppgifter**.
    - Dubbelklicka på **Tillåt att delegera nya autentiseringsuppgifter**och välj **aktive rad**.
    - I **alternativ**klickar du på **Visa**och lägger till varje Hyper-V-värd som du vill identifiera i listan med **WSMan/** som prefix.
    - I **delegering av autentiseringsuppgifter**dubbelklickar du på **Tillåt att delegera nya AUTENTISERINGSUPPGIFTER med endast NTLM-** serverautentisering. Lägg återigen till varje Hyper-V-värd som du vill identifiera i listan, med **WSMan/** som prefix.

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut från enheten till Hyper-V-värdar eller-kluster och starta VM-identifiering.

1. I **användar namn** och **lösen ord**anger du de kontoautentiseringsuppgifter som installationen ska använda för att identifiera virtuella datorer. Ange ett eget namn för autentiseringsuppgifterna och klicka på **Spara information**.
2. Klicka på **Lägg till värd**och ange information om Hyper-V-värd/kluster.
3. Klicka på **validera**. Efter verifieringen visas antalet virtuella datorer som kan identifieras på varje värd/kluster.
    - Om verifieringen Miss lyckas för en värd granskar du felet genom att hovra över ikonen i kolumnen **status** . Åtgärda problem och verifiera igen.
    - Om du vill ta bort värdar eller kluster väljer du > **ta bort**.
    - Du kan inte ta bort en speciell värd från ett kluster. Du kan bara ta bort hela klustret.
    - Du kan lägga till ett kluster även om det finns problem med specifika värdar i klustret.
4. Efter verifieringen klickar du på **Spara och starta identifiering** för att starta identifierings processen.

Detta startar identifieringen. Det tar ungefär 15 minuter för metadata för identifierade virtuella datorer som visas i Azure Portal.

## <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

När identifieringen är klar kan du kontrol lera att de virtuella datorerna visas i portalen.

1. Öppna instrument panelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar** > **Azure Migrate: Server utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.


## <a name="next-steps"></a>Nästa steg

Testa [Hyper-V-utvärdering](tutorial-assess-hyper-v.md) med Azure Migrate Server-utvärdering.
