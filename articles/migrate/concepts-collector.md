---
title: Installation av insamlaren i Azure migrera | Microsoft Docs
description: En översikt över insamlaren installation och hur du konfigurerar den.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: 059f577c138847af04e92ce9ab12a8de88251c73
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="collector-appliance"></a>Installation av insamlaren

[Azure migrera](migrate-overview.md) utvärderar lokala arbetsbelastningar för migrering till Azure. Den här artikeln innehåller information om hur du använder insamlaren installation.



## <a name="overview"></a>Översikt

En Azure migrera insamlare är en förenklad installation som kan användas för att identifiera lokala vCenter-miljö. Den här enheten identifieras lokala VMware-datorer och skickar metadata om dem till Azure migrera-tjänsten.

Insamlaren installation är ett OVF som du kan hämta från Azure migrera projektet. Den instantierar en VMware-dator med 4 kärnor, 8 GB RAM-minne och en disk på 80 GB. Enhetens operativsystemet är Windows Server 2012 R2 (64-bitars).

Du kan skapa insamlaren genom att följa stegen här - [hur du skapar den virtuella datorn insamlaren](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="collector-communication-diagram"></a>Insamlaren kommunikation diagram

![Insamlaren kommunikation diagram](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Komponent      | För att kommunicera med   | Port som krävs                            | Orsak                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Insamlare      | Tjänsten Azure Migrate | TCP 443                                  | Insamlaren ska kunna kommunicera med tjänsten via SSL-port 443 |
| Insamlare      | vCenter Server        | Standard 443                             | Insamlaren ska kunna kommunicera med vCenter-servern. Ansluter den till vCenter på 443 som standard. Om vCenter lyssnar på en annan port, ska porten vara tillgänglig som utgående port på insamlaren |
| Insamlare      | RDP|   | TCP 3389 | Att kunna RDP till insamlaren-dator |





## <a name="collector-pre-requisites"></a>Förutsättningar för insamlaren

Insamlaren behöver skicka några förutvärdering kontroller för att kontrollera att den kan ansluta till tjänsten Azure migrera och överföra identifierade data. Den här artikeln tittar på var och en av förutsättningarna och förstå varför det är obligatoriskt.

### <a name="internet-connectivity"></a>Internetanslutning

Insamlaren installation måste vara ansluten till internet för att skicka information identifierade datorer. Du kan ansluta datorn till internet i något av följande två sätt.

1. Du kan konfigurera insamlaren så att den har direkt Internetanslutning.
2. Du kan konfigurera insamlaren ansluter via en proxyserver.
    * Om proxyservern kräver autentisering, kan du ange användarnamnet och lösenordet i anslutningsinställningarna.
    * IP-adress/FQDN för proxyservern måste ha formatet http://IPaddress eller http://FQDN. Http-proxy stöds.

> [!NOTE]
> HTTPS-baserade proxyservrar stöds inte av insamlaren.

#### <a name="whitelisting-urls-for-internet-connection"></a>Vitlistning av URL: er för internet-anslutning

Förutvärdering Kontrollera är om insamlaren kan ansluta till internet via de angivna inställningarna. Kontrollen av anslutningen verifieras genom att ansluta till en lista över webbadresser som anges i följande tabell. Om du använder alla URL-baserade brandväggen proxy för att styra utgående anslutning, att kontrollera listan över godkända följande obligatoriska URL: er:

**URL** | **Purpose**  
--- | ---
*.portal.azure.com | Krävs för att kontrollera anslutningen till Azure-tjänsten och validera tidssynkronisering utfärdar.

Dessutom kontrollen också försöker att verifiera anslutningarna till följande URL: er men misslyckas inte kontrollen om det är inte tillgänglig. Konfigurera godkända för följande URL: er är valfritt men du måste utföra manuella åtgärder för att minimera förutvärdering kontrollen.

**URL** | **Purpose**  | **Om du inte godkända**
--- | --- | ---
*.oneget.org:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI modul. | PowerCLI installationen misslyckas. Installera modulen manuellt.
*.windows.net:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI modul. | PowerCLI installationen misslyckas. Installera modulen manuellt.
*.windowsazure.com:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI modul. | PowerCLI installationen misslyckas. Installera modulen manuellt.
*.powershellgallery.com:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI modul. | PowerCLI installationen misslyckas. Installera modulen manuellt.
*.msecnd.net:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI modul. | PowerCLI installationen misslyckas. Installera modulen manuellt.
*.visualstudio.com:443 | Krävs för baserad att ladda ned powershell vCenter PowerCLI modul. | PowerCLI installationen misslyckas. Installera modulen manuellt.

### <a name="time-is-in-sync-with-the-internet-server"></a>Tid är synkroniserad med internet-servern

Insamlaren ska vara synkroniserade med tiden Internetserver så autentiseras förfrågningar till tjänsten. Portal.azure.com url ska kunna nås från insamlaren så att tid som kan verifieras. Om datorn är inte synkroniserade, måste du ändra tiden klockan på den insamlaren virtuella datorn så att den matchar den aktuella tiden på följande sätt:

1. Öppna en kommandotolk för administratörer på den virtuella datorn.
1. Kör w32tm /tz för att kontrollera tidszonen.
1. Kör w32tm/resync för att synkronisera tiden.

### <a name="collector-service-should-be-running"></a>Collector-tjänsten ska köras

Azure migrera Collector-tjänsten ska köras på datorn. Den här tjänsten startas automatiskt när datorn startas. Om tjänsten inte körs kan du starta den *Azure migrera insamlaren* tjänsten via Kontrollpanelen. Collector-tjänsten ansvarar för att ansluta till vCenter-servern, samla in data för den dator metadata och prestanda och skicka den till tjänsten.

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5 

VMware PowerCLI powershell-modulen måste vara installerad så att insamlaren kan kommunicera med vCenter-servern och fråga om information om datorn och deras prestandadata. Powershell-modulen automatiskt hämtas och installeras som en del av kontrollen krävs. Automatisk hämtning kräver några vitlistad för URL: er, misslyckas som du måste ange antingen åtkomst av vitlistning av dem, eller installera modulen manuellt.

Installera modulen manuellt med hjälp av följande steg:

1. För att installera PowerCli insamlaren utan internet-anslutning, följer du stegen i [länken](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html) .
2. När du har installerat PowerShell-modulen på en annan dator som har tillgång till internet, måste du kopiera filerna VMware.* från samma dator till insamlaren-datorn.
3. Starta om de nödvändiga kontrollerna och bekräfta att PowerCLI är installerad.

## <a name="connecting-to-vcenter-server"></a>Ansluta till vCenter-servern

Insamlaren ska ansluta till vCenter-servern och kan fråga efter de virtuella datorerna och deras metadata sina prestandaräknare. Dessa data används av projektet för att beräkna en utvärdering.

1. För att ansluta till vCenter-servern, en skrivskyddad konto med behörighet som anges i följande tabell kan användas för att köra identifieringen. 

    |Aktivitet  |Nödvändiga roll-konto  |Behörigheter  |
    |---------|---------|---------|
    |Insamlaren installation baserad identifiering    | Du behöver minst en skrivskyddad användare        |Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad         |

2. Endast datacenter som är tillgängliga för det angivna kontot vCenter kan användas för identifiering.
3. Du måste ange Vcentre-FQDN eller IP-adress att ansluta till vCenter-servern. Som standard ansluter via port 443. Om du har konfigurerat vCenter att lyssna på ett annat portnummer, kan du ange den som en del av serveradressen i formatet IPAddress:Port_Number eller FQDN:Port_Number.
4. Statistik inställningarna för vCenter-servern ska anges till nivå 3 innan du börjar distributionen. Om nivån är lägre än 3 identifieringen kommer att slutföras, men kommer inte att samlas in prestandadata för lagring och nätverk. Assessment storlek rekommendationerna baseras i det här fallet på prestandadata för CPU och minne och konfigurationsdata för disk och nätverkskort. [Läs mer](./concepts-collector.md) på vilka data som samlas in och hur den påverkar bedömningen.
5. Insamlaren ska ha ett nätverk fri vCenter-servern.

> [!NOTE]
> Endast versioner vCenter Server 5.5, 6.0 och 6.5 officiellt.

> [!IMPORTANT]
> Vi rekommenderar att du vanliga filegenskaper (3) för statistik nivån så att alla räknare samlas in korrekt. Om du har vCenter på en lägre nivå kan bara några räknare samlas helt, med de övriga anges som 0. Bedömning kan sedan visa ofullständiga data. 

### <a name="selecting-the-scope-for-discovery"></a>Att välja omfattning för identifiering av

Du kan välja ett scope för att identifiera när du är ansluten till vCenter. Att välja ett scope identifierar alla virtuella datorer från den angivna vCenter inventering sökvägen.

1. Omfattningen kan vara ett datacenter, en mapp eller en ESXi-värd. 
2. Du kan bara välja ett omfång i taget. Om du vill välja flera virtuella datorer kan du slutföra en identifiering och starta om processen för identifiering med ett nytt scope.
3. Du kan bara välja en omfattning som har *mindre än 1500 virtuella datorer*.

## <a name="specify-migration-project"></a>Ange migreringsprojekt

När lokal vCenter är ansluten och en omfattning har angetts kan ange du nu migrering projektinformationen som ska användas för identifiering och assessment. Ange projekt-ID och nyckel och ansluta.

## <a name="start-discovery-and-view-collection-progress"></a>Starta identifiering och visa förloppet för samlingen

När identifieringen startar de virtuella datorerna vCenter identifieras och deras metadata och prestanda data skickas till servern. Statusen informerar dig om följande-ID:

1. Insamlaren-ID: Ett unikt ID som ges till insamlaren-datorn. Detta ID ändras inte för en viss dator över olika identifieringar. Du kan använda detta ID vid fel när rapportera problemet till Microsoft Support.
2. Sessions-ID: Ett unikt ID för det jobb som körs för den samlingen. Du kan referera till samma sessions-ID i portalen när identifiering av jobbet har slutförts. Detta ID ändras för varje samling jobb. Vid fel, kan du rapportera detta ID till Microsoft Support.

### <a name="what-data-is-collected"></a>Vilka data som samlas in?

Samlingen jobbet identifieras följande statiska metadata om de valda virtuella datorerna. 

1. VM visningsnamn (på vCenter)
2. Virtuella datorns lager sökväg (värd/mapp i vCenter)
3. IP-adress
4. MAC-adress
5. Antal kärnor, diskar, nätverkskort
6. RAM-minne, diskstorlekar
7. Och prestandaräknare för virtuell dator, Disk och nätverk som anges i tabellen nedan.

I följande tabell visas de prestandaräknare som samlas in och visar också utvärderingsresultat som påverkas om räknaren inte samlas in.

|Räknaren                                  |Nivå    |Nivå per enhet  |Bedömning påverkan                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |Ej tillämpligt                |Rekommenderade VM-storlek och kostnad                    |
|mem.usage.average                        | 1       |Ej tillämpligt                |Rekommenderade VM-storlek och kostnad                    |
|virtualDisk.read.average                 | 2       |2                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|virtualDisk.write.average                | 2       |2                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|net.received.average                     | 2       |3                 |VM-storlek och nätverket kostnad                        |
|net.transmitted.average                  | 2       |3                 |VM-storlek och nätverket kostnad                        |

> [!WARNING]
> Om du precis har högre statistik, tar det till en dag att generera prestandaräknare. Därför rekommenderar vi att du kör identifieringen efter en dag.

### <a name="time-required-to-complete-the-collection"></a>Tid som krävs för att slutföra samlingen

Insamlaren endast identifieras data för den dator och skickar den till projektet. Projektet kan ta längre tid innan identifierade data visas på portalen och du kan börja skapa en utvärdering.

Baserat på antalet virtuella datorer i det valda omfånget, tar det upp till 15 minuter för att skicka statiska metadata i projektet. När statiska metadata är tillgängliga på portalen kan du se en lista över datorer i portalen och börja skapa grupper. En utvärdering kan inte skapas förrän samling jobbet har slutförts och projektet har bearbetat data. En gång samling jobbet slutförs på insamlaren, det kan ta upp till en timme för prestandadata ska vara tillgängliga på portalen, baserat på antalet virtuella datorer i det valda omfånget.

## <a name="locking-down-the-collector-appliance"></a>Låsa insamlaren-enhet
Vi rekommenderar att du kör kontinuerligt Windows-uppdateringar på insamlaren-enhet. Om insamling och inte har uppdaterats för 45 dagar startar insamlaren automatiskt stänga av datorn. Om en identifiering körs inaktiveras datorn inte, även om den har passerat dess 45 dagar. Efter identifiering av jobbet har slutförts, kommer datorn att stängas av. Om du använder insamlaren mer än 45 dagar, rekommenderar vi att hålla datorn uppdateras vid alla tidpunkter genom att köra Windows update.

Vi rekommenderar också att följande steg för att skydda din enhet
1. Dela eller inte misplace administratörslösenord med obehöriga personer.
2. Stäng av enheten som.
3. Placera enheten i ett skyddat nätverk.
4. När arbetet migreringen är klar, ta bort en enhet-instans. Se till att även ta bort disken säkerhetskopiera filer (VMDKs) som diskarna kanske har vCenter autentiseringsuppgifter cachelagras på dem.

## <a name="how-to-upgrade-collector"></a>Så här uppgraderar du insamlaren

Du kan uppgradera insamlaren till den senaste versionen utan att hämta ägg igen.

1. Ladda ned senaste [uppgraderingspaket](https://aka.ms/migrate/col/latestupgrade).
2. För att säkerställa att hämtade snabbkorrigeringen är säker, öppna kommandofönstret administratör och kör följande kommando för att generera hash för ZIP-filen. Genererade hash-värdet ska vara identiskt med hash-algoritmen som nämns mot en viss version:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    
    (exempel på användning C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.5.zip SHA256)
3. Kopiera zip-filen till den Azure migrera insamlaren virtuell datorn (insamlaren installation).
4. Högerklicka på zip-filen och välj extrahera alla.
5. Högerklicka på Setup.ps1 och välj kör med PowerShell och följ anvisningarna på skärmen för att installera uppdateringen.

### <a name="list-of-updates"></a>Listan över uppdateringar

#### <a name="upgrade-to-version-1097"></a>Uppgradera till version 1.0.9.7

För uppgradering till version 1.0.9.7 download [paketet](https://aka.ms/migrate/col/upgrade_9_7)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

#### <a name="upgrade-to-version-1095"></a>Uppgradera till version 1.0.9.5

För uppgradering till version 1.0.9.5 download [paketet](https://aka.ms/migrate/col/upgrade_9_5)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | d969ebf3bdacc3952df0310d8891ffdf
SHA1 | f96cc428eaa49d597eb77e51721dec600af19d53
SHA256 | 07c03abaac686faca1e82aef8b80e8ad8eca39067f1f80b4038967be1dc86fa1

## <a name="next-steps"></a>Nästa steg

[Konfigurera en bedömning för lokala virtuella VMware-datorer](tutorial-assessment-vmware.md)
