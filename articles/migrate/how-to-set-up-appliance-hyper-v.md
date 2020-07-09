---
title: Konfigurera en Azure Migrate-apparat för Hyper-V
description: Lär dig hur du konfigurerar en Azure Migrate-apparat för att utvärdera och migrera virtuella Hyper-V-datorer.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 56b034709309a3afe9d18df7af9ababc74a24cee
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109713"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Konfigurera en installation för virtuella Hyper-V-datorer

Följ den här artikeln för att konfigurera Azure Migrate-installationen för utvärdering av virtuella Hyper-V-datorer med verktyget [Azure Migrate: Server bedömning](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

[Azure Migrate](migrate-appliance.md) -installationen är en förenklad utrustning som används av Azure Migrate: Server bedömning/migrering för att identifiera lokala virtuella Hyper-V-datorer och skicka VM-metadata/prestanda data till Azure.

Du kan distribuera installationen på ett par olika sätt:

- Konfigurera på en virtuell Hyper-V-dator med en nedladdad virtuell hård disk. Detta är den metod som beskrivs i den här artikeln.
- Konfigurera på en virtuell Hyper-V-dator eller fysisk dator med ett PowerShell-skript. [Den här metoden](deploy-appliance-script.md) ska användas om du inte kan konfigurera en virtuell dator med en virtuell hård disk, eller om du befinner dig i Azure Government.

När du har skapat enheten kontrollerar du att den kan ansluta till Azure Migrate: Server utvärdering, konfigurera den för första gången och registrera den med Azure Migrate projektet.

## <a name="appliance-deployment-vhd"></a>Installation av utrustning (VHD)

Så här konfigurerar du installationen med en VHD-mall:

- Ladda ned en komprimerad virtuell Hyper-V-hårddisk från Azure Portal.
- Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering.
- Konfigurera enheten för första gången och registrera den med Azure Migrate-projektet.

## <a name="download-the-vhd"></a>Ladda ned den virtuella hård disken

Hämta den zippade VHD-mallen för enheten.

1. I **mål**  >  **servrar**för migrering  >  **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2. I **identifiera datorer**  >  **är dina datorer virtualiserade?**, klicka på **Ja, med Hyper-V**.
3. Klicka på **Ladda ned** för att ladda ned VHD-filen.

    ![Hämta virtuell dator](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den virtuella hård disken
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  För version 2.19.11.12 ska den genererade hashen matcha de här [inställningarna](./tutorial-assess-hyper-v.md#verify-security).




## <a name="create-the-appliance-vm"></a>Skapa VM-enheten

Importera den hämtade filen och skapa den virtuella datorn.

1. Extrahera den zippade VHD-filen till en mapp på Hyper-V-värden som ska vara värd för den virtuella dator enheten. Tre mappar extraheras.
2. Öppna Hyper-V Manager. Klicka på **Importera virtuell dator**i **åtgärder**.

    ![Distribuera virtuell hård disk](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. I guiden Importera virtuell dator > **innan du börjar klickar du**på **Nästa**.
3. I **hitta mapp**anger du den mapp som innehåller den extraherade virtuella hård disken. Klicka sedan på **Nästa**.
1. I **Välj virtuell dator**klickar du på **Nästa**.
2. I **Välj import typ**klickar **du på Kopiera den virtuella datorn (skapa ett nytt unikt ID)**. Klicka sedan på **Nästa**.
3. I **Välj mål**låter du standardvärdet vara kvar. Klicka på **Nästa**.
4. Lämna standardinställningen i **Storage Folders**. Klicka på **Nästa**.
5. I **Välj nätverk**anger du den virtuella växel som den virtuella datorn ska använda. Växeln behöver Internet anslutning för att skicka data till Azure.
6. Granska inställningarna i **Sammanfattning**. Klicka sedan på **Slutför**.
7. Starta den virtuella datorn i Hyper-V Manager > **Virtual Machines**.


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns virtuella datorer kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.

## <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången. Om du distribuerar installationen med hjälp av ett skript i stället för en virtuell hård disk är de två första stegen i proceduren inte tillämpliga.

1. I Hyper-V Manager > **Virtual Machines**högerklickar du på den virtuella datorn > **Anslut**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att klicka på genvägen till appen.
1. I webbappen > **Konfigurera krav**gör du följande:
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


### <a name="delegate-credentials-for-smb-vhds"></a>Delegera autentiseringsuppgifter för SMB-VHD: er

Om du kör virtuella hård diskar på SMB: er måste du aktivera delegering av autentiseringsuppgifter från installationen till Hyper-V-värdarna. Så här gör du detta från enheten:

1. Kör det här kommandot på den virtuella datorn. HyperVHost1/HyperVHost2 är exempel värd namn.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Du kan också göra detta i redigerare för lokalt grupprincipobjekt på enheten:
    - I dator konfiguration för **lokal dator princip**  >  **Computer Configuration**klickar du på **administrativa mallar**  >  **System**  >  **delegering av systemautentiseringsuppgifter**.
    - Dubbelklicka på **Tillåt att delegera nya autentiseringsuppgifter**och välj **aktive rad**.
    - I **alternativ**klickar du på **Visa**och lägger till varje Hyper-V-värd som du vill identifiera i listan med **WSMan/** som prefix.
    - I **delegering av autentiseringsuppgifter**dubbelklickar du på **Tillåt att delegera nya AUTENTISERINGSUPPGIFTER med endast NTLM-** serverautentisering. Lägg återigen till varje Hyper-V-värd som du vill identifiera i listan, med **WSMan/** som prefix.

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut från enheten till Hyper-V-värdar eller-kluster och starta VM-identifiering.

1. I **användar namn** och **lösen ord**anger du de kontoautentiseringsuppgifter som installationen ska använda för att identifiera virtuella datorer. Ange ett eget namn för autentiseringsuppgifterna och klicka på **Spara information**.
2. Klicka på **Lägg till värd**och ange information om Hyper-V-värd/kluster.
3. Klicka på **Validate** (Validera). Efter verifieringen visas antalet virtuella datorer som kan identifieras på varje värd/kluster.
    - Om verifieringen Miss lyckas för en värd granskar du felet genom att hovra över ikonen i kolumnen **status** . Åtgärda problem och verifiera igen.
    - Om du vill ta bort värdar eller kluster väljer du > **ta bort**.
    - Du kan inte ta bort en speciell värd från ett kluster. Du kan bara ta bort hela klustret.
    - Du kan lägga till ett kluster även om det finns problem med specifika värdar i klustret.
4. Efter verifieringen klickar du på **Spara och starta identifiering** för att starta identifierings processen.

Detta startar identifieringen. Det tar ungefär 15 minuter för metadata för identifierade virtuella datorer som visas i Azure Portal.

## <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

När identifieringen är klar kan du kontrol lera att de virtuella datorerna visas i portalen.

1. Öppna instrument panelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar**  >  **Azure Migrate: Server utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.


## <a name="next-steps"></a>Nästa steg

Testa [Hyper-V-utvärdering](tutorial-assess-hyper-v.md) med Azure Migrate Server-utvärdering.
