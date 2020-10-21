---
title: Migrering av StorSimple 8000-serien till Azure File Sync
description: Lär dig hur du migrerar en StorSimple 8100-eller 8600-apparat till Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: dcb690829aaed1d5a256f53766f05c922f8b2a9a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331122"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 och 8600-migrering till Azure File Sync

StorSimple 8000-serien representeras av antingen 8100 eller 8600 fysiska, lokala apparater och deras moln tjänst komponenter. Det är möjligt att migrera data från någon av dessa apparater till en Azure File Syncs miljö. Azure File Sync är standard och strategisk Azure-tjänst som StorSimple-apparater kan migreras till.

StorSimple 8000-serien kommer att uppnå [livs längd](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) i december 2022. Det är viktigt att börja planera migreringen så snart som möjligt. Den här artikeln innehåller de steg som krävs för att migrera till Azure File Sync. 

## <a name="phase-1-prepare-for-migration"></a>Fas 1: Förbered för migrering

Det här avsnittet innehåller de steg som du bör utföra i början av migreringen från StorSimple-volymer till Azure-filresurser.

### <a name="inventory"></a>Inventering
När du börjar planera migreringen ska du först identifiera alla StorSimple-enheter och-volymer som du behöver migrera. När du har gjort det kan du välja den bästa sökvägen för migrering.

* StorSimple fysiska apparater (8000-serien) Använd den här migreringsguiden. 
* Virtuella apparater, [StorSimple 1200-serien, använder en annan migrations guide](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Översikt över migrerings kostnad
Migreringar till Azure-filresurser från StorSimple volymer via data omvandlings jobb i en StorSimple Data Manager resurs är kostnads fria. Det finns dock andra kostnader som kan uppstå under och efter migreringen:

* **Utgående nätverk:** Dina StorSimple-filer är Live i ett lagrings konto inom en angiven Azure-region. Om du etablerar Azure-filresurser som du migrerar till i ett lagrings konto som finns i samma Azure-region inträffar ingen utgående kostnad. Du kan flytta dina filer till ett lagrings konto i en annan region som en del av migreringen. I så fall kommer utgående kostnader att gälla för dig.
* **Azure-fil resurs transaktioner:** När filer kopieras till en Azure-filresurs (som en del av en migrering eller utanför en), tillämpas transaktions kostnader som filer och metadata skrivs. Vi rekommenderar att du startar Azure-filresursen på transaktionens optimerade nivå under migreringen. Växla till önskad nivå när migreringen är klar. Stegen nedan kommer att anropa detta vid lämplig tidpunkt.
* **Ändra en Azure-fildelnings nivå:** Ändra nivån för en Azure-filresurs kostnader transaktioner. I de flesta fall är det mer kostnads effektivt att följa råd från föregående punkt.
* **Lagrings kostnad:** När den här migreringen börjar kopiera filer till en Azure-filresurs, förbrukas Azure Files lagring och faktureras. 
* **StorSimple:** Tills du har möjlighet att avetablera StorSimple-enheter och lagrings konton, kommer StorSimple-kostnader för lagring, säkerhets kopiering och installationer att fortsätta att gälla.

### <a name="direct-share-access-vs-azure-file-sync"></a>Direkt resurs åtkomst jämfört med Azure File Sync
Azure-filresurser öppnar en helt ny värld av möjligheter att strukturera distributionen av fil tjänster. En Azure-filresurs är bara en SMB-resurs i molnet, som du kan konfigurera så att användarna får åtkomst direkt över SMB-protokollet med den välkända Kerberos-autentiseringen och befintliga NTFS-behörigheter (ACL: er för filer och mappar) som fungerar internt. [Lär dig mer om identitets baserad åtkomst till Azure-filresurser](storage-files-active-directory-overview.md).

Ett alternativ till direkt åtkomst är en [Azure File Sync](https://aka.ms/AFS). Azure File Sync är en direkt analog för att StorSimple ska kunna cachelagra ofta använda filer på plats. 

Azure File Sync är en moln tjänst från Microsoft, baserat på två huvud komponenter:

* Filsynkronisering och moln nivåer.
* Fil resurser som intern lagring i Azure, som kan nås via flera protokoll som SMB och fil REST. 

Azure-filresurser behåller viktiga fil åter givningen på lagrade filer som attribut, behörigheter och tidsstämplar. Med Azure-filresurser behöver inte längre ett program eller en tjänst för att tolka filer och mappar som lagras i molnet. Du kan komma åt dem internt över välkända protokoll och klienter som Windows Utforskaren. Med Azure-filresurser kan du lagra generella fil Server data och program data i molnet. Säkerhets kopiering av en Azure-filresurs är en inbyggd funktion som kan förbättras ytterligare av Azure Backup.

Den här artikeln fokuserar på stegen för migreringen. Om du innan du migrerar vill lära dig mer om Azure File Sync rekommenderar vi följande artiklar:

* [Azure File Sync-översikt](https://aka.ms/AFS "Översikt")
* [Guide för Azure File Sync distribution](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Krypterings nyckel för tjänst data för StorSimple
När du först konfigurerar StorSimple-installationen skapade den en*krypterings nyckel för tjänst data*och instruerar dig att lagra nyckeln på ett säkert sätt.
Den här nyckeln används för att kryptera alla data i det associerade Azure Storage-kontot, som StorSimple-enheten lagrar filerna i.

Krypterings nyckeln för tjänst data krävs för en lyckad migrering. Nu är det dags att hämta den här nyckeln från dina poster för var och en av de olika enheterna i inventeringen.

Om du inte kan hitta nyckeln (erna) i dina poster kan du hämta nyckeln från enheten. Varje apparat har en unik krypterings nyckel.

* Skicka en support förfrågan med Microsoft Azure via Azure Portal. Innehållet i begäran ska ha StorSimple enhets serie nummer och begäran om att hämta krypterings nyckeln för tjänst data. 
* En StorSimple support tekniker kontaktar dig med en begäran om ett skärm delnings möte.
* Se till att innan mötet börjar ansluter du till din StorSimple-apparat [via en serie konsol](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) eller via en [fjärran sluten PowerShell-session](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple). 

> [!CAUTION]
> Tänk på följande när du bestämmer hur du ska ansluta till StorSimple-enheten:
> * Att ansluta via en HTTPS-session är det säkraste och rekommenderade alternativet.
> * Anslutning direkt till enhetens serie konsol är säker, men det går inte att ansluta till serie konsolen över nätverks växlar. 
> * HTTP-session-anslutningar är ett alternativ, men de är **inte krypterade** och rekommenderas därför inte om de inte används i ett stängt, betrott nätverk.

### <a name="storsimple-volume-backups"></a>StorSimple Volume-säkerhetskopieringar
StorSimple erbjuder differentiella säkerhets kopior på volym nivå. Azure-filresurser har också denna möjlighet, som kallas resurs ögonblicks bilder.

Bestäm om du vill ha en del av migreringen och du har också en skyldighet att flytta eventuella säkerhets kopior.

> [!CAUTION]
> Stoppa detta om du måste migrera säkerhets kopior från StorSimple-volymer.
>
> Du kan för närvarande bara migrera din senaste volym säkerhets kopia. Stöd för migrering av säkerhets kopia kommer att tas i slutet av 2020.
> Om du startar nu kan du inte "bulta" dina säkerhets kopieringar senare.
> I den kommande versionen måste säkerhets kopieringarna "spelas upp" på Azure-filresurser från äldsta till nyaste, med ögonblicks bilder av Azure-filresurser som tagits i mellan.

Om du bara vill migrera Live-data och inte har några krav på säkerhets kopieringar kan du fortsätta följa den här hand boken.
Om du bara har ett mycket kortsiktigt säkerhets kopierings krav, en månad eller två, kan du välja att fortsätta migreringen nu och bara avetablera dina StorSimple-resurser efter den perioden. Med den här metoden kan du skapa så mycket säkerhets kopierings historik på Azure-filresursens sida som du behöver. Under tiden som du håller båda systemen igång gäller ytterligare kostnader, vilket gör att det inte är en metod som du bör tänka på om du behöver mer än mycket kortsiktig kvarhållning av säkerhets kopior.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Mappa befintliga StorSimple-volymer till Azure-filresurser
[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Antal lagrings konton
Migreringen kommer förmodligen att ha nytta av en distribution av flera lagrings konton, som var och en har ett mindre antal Azure-filresurser.

Om dina fil resurser är mycket aktiva (används av många användare och/eller program) kan bara två Azure-filresurser uppnå lagrings kontots prestanda gräns. Därför är det bästa sättet att migrera till flera lagrings konton, var och en med sina egna enskilda fil resurser och inte fler än två eller tre resurser per lagrings konto.

Ett bra tips är att distribuera lagrings konton med en enda fil resurs. Du kan poola flera Azure-filresurser till samma lagrings konto, om du har lagrings resurser i dem.

Dessa överväganden gäller mer för [direkt åtkomst till molnet](#direct-share-access-vs-azure-file-sync) (via en virtuell Azure-dator eller-tjänst) än för Azure File Sync. Om du endast planerar att använda Azure File Sync på dessa resurser är det bra att gruppera flera i ett enda Azure Storage-konto. Tänk också på att du kanske vill lyfta upp och byta en app i molnet, som sedan kan komma åt en fil resurs direkt. Eller så kan du börja använda en tjänst i Azure som också kan dra nytta av högre IOPS-och data flödes nummer. 

Om du har skapat en lista över dina resurser bör du mappa varje resurs till det lagrings konto som den kommer att finnas i.

> [!IMPORTANT]
> Se till att du bestämmer dig för en Azure-region och sedan varje lagrings konto och Azure File Sync resurs matchning samma region som du har valt.

### <a name="phase-1-summary"></a>Översikt över fas 1
I slutet av fas 1:
* Du har en översikt över dina StorSimple-enheter och-volymer.
* Data omvandlings tjänsten är redo att komma åt dina StorSimple-volymer i molnet eftersom du har hämtat krypterings nyckeln för tjänst data för varje StorSimple-enhet.
* Du har ingen plan för att inte bara de volymer som behöver migreras, utan även hur du mappar dina volymer till lämpligt antal Azure-filresurser och lagrings konton.

> [!CAUTION]
> Om du måste migrera säkerhets kopior från StorSimple-volymer ska du **stoppa här**.
>
> Den här metoden är beroende av nya funktioner för datatransformations tjänsten som för närvarande inte kan migrera säkerhets kopior. Stöd för migrering av säkerhets kopia kommer att tas i slutet av 2020.
> Du kan för närvarande bara migrera dina real tids data. Om du startar nu kan du inte "bulta" dina säkerhets kopieringar senare.
> Säkerhets kopieringar måste "spelas upp" på Azure-filresurser från äldsta till nyaste till real tids data, med Azure-filresurs ögonblicks bilder mellan.

Om du bara vill migrera Live-data och inte har några krav på säkerhets kopieringar kan du fortsätta följa den här hand boken.

## <a name="phase-2-deploying-azure-storage-and-migration-resources"></a>Fas 2: Distribuera Azure-lagring och migrering av resurser

I det här avsnittet beskrivs överväganden kring hur du distribuerar de olika resurs typer som behövs i Azure. Vissa kommer att inneha migreringen av data post och vissa behövs bara för migreringen. Börja inte distribuera resurser förrän du har slutfört din distributions plan. Det är svårt, ibland omöjligt, att ändra vissa aspekter av dina Azure-resurser när de har distribuerats.

### <a name="deploy-storage-accounts"></a>Distribuera lagrings konton
Du kommer troligen att behöva distribuera flera Azure Storage-konton. Var och en har ett mindre antal Azure-filresurser, enligt din distributions plan, som slutfördes i föregående avsnitt i den här artikeln. Kom till Azure Portal för att [distribuera dina planerade lagrings konton](../common/storage-account-create.md#create-a-storage-account). Överväg att följa följande grundläggande inställningar för alla nya lagrings konton:

##### <a name="subscription"></a>Prenumeration
Du kan använda samma prenumeration som du använde för din StorSimple-distribution eller en annan. Den enda begränsningen är att din prenumeration måste finnas i samma Azure AD-klient som StorSimple-prenumerationen. Överväg att flytta StorSimple-prenumerationen till rätt klient organisation innan du påbörjar en migrering. Du kan bara flytta hela prenumerationen, enskilda StorSimple-resurser kan inte flyttas till en annan klient organisation eller prenumeration.

##### <a name="resource-group"></a>Resursgrupp
Resurs grupper hjälper till med organisation av resurser och administrations hanterings behörigheter. Lär dig mer om [resurs grupper i Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

##### <a name="storage-account-name"></a>Lagringskontonamn
Namnet på ditt lagrings konto kommer att bli en del av en URL och har vissa begränsningar. I namngivnings konventionen bör du tänka på att lagrings konto namn måste vara unika i världen, tillåta endast gemener och siffror, kräva mellan 3 och 24 tecken och inte tillåta specialtecken som bindestreck eller under streck. Se även: [namngivnings regler för Azure Storage-resurser](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

##### <a name="location"></a>Plats
Lagrings kontots "plats" eller Azure-region är mycket viktigt. Om du använder Azure File Sync måste alla dina lagrings konton finnas i samma region som din resurs för synkroniseringstjänsten för lagring. Den Azure-region som du väljer bör vara nära eller central för dina lokala servrar/användare. När din resurs har distribuerats kan du inte ändra dess region.

Du kan välja en annan region än där dina StorSimple-data (lagrings kontot) för närvarande finns.

> [!IMPORTANT]
> Om du väljer en annan region än din aktuella, StorSimple lagrings konto plats, [kommer utgående kostnader att gälla](https://azure.microsoft.com/pricing/details/bandwidth) under migreringen. Data kommer att lämna StorSimple-regionen och ange din nya lagrings konto region. Inga avgifter för bandbredd gäller om du bor inom samma Azure-region.

##### <a name="performance"></a>Prestanda
Du har möjlighet att välja Premium Storage (SSD) för Azure-filresurser eller standard lagring. Standard lagring innehåller [flera nivåer för en fil resurs](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). Standard Storage är det bästa alternativet för de flesta kunder som migrerar från StorSimple.

Är du fortfarande osäker?
* Välj Premium Storage om du behöver [prestanda för en Premium Azure-filresurs](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
* Välj standard lagring för generell användning av fil Server arbets belastningar, inklusive frekventa data och Arkiv data. Välj också standard lagring om den enda arbets belastningen på resursen i molnet ska Azure File Sync.

##### <a name="account-kind"></a>Typ av konto
* För standard lagring väljer du: *StorageV2 (generell användning v2)*
* För Premium-filresurser väljer du: *FileStorage*

##### <a name="replication"></a>Replikering
Det finns flera tillgängliga replikeringsinställningar. Läs mer om de olika typerna av replikering.

Välj endast från något av följande två alternativ:
* *Lokalt redundant lagring (LRS)*
* *ZRS (Zone redundant Storage)* – som inte är tillgänglig i alla Azure-regioner.

> [!NOTE]
> Endast LRS-och ZRS-redundans typer är kompatibla med de stora Azure-filresurserna med 100TiB-kapacitet.

Globalt redundant lagring (alla variationer) stöds inte för närvarande. Du kan byta typ av redundans senare och GRS när supporten för den kommer i Azure.

##### <a name="enable-100tib-capacity-file-shares"></a>Aktivera fil resurser för 100TiB-kapacitet

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="En bild som visar fliken Avancerat i Azure Portal för att skapa ett lagrings konto.":::
    :::column-end:::
    :::column:::
        Under avsnittet *Avancerat* i guiden Nytt lagrings konto i Azure Portal kan du aktivera stöd för *stora fil resurser* i det här lagrings kontot. Om det här alternativet inte är tillgängligt för dig väljer du förmodligen fel typ av redundans. Se till att du bara väljer LRS eller ZRS för att det här alternativet ska bli tillgängligt.
    :::column-end:::
:::row-end:::

Väljer för de stora 100TiB-kapacitets fil resurserna har flera fördelar:
* Prestandan ökar avsevärt jämfört med de mindre 5TiB-kapacitets fil resurserna. (till exempel: 10 gånger IOPS)
* Migreringen blir betydligt snabbare.
* Du ser till att en fil resurs har tillräckligt med kapacitet för att rymma alla data som du migrerar till den.
* Framtida tillväxt omfattas.

### <a name="azure-file-shares"></a>Azure-filresurser
När dina lagrings konton har skapats kan du navigera till avsnittet *"fil resurs"* i lagrings kontot och distribuera lämpligt antal Azure-filresurser enligt ditt schema för migrering från fas 1. Överväg att följa följande grundläggande inställningar för dina nya fil resurser i Azure:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="En bild som visar fliken Avancerat i Azure Portal för att skapa ett lagrings konto.":::
    :::column-end:::
    :::column:::
        </br>**Namn**</br>Gemener, siffror och bindestreck stöds.</br></br>**Kvot**</br>Kvoten är jämförbar med SMB-hårddisk på en Windows-Server. Det bästa sättet är att inte ställa in en kvot som migrering och andra tjänster kommer att Miss förfaller när kvoten nås.</br></br>**Nivå**</br>Välj *transaktion optimerad* för den nya fil resursen. Under migreringen sker många transaktioner och det är mer kostnads effektivt att ändra nivån senare till den nivå som passar din arbets belastning bäst.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager
Den Azure-resurs som ska innehålla migreringsjobb kallas för en *StorSimple Data Manager*. Klicka på ny resurs och Sök efter den och klicka sedan på *skapa*.

Det här är en tillfällig resurs som används för dirigering. du avetablerar den efter att migreringen är slutförd. Den bör distribueras i samma prenumeration, resurs grupp och region som ditt StorSimple-lagrings konto.

### <a name="azure-file-sync"></a>Azure File Sync
Azure File Sync (AFS) gör att du kan lägga till lokal cachelagring av de filer som ofta används. På samma sätt som med funktionerna för cachelagring av StorSimple erbjuder den moln nivå funktionen för AFS en lokal åtkomst fördröjning i kombination med förbättrad kontroll över tillgänglig cache-kapacitet på Windows Server och synkronisering på flera platser. Om du har en lokal cache som mål, så kan du, i ditt lokala nätverk, förbereda en virtuell Windows Server-dator (fysiska servrar och failover-kluster stöds också), med tillräckligt "Direct Attached Storage (DAS)"-kapacitet. 

> [!IMPORTANT]
> Konfigurera inte Azure File Sync ännu är det bäst att ställa in Azure File Sync när migreringen av resursen har slutförts. Distribution av AFS ska inte starta före fas 4 i en migrering.

### <a name="phase-2-summary"></a>Fas 2-Sammanfattning
I slutet av fas 2 har du distribuerat dina lagrings konton, alla Azure-filresurser över dem och har även en StorSimple Data Manager-resurs. Du kommer att använda den senare i fas 3 när du faktiskt konfigurerar dina migreringsjobb.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fas 3: skapa och kör ett migreringsjobb
I det här avsnittet beskrivs hur du konfigurerar ett migreringsjobb och noga mappar katalogerna på en StorSimple-volym som ska kopieras till den Azure-filresurs du väljer. Kom igång genom att navigera till StorSimple Data Manager, hitta **jobb definitioner** på menyn och välja **+ jobb definition**. Mål lagrings typen är standard: *Azure-filresursen*.

![StorSimple 8000-seriens migreringsjobb.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "En skärm bild av jobb definitionerna Azure Portal med en ny jobb definitions dialog ruta öppen, som frågar efter typen av jobb: kopiera till en fil resurs eller en BLOB-behållare.")

> [!IMPORTANT]
> Stoppa eventuella automatiskt schemalagda säkerhets kopieringar av dina StorSimple-volymer innan du kör några migreringsjobb.

:::row:::
    :::column:::
        ![Migrering av StorSimple 8000-serien.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "En skärm bild av det nya jobb skapande formuläret för ett datatransformerings jobb.")
    :::column-end:::
    :::column:::
        **Jobb definitions namn**</br>Det här namnet ska vara en indikation på den uppsättning filer som du flyttar. Det är en bra idé att ge det samma namn som Azure-filresursen. </br></br>**Plats där jobbet körs**</br>När du väljer en region måste du välja samma region som din StorSimple lagrings konto eller, om det inte är tillgängligt, en region nära den. </br></br><h3>Källa</h3>**Käll prenumeration**</br>Välj den prenumeration där du lagrar din StorSimple-Enhetshanteraren-resurs. </br></br>**StorSimple-resurs**</br>Välj din StorSimple Enhetshanteraren din installation är registrerad hos. </br></br>**Krypteringsnyckel för tjänstdata**</br>Se [föregående avsnitt i den här artikeln](#storsimple-service-data-encryption-key), om du inte kan hitta nyckeln i dina poster. </br></br>**Enhet**</br>Välj din StorSimple-enhet som innehåller den volym dit du vill migrera. </br></br>**Volym**</br>Välj käll volymen. Senare bestämmer du om du vill migrera hela volymen eller under kataloger till Azure-webbplatsens mål resurs. </br></br><h3>Mål</h3>Välj prenumeration, lagrings konto och Azure-filresurs som mål för det här migreringsjobbet.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Den senaste säkerhets kopian av volymen kommer att användas för att utföra migreringen. Se till att det finns minst en säkerhets kopia av volymen eller att jobbet inte fungerar. Se också till att den senaste säkerhets kopian du har är ganska nyligen, för att behålla delta i Live-resursen så liten som möjligt. Det kan vara värt att aktivera och slutföra en annan säkerhets kopiering av volymen manuellt **innan** du kör jobbet som du nyss skapade.

### <a name="directory-mapping"></a>Katalog mappning
Detta är valfritt för ditt migreringsjobb. Om du lämnar den tom, kommer **alla** filer och mappar i roten på din StorSimple-volym att flyttas till roten för Azure-filresursen. I de flesta fall är det inte bästa sättet att lagra en hel volyms innehåll i en Azure-filresurs. Det är ofta bättre att dela upp volymens innehåll över flera fil resurser i Azure. Om du inte redan har gjort en plan går du först igenom det här avsnittet: [mappa din StorSimple-volym till Azure-filresurser](#map-your-existing-storsimple-volumes-to-azure-file-shares)

Som en del av din migrerings plan kanske du har bestämt att mapparna på en StorSimple volym måste delas upp i flera Azure-filresurser. I så fall kan du göra detta delat genom att:
1. Genom att definiera flera jobb för att migrera mapparna på en volym har vardera samma StorSimple volym källa men en annan Azure-filresurs som mål.
1. Ange exakt vilka mappar från StorSimple-volymen som måste migreras till den angivna fil resursen med hjälp av avsnittet *katalog mappning* i formuläret för att skapa jobb och följa specifika [mappnings-semantik](#semantic-elements).

> [!IMPORTANT]
> Det går inte att verifiera Sök vägarna och mappnings uttrycken i det här formuläret när formuläret skickas. Om mappningar anges felaktigt kan ett jobb antingen Miss lyckas fullständigt eller producera ett oönskat resultat. I så fall är det oftast bäst att ta bort Azure-filresursen, återskapa den och sedan korrigera mappnings instruktionerna i ett nytt migreringsjobb för resursen. Om du kör ett nytt jobb med ett fast mappnings uttryck kan du åtgärda utelämnade mappar och ta med dem i den befintliga resursen. Men endast mappar som utelämnats på grund av Sök vägs fel kan åtgärdas på det här sättet.

#### <a name="semantic-elements"></a>Semantiska element
En mappning uttrycks från vänster till höger: [\Source sökväg] \> [\target sökväg].

|Semantiskt Character          | Innebörd  |
|:---------------------------|:---------|
| **\\**                     | indikator för rotnivå        |
| **\>**                     | [källa] och [Target] mappnings operator        |
|**\|** eller RETUR (ny rad) | avgränsare för två instruktioner för mappning av mappar. </br>Alternativt kan du utelämna det här specialtecknet och trycka på RETUR för att hämta nästa mappnings uttryck på en egen rad.        |

### <a name="examples"></a>Exempel
Flyttar innehållet i mappen "användar data" till roten för mål fil resursen:
``` console
\User data > \\
```
Flyttar hela volym innehållet till en ny sökväg på mål fil resursen:
``` console
\ \> \Apps\HR tracker
```
Flyttar innehållet i källmappen till en ny sökväg på mål fil resursen:
``` console
\HR resumes-Backup \> \Backups\HR\resumes
```
Sorterar flera käll platser i en ny katalog struktur:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Semantiska regler
* Ange alltid mappsökvägar i förhållande till rot nivån. 
* Påbörja varje mappsökväg med en indikator för rotnivå " \" . 
* Ta inte med enhets beteckningar. 
* När du anger flera sökvägar kan inte käll-eller mål Sök vägar överlappa varandra:</br>
   Ogiltigt överlappande käll Sök vägs exempel:</br>
    *\\folder\1 > \\ mapp*</br>
    *\\mapp \\ 1 \\ 2 > \\*</br>
   Ogiltig mål Sök väg överlappande exempel:</br>
   *\\mapp > \\*</br>
   *\\> \\*</br>
* Källmappen som inte finns kommer att ignoreras. 
* Mappstrukturer som inte finns på målet kommer att skapas. 
* Precis som Windows: mappnamn är Skift läges okänsliga men är Skift läges känsliga.

> [!NOTE]
> innehållet i mappen "*\System Volume Information*" och "*$Recycle. bin*" på din StorSimple-volym kopieras inte av Transformations jobbet.

### <a name="phase-3-summary"></a>Sammanfattning av fas 3
I slutet av fas 3 har du kört dina data omvandlings jobb från StorSimple-volymer till Azure-filresurser. Nu kan du fokusera på att konfigurera antingen Azure File Sync för resursen (när migreringen för en resurs har slutförts) eller direkt resurs åtkomst för dina informations anställda och appar till Azure-filresursen.

## <a name="phase-4-accessing-your-azure-file-shares"></a>Fas 4: få åtkomst till dina Azure-filresurser

Det finns två huvudsakliga strategier för att komma åt Azure-fil resurser:

* **Azure File Sync:** [så här distribuerar du Azure File Sync](#deploy-azure-file-sync) till en lokal Windows Server. AFS har alla fördelar med en lokal cache, precis som StorSimple.
* **Direkt delnings åtkomst:** [så här distribuerar du direkt delnings åtkomst](#deploy-direct-share-access). Använd den här strategin om ditt åtkomst scenario för en specifik Azure-filresurs inte kommer att ha nytta av lokal cachelagring eller om du inte längre har möjlighet att vara värd för en lokal Windows Server. Här kommer dina användare och appar fortsätta att komma åt SMB-resurser via SMB-protokollet, men dessa resurser finns inte längre på en lokal server, utan direkt i molnet.

Du bör redan ha bestämt vilket alternativ som passar dig bäst, i [steg 1](#phase-1-prepare-for-migration) i den här hand boken.

Resten av det här avsnittet fokuserar på distributions anvisningar.

### <a name="deploy-azure-file-sync"></a>Distribuera Azure File Sync

Det är dags att distribuera en del av Azure File Sync:
1. Skapa Azure File Sync moln resurs.
1. Distribuera Azure File Sync-agenten på din lokal-server.
1. Registrera servern med moln resursen

Skapa inte några Sync-grupper än. Att konfigurera synkronisering med en Azure-filresurs bör bara ske när migreringen till en Azure-filresurs har slutförts. Om du börjar använda File Sync innan migreringen har slutförts, kommer migreringen att bli svår eftersom du inte enkelt kan se när det är dags att påbörja en överhuggning.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Distribuera Azure File Sync moln resurs

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Om du vill ändra i vilken Azure-region dina data finns i när migreringen är färdig distribuerar du tjänsten för synkronisering av lagring i samma region som mål lagrings kontona för migreringen.

#### <a name="deploy-an-on-premises-windows-server"></a>Distribuera en lokal Windows Server

* Skapa en Windows Server 2019 – minst 2012R2 – som en virtuell dator eller fysisk server. Ett kluster för växling vid fel i Windows Server stöds också. Återanvänd inte servern fram till StorSimple 8100 eller 8600.
* Etablera eller Lägg till direktansluten lagring (DAS jämfört med NAS, vilket inte stöds).

Vi rekommenderar att du ger din nya Windows Server en lika stor eller större mängd lagrings utrymme än din StorSimple 8100-eller 8600-installation har lokalt tillgänglig för cachelagring. Du kommer att använda Windows Server på samma sätt som du använde StorSimple-installationen, om den har samma mängd lagrings utrymme som installationen, bör caching-upplevelsen vara liknande, om inte samma.
Du kan lägga till eller ta bort lagrings utrymme från Windows Server på. På så sätt kan du skala din lokala volym storlek och mängden lokal lagring som är tillgänglig för cachelagring.

#### <a name="prepare-the-windows-server-for-file-sync"></a>Förbereda Windows Server för filsynkronisering

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server"></a>Konfigurera Azure File Sync på Windows Server

Din registrerade lokala Windows Server måste vara klar och ansluten till Internet för den här processen.

> [!IMPORTANT]
> Din StorSimple-migrering av filer och mappar till Azure-filresursen måste slutföras innan du fortsätter. Se till att det inte finns några fler ändringar som görs i fil resursen.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Se till att aktivera moln skiktning! Moln nivåer är Azure File Sync funktion som gör det möjligt för den lokala servern att ha mindre lagrings kapacitet än vad som lagras i molnet, men som har det fullständiga namn området tillgängligt. Lokalt intressanta data cachelagras också lokalt för snabb, lokal åtkomst prestanda. En annan orsak till att aktivera moln nivåer i det här steget är att vi inte vill synkronisera fil innehåll i det här skedet. endast namn området ska flyttas vid denna tidpunkt.

### <a name="deploy-direct-share-access"></a>Distribuera direkt delning – åtkomst

:::row:::
    :::column:::
        [![Steg-för-steg-guide och demo om hur du exponerar Azure-filresurser på ett säkert sätt direkt till informations arbetare och appar – Klicka för att spela upp!](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        Den här videon är en guide och en demonstration av hur du på ett säkert sätt kan exponera Azure-filresurser direkt till informations arbetare och appar i fem enkla steg.</br>
        En dedikerad dokumentation om video referenser för vissa ämnen:
* [Identitetsöversikt](storage-files-active-directory-overview.md)
* [Så här ansluter du till ett lagrings konto](storage-files-identity-auth-active-directory-enable.md)
* [Översikt över nätverk för Azure-filresurser](storage-files-networking-overview.md)
* [Konfigurera offentliga och privata slut punkter](storage-files-networking-endpoints.md)
* [Så här konfigurerar du en S2S VPN](storage-files-configure-s2s-vpn.md)
* [Så här konfigurerar du en Windows P2S VPN](storage-files-configure-p2s-vpn-windows.md)
* [Så här konfigurerar du en Linux P2S VPN](storage-files-configure-p2s-vpn-linux.md)
* [Så här konfigurerar du DNS-vidarebefordran](storage-files-networking-dns.md)
* [Konfigurera DFS-N](https://aka.ms/AzureFiles/Namespaces)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Sammanfattning för fas 4
I den här fasen har du skapat och kört flera DTS-jobb ( *data Transformation service* ) i *StorSimple Data Manager*. Dessa jobb har migrerat dina filer och mappar till Azure-filresurser. Dessutom har du distribuerat antingen Azure File Sync eller för berett nätverks-och lagrings konton för direkt delnings åtkomst.

## <a name="phase-5-user-cut-over"></a>Fas 5: överhuggen av användare
Den här fasen är allt om att figursätta migreringen:
* Planera stillestånds tiden.
* Kom ifatt med eventuella ändringar som dina användare och appar har producerat på StorSimple-sidan medan data omvandlings jobben i fas 3 har körts. 
* Användarna får inte till gång till den nya Windows-servern med Azure File Sync eller Azure-filresurser via direkt delning-åtkomst.

### <a name="plan-your-downtime"></a>Planera din stillestånds tid
Den här metoden för migrering kräver vissa stillestånds tider för dina användare och appar. Målet är att hålla stillestånds tiden till ett minimum och följande överväganden kan hjälpa:

* Se till att dina StorSimple-volymer är tillgängliga när du kör dina data omvandlings jobb.
* När du har kört dina datamigreringar för en resurs är det dags att ta bort användar åtkomsten (minst Skriv åtkomst) från StorSimple-volymerna/resurserna. En slutgiltig RoboCopy kommer att fånga upp Azure-filresursen och sedan kan du klippa ut användare. Var du kör RoboCopy beror på om du väljer att använda Azure File Sync eller direkt delnings åtkomst. Det kommande avsnittet om RoboCopy omfattar detta.
* När du har slutfört RoboCopy-fångsten är du redo att exponera den nya platsen för dina användare. Antingen Azure-filresursen direkt eller en SMB-resurs på en Windows-Server med AFS. En DFS-N-distribution hjälper ofta att snabbt och effektivt göra en klipps över. Den behåller dina befintliga resurs adresser konsekvent och pekar på en ny plats, som innehåller dina migrerade filer och mappar.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Avgöra när ditt namn område har synkroniserats fullständigt till servern

När du använder Azure File Sync för en Azure-filresurs är det viktigt att du bestämmer att hela namn området har laddats ned till servern innan du påbörjar en lokal RoboCopy. Hur lång tid det tar att ladda ned ditt namn område beror på antalet objekt i Azure-filresursen. Det finns två metoder för att avgöra om ditt namn område har anlänt på servern:

#### <a name="azure-portal"></a>Azure Portal
Du kan använda Azure Portal för att se när ditt namn område har anlänt.
* Logga in på Azure Portal, navigera till den synkroniserade gruppen och kontrol lera synkroniseringsstatus för Sync-gruppen och Server slut punkten. 
* Den intressanta orienteringen är Ladda ned: om Server slut punkten är nyligen etablerad visas den **inledande synkroniseringen** som anger att namn området fortfarande kommer att avslutas.
När du har ändrat vad som helst men *inledande synkronisering*fylls ditt namn område i fullständigt på servern och du är redo att fortsätta med en lokal Robocopy.

#### <a name="windows-server-event-viewer"></a>Windows Server-Loggboken
ou kan också använda Loggboken på din Windows-Server för att se när namn rummet har anlänt fullständigt.

1. Öppna **Loggboken** och navigera till **program och tjänster**.
1. Navigera och öppna **Microsoft\FileSync\Agent\Telemetry**.
1. Leta efter den senaste **händelse 9102**som motsvarar en slutförd Sync-session.
1. Välj **information** och bekräfta att du tittar på en händelse där **SyncDirection** -värdet **hämtas**.
1. Under tiden där ditt namn område har laddats ned till servern, kommer det att finnas en enskild händelse med **scenario**, värde **FullGhostedSync** och **HResult**  =  **0**.
1. Om du saknar den händelsen kan du också söka efter andra **9102-händelser** med **SyncDirection**  =  **nedladdning** och **scenario**  =  **"RegularSync"**. Att hitta någon av dessa händelser indikerar också att namn området har laddat ned och synkroniseringen har slutförts till vanliga synkroniseringar, oavsett om det finns något att synkronisera eller inte, för tillfället.

### <a name="a-final-robocopy"></a>En slutgiltig RoboCopy
Nu finns det skillnader mellan din lokala Windows Server och StorSimple 8100-eller 8600-produkten:

1. Du måste komma igång med de ändringar som användare/appar har producerat på StorSimple-sidan när migreringen var pågående.
1. I de fall där du använder Azure File Sync: StorSimple-installationen har ett ifyllt cacheminne jämfört med Windows Server bara ett namn område utan fil innehåll som lagras lokalt för tillfället. Det slutliga RoboCopy kan hjälpa dig att komma igång med din lokala AFS-cache genom att hämta lokalt cachelagrat fil innehåll så mycket som det är tillgängligt och får plats på AFS-servern.
1. Vissa filer kan ha lämnats av data omvandlings jobbet på grund av ogiltiga tecken. I så fall, kopierar du dem till den Azure File Sync aktiverade Windows Server. Senare kan du justera dem så att de kommer att synkroniseras. Om du inte använder Azure File Sync för en viss resurs, är det bättre att byta namn på filerna med ogiltiga tecken på StorSimple-volymen och sedan köra RoboCopy direkt mot Azure-filresursen. 

> [!WARNING]
> Du får inte starta RoboCopy innan servern har ett namn område för en Azure-filresurs som hämtats fullständigt!
> Utcheckning: "[Bestäm när ditt namn område har laddats ned helt till servern](#determine-when-your-namespace-has-fully-synced-to-your-server)"

 Du vill bara kopiera filer som har ändrats efter att migreringsjobbet senast kördes och filer som inte har flyttats genom dessa jobb tidigare. Du kan lösa problemen varför de inte flyttades senare på servern när migreringen är klar. Se [Azure File Sync fel sökning](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

RoboCopy har flera parametrar. Följande visar ett färdigt kommando och en lista över orsaker till att välja dessa parametrar:

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Lägg

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Tillåter att RoboCopy körs med flera trådar. Standardvärdet är 8, Max är 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Matar ut status till logg filen som UNICODE (skriver över befintlig logg).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Utdata till konsol fönstret. Används tillsammans med utdata i en loggfil.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Kör RoboCopy i samma läge som ett säkerhets kopierings program använder. Det gör att RoboCopy kan flytta filer som den aktuella användaren inte har behörighet till.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Gör det möjligt för RoboCopy att endast överväga delta mellan källan (StorSimple-enheten) och mål (Windows Server-katalog).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      åter givningen av fil kopian (standard är/COPY: DAT), kopierings flaggor: D = data, A = attribut, T = tidsstämplar, S = Security = NTFS ACL, O = ägar information, U = gransknings information
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      Kopiera ALL fil information (motsvarande/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      åter givning för kopian av kataloger (standard är/DCOPY: DA), kopierings flaggor: D = data, A = attribut, T = tidsstämplar
   :::column-end:::
:::row-end:::

När du konfigurerar käll-och mål platserna för RoboCopy-kommandot, se till att du granskar strukturen för källa och mål för att se till att de matchar. Om du använde funktionen katalog mappning i migreringsjobbet kan rot Katalog strukturen skilja sig från strukturen på din StorSimple-volym. I så fall kan du behöva flera RoboCopy-jobb, ett för varje under katalog. Eftersom det här RoboCopy-kommandot använder/MIR flyttas inte filer som är desamma (skiktade filer för instans), men om du får fel källa och mål Sök väg, så rensar/MIR även katalog strukturer på din Windows Server/Azure-filresurs som inte finns på StorSimple-käll Sök vägen. De måste matcha exakt för RoboCopy-jobbet för att uppnå att det är avsett att bara uppdatera det migrerade innehållet med de senaste ändringarna som gjorts när migreringen pågår. 

Se efter i RoboCopy-logg filen om filerna har varit kvar bakom. Om det finns problem korrigerar du dem och kör kommandot RoboCopy igen. Avetablera inte några StorSimple-resurser innan du åtgärdar utestående problem för filer eller mappar som du bryr dig om.

Om du inte använder Azure File Sync för att cachelagra den aktuella Azure-filresursen i fråga, men istället för att välja direkt resurs åtkomst:
1. [Montera Azure-filresursen](storage-how-to-use-files-windows.md#mount-the-azure-file-share) som en nätverks enhet på en lokal Windows-dator.
1. Utför RoboCopy mellan din StorSimple och den monterade Azure-filresursen. Om filerna inte kopieras, korrigerar du namnen på StorSimple-sidan för att ta bort ogiltiga tecken och försöka RoboCopy igen. Det tidigare angivna RoboCopy-kommandot kan köras flera gånger med/o vilket orsakar onödig återställning till StorSimple.

### <a name="user-cut-over"></a>Användaren klipps över

Om du använder Azure File Sync måste du förmodligen skapa SMB-resurserna på den AFS-aktiverade Windows Server som matchar de resurser du hade på StorSimple-volymerna. Du kan läsa in det här steget och göra det tidigare så att du inte förlorar tid här, men du måste se till att innan den här punkten, har ingen åtkomst till att göra ändringar i Windows Server.

Om du har en DFS-N-distribution kan du peka DFN-Namespaces till nya mappar för servermappar. Om du inte har en DFS-N-distribution, och du framförde din 8100/8600-enhet lokalt med en Windows Server, kan du ta bort servern från domänen. Sedan ansluter domänen till din nya, Azure File Sync aktive rad, Windows Server. Under den processen ger du servern samma server namn och resurs namn som den gamla servern, så att stycknings listan förblir transparent för dina användare, din grup princip och dina skript.

[Läs mer om DFS-N](https://aka.ms/AzureFiles/Namespaces)

## <a name="deprovision"></a>Avetablera
När du avetablerar en resurs förlorar du åtkomsten till konfigurationen av den resursen samt dess data. Det går inte att ta bort etableringen, så fortsätt inte förrän du har bekräftat att migreringen är slutförd och att det inte finns några beroenden alls på de StorSimple-filer, mappar eller volym säkerhets kopior som du ska avetablera.

Innan du börjar är det en bra idé att studera din nya Azure File Sync distribution i produktion, för en bit. Det ger dig alternativ för att åtgärda eventuella problem som kan uppstå.
När du har observerat din Azure File Sync-distribution under minst några få dagar kan du börja med att avetablera resurser i följande ordning:

1. Avetablera din StorSimple Data Manager-resurs via Azure Portal.
   Alla DTS-jobb tas bort med det. Du kan inte enkelt hämta kopierings loggarna. Om de är viktiga för dina poster kan du hämta dem innan du avetablerar.
1. Se till att dina StorSimple fysiska enheter har migrerats och avregistrera dem.
   Om du inte är helt säker på att de har migrerats ska du inte fortsätta. Om du avetablerar dessa resurser när de fortfarande behövs kan du inte återställa data eller deras konfiguration.
1. Om det inte finns några fler registrerade enheter kvar i ett StorSimple-Enhetshanteraren kan du fortsätta att ta bort den Enhetshanteraren resursen.
1. Det är nu dags att ta bort StorSimple Storage-kontot i Azure. Stoppa och bekräfta att migreringen är slutförd och ingenting och ingen är beroende av dessa data innan du fortsätter.
1. Dra ut den fysiska StorSimple från ditt data Center.
1. Om du äger StorSimple-installationen är du kostnads fri att återvinna datorn. 
   Om din enhet lånas ut, så meddela leasegivaren och returnera enheten efter behov.

Migreringen är klar.

> [!NOTE]
> Har du fortfarande frågor eller påträffat eventuella problem?</br>
> Vi är här för att hjälpa: AzureFilesMigration@microsoft . com


## <a name="next-steps"></a>Nästa steg

* Bekanta dig med [Azure File Sync: aka.MS/AFS](https://aka.ms/AFS).
* Förstå flexibiliteten i principer för [moln nivåer](storage-sync-cloud-tiering.md) .
* [Aktivera Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) på dina Azure-filresurser för att schemalägga ögonblicks bilder och definiera scheman för kvarhållning av säkerhets kopior.
* Om du ser i Azure Portal att vissa filer inte synkroniseras permanent kan du läsa [fel söknings guiden](storage-sync-files-troubleshoot.md) för steg för att lösa problemen.
