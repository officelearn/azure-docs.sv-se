---
title: Om insamlingsprogrammet i Azure Migrate | Microsoft Docs
description: Innehåller information om insamlingsprogrammet i Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: snehaa
services: azure-migrate
ms.openlocfilehash: 6f843fedafd68d4e04d181af2c6d7542baaf0144
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104224"
---
# <a name="about-the-collector-appliance"></a>Om insamlingsprogrammet

 Den här artikeln innehåller information om Azure Migrate Collector.

Azure Migrate Collector är en enkel installation som kan användas för att identifiera en lokal vCenter-miljö för utvärdering med den [Azure Migrate](migrate-overview.md) tjänsten före migreringen till Azure.  

## <a name="discovery-method"></a>Identifieringsmetod

Tidigare fanns det två alternativ för insamlingsprogrammet, enstaka identifiering och kontinuerlig identifiering. Identifiering av enstaka modellen är nu inaktuellt eftersom det förlitade sig tidigare på statistikinställningarna för vCenter-Server för insamling av prestandadata (nödvändiga statistikinställningarna anges till nivå 3) och samlas även in genomsnittlig räknare (i stället för låg belastning), vilket resulterade i under storlek. Identifiering av kontinuerlig modellen säkerställer detaljerade datainsamling och leder till korrekt storlek på grund av samling av högsta räknare. Nedan visas hur det fungerar:

Insamlingsprogrammet anslutna kontinuerligt till Azure Migrate-projektet och kontinuerligt samlar in prestandadata för virtuella datorer.

- Insamlaren profiler kontinuerligt den lokala miljön för att samla in användningsdata i realtid var 20: e sekund.
- Installationen samlar in 20 sekunder exemplen och skapar en enskild datapunkt var 15: e minut.
- För att skapa datan punkt installationen väljer det högsta värdet 20 sekunder exemplen och skickar det till Azure.
- Den här modellen inte är beroende statistikinställningarna för vCenter-servern att samla in prestandadata.
- Du kan stoppa kontinuerlig profilering vid när som helst från insamlaren.

**Omedelbar tillfredsställelse:** Med kontinuerlig discovery-installation, när identifieringen har slutförts (det tar några timmar beroende på hur många virtuella datorer), kan du direkt skapa utvärderingar. Eftersom prestandadatainsamlingen startar när du påbörjar identifieringen bör du välja storlekskriteriet i utvärderingen som *som lokalt* om du behöver omedelbar tillfredsställelse. För prestandabaserade utvärderingar rekommenderas det att du väntar minst en dag efter att identifieringen har påbörjats för att få tillförlitliga storleksrekommendationer.

Installationen endast samlar in prestandadata kontinuerligt, upptäcks inte varje konfigurationsändring i den lokala miljön (dvs. VM-tillägg, borttagning, disk tillägg osv.). Om det finns en konfigurationsändring i den lokala miljön kan du göra följande för att återspegla ändringarna i portalen:

- Tillägg av objekt (virtuella datorer, diskar, kärnor osv.): Om du vill återspegla dessa ändringar på Azure-portalen kan du stoppa identifieringen från installationen och sedan starta den igen. Då uppdateras ändringarna i Azure Migrate-projektet.

- Borttagning av virtuella datorer: På grund av hur installationen är utformad återspeglas inte borttagning av virtuella datorer även om du stoppar och startar identifieringen. Det beror på att data från efterföljande identifieringar läggs till äldre identifieringar och inte åsidosätts. I det här fallet kan du helt enkelt ignorera den virtuella datorn genom att ta bort den från gruppen och beräkna utvärderingen.

> [!NOTE]
> Installationen för engångsidentifiering är nu inaktuell eftersom den här metoden förlitade sig på vCenter Servers statistikinställningarna för tillgänglighet av prestandadatapunkt och samlade in räknare för genomsnittlig prestanda, vilket resulterade i för små VM-storlekar för migrering till Azure.

## <a name="deploying-the-collector"></a>Distribuera insamlaren

Du distribuerar insamlingsprogrammet med hjälp av en OVF-mall:

- Du kan hämta OVF-mall från ett Azure Migrate-projekt i Azure-portalen. Du kan importera den nedladdade filen till vCenter-servern för att ställa in insamlingsprogrammet VM.
- Från OVF ställer VMware in en virtuell dator med 8 kärnor, 16 GB RAM-minne och en disk på 80 GB. Operativsystemet är Windows Server 2016 (64-bitars).
- När du kör insamlaren, kör ett antal kontroller av förutsättningar för att se till att insamlaren kan ansluta till Azure Migrate.

