---
title: Konfigurera en Azure Migrate-installation för VMware
description: Lär dig hur du konfigurerar en Azure Migrera-installation för att bedöma och migrera virtuella virtuella datorer med VMware.
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 7a7d0007d2824abc781411f9529f9fa4ac89e55c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336793"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Konfigurera en apparat för virtuella datorer med VMware

I den hÃ¤r artikeln beskrivs hur du konfigurerar Azure Migrate-anordningen fÃ¤r utvärdering med verktyget [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) och för agentless migration med verktyget [Azure Migrate:Server Migration.](migrate-services-overview.md#azure-migrate-server-migration-tool)

[Azure Migrate-installationen](migrate-appliance.md) är en lättinstallation som används av Azure Migrate:Server Assessment and Server Migration för att identifiera lokala virtuella datorer med VMware, skicka VM-metadata/prestandadata till Azure och för replikering av virtuella datorer med VMware under agentlös migrering.

Du kan konfigurera Azure Migrate-enheten för VMware VM-utvärdering med hjälp av en OVA-mall som du hämtar eller med hjälp av ett PowerShell-installationsskript. I den här artikeln beskrivs hur du ställer in apparaten med hjälp av OVA-mallen. Om du vill konfigurera apparaten med skriptet följer du instruktionerna i den [här artikeln](deploy-appliance-script.md).


## <a name="appliance-deployment-ova"></a>Distribution av apparater (OVA)

Så här ställer du in apparaten med hjälp av en OVA-mall:
- Hämta en OVA-mallfil och importera den till vCenter Server.
- Skapa installationen och kontrollera att den kan ansluta till Azure Migrate Server Assessment.
- Konfigurera installationen för första gången och registrera den med Azure Migrate-projektet.

## <a name="download-the-ova-template"></a>Ladda ner OVA-mallen

1. Klicka på **Identifiera**i **migreringsmålsservrar:** > **Servers** > **Serverutvärdering.**
2. I **Upptäck-datorer** >  **Yes, with VMWare vSphere hypervisor****Virtualiseras dina datorer?**
3. Klicka på **Ladda ned** för att ladda ned .OVA-mallfilen.

  ![Val för att ladda ner en OVA-fil](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verifiera säkerhet

Kontrollera att OVA-filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hash för OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. För den senaste installationen version, den genererade hash bör matcha dessa [inställningar](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security).



## <a name="create-the-appliance-vm"></a>Skapa den virtuella datorn för den använda datorn

Importera den hämtade filen och skapa en virtuell dator.

1. Klicka på **OVF-mall** > för filutgjutning i vSphere-klientkonsolen .**Deploy OVF Template**
![Menykommando för distribution av en OVF-mall](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Ange platsen för OVA-filen i guiden Distribuera **OVF-mall > källa.**
3. Ange ett eget namn för den virtuella datorn i **Namn** och **Plats.** Välj det lagerobjekt där den virtuella datorn ska vara värd.
5. I **Host/Cluster**anger du den värd eller det kluster som den virtuella datorn ska köras på.
6. I **Lagring**anger du lagringsmålet för den virtuella datorn.
7. I **Diskformat** anger du disktyp och storlek.
8. I **Nätverksmappning**anger du det nätverk som den virtuella datorn ska ansluta till. Nätverket behöver internetanslutning för att skicka metadata till Azure Migrate Server Assessment.
9. Granska och bekräfta inställningarna. Klicka sedan på **Slutför**.


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till Azure

Se till att den virtuella datorn för den virtuella enheten kan ansluta till [Azure-url:er](migrate-appliance.md#url-access).


## <a name="configure-the-appliance"></a>Konfigurera apparaten

Ställ in apparaten för första gången. Om du distribuerar apparaten med ett skript i stället för en OVA-mall är de två första stegen i proceduren inte tillämpliga.

1. Högerklicka på den virtuella datorn i vSphere-klientkonsolen > **Öppna konsol**.
2. Ange språk, tidszon och lösenord för apparaten.
3. Öppna en webbläsare på alla datorer som kan ansluta till den virtuella datorn och öppna url:en för appen för installationen: **https://*apparatens namn eller IP-adress:* 44368**.

   Alternativt kan du öppna appen från apparatens skrivbord genom att klicka på appgenvägen.
4. Gör följande i webbappen > **Konfigurera förutsättningar:**
    - **Licens**: Acceptera licensvillkoren och läs informationen från tredje part.
    - **Anslutning**: Appen kontrollerar att den virtuella datorn har tillgång till internet. Om den virtuella datorn använder en proxy:
        - Klicka på **Proxyinställningar**och ange proxyadress och http://ProxyIPAddress lyssningsport i formuläret eller http://ProxyFQDN.
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
    - **Tidssynkronisering**: Tiden är verifierad. Tiden på apparaten ska vara synkroniserad med internettiden för att identifieringen ska fungera korrekt.
    - **Installera uppdateringar:** Azure Migrate kontrollerar att de senaste installationsuppdateringarna är installerade.
    - **Installera VDDK:** Azure Migrate kontrollerar att VMWare vSphere Virtual Disk Development Kit (VDDK) är installerat.
        - Azure Migrates använder VDDK för att replikera datorer under migreringen till Azure.
        - Ladda ner VDDK 6.7 från VMware och extrahera det nedladdade zip-innehållet till den angivna platsen på apparaten.

## <a name="register-the-appliance-with-azure-migrate"></a>Registrera installationen med Azure Migrate

1. Klicka på **Logga in**. Om den inte visas kontrollerar du att du har inaktiverat popup-blockeraren i webbläsaren.
2. Logga in med dina Azure-autentiseringsuppgifter på den nya fliken.
    - Logga in med ditt användarnamn och lösenord.
    - Logga in med en PIN-kod stöds inte.
3. När du har loggat in går du tillbaka till webbappen.
2. Välj den prenumeration där Azure Migrate-projektet skapades. Välj sedan projektet.
3. Ange ett namn på apparaten. Namnet ska vara alfanumeriskt med högst 14 tecken.
4. Klicka på **Registrera**.


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>Starta kontinuerlig identifiering genom att tillhandahålla autentiseringsuppgifter för vCenter Server och VM

Installationen måste ansluta till vCenter Server för att identifiera konfigurations- och prestandadata för de virtuella datorerna.

### <a name="specify-vcenter-server-details"></a>Ange vCenter Server-information
1. Ange namn (FQDN) eller IP-adress för vCenter-servern i Ange information om **vCenter**Server. Du kan lämna standardporten eller ange en anpassad port som vCenter Server lyssnar på.
2. I **Användarnamn** och **Lösenord**anger du de skrivskyddade kontoautentiseringsuppgifter som används för att identifiera virtuella datorer på vCenter-servern. Du kan begränsa identifieringen genom att begränsa åtkomsten till vCenter-kontot. [Läs mer](set-discovery-scope.md).
3. Klicka på **Validera anslutning** för att kontrollera att enheten kan ansluta till vCenter Server.

### <a name="specify-vm-credentials"></a>Ange autentiseringsuppgifter för virtuella datorer
För identifiering av program, roller och funktioner och visualisering av beroenden för de virtuella datorerna kan du tillhandahålla en VM-autentiseringsversion som har åtkomst till virtuella datorer. Du kan lägga till en autentiseringsuppgifter för virtuella Windows-datorer och en autentiseringsuppgifter för virtuella Linux-datorer. [Läs mer](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) om de åtkomstbehörigheter som behövs.

> [!NOTE]
> Den här indata är valfri och behövs för att möjliggöra programidentifiering och agentlös beroendevisualisering.

1. Klicka på **Lägg till autentiseringsuppgifter** **i Upptäck program och beroenden på virtuella datorer**.
2. Välj **operativsystem**.
3. Ange ett eget namn för autentiseringsuppgifterna.
4. I **Användarnamn** och **lösenord**anger du ett konto som har minst gäståtkomst på de virtuella datorerna.
5. Klicka på **Lägg till**.

När du har angett autentiseringsuppgifterna vCenter Server och VM (valfritt) klickar du på **Spara och startar identifieringen** för att starta identifieringen av den lokala miljön.

Det tar cirka 15 minuter innan metadata för identifierade virtuella datorer visas i portalen. Identifiering av installerade program, roller och funktioner tar lite tid, varaktigheten beror på antalet virtuella datorer som upptäcks. För 500 virtuella datorer tar det ungefär 1 timme för programinventeringen att visas i Azure Migrate-portalen.

## <a name="next-steps"></a>Nästa steg

Granska självstudier för [VMware bedömning](tutorial-assess-vmware.md) och [agentless migrering](tutorial-migrate-vmware.md).
