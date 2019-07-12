---
title: Konfigurera en apparat för Azure Migrate utvärdering/servermigrering för Hyper-V-datorer | Microsoft Docs
description: Beskriver hur du ställer in en installation för identifiering, bedömning och migrering av Hyper-V virtuella datorer med Azure Migrate utvärdering/servermigrering utan agenter.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: c531fe49ebff6c021547c2d1c2f382bcd6c9caef
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811770"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Konfigurera en installation för Hyper-V-datorer

Den här artikeln beskriver hur du ställer in Azure Migrate-installation om du utvärdera Hyper-V-datorer med verktyget Azure Migrate Server-utvärdering, eller migrerar virtuella VMware-datorer till Azure med hjälp av verktyget Azure Migrate servermigrering.

Hyper-V VM-installationen är en enkel installation som används av Azure Migrate utvärdering/servermigrering för att göra följande:

- Upptäck lokala Hyper-V-datorer.
- Skicka metadata och prestanda för identifierade virtuella datorer till Azure Migrate utvärdering/servermigrering.

[Läs mer](migrate-appliance.md) om Azure Migrate-installationen.


## <a name="appliance-deployment-steps"></a>Distributionsstegen för installation

Du ställer in enheten som du:
- Ladda ned en komprimerad Hyper-V virtuell Hårddisk från Azure-portalen.
- Skapa och kontrollera att den kan ansluta till Azure Migrate Server-utvärdering. 
- Konfigurera programmet för första gången och registrera den med Azure Migrate-projektet.

## <a name="download-the-vhd"></a>Ladda ned den virtuella Hårddisken

Ladda ned den komprimerade VHD-mallen för produkten.