- [Läs mer](tutorial-assessment-vmware.md#create-the-collector-vm) om hur du skapar insamlaren.


## <a name="collector-prerequisites"></a>Insamlaren krav

Insamlaren måste klara några nödvändiga kontroller för att säkerställa att den kan ansluta till Azure Migrate-tjänsten via internet och ladda upp identifierade data.

- **Verifiera Azure-molnet**: Insamlaren behöver veta Azure-molnet som du planerar att migrera.
    - Välj Azure Government om du planerar att migrera till Azure Government-molnet.
    - Välj Azure Global om du planerar att migrera till kommersiella Azure-molnet.
    - Baserat på molnet som anges här kan skickar installationen identifierade metadata till respektive slutpunkter.
- **Kontrollera Internetanslutningen**: Insamlaren kan ansluta till internet, direkt eller via en proxyserver.
    - Kravkontrollen verifierar anslutningen till [URL: er med obligatoriska och valfria](#urls-for-connectivity).
    - Om du har en direkt anslutning till internet kan krävs ingen specifik åtgärd, förutom att se till att insamlaren kan nå URL: er som krävs.
    - Om du ansluter via en proxyserver, notera den [krav nedan](#connect-via-a-proxy).
- **Kontrollera tidssynkronisering**: Insamlaren ska synkroniseras med internet-tidsservern så autentiseras begäranden till tjänsten.
    - Portal.azure.com URL: en ska vara kan nås från insamlaren så att tiden kan valideras.
    - Om datorn har inte synkroniserats, måste du ändra clock-tid på den insamlaren virtuella datorn så att den matchar den aktuella tiden. För att göra det här öppnar du en kommandotolk för administratör på den virtuella datorn kör **w32tm /tz** att kontrollera den aktuella tidszonen. Kör **w32tm/resync** att synkronisera tiden.
- **Kontrollera collector-tjänsten körs**:  Azure Migrate Collector-tjänsten ska köras på VM-insamlaren.
    - Den här tjänsten startas automatiskt när datorn startas.
    - Om tjänsten inte körs startar du den från Kontrollpanelen.
    - Collector-tjänsten ansluter till vCenter-servern, samlar in den virtuella datorn metadata och prestandadata och skickar dem till Azure Migrate-tjänsten.
- **Kontrollera VMware PowerCLI 6.5 installerades**: VMware PowerCLI 6.5 PowerShell-modulen måste installeras på den virtuella datorn i insamlaren, så att den kan kommunicera med vCenter-servern.
    - Om insamlaren kan komma åt de URL: er som krävs för att installera modulen, är det installera automatiskt under distributionen av Logginsamlaren.
    - Om insamlaren inte kan installera modulen under distributionen, måste du [installera det manuellt](#install-vwware-powercli-module-manually).
- **Kontrollera anslutningen till vCenter Server**: Insamlaren måste kunna vCenter-servern och fråga för virtuella datorer, metadata och prestandaräknare. [Verifiera förutsättningar](#connect-to-vcenter-server) för att ansluta.


### <a name="connect-to-the-internet-via-a-proxy"></a>Ansluta till internet via en proxyserver

- Om proxyservern kräver autentisering, kan du ange det användarnamn och lösenord när du konfigurerar insamlaren.
- IP-adressen/FQDN för proxyservern bör anges som *http://IPaddress* eller *http://FQDN*.
- Endast HTTP-proxy stöds. HTTPS-baserade proxyservrar stöds inte av insamlaren.
- Om proxyservern är en spärrande proxy, måste du importera proxy-certifikatet för VM-insamlaren.
    1. I den Virtuella insamlardatorn, går du till **Start-menyn** > **hantera datorcertifikat**.
    2. I verktyget certifikat under **certifikat – lokal dator**, hitta **betrodda utgivare** > **certifikat**.

        ![Certifikat-verktyget](./media/concepts-intercepting-proxy/certificates-tool.png)

    3. Kopiera proxy-certifikatet till den Virtuella insamlardatorn. Du kan behöva hämta den från din nätverksadministratör.
    4. Dubbelklicka för att öppna certifikatet och klicka på **installera certifikat**.
    5. I guiden Importera certifikat > Store plats, Välj **lokal dator**.

    ![Plats för certifikatarkiv](./media/concepts-intercepting-proxy/certificate-store-location.png)

    6. Välj **placera alla certifikat i nedanstående arkiv** > **Bläddra** > **betrodda utgivare**. Klicka på **Slutför** att importera certifikatet.

    ![Certifikatarkivet](./media/concepts-intercepting-proxy/certificate-store.png)

    7. Kontrollera att certifikatet har importerats som förväntat och kontrollera att internet-anslutning kravkontrollen fungerar som förväntat.




### <a name="urls-for-connectivity"></a>URL: er för anslutning

Anslutningskontrollen verifieras genom att ansluta till en lista över webbadresser.

**URL** | **Detaljer**  | **Kontrollen av förutsättningar**
--- | --- | ---
*.portal.azure.com | Gäller för Azure-Global. Kontrollerar anslutningen med Azure-tjänsten och tidssynkronisering. | Åtkomst till URL måste anges.<br/><br/> Kravkontroll misslyckas om det finns ingen nätverksanslutning.
*. portal.azure.us | Gäller endast för Azure Government. Kontrollerar anslutningen med Azure-tjänsten och tidssynkronisering. | Åtkomst till URL måste anges.<br/><br/> Kravkontroll misslyckas om det finns ingen nätverksanslutning.
*.oneget.org:443<br/><br/> *.windows.net:443<br/><br/> *.windowsazure.com:443<br/><br/> *.powershellgallery.com:443<br/><br/> *.msecnd.net:443<br/><br/> *.visualstudio.com:443| Används för att hämta PowerShell-modulen vCenter PowerCLI. | Åtkomst till URL: er som är valfritt.<br/><br/> Kravkontrollen misslyckas inte.<br/><br/> Installation av automatiska modulen på VM-insamlaren misslyckas. Du måste installera modulen manuellt.


### <a name="install-vmware-powercli-module-manually"></a>Installera VMware PowerCLI-modulen manuellt

1. Installera modulen med [här](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html). Dessa steg beskriver både online och offline-installation.
2. Om VM-insamlaren är offline och installera om modulen på en annan dator med Internetåtkomst, som du behöver kopiera VMware.* filerna från den datorn för VM-insamlaren.
3. Efter installationen kan du starta om förhandskontroller för att bekräfta att PowerCLI har installerats.

### <a name="connect-to-vcenter-server"></a>Anslut till vCenter Server

Insamlaren ansluter till vCenter-servern och frågar om VM-metadata och prestandaräknare. Här är vad du behöver för anslutningen.

- Endast versioner vCenter Server 5.5, 6.0 och 6.5.
- Du behöver ett skrivskyddat konto med behörigheterna som sammanfattas nedan för identifiering. Endast datacenter som är tillgängliga med kontot kan användas för identifiering.
- Som standard kan du ansluta till vCenter-servern med ett fullständigt domännamn eller IP-adress. VCenter-servern lyssnar på en annan port ska du ansluta till den med hjälp av formuläret *IPAddress:Port_Number* eller *FQDN:Port_Number*.
- Om du vill samla in prestandadata för lagring och nätverk, statistikinställningarna för vCenter-Server måste vara inställd på nivå tre.
- Om kompatibilitetsnivå är lägre än tre, identifiering fungerar men prestandadata samlas inte in. Räknare kan samlas in, men andra anges till noll.
- Om prestandadata för lagring och nätverk samlas inte in, är rekommendationer för storlek baserat prestandadata för CPU och minne och på konfigurationsdata för disk och nätverkskort.
- Insamlaren ska ha nätverket åtkomst till vCenter-servern.

#### <a name="account-permissions"></a>Behörigheterna

**Konto** | **Behörigheter**
--- | ---
Minst ett skrivskyddat användarkonto | Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad   


## <a name="collector-communications"></a>Insamlaren kommunikation

Insamlaren kommunicerar som sammanfattas i följande diagram och tabell.

![Insamlaren kommunikation diagram](./media/tutorial-assessment-vmware/portdiagram.PNG)


**Insamlaren kommunicerar med** | **Port** | **Detaljer**
--- | --- | ---
Tjänsten Azure Migrate | TCP 443 | Insamlaren kommunicerar med Azure Migrate-tjänsten över SSL-port 443.
vCenter Server | TCP 443 | Insamlaren måste kunna kommunicera med vCenter-servern.<br/><br/> Som standard ansluter den till vCenter på 443.<br/><br/> Om vCenter-servern lyssnar på en annan port ska ska den porten vara tillgänglig som utgående port på insamlaren.
RDP | TCP 3389 |


## <a name="securing-the-collector-appliance"></a>Skydda insamlingsprogrammet

Vi rekommenderar följande steg för att skydda insamlingsprogrammet:

- Inte dela eller tappar bort administratörslösenord med obehöriga personer.
- Stäng av installationen när du inte är i användning.
- Placera en enhet i ett nätverk.
- När migreringen är klar tar du bort instansen för installation.
- Dessutom efter migreringen, även ta bort disk säkerhetskopierade filer (vmdk: er), eftersom diskarna kan ha autentiseringsuppgifter för vCenter cachelagrats på dem.

## <a name="os-license-in-the-collector-vm"></a>OS-licens i den Virtuella insamlardatorn

Insamlaren levereras med en utvärderingslicens för Windows Server 2012 R2 som är giltig i 180 dagar. Om utvärderingsperioden upphör att gälla för dina VM-insamlaren, rekommenderas det att hämta en ny OVA och skapar en ny installation.

## <a name="updating-the-os-of-the-collector-vm"></a>Uppdaterar Operativsystemet på den Virtuella insamlardatorn

Även om insamlingsprogrammet har en utvärderingslicens för i 180 dagar, måste du uppdatera Operativsystemet på installationen för att undvika att automatiskt stänga ned enhetens kontinuerligt.

- Om insamlaren kan inte uppdateras i 60 dagar, startar den automatiskt stänger av datorn.
- Om en identifiering körs stängas datorn inte av, även om 60 dagar har gått. Datorn stängs när identifieringen har slutförts.
- Om du har använt insamlaren i mer än 60 dagar, rekommenderar vi att hålla datorn uppdaterat hela tiden genom att köra Windows update.

## <a name="upgrading-the-collector-appliance-version"></a>Uppgradera insamlaren installation version

Du kan uppgradera insamlaren till den senaste versionen utan att hämta ova-filen igen.

1. Ladda ned den [senaste visas uppgraderingspaket](concepts-collector-upgrade.md)
2. För att säkerställa att den nedladdade snabbkorrigeringen är säker, Öppna kommandofönster för administratör och kör följande kommando för att generera en hash för ZIP-filen. Den genererade hashen måste matcha med hash nämns mot den specifika versionen:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (exempel på användning C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Kopiera zip-filen till Azure Migrate insamlarens virtuella dator (insamlingsprogrammet).
4. Högerklicka på zip-filen och välja extrahera alla.
5. Högerklicka på Setup.ps1 och välj kör med PowerShell och följ anvisningarna på skärmen för att installera uppdateringen.

## <a name="discovery-process"></a>Processen för identifiering

När installationen har konfigurerats, kan du köra identifiering. Så fungerar här som:

- Du kan köra en identifiering av omfång. Alla virtuella datorer i den angivna vCenter inventering sökvägen ska identifieras.
    - Du kan ange ett omfång i taget.
    - Omfånget kan innehålla 1 500 virtuella datorer eller mindre.
    - Omfånget kan vara en datacenter-, mapp- eller ESXi-värden.
- När du har anslutit till vCenter Server, kan du ansluta genom att ange ett migreringsprojekt för samlingen.
- Virtuella datorer har identifierats och deras metadata och prestanda skickas till Azure. De här åtgärderna är en del av ett jobb i samlingen.
    - Insamlaren ges ett specifikt ID för insamlaren som sparas för en viss dator mellan identifieringar.
    - Ett jobb för som körs samling ges specifika sessions-ID. ID: T ändras för varje samling jobb och kan användas för felsökning.

### <a name="collected-metadata"></a>Insamlade metadata

Insamlingsprogrammet identifierar följande konfigurationsmetadata för varje virtuell dator. Konfigurationsinformationen för de virtuella datorerna finns en timme efter att du startar identifieringen.

- Visningsnamn för virtuell dator (på vCenter-Server)
- Virtuella datorns lager sökväg (värd/mappen på vCenter-Server)
- IP-adress
- MAC-adress
- Operativsystem
- Antal kärnor, diskar, nätverkskort
- Minnesstorlek, diskstorlekar
- Prestandaräknare för den virtuella datorn, disk och nätverk.

#### <a name="performance-counters"></a>Prestandaräknare

 Insamlaren samlar in följande prestandaräknare för varje virtuell dator från ESXi-värd med ett intervall på 20 sekunder. Dessa räknare är vCenter räknare och även om termer som säger medelvärde, 20 sekunder exemplen finns räknare i realtid. Prestandadata för de virtuella datorerna börjar bli tillgänglig i portalen två timmar efter identifieringen har startats. Vi rekommenderar starkt att vänta minst en dag innan du skapar prestandabaserad utvärderingar för att få korrekt rätt storleksrekommendationer. Om du letar efter direkt, du kan skapa utvärderingar med storlekskriteriet som *som lokalt* som inte ska undersöka prestandadata för rätt storleksändring.

**Räknaren** |  **Påverkan på utvärdering**
--- | ---
CPU.Usage.Average | Rekommenderad storlek och kostnad  
Mem.Usage.Average | Rekommenderad storlek och kostnad  
virtualDisk.read.average | Beräknar diskens storlek, kostnaden för lagring, VM-storlek
virtualDisk.write.average | Beräknar diskens storlek, kostnaden för lagring, VM-storlek
virtualDisk.numberReadAveraged.average | Beräknar diskens storlek, kostnaden för lagring, VM-storlek
virtualDisk.numberWriteAveraged.average | Beräknar diskens storlek, kostnaden för lagring, VM-storlek
NET.Received.Average | Beräknar storlek på virtuell dator                          
NET.Transmitted.Average | Beräknar storlek på virtuell dator     

## <a name="next-steps"></a>Nästa steg

[Konfigurera en utvärdering för lokala virtuella VMware-datorer](tutorial-assessment-vmware.md)
