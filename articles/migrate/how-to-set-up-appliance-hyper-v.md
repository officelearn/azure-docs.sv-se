---
title: Konfigurera en Azure Migrate-apparat för Hyper-V
description: Lär dig hur du konfigurerar en Azure Migrera-installation för att bedöma och migrera virtuella virtuella datorer med hyper-V.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 80db2c1d4f5482604ca1507174b127c150f76044
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336801"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Konfigurera en apparat för virtuella hyper-virtuella datorer

I den hÃ¤r artikeln beskrivs hur du konfigurerar Azure Migrate-anordningen för bedömning av virtuella hyper-v-datorer med verktyget [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)

[Azure Migrate-installationen](migrate-appliance.md) är en lättinstallation som används av Azure Migrate:Server Assessment/Migration för att identifiera lokala virtuella datorer med hyper-vm och skicka VM-metadata/prestandadata till Azure.

Du kan konfigurera Azure Migrate-enheten för Hyper-V VM-utvärdering med hjälp av en VHD-mall som du hämtar eller med hjälp av ett PowerShell-installationsskript. I den här artikeln beskrivs hur du konfigurerar enheten med hjälp av VHD-mallen. Om du vill konfigurera apparaten med skriptet följer du instruktionerna i den [här artikeln](deploy-appliance-script.md).


## <a name="appliance-deployment-vhd"></a>Distribution av apparat (VHD)

Så här konfigurerar du enheten med hjälp av en VHD-mall:

- Hämta en komprimerad Hyper-V-hårddisk från Azure-portalen.
- Skapa installationen och kontrollera att den kan ansluta till Azure Migrate Server Assessment.
- Konfigurera installationen för första gången och registrera den med Azure Migrate-projektet.

## <a name="download-the-vhd"></a>Ladda ner den virtuella hårddisken

Ladda ner den zippade VHD-mallen för apparaten.

1. Klicka på **Identifiera**i **migreringsmålsservrar:** > **Servers** > **Serverutvärdering.**
2. **I** **Upptäck-datorer** > **Virtualiseras dina datorer?**
3. Klicka på **Hämta** om du vill hämta VHD-filen.

    ![Ladda ner vm](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrollera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hash för DEN VIRTUELLA HÅRDDISKEN
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  För apparat version 2.19.11.12, bör den genererade hash matcha dessa [inställningar](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security).




## <a name="create-the-appliance-vm"></a>Skapa den virtuella datorn för den använda datorn

Importera den hämtade filen och skapa den virtuella datorn.

1. Extrahera den zippade VHD-filen till en mapp på Hyper-V-värden som är värd för den virtuella datorn. Tre mappar extraheras.
2. Öppna Hyper-V Manager. Klicka på **Importera virtuell dator i** **Åtgärder**.

    ![Distribuera VIRTUELLD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Klicka på **Nästa**i > guiden Importera virtuell dator **innan du börjar**.
3. I **Sök mapp**anger du mappen som innehåller den extraherade virtuella hårddisken. Klicka sedan på **Nästa**.
1. Klicka på **Nästa** **i Välj virtuell dator**.
2. Klicka på Kopiera **den virtuella datorn (skapa ett nytt unikt ID)** i **Välj importtyp**. Klicka sedan på **Nästa**.
3. Lämna standardinställningen i **Välj mål.** Klicka på **Nästa**.
4. Lämna standardinställningen i **Lagringsmappar.** Klicka på **Nästa**.
5. Ange den virtuella växel som den virtuella datorn ska använda i **Välj nätverk.** Växeln behöver internetanslutning för att skicka data till Azure.
6. Granska inställningarna i **Sammanfattning.** Klicka sedan på **Slutför**.
7. Starta den virtuella datorn i Hyper-V **Manager-> virtuella datorer.**


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till Azure

Se till att den virtuella datorn för den virtuella enheten kan ansluta till [Azure-url:er](migrate-appliance.md#url-access).

## <a name="configure-the-appliance"></a>Konfigurera apparaten

Ställ in apparaten för första gången. Om du distribuerar enheten med ett skript i stället för en virtuell hårddisk är de två första stegen i proceduren inte tillämpliga.

1. Högerklicka på den virtuella datorn **Virtual Machines**> > **Connect**i Hyper-V Manager-hanteraren.
2. Ange språk, tidszon och lösenord för apparaten.
3. Öppna en webbläsare på alla datorer som kan ansluta till den virtuella datorn och öppna url:en för appen för installationen: **https://*apparatens namn eller IP-adress:* 44368**.

   Alternativt kan du öppna appen från apparatens skrivbord genom att klicka på appgenvägen.
1. Gör följande i webbappen > **Konfigurera förutsättningar:**
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


### <a name="delegate-credentials-for-smb-vhds"></a>Delegera autentiseringsuppgifter för virtuella SMB-hårddiskar

Om du kör virtuella hårddiskar på små och medelstora företag måste du aktivera delegering av autentiseringsuppgifter från enheten till Hyper-V-värdarna. Så här gör du från apparaten:

1. Kör det här kommandot på den virtuella datorn. HyperVHost1/HyperVHost2 är exempelvärden.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Du kan också göra detta i redigeraren för lokala grupprinciper på apparaten:
    - Klicka på **Administrativa mallar** > **Systemautentiseringsuppgifter****System** > delegation i Konfiguration av lokal**datorprincipdator.** **Local Computer Policy** > 
    - Dubbelklicka på **Tillåt delegering av nya autentiseringsuppgifter**och välj **Aktiverad**.
    - I **Alternativ**klickar du på **Visa**och lägger till varje Hyper-V-värd som du vill identifiera i listan, med **wsman/** som prefix.
    - Dubbelklicka på **Tillåt delegera nya autentiseringsuppgifter med NTLM-serverautentisering**i **autentiseringsuppgifter.** Lägg till varje Hyper-V-värd som du vill identifiera i listan, med **wsman/** som prefix.

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut från installationen till Hyper-V-värdar eller kluster och starta vm-identifiering.

1. I **Användarnamn** och **Lösenord**anger du de kontouppgifter som används för att identifiera virtuella datorer. Ange ett eget namn för autentiseringsuppgifterna och klicka på **Spara information**.
2. Klicka på **Lägg till värd**och ange information om Hyper-V-värd/kluster.
3. Klicka på **Validate** (Validera). Efter verifiering visas antalet virtuella datorer som kan identifieras på varje värd/kluster.
    - Om valideringen misslyckas för en värd granskar du felet genom att hovra över ikonen i kolumnen **Status.** Åtgärda problem och verifiera igen.
    - Om du vill ta bort värdar eller kluster markerar du > **Ta bort**.
    - Du kan inte ta bort en viss värd från ett kluster. Du kan bara ta bort hela klustret.
    - Du kan lägga till ett kluster, även om det finns problem med specifika värdar i klustret.
4. När du har validerat klickar du på **Spara och startar identifieringen** för att starta identifieringsprocessen.

Detta startar upptäckt. Det tar cirka 15 minuter innan metadata för identifierade virtuella datorer visas i Azure-portalen.

## <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

När identifieringen är klar kan du kontrollera att de virtuella datorerna visas i portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. I **Azure Migrate - Servers** > **Azure Migrate: Server Assessment** sida, klicka på ikonen som visar antalet för identifierade **servrar**.


## <a name="next-steps"></a>Nästa steg

Prova [Hyper-V-utvärdering](tutorial-assess-hyper-v.md) med Azure Migrate Server Assessment.
