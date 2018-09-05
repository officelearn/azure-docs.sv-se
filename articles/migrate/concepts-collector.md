---
title: Insamlingsprogrammet i Azure Migrate | Microsoft Docs
description: Översikt över insamlingsprogrammet och hur du konfigurerar den.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/25/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: 74caf0ab052e1f6558dc20d15d84c01177b3f9cb
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665588"
---
# <a name="collector-appliance"></a>Insamlingsprogrammet

[Azure Migrate](migrate-overview.md) utvärderar lokala arbetsbelastningar för migrering till Azure. Den här artikeln innehåller information om hur du använder insamlingsprogrammet.



## <a name="overview"></a>Översikt

Ett Azure Migrate Collector är en enkel installation som kan användas för att identifiera din lokala vCenter-miljö. Den här installationen identifierar lokala VMware-datorer och skickar metadata om dem till Azure Migrate-tjänsten.

Insamlaren är en OVF som du kan hämta från Azure Migrate-projektet. Den skapar en instans av en virtuell VMware-dator med 4 kärnor, 8 GB RAM-minne och en disk på 80 GB. Operativsystemet på enheten som är Windows Server 2012 R2 (64-bitars).

Du kan skapa insamlaren genom att följa stegen här – [så här skapar du VM-insamlaren](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="collector-communication-diagram"></a>Insamlaren kommunikation diagram

![Insamlaren kommunikation diagram](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Komponent      | För att kommunicera med   | Port som krävs                            | Orsak                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Insamlare      | Tjänsten Azure Migrate | TCP 443                                  | Insamlaren ska kunna kommunicera med tjänsten via SSL-port 443 |
| Insamlare      | vCenter Server        | Standard 443                             | Insamlaren ska kunna kommunicera med vCenter-servern. Den ansluter till vCenter på 443 som standard. Om vCenter lyssnar på en annan port ska ska den porten vara tillgängliga som utgående port på insamlaren |
| Insamlare      | RDP|   | TCP 3389 | Du ska kunna använda RDP till insamlardatorn |





## <a name="collector-pre-requisites"></a>Förutsättningar för insamlaren

Insamlaren måste skicka några sökningar efter nödvändiga komponenter för att säkerställa att den kan ansluta till tjänsten Azure Migrate och ladda upp den identifierade data. Den här artikeln tittar på var och en av förutsättningarna och förstå varför det är obligatoriskt.

### <a name="internet-connectivity"></a>Internetanslutning

Insamlingsprogrammet måste vara ansluten till internet för att skicka information om identifierade datorer. Du kan ansluta datorn till internet i något av följande två sätt.

1. Du kan konfigurera insamlaren för att ha direkt Internetanslutning.
2. Du kan konfigurera insamlaren ansluter via en proxyserver.
    * Om proxyservern kräver autentisering, kan du ange användarnamnet och lösenordet i anslutningsinställningarna.
    * IP-adressen/FQDN för proxyservern måste ha formatet http://IPaddress eller http://FQDN. Endast http-proxy stöds.

> [!NOTE]
> HTTPS-baserade proxyservrar stöds inte av insamlaren.

#### <a name="internet-connectivity-with-intercepting-proxy"></a>Ansluten till Internet med spärra proxy

Om proxyservern som du använder för att ansluta till internet är en spärrande proxy, behöver du importera proxy-certifikatet till den Virtuella insamlardatorn. Följande är anvisningar om hur du kan importera certifikatet till den Virtuella insamlardatorn.

1. I den Virtuella insamlardatorn, går du till **Start-menyn** och leta upp och öppna **hantera datorcertifikat**.
2. I verktyget certifikat i det vänstra fönstret under **certifikat – lokal dator**, hitta **betrodda utgivare**. Under **betrodda utgivare**, klickar du på **certifikat** att se listan över certifikat i rutan till höger.

    ![Certifikat-verktyget](./media/concepts-intercepting-proxy/certificates-tool.png)

3. Kopiera dina proxy-certifikatet till den Virtuella insamlardatorn. Du kan behöva kontakta teamet för nätverk-administratör i din organisation att hämta det här certifikatet.
4. Dubbelklicka på certifikatet att öppna den. Klicka på **installera certifikat**. Då kommer du att guiden Importera certifikat.
5. I guiden Importera certifikat för Store-plats, väljer **lokal dator**. **Klicka på nästa**.

    ![Plats för certifikatarkiv](./media/concepts-intercepting-proxy/certificate-store-location.png)

6. Välj alternativet att **placera alla certifikat i nedanstående arkiv**. Klicka på **Bläddra** och välj **betrodda utgivare** från listan över certifikat som kommer upp. Klicka på **Nästa**.

    ![Certifikatarkivet](./media/concepts-intercepting-proxy/certificate-store.png)
    
7. Klicka på **Slutför**. Certifikatet importeras. 
8. Alternativt kan du verifiera certifikatet har importerats genom att öppna verktyget certifikat som i steg 1 och 2 ovan.
9. Kontrollera den nödvändiga kontrollen för internet-anslutningen har utförts på Azure Migrate collector-appen.


#### <a name="whitelisting-urls-for-internet-connection"></a>Lista över tillåtna URL: er för internet-anslutning

Den nödvändiga kontrollen har lyckats om insamlaren kan ansluta till internet via de angivna inställningarna. Anslutningskontrollen verifieras genom att ansluta till en lista över webbadresser som anges i följande tabell. Om du använder någon Webbadressbaserad brandväggsproxy för att styra utgående anslutningar, var noga med att du godkänna följande obligatoriska URL: er:

**URL** | **Syfte**  
--- | ---
*.portal.azure.com | Krävs för att kontrollera anslutningen med Azure-tjänsten och validera tidssynkronisering utfärdar.

Dessutom kontrollen också försöker verifiera anslutningarna till följande webbadresser men misslyckas inte kontrollen om det är inte tillgänglig. Konfigurera listan över godkända adresser för följande URL: er är valfritt, men du behöver ta manuella steg för att minimera den nödvändiga kontrollen.

**URL** | **Syfte**  | **Vad händer om du inte godkända**
--- | --- | ---
*.oneget.org:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI-modulen. | PowerCLI-installationen misslyckas. Installera modulen manuellt.
*.windows.net:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI-modulen. | PowerCLI-installationen misslyckas. Installera modulen manuellt.
*.windowsazure.com:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI-modulen. | PowerCLI-installationen misslyckas. Installera modulen manuellt.
*.powershellgallery.com:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI-modulen. | PowerCLI-installationen misslyckas. Installera modulen manuellt.
*.msecnd.net:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI-modulen. | PowerCLI-installationen misslyckas. Installera modulen manuellt.
*.visualstudio.com:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI-modulen. | PowerCLI-installationen misslyckas. Installera modulen manuellt.

### <a name="time-is-in-sync-with-the-internet-server"></a>Tiden är synkroniserad med internet-servern

Insamlaren ska vara synkroniserade med internet-tidsservern så autentiseras begäranden till tjänsten. Portal.azure.com URL: en ska vara kan nås från insamlaren så att tiden kan valideras. Om datorn är osynkroniserad, måste du ändra clock-tid på den insamlaren virtuella datorn så att den matchar den aktuella tiden på följande sätt:

1. Öppna Kommandotolken som administratör på den virtuella datorn.
1. Kör w32tm /tz för att kontrollera den aktuella tidszonen.
1. Kör w32tm/resync för att synkronisera tiden.

### <a name="collector-service-should-be-running"></a>Collector-tjänsten ska köras

Azure Migrate Collector-tjänsten ska köras på datorn. Den här tjänsten startas automatiskt när datorn startas. Om tjänsten inte körs kan du starta den *Azure Migrate Collector* tjänsten via Kontrollpanelen. Collector-tjänsten ansvarar för att ansluta till vCenter-servern, samla in Maskindata metadata och prestanda och skicka den till tjänsten.

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5

VMware PowerCLI powershell-modulen måste vara installerad så att insamlaren kan kommunicera med vCenter-servern och fråga efter information om datorn och deras prestandadata. Powershell-modulen är automatiskt hämtas och installeras som en del av den nödvändiga kontrollen. Automatisk nedladdning kräver några URL: er tillåts, misslyckas som du måste antingen ge åtkomst till genom att vitlista dem eller installera modulen manuellt.

Installera modulen manuellt med hjälp av följande steg:

1. Följ stegen i för att installera PowerCli insamlaren utan internet-anslutning, [den här länken](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html) .
2. När du har installerat PowerShell-modulen på en annan dator som är ansluten till internet, måste du kopiera filerna VMware.* från den datorn till insamlardatorn.
3. Starta om de nödvändiga kontrollerna och bekräfta att PowerCLI har installerats.

## <a name="connecting-to-vcenter-server"></a>Ansluta till vCenter-Server

Insamlaren ska ansluta till vCenter-servern och kunna skicka frågor för de virtuella datorerna och deras metadata sina prestandaräknare. Dessa data används av projektet för att beräkna en utvärdering.

1. För att ansluta till vCenter-servern, ett skrivskyddat konto med behörigheter som anges i följande tabell kan användas för att köra identifieringen.

    |Aktivitet  |Nödvändiga administratörsrollskontot  |Behörigheter  |
    |---------|---------|---------|
    |Insamlaren installation baserad identifiering    | Du behöver minst en skrivskyddad användare        |Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad         |

2. Dessa datacenter som är tillgängliga för det angivna vCenter-kontot kan nås för identifiering.
3. Du måste ange Vcentre-FQDN/IP-adress att ansluta till vCenter-servern. Som standard ansluter det via port 443. Om du har konfigurerat vCenter för att lyssna på ett annat portnummer, kan du ange den som en del av serveradressen i formatet IPAddress:Port_Number eller FQDN:Port_Number.
4. Statistikinställningarna för vCenter-servern ska vara inställd på nivå 3 innan du påbörjar distributionen. Om kompatibilitetsnivå är lägre än 3 identifieringen slutförs, men prestandadata för lagring och nätverk samlas inte in. Storleksrekommendationer för utvärdering baseras i det här fallet på prestandadata för CPU och minne och konfigurationsdata för disk och nätverkskort. [Läs mer](./concepts-collector.md) på vilka data som samlas in och hur det påverkar utvärderingen.
5. Insamlaren ska ha nätverket åtkomst till vCenter-servern.

> [!NOTE]
> Endast versioner vCenter Server 5.5, 6.0 och 6.5 officiellt.

> [!IMPORTANT]
> Vi rekommenderar att du ställer in vanliga filegenskaper (3) för statistik nivå så att alla prestandaräknare som samlas in korrekt. Om du har satt till en lägre nivå vCenter, kan endast några räknare samlas helt, med resten har angetts till 0. Utvärderingen kan sedan visa ofullständiga data.

### <a name="selecting-the-scope-for-discovery"></a>Att välja omfattningen för identifiering

Du kan välja en omfattning att identifiera efter att ha anslutit till vCenter. Markerar ett omfång identifierar alla virtuella datorer från den angivna vCenter inventering sökvägen.

1. Omfånget kan vara ett datacenter, en mapp eller en ESXi-värd.
2. Du kan bara välja ett omfång i taget. För att välja flera virtuella datorer, kan du genomföra en identifiering och starta om processen för identifiering med ett nytt scope.
3. Du kan bara välja en omfattning som har *mindre än 1 500 virtuella datorer*.

## <a name="specify-migration-project"></a>Ange migreringsprojektet

När den lokala vCenter är ansluten och en omfattning har angetts kan ange du nu migrering projekt-detaljer som ska användas för identifiering och utvärdering. Ange projekt-ID och nyckel och Anslut.

## <a name="start-discovery-and-view-collection-progress"></a>Starta identifiering och visa insamlingsförloppet

När identifieringen startar de virtuella datorerna på vCenter identifieras och deras metadata och prestanda skickas till servern. Statusen informerar dig om följande-ID:

1. Insamlar-ID: Ett unikt ID som ges till din dator för Logginsamlaren. Detta ID ändras inte för en viss dator över olika identifieringar. Du kan använda detta ID vid fel när rapportera problemet till Microsoft Support.
2. Sessions-ID: Ett unikt ID för det pågående jobbet är samling. Du kan referera till samma sessions-ID i portalen om identifiering av jobbet har slutförts. Det här ID: T ändras för varje samling jobbet. Vid fel, kan du rapportera detta ID till Microsoft Support.

### <a name="what-data-is-collected"></a>Vilka data som samlas in?

Samling jobbet identifieras följande statiska metadata om de valda virtuella datorerna.

1. VM-visningsnamn (på vCenter)
2. Virtuella datorns lager sökväg (värd/mapp i vCenter)
3. IP-adress
4. MAC-adress
5. Operativsystem
5. Antal kärnor, diskar, nätverkskort
6. Minnesstorlek, diskstorlekar
7. Och prestandaräknarna för den virtuella datorn, disk och nätverk som anges i tabellen nedan.

I följande tabell visas de prestandaräknare som samlas in och visar också utvärderingsresultat som påverkas om en särskild räknare inte har samlats in.

|Räknare                                  |Nivå    |Nivå per enhet  |Utvärdering av påverkan                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|CPU.Usage.Average                        | 1       |Ej tillämpligt                |Rekommenderad storlek och kostnad                    |
|Mem.Usage.Average                        | 1       |Ej tillämpligt                |Rekommenderad storlek och kostnad                    |
|virtualDisk.read.average                 | 2       |2                 |Diskens storlek, kostnaden för lagring och VM-storlek         |
|virtualDisk.write.average                | 2       |2                 |Diskens storlek, kostnaden för lagring och VM-storlek         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Diskens storlek, kostnaden för lagring och VM-storlek         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Diskens storlek, kostnaden för lagring och VM-storlek         |
|NET.Received.Average                     | 2       |3                 |VM-storlek och nätverk kostnad                        |
|NET.Transmitted.Average                  | 2       |3                 |VM-storlek och nätverk kostnad                        |

> [!WARNING]
> Om du precis har högre statistik, ska det ta upp till en dag att generera prestandaräknare. Därför rekommenderar vi att du kör identifieringen efter en dag.

### <a name="time-required-to-complete-the-collection"></a>Tid som krävs för att slutföra insamlingen

Insamlaren endast identifieras data för den dator och skickar det till projektet. Projektet kan ta längre tid innan de identifierade data visas på portalen och du kan börja skapa en utvärdering.

Baserat på antalet virtuella datorer i det valda omfånget, tar det upp till 15 minuter för att skicka statiska metadata i projektet. När statiska metadata är tillgängliga på portalen kan du se en lista över datorer i portalen och börja skapa grupper. En utvärdering kan inte skapas förrän samling jobbet har slutförts och projektet har bearbetat data. När samlingen jobbet har slutförts på insamlaren, det kan ta upp till en timme efter prestandadata ska vara tillgängliga på portalen, baserat på antalet virtuella datorer i det valda omfånget.

## <a name="locking-down-the-collector-appliance"></a>Låsa insamlingsprogrammet
Vi rekommenderar att körs kontinuerliga Windows-uppdateringar på insamlingsprogrammet. Om en insamlare inte har uppdaterats i 60 dagar startar insamlaren automatiskt stänger av datorn. Om en identifiering körs kommer datorn inte stängas av, även om den har passerat dess 60 dagars period. Efter identifiering av jobbet har slutförts, datorn kommer att inaktiveras. Om du använder insamlaren i mer än 45 dagar, rekommenderar vi att hålla datorn uppdaterat hela tiden genom att köra Windows update.

Vi rekommenderar också följande steg för att skydda din installation
1. Dela inte eller tappar bort administratörslösenord med obehöriga personer.
2. Stäng av installationen när du inte är i användning.
3. Placera en enhet i ett nätverk.
4. När migreringen arbetet är klart, tar du bort instansen för installation. Måste du också ta bort disken säkerhetskopiering av filer (vmdk: er), eftersom diskarna kan ha autentiseringsuppgifter för vCenter cachelagrats på dem.

## <a name="how-to-upgrade-collector"></a>Uppgradera insamlaren

Du kan uppgradera insamlaren till den senaste versionen utan att hämta ova-filen igen.

1. Hämta senaste [uppgraderingspaketet](https://aka.ms/migrate/col/upgrade_9_14) (version 1.0.9.14).
2. För att säkerställa att den nedladdade snabbkorrigeringen är säker, Öppna kommandofönster för administratör och kör följande kommando för att generera en hash för ZIP-filen. Den genererade hashen måste matcha med hash nämns mot den specifika versionen:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (exempel på användning C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)
3. Kopiera zip-filen till Azure Migrate insamlarens virtuella dator (insamlingsprogrammet).
4. Högerklicka på zip-filen och välja extrahera alla.
5. Högerklicka på Setup.ps1 och välj kör med PowerShell och följ anvisningarna på skärmen för att installera uppdateringen.

### <a name="list-of-updates"></a>Listan över uppdateringar

#### <a name="upgrade-to-version-10914"></a>Uppgradera till version 1.0.9.14

Hash-värden för uppgradering [paketera 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="upgrade-to-version-10913"></a>Uppgradera till version 1.0.9.13

Hash-värden för uppgradering [paketera 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

#### <a name="upgrade-to-version-10911"></a>Uppgradera till version 1.0.9.11

Hash-värden för uppgradering [paketera 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

#### <a name="upgrade-to-version-1097"></a>Uppgradera till version 1.0.9.7

Hash-värden för uppgradering [paketera 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="next-steps"></a>Nästa steg

[Konfigurera en utvärdering för lokala virtuella VMware-datorer](tutorial-assessment-vmware.md)