1. I **migrering mål** > **servrar** > **Azure Migrate: Server-utvärdering**, klickar du på **identifiera**.
2. I **identifiera datorer** > **är dina datorer virtualiserade?** , klickar du på **Ja, med Hyper-V**.
3. Klicka på **hämta** att ladda ned VHD-filen.

    ![Hämta virtuell dator](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Kontrollera säkerheten

Kontrollera att den komprimerade filen är säker, innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera en hash för den virtuella Hårddisken
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Den genererade hashen måste matcha nedanstående inställningar för installation version 1.19.05.10.

  **Algoritm** | **Hash-värde**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="create-the-appliance-vm"></a>Skapa virtuell dator

Importera den nedladdade filen och skapa den virtuella datorn.

1. Extrahera komprimerade VHD-filen till en mapp på Hyper-V-värden som är värd för den VM-installationen. Tre mappar extraheras.
2. Öppna Hyper-V Manager. I **åtgärder**, klickar du på **Importera virtuell dator**.

    ![Distribuera virtuell Hårddisk](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. I guiden Importera virtuell dator > **innan du börjar**, klickar du på **nästa**.
3. I **mapp**, ange den mapp som innehåller den extrahera virtuella Hårddisken. Klicka sedan på **Nästa**.
1. I **Välj virtuell dator**, klickar du på **nästa**.
2. I **Välj importtyp**, klickar du på **kopiera den virtuella datorn (skapa ett nytt unikt ID)** . Klicka sedan på **Nästa**.
3. I **väljer mål**, lämnar du standardinställningen. Klicka på **Nästa**.
4. I **Storage mappar**, lämnar du standardinställningen. Klicka på **Nästa**.
5. I **Välj nätverk**, ange den virtuella växeln som den virtuella datorn ska använda. Växeln måste ha en Internetanslutning för att skicka data till Azure.
6. I **sammanfattning**, granskar du inställningarna. Klicka sedan på **Slutför**.
7. I Hyper-V Manager > **virtuella datorer**, starta den virtuella datorn.


### <a name="verify-appliance-access-to-azure"></a>Verifiera installationen åtkomst till Azure

Kontrollera att installationen VM kan ansluta till [Azure URL: er](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Konfigurera programmet

Ställ in installationen för första gången.

1. I Hyper-V Manager > **virtuella datorer**, högerklicka på den virtuella datorn > **Connect**.
2. Ange språk, tidszon och lösenord för installationen.
3. Öppna en webbläsare på en dator som kan ansluta till den virtuella datorn och öppna URL: en för webbappen installation: **https://*installationsnamnet eller IP-adress*: 44368**.

   Du kan också öppna appen från skrivbordet installation genom att klicka på genvägen till appen.
1. I webbapp > **konfigurera förhandskraven**, gör du följande:
    - **Licens**: Acceptera licensvillkoren och läs informationen från tredje part.
    - **Anslutningen**: Appen kontrollerar att den virtuella datorn har Internetåtkomst. Om den virtuella datorn använder en proxyserver:
        - Klicka på **proxyinställningar**, och anger proxyadress och lyssningsport, i formatet http://ProxyIPAddress eller http://ProxyFQDN.
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
    - **Tid synkronisering**: Tid har verifierats. Tiden på enheten som ska vara synkroniserad med internettiden för identifiering av VM ska fungera korrekt.
    - **Installera uppdateringar**: Azure Migrate Server-utvärdering kontrollerar du att installationen har de senaste uppdateringarna installerade.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klicka på **logga In**. Om den inte visas kan du kontrollera att du har inaktiverat blockering av popup-fönster i webbläsaren.
2. På den nya fliken kan du logga in med dina autentiseringsuppgifter för Azure. 
    - Logga in med ditt användarnamn och lösenord.
    - Logga in med en PIN-kod stöds inte.
3. När du har loggat in kan gå tillbaka till webbappen.
4. Välj den prenumeration som Azure Migrate-projektet har skapats. Välj projektet.
5. Ange ett namn för produkten. Namnet bör vara alfanumeriskt med 14 tecken eller mindre.
6. Klicka på **registrera**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegera autentiseringsuppgifter för SMB virtuella hårddiskar

Om du använder virtuella hårddiskar på små och medelstora företag, måste du aktivera delegering av autentiseringsuppgifter från installationen till Hyper-V-värdar. Att göra detta från installationen:

1. Kör det här kommandot på installationen VM. HyperVHost1/HyperVHost2 är exempel värdnamn.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Du kan också göra detta i den lokala Redigeraren för i installationen:
    - I **lokal datorprincip** > **Datorkonfiguration**, klickar du på **Administrationsmallar** > **System**  >  **Autentiseringsuppgifter delegering**.
    - Dubbelklicka på **Tillåt delegering av nya autentiseringsuppgifter**, och välj **aktiverad**.
    - I **alternativ**, klickar du på **visa**, och Lägg till varje Hyper-V-värd som du vill identifiera i listan med **wsman /** som ett prefix.
    - I **delegering av autentiseringsuppgifter**, dubbelklicka på **Tillåt delegering av nya autentiseringsuppgifter med endast NTLM-autentisering**. Igen, lägga till varje Hyper-V-värd som du vill identifiera i listan med **wsman /** som ett prefix.

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifieringen

Ansluta från installationen till Hyper-V-värdar eller kluster och starta identifieringen av virtuella datorer.

1. I **användarnamn** och **lösenord**, ange de kontoautentiseringsuppgifter som installationen använder för att identifiera virtuella datorer. Ange ett eget namn för autentiseringsuppgifterna och klicka på **spara information**.
2. Klicka på **Lägg till värd**, och ange information om Hyper-V-värd/kluster.
3. Klicka på **Validera**. Efter valideringen kan visas antalet virtuella datorer som kan identifieras på varje värdkluster.
    - Om valideringen misslyckas för en värd, granska felet genom att hovra över ikonen i den **Status** kolumn. Åtgärda problem och validera igen.
    - Om du vill ta bort värdar eller kluster, Välj > **ta bort**.
    - Du kan inte ta bort en viss värd från ett kluster. Du kan bara ta bort hela klustret.
    - Du kan lägga till ett kluster, även om det finns problem med specifika värdar i klustret.
4. Efter valideringen kan klickar du på **spara och starta identifieringen** att starta identifieringsprocessen.

Detta startar identifieringen. Det tar ungefär 15 minuter för metadata för identifierade virtuella datorer visas i Azure-portalen. 

## <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

När identifieringen har slutförts kan du kontrollera att de virtuella datorerna visas i portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. I **Azure Migrate - servrar** > **Azure Migrate: Server-utvärdering** klickar du på den ikon som visar antalet för **identifierade servrar**. 


## <a name="next-steps"></a>Nästa steg

Prova att använda [Hyper-V-utvärdering](tutorial-assess-hyper-v.md) med Azure Migrate Server-utvärdering.