---
title: "Använda Azure Import/Export för att överföra data till och från Azure Storage | Microsoft Docs"
description: "Lär dig hur du skapar importera och exportera jobben i Azure-portalen för att överföra data till och från Azure Storage."
author: muralikk
manager: syadav
services: storage
ms.service: storage
ms.topic: article
ms.date: 02/28/2018
ms.author: muralikk
ms.openlocfilehash: e9fce2530bc4e654304b946cea1715ac8e2ce6fa
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="use-the-microsoft-azure-importexport-service-to-transfer-data-to-azure-storage"></a>Använda tjänsten Microsoft Azure Import/Export för att överföra data till Azure Storage
I den här artikeln får stegvisa instruktioner om hur du använder Azure Import/Export service att säkert överföra stora mängder data till Azure Blob storage och Azure filer av leverans diskenheterna till ett Azure-datacenter. Den här tjänsten kan också användas för att överföra data från Azure storage hårddiskar och levereras till dina lokala platser. Du kan importera data från en enskild interna SATA-disk till Azure Blob storage eller Azure-filer. 

> [!IMPORTANT] 
> Den här tjänsten accepteras bara interna SATA-hårddiskar och SSD-enheter bara. Inga andra enheten stöds. Skicka inte externa hårddiskar NAS-enheter, osv., som de ska returneras om möjligt eller på annat sätt borttagna.
>
>

Följ de nedanstående steg om data på disken som ska importeras till Azure Storage.
### <a name="step-1-prepare-the-drives-using-waimportexport-tool-and-generate-journal-files"></a>Steg 1: Förbered enheten/s-verktyget WAImportExport och generera journal fil/s.

1.  Identifiera vilka data som ska importeras till Azure Storage. Du kan importera kataloger och fristående filer på en lokal server eller en nätverksresurs.
2.  Beroende på storleken på data anskaffa antalet krävs 2,5 tum SSD eller 2,5-tums eller 3,5-tums SATA II eller III hårddiskar.
3.  Koppla hårddiskar direkt med SATA eller med adaptrar för externa USB till en windows-dator.
1.  Skapa en NTFS-volym på varje hårddisk och tilldela en enhetsbeteckning till volymen. Inga monteringspunkter.
2.  Aktivera säker plats-bitarskryptering på volymen om du vill aktivera kryptering på windows-dator. Följ instruktionerna på https://technet.microsoft.com/en-us/library/cc731549(v=ws.10).aspx.
3.  Helt kopiera data till dessa krypterade enda NTFS-volymer på diskar med hjälp av kopiera och klistra in eller dra & släppa eller Robocopy eller något sådant verktyg.
7.  Hämta WAImportExport V1 från https://www.microsoft.com/en-us/download/details.aspx?id=42659
8.  Packa upp till standard mappen waimportexportv1. Till exempel C:\WaImportExportV1  
9.  Kör som administratör och öppna ett PowerShell eller kommandoraden och ändra katalogen till mappen uppackade. Till exempel cd C:\WaImportExportV1
10. Kopiera följande kommandorad till en textredigerare och redigera det för att skapa en kommandorad:

    ```
    ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:***== /t:D /bk:*** /srcdir:D:\ /dstdir:ContainerName/ 
    ```
    
    I följande tabell beskrivs dessa alternativ:

    |Alternativ  |Beskrivning  |
    |---------|---------|
    |/j:     |Namnet på journal-fil med filnamnstillägget .jrn. En journal-fil skapas per enhet. Du bör använda disk serienumret som namn på filen.         |
    |/sk:     |Nyckel för Azure Storage-konto.         |
    |/t:     |Enhetsbeteckning på disken för att skickas. Till exempel enhet `D`.         |
    |/bk:     |BitLocker-nyckel för enheten.         |
    |/srcdir:     |Enhetsbeteckningen för disken som ska levereras följt av `:\`. Till exempel `D:\`.         |
    |/dstdir:     |Namnet på målbehållare i Azure Storage         |

1. Upprepa steg 10 för varje disk som ska levereras.
2. En journal-fil med namnet som angetts med parametern /j: skapas för varje körning av kommandoraden.

### <a name="step-2-create-an-import-job-on-azure-portal"></a>Steg 2: Skapa ett importjobb på Azure-portalen.

1. Logga in på https://portal.azure.com/ och under fler tjänster -> STORAGE -> ”Import/export jobb” klickar du på **skapa Import-/ exportjobb**.

2. Grunderna under Välj ”Import i Azure”, ange en sträng för jobbnamn, välja en prenumeration, ange eller välj en resursgrupp. Ange ett beskrivande namn för importjobbet. Observera att det namn som du anger kan innehålla endast små bokstäver, siffror, bindestreck och understreck, måste börja med en bokstav och får inte innehålla blanksteg. Du använder det namn du väljer att spåra dina jobb när de pågår och när de har slutförts.

3. I informationsavsnittet jobbet överför journalfiler för enheten som du fick vid förberedelsen av enheten. Om waimportexport.exe version1 används, måste du överföra en fil för varje enhet som du har förberett. Välj lagringskonto som data ska importeras till i avsnittet ”Importera mål” Storage-konto. Samlingsbibliotek platsen fylls i automatiskt baserat på regionen som det lagringskonto som valts.
   
   ![Skapa importjobb - steg3](./media/storage-import-export-service/import-job-03.png)
4. I RETUR leverans avsnittet info, Välj en operatör från den nedrullningsbara listrutan och ange en giltig operatör kontonummer som du har skapat med den operatör. Microsoft använder kontot för att leverera enheter tillbaka till dig när importjobbet har slutförts. Ange en fullständig och giltig kontaktperson, telefon, e-post, gatuadress, ort, zip, stat/proviince och land/region.
   
5. I avsnittet Sammanfattning tillhandahålls Azure-DataCenter leveransadress som ska användas för att leverera diskar till Azure-Domänkontrollant. Se till att Jobbnamn och fullständig adress nämns på etiketten leverans. 

6. Klicka på OK på sidan Sammanfattning för att importera jobbet Skapa.

### <a name="step-3-ship-the-drives-to-the-azure-datacenter-shipping-address-provided-in-step-2"></a>Steg 3: Leverera enheten/s till Azure-Datacenter leveransadress som anges i steg 2.
FedEx, UPS eller DHL kan användas för att leverera paketet till Azure-Domänkontrollant.

### <a name="step-4-update-the-job-created-in-step2-with-tracking-number-of-the-shipment"></a>Steg 4: Uppdatera jobb som skapats i steg 2 med spåra antalet leveransen.
Efter leverans diskarna, gå tillbaka till den **Import/Export** sida på Azure portal om du vill uppdatera antalet spårning med nedanstående steg ett) navigera och klicka på Importera jobbet b) Klicka på **uppdatera jobbstatus och spåra information När enheter levereras**. c) Välj kryssrutan ”Markera som levererats”-d) anger antalet operatör och spårning.
Om antalet spårning inte har uppdaterats inom två veckor för att skapa jobbet jobbet upphör att gälla. Jobbförloppet kan spåras på portalens instrumentpanel. Se vad varje Jobbstatus i föregående avsnitt innebär av [visa status för din](#viewing-your-job-status).

## <a name="when-should-i-use-the-azure-importexport-service"></a>När ska jag använda Azure Import/Export-tjänsten?
Överväg att använda Azure Import/Export service när överföra eller hämta data via nätverket är långsamt eller att få ytterligare nätverksbandbredden är mycket höga kostnaden.

Du kan använda den här tjänsten i scenarier såsom:

* Migrering av data till molnet: flytta stora mängder data till Azure snabbt och kostnadseffektivt sätt.
* Innehållsdistribution: snabbt skicka data till customer-platser.
* Säkerhetskopiering: Ta säkerhetskopior av lokala data ska lagras i Azure Storage.
* Återställning av data: återställa stora mängder data som lagras i lagring och dem till din lokala plats.

## <a name="prerequisites"></a>Förutsättningar
I det här avsnittet listas vi kraven för att använda den här tjänsten. Läs dem noga innan du levererar dina enheter.

### <a name="storage-account"></a>Lagringskonto
Du måste ha en befintlig Azure-prenumeration och en eller flera lagringskonton för att använda tjänsten Import/Export. Azure Import/Export stöder endast klassiska, Blob Storage-konton och generella v1 storage-konton. Varje jobb kan användas för att överföra data till eller från en enda storage-konto. Med andra ord kan inte ett enda import/export-jobb vara över flera lagringskonton. Information om hur du skapar ett nytt lagringskonto finns [hur du skapar ett Lagringskonto](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> Tjänsten Azure Import exportera har inte stöd för lagringskonton där den [virtuella nätverksslutpunkter](../../virtual-network/virtual-network-service-endpoints-overview.md) funktionen har aktiverats. 
> 
> 

### <a name="data-types"></a>Datatyper
Du kan använda tjänsten Azure Import/Export för att kopiera data till **Block** blobar **sidan** blobbar, eller **filer**. Däremot kan du bara exportera **Block** blobar **sidan** blobbar eller **Append** blobbar från Azure storage med hjälp av den här tjänsten. Tjänsten stöder endast import av Azure-filer till Azure storage. Exportera Azure-filer stöds inte för närvarande.

### <a name="job"></a>Jobb
Om du vill påbörja processen att importeras till eller exporteras från lagring skapa du först ett jobb. Ett jobb kan vara ett importjobb eller ett exportjobb:

* Skapa ett importjobb när du vill överföra data du har lokala till Azure storage-konto.
* Skapa ett exportjobb när du vill överföra data som lagras i ditt lagringskonto till hårddiskar som levereras till Microsoft. När du skapar ett jobb kan meddela du tjänsten Import/Export att du kommer att leverera en eller flera hårddiskar till ett Azure-datacenter.

* För ett importjobb ska du leverera hårddiskar som innehåller dina data.
* För ett exportjobb ska du leverera tomt hårddiskar.
* Du kan leverera upp till 10 hårddiskar per jobb.

Du kan skapa en importera eller exportera jobb med hjälp av Azure-portalen eller [Azure Storage Import/Export REST API](/rest/api/storageimportexport).

> [!Note]
> RDFE-API: er stöds inte 28 februari 2018 och senare. Om du vill fortsätta använda tjänsten, migrera till den [ARM Import/Export REST API: er](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/storageimportexport/resource-manager/Microsoft.ImportExport/stable/2016-11-01/storageimportexport.json). 

### <a name="waimportexport-tool"></a>WAImportExport-verktyget
Det första steget i att skapa en **importera** jobbet är att förbereda dina enheter som ska levereras för import. För att förbereda dina enheter, måste du ansluta till en lokal server och kör verktyget WAImportExport på den lokala servern. Verktyget WAImportExport underlättar kopiera dina data från enheten, kryptera data på enheten med BitLocker och generera journalfiler enhet.

Journal-filer lagrar grundläggande information om jobbet och enheten, till exempel serienummer för enheten och lagringskontonamn. Den här journalfilen lagras inte på enheten. Den används när importen jobbet skapades. Steg för steg-information om jobb skapas tillhandahålls nedan.

Verktyget WAImportExport är endast kompatibel med 64-bitars Windows-operativsystem. Finns det [operativsystemet](#operating-system) för specifika OS-versioner som stöds.

Hämta den senaste versionen av den [WAImportExport verktyget](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). Mer information om hur du använder verktyget WAImportExport finns i [med hjälp av verktyget WAImportExport](storage-import-export-tool-how-to.md).

>[!NOTE]
>**Tidigare Version:** kan du [hämta WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) versionen av verktyget och referera till [WAImportExpot V1 användning guiden](storage-import-export-tool-how-to-v1.md). WAImportExpot V1-versionen av verktyget ger stöd för **förbereda diskar när data redan före skrivs till disken**. Om nyckeln endast tillgängliga SAS-nyckel som du behöver använda WAImportExpot V1-verktyget.

>

### <a name="hard-disk-drives"></a>Hårddiskar
Endast 2,5 tum SSD eller 2,5-tums eller 3,5-tums SATA II eller III interna HDD stöds för användning med Import/Export-tjänsten. Ett enda import/export-jobb kan ha högst 10 Hårddisk/SSD och varje enskild Hårddisk/SSD-enheter kan vara av valfri storlek. Stort antal enheter går att sprida över flera jobb och det finns inga begränsningar för antalet jobb som kan skapas. 

För importjobb bearbetas bara den första datavolymen på enheten. Datavolym måste vara formaterad med NTFS.

> [!IMPORTANT]
> Externa hårddiskar som levereras med en inbyggd USB-adapter stöds inte av den här tjänsten. Disk i versaler och gemener i en extern Hårddisk kan inte användas; Skicka inte externa hårddiskar.
> 
> 

Nedan följer en lista över externa USB-adaptrar används för att kopiera data till interna hårddiskar. Anker 68UPSATAA - 02 för Anker 68UPSHHDS för Startech SATADOCK22UE Orico 6628SUS3 C BK (6628-serien) Thermaltake BlacX frekvent växlingen SATA externa hårddisken enhet dockningsstation (USB 2.0 & eSATA)

### <a name="encryption"></a>Kryptering
Data på enheten måste krypteras med BitLocker-diskkryptering. Den här kryptering skyddar dina data när den är i överföringen.

Importera jobb finns det två sätt att utföra krypteringen. Det första sättet är att ange alternativet när du använder dataset CSV-fil när du kör verktyget WAImportExport vid förberedelsen av enheten. Det andra sättet är att aktivera BitLocker-kryptering manuellt på enheten och ange krypteringsnyckeln i driveset CSV när du kör kommandoraden i WAImportExport verktyget vid förberedelsen av enheten.

För exportjobb när data kopieras till enheterna tjänsten kommer att kryptera enheten med BitLocker innan leverans till dig. Krypteringsnyckeln tillhandahålls via Azure portal.  

### <a name="operating-system"></a>Operativsystem
Du kan använda något av följande 64-bitars operativsystem för att förbereda hårddisken med hjälp av verktyget WAImportExport innan enheten till Azure:

Windows 7 Enterprise, Windows 7 Ultimate Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Alla dessa operativsystem stöder BitLocker-diskkryptering.

### <a name="locations"></a>Platser
Tjänsten Azure Import/Export stöder kopiering av data till och från alla offentliga Azure storage-konton. Du kan leverera hårddiskar till något av de angivna platserna. Om ditt lagringskonto är i en offentlig Azure-plats som inte anges här är en alternativ leveransplats anges när du skapar jobbet med Azure-portalen eller Import/Export REST API.

Leverans platser som stöds:

* Östra USA
* Västra USA
* Östra USA 2
* Västra USA 2
* Centrala USA
* Norra centrala USA
* Södra centrala USA
* Västra centrala USA
* Norra Europa
* Västra Europa
* Östasien
* Sydostasien
* Östra Australien
* Sydöstra Australien
* Västra Japan
* Östra Japan
* Indien, centrala
* Södra Indien
* Indien, västra
* Centrala Kanada
* Östra Kanada
* Södra Brasilien
* Centrala Korea
* Virginia (USA-förvaltad region)
* Iowa (USA-förvaltad region)
* US DoD, östra
* US DoD, centrala
* Östra Kina
* Norra Kina
* Storbritannien, södra
* Centrala Tyskland
* Nordöstra Tyskland

### <a name="shipping"></a>Fraktas
**Leverans enheter till datacentret:**

När du skapar ett jobb som importeras eller exporteras, kommer du angav en leveransadress för en av platserna som stöds för att leverera dina enheter. Leveransadress som beror på platsen för ditt lagringskonto, men får inte vara samma som din lagringsplats för kontot.

FedEx, UPS eller DHL kan användas för att leverera dina enheter till leveransadressen.

**Leverans enheter från datacentret:**

Du måste ange en avsändaradress för att Microsoft ska använda vid leverans enheterna tillbaka när jobbet har slutförts när du skapar ett jobb som importeras eller exporteras. Kontrollera att du anger en giltig avsändaradress för att undvika fördröjningar i bearbetning.

En operatör ska ha rätt spårning för att upprätthålla spårbarhet. Du måste ange en giltig FedEx UPS, eller DHL operatör konto som ska användas av Microsoft för att leverera enheterna igen. En FedEx, UPS eller DHL kontonummer krävs för leverans enheter tillbaka från USA och Europa. En DHL kontonummer krävs för leverans enheter tillbaka från Asien och Australien platser. Om du inte har någon kan du skapa en [FedEx](http://www.fedex.com/us/oadr/) (för USA och Europa) eller [DHL](http://www.dhl.com/) (Asien och Australien) operatör-konto. Om du redan har en operatör kontonummer, kontrollerar du att den är giltig.

I leverans dina paket, måste du följa villkor på [licensvillkor för Microsoft Azure Service](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Observera att fysiska media som du levererar kan behöva korsa internationella gränser. Du ansvarar för att säkerställa att din fysiska media och dina data importeras eller exporteras enligt tillämplig lagstiftning. Kontrollera med din rådgivare att kontrollera att dina media och data enligt lag kan levereras till identifierade datacentret innan levereras det fysiska mediet. Detta ser till att den når Microsoft inom rimlig tid. Alla paket som passerar internationella gränser måste till exempel en faktura åtföljas med paketet (utom om passerar gränser inom EU). Du kan skriva ut en fylld kopia av fakturan från operatör webbplats. Exempel på fakturor är [DHL faktura](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) och [FedEx faktura](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Kontrollera att Microsoft inte har angetts som Exportverktyget.
> 
> 

## <a name="how-does-the-azure-importexport-service-work"></a>Hur fungerar Azure Import/Export-tjänsten?
Du kan överföra data mellan lokal plats och Azure storage med hjälp av tjänsten Azure Import/Export av jobb och leverera hårddiskar till ett Azure-datacenter. Varje hårddisk som du levererar är associerad med ett enda jobb. Varje jobb är associerat med ett enda storage-konto. Granska de [förutsättningar avsnittet](#pre-requisites) noggrant mer information om egenskaperna för den här tjänsten, till exempel vilka datatyper, disk-typer, platser och leverans.

I det här avsnittet beskrivs hög nivå steg som ingår i import och export av jobb. Senare i den [Snabbstart avsnittet](#quick-start), stegvisa anvisningar för att skapa en importera och exportera jobbet har angetts.

### <a name="inside-an-import-job"></a>I ett importjobb
Ett importjobb omfattar följande steg på en hög nivå:

* Avgör vilka data som ska importeras och antalet enheter som du behöver.
* Identifiera målplatsen för blob- eller fil för dina data i Azure-lagring.
* Verktyget WAImportExport används för att kopiera data till en eller flera hårddiskar och kryptera dem med BitLocker.
* Skapa ett importjobb i dina mål-lagringskontot med hjälp av Azure-portalen eller Import/Export REST API. Om du använder Azure-portalen, överför journalfiler enhet.
* Ange avsändaradressen och operatör kontonummer som ska användas för att leverera enheterna till dig.
* Leverera hårddiskar till leveransadress tillhandahålls när jobbet skapas.
* Uppdatera leveransen spårnings-ID i Jobbinformationen import och skicka importjobbet.
* Enheter mottas och bearbetas på Azure-datacentret.
* Enheter levereras med ditt konto operatör avsändaradressen i importjobbet.
  
    ![Bild 1:Import jobbet flöde](./media/storage-import-export-service/importjob.png)

### <a name="inside-an-export-job"></a>I ett exportjobb
> [!IMPORTANT]
> Tjänsten stöder bara export av Azure-BLOB och stöder inte export av Azure-filer...
> 
>

Ett exportjobb omfattar följande steg på en hög nivå:

* Avgör vilka data som ska exporteras och antalet enheter som du behöver.
* Identifiera källan blobbar eller behållaren sökvägar för data i Blob storage.
* Skapa ett exportjobb i din käll-lagringskonto med hjälp av Azure-portalen eller Import/Export REST API.
* Ange källa blobbar eller behållaren sökvägar för dina data i exportjobbet.
* Ange avsändaradressen och operatör kontonummer för som ska användas för att leverera enheterna till dig.
* Leverera hårddiskar till leveransadress tillhandahålls när jobbet skapas.
* Uppdatera leveransen spårnings-ID i Jobbinformationen export och skicka exportjobbet.
* Enheterna tas emot och bearbetas på Azure-datacentret.
* Enheterna som är krypterad med BitLocker; nycklarna är tillgängliga via Azure portal.  
* Enheterna som levereras med ditt konto operatör avsändaradressen i importjobbet.
  
    ![Bild 2:Export jobbet flöde](./media/storage-import-export-service/exportjob.png)

### <a name="viewing-your-job-and-drive-status"></a>Visa status för jobbet och enhet
Du kan spåra statusen för din importera eller exportera jobben från Azure-portalen. Klicka på den **Import/Export** fliken. En lista över jobb visas på sidan.

![Visa jobbets status](./media/storage-import-export-service/jobstate.png)

Du ser något av följande jobbstatus beroende på om enheten är i processen.

| Jobbstatus | Beskrivning |
|:--- |:--- |
| Skapar | När ett jobb skapas, är dess status inställd på att skapa. Jobbet är i läget Skapa, förutsätter tjänsten Import/Export av enheterna som inte har levererats till datacentret. Ett jobb kan vara i läget Skapa upp till två veckor, efter vilken bort automatiskt av tjänsten. |
| Fraktas | När du levererar paketet ska du uppdatera spårningsinformation i Azure-portalen.  Detta kommer att stänga jobbet i ”leverans”. Jobbet kommer att finnas kvar i tillståndet leverans i upp till två veckor. 
| Mottagning | När alla enheter har tagits emot på Datacenter anges jobbets status till mottagen. |
| Överförs | När minst en enhet har startat bearbetning visas anges jobbets status till överföra. Se avsnittet enhet tillstånd under detaljerad information. |
| Packas | När alla enheter har bearbetat kommer jobbet att placeras i tillståndet paketering tills enheterna som har levererats till dig. |
| Slutförd | När alla enheter har levererats till kunden, om jobbet har slutförts utan fel, kommer jobbet anges till slutfört tillstånd. Jobbet tas automatiskt bort efter 90 dagar i slutfört tillstånd. |
| Stängd | När alla enheter har levererats till kunden, om det fanns några fel under bearbetning av jobbet, kommer jobbet anges till tillståndet Closed. Jobbet tas automatiskt bort efter 90 dagar i tillståndet Closed. |

I tabellen nedan beskrivs livscykeln för en enskild enhet som den övergår till ett jobb som importeras eller exporteras. Det aktuella tillståndet för varje enhet i ett jobb visas nu i Azure Portal.
I följande tabell beskrivs varje tillstånd varje enhet i ett jobb kan passera.

| Enhetsstatus | Beskrivning |
|:--- |:--- |
| Angivna | För ett importjobb är ursprungligt tillstånd för en enhet angivna tillstånd när jobbet har skapats från Azure-portalen. För ett exportjobb eftersom ingen enhet anges när jobbet skapas första enhet tillstånd är tillståndet Received. |
| Mottagning | Enheten övergår till tillståndet Received när operatorn Import/Export service har bearbetat de enheter som har tagits emot från företaget leverans för importen. Tillstånd för första enhet är ett exportjobb tillståndet Received. |
| NeverReceived | Enheten flyttas till tillståndet NeverReceived när paketet för ett jobb kommer, men paketet innehåller inte enheten. En enhet kan också flytta detta tillstånd om det har två veckor eftersom tjänsten har tagit emot leveransinformation, men paketet har inte kommit på datacenter. |
| Överförs | En enhet flyttas till överföra tillstånd när tjänsten startar att överföra data från enheten till Windows Azure Storage. |
| Slutförd | En enhet flyttas till slutfört tillstånd när tjänsten har överförts alla data utan fel.
| CompletedMoreInfo | En enhet flyttas till CompletedMoreInfo tillstånd när tjänsten några problem uppstod vid kopiering av data från eller till enheten. Informationen kan omfatta fel, varningar och informationsmeddelanden om överskrivning blobbar.
| ShippedBack | Enheten flyttas till ShippedBack tillstånd när den har levererats från data center tillbaka avsändaradressen. |

Den här avbildningen från Azure-portalen visar tillståndet för enheten för en exempel-jobb:

![Visa status för enhet](./media/storage-import-export-service/drivestate.png)

I följande tabell beskrivs enhet fel tillstånd och åtgärder som vidtas för varje tillstånd.

| Enhetsstatus | Händelse | Lösning / nästa steg |
|:--- |:--- |:--- |
| NeverReceived | En enhet som har markerats som NeverReceived (eftersom den inte skickades som en del av det jobbet leveransen) tas emot i en annan leveransen. | Driftteamet flyttar du enheten till tillståndet Received. |
| Gäller inte | En enhet som inte är en del av jobb som anländer på datacenter som en del av ett annat jobb. | Enheten kommer att markeras som en extra enhet och returneras till kunden när jobbet som är associerade med det ursprungliga paketet har slutförts. |

### <a name="time-to-process-job"></a>Tid för att bearbeta jobb
Hur lång tid det tar att bearbeta en import-/ exportjobb varierar beroende på olika faktorer, till exempel leveranstid jobbet typ, typen och storleken på data som kopieras och storleken på de diskar som angetts. Import/Export-tjänsten har inte ett SLA men när diskarna har tagits emot tjänsten strävar efter att slutföra kopian i 7 till 10 dagar. Du kan använda REST API för att spåra jobbförloppet närmare. Det finns en procent fullständig parameter i listan jobb åtgärden som ger en indikation på Kopiera pågår. Nå ut till oss om du behöver en uppskattning att slutföra en kritisk import/export av jobbet.

### <a name="pricing"></a>Prissättning
**Enheten som hanterar avgift**

Det finns en avgift för hantering av enheten för varje enhet som behandlas som en del av din importera eller exportera jobb. Se information på den [priser för Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Leverera kostnader**

När du levererar enheter till Azure betalar kostnaden leverans till leverans operatör. När Microsoft returnerar enheterna du debiteras kostnaden leverans till kontot operatör som du angav på jobbet skapades.

**Transaktionskostnader**

Det finns ingen transaktionskostnader utöver standardlagring transaktionskostnader när du importerar data till Azure Storage. Standard utgång avgifterna är tillämpliga när exporteras data från Blob storage. Mer information om transaktionskostnader finns [dataöverföring priser.](https://azure.microsoft.com/pricing/details/data-transfers/)



## <a name="how-to-import-data-into-azure-file-storage-using-internal-sata-hdds-and-ssds"></a>Hur du importerar data till Azure File Storage med hjälp av interna SATA-hårddiskar och SSD-enheter?
Följ de nedanstående steg om data på disken som ska importeras till Azure File Storage.
Det första steget när du importerar data med hjälp av tjänsten Azure Import/Export är att förbereda dina enheter med hjälp av verktyget WAImportExport. Följ stegen nedan för att förbereda dina enheter.

1. Identifiera vilka data som ska importeras till Azure File Storage. Detta kan vara kataloger och fristående filer på den lokala servern eller en nätverksresurs.  
2. Bestämma antalet enheter som du behöver beroende på storleken på data. Ange antalet krävs 2,5 tum SSD eller 2,5-tums eller 3,5-tums SATA II eller III hårddiskar.
4. Fastställa kataloger och/eller fristående filer som ska kopieras till varje hårddisk.
5. Skapa CSV-filer för datauppsättningen och driveset.
    
  Nedan visas ett exempel dataset CSV-filen exempel för att importera data som Azure-filer:
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   I exemplet ovan kopieras 100M_1.csv.txt till roten i ”filresursen”. Om ”filresursen” inte finns, skapas en. Alla filer och mappar under 50M_original ska rekursivt kopieras till filresursen. Mappstruktur bibehålls.

    Lär dig mer om [förbereder dataset CSV-filen](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    


    **Driveset CSV-fil**

    Värdet för flaggan driveset är en CSV-fil som innehåller listan över diskar som mappas enhetsbeteckningarna för verktyget att hämta listan över diskar korrekt förberedas. 

    Nedan följer exempel på driveset CSV-fil:
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    I ovanstående exempel förutsätts det att två diskar har anslutits och grundläggande NTFS-volymer med volymbeteckning G:\ och H:\ har skapats. Verktyget Formatera och kryptera den disk som är värd för H:\ och formatera inte eller kryptera den disk som värd för volymen G:\.

    Lär dig mer om [förbereder driveset CSV-filen](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file).

6.  Använd den [WAImportExport verktyget](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) att kopiera data till en eller flera hårddiskar.
7.  Du kan ange ”kryptera” på kryptering fält i drivset CSV-fil för att aktivera BitLocker-kryptering på hårddisken. Du kan också aktivera BitLocker-kryptering manuellt på hårddisken och anger ”AlreadyEncrypted” och ange nyckeln i driveset CSV när du kör verktyget.

8. Ändra inte data på hårddiskar eller journal-fil när du har slutfört förberedelse av disken.

> [!IMPORTANT]
> Varje hårddisk som du förbereder resulterar i en journal-fil. När du skapar importjobbet med Azure-portalen måste du överföra alla journalfiler enheter som ingår i det importjobbet. Enheter utan journalen inte kommer att bearbeta filer.
> 
>

Nedan ser du kommandon och exempel för att förbereda hårddisken med WAImportExport verktyg.

WAImportExport verktyget PrepImport kommando för den första kopia sessionen att kopiera kataloger och/eller filer med en ny kopia-session:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Importera exempel 1**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

För att **lägga till fler enheter**, skapa en ny driveset-fil och kör kommandot som nedan. Ange en ny driveset CSV-fil för efterföljande kopiera sessioner till olika hårddiskar än vad som anges i InitialDriveset CSV-filen, och ange den som ett värde i parametern ”AdditionalDriveSet”. Använd den **samma journalfil** ge namn och en **nytt sessions-ID**. Formatet för AdditionalDriveset CSV-fil är samma som InitialDriveSet format.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**Importera exempel 2**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

För att lägga till ytterligare data i samma driveset WAImportExport verktyget PrepImport kommando kan anropas för efterföljande kopiera sessioner att kopiera ytterligare filkatalogen: efterföljande kopiera sessioner till samma hårddiskar anges i InitialDriveset CSV-fil, ange den **samma journalfil** ge namn och en **nytt sessions-ID**; behöver inte ange lagringskontots åtkomstnyckel.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**Importera exempel 3**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Se mer information om hur du använder verktyget WAImportExport i [förbereder hårddiskar för import](storage-import-export-tool-preparing-hard-drives-import.md).

Dessutom finns den [Exempelarbetsflöde att förbereda hårddiskar för ett importjobb](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md) detaljerade steg för steg-instruktioner.  



## <a name="create-an-export-job"></a>Skapa ett exportjobb
Skapa ett exportjobb för att meddela tjänsten Import/Export att du kommer att skicka en eller flera tomma enheter till datacentret så att data kan exporteras från ditt lagringskonto till enheterna och enheterna sedan levereras till dig.

### <a name="prepare-your-drives"></a>Förbereda dina enheter
Inledande kontroller i följande rekommenderas för att förbereda dina enheter för ett exportjobb:

1. Kontrollera antalet diskar som krävs med hjälp av verktyget WAImportExport PreviewExport kommando. Mer information finns i [Förhandsgranska enhet användning för ett jobb exportera](https://msdn.microsoft.com/library/azure/dn722414.aspx). Det hjälper dig att förhandsgranska diskanvändning för blobbar som du har valt, baserat på storleken på de enheter som du ska använda.
2. Kontrollera att du kan läsa/skriva till hårddisken som ska levereras för exportjobbet.

### <a name="create-the-export-job"></a>Skapa exportjobbet
1. Om du vill skapa ett exportjobb navigerar du till fler tjänster -> STORAGE -> ”Import/export jobb” på Azure-portalen. Klicka på **skapa Import-/ exportjobb**.
2. I steg 1 grunderna Välj ”Exportera från Azure”, ange en sträng för jobbnamn, välja en prenumeration, ange eller välj en resursgrupp. Ange ett beskrivande namn för importjobbet. Observera att det namn som du anger kan innehålla endast små bokstäver, siffror, bindestreck och understreck, måste börja med en bokstav och får inte innehålla blanksteg. Du använder det namn du väljer att spåra dina jobb när de pågår och när de har slutförts. Ange kontaktinformation för personen som ansvarar för den här exportjobb. 

3. I steg 2 jobbinformation, väljer du lagringskontot som data ska exporteras från i avsnittet Storage-konto. Samlingsbibliotek platsen fylls automatiskt i baserat på regionens lagringskonto som valts. Ange vilka blob-data som du vill exportera från ditt lagringskonto till tomma enheten eller enheter. Du kan välja att exportera alla blob-data i lagringskontot eller kan du ange vilket blobbar eller anger blobbposter att exportera.
   
   Om du vill ange en blob att exportera den **lika med** selector, och ange den relativa sökvägen till blob, från och med behållarens namn. Använd *$root* ange root-behållaren.
   
   Ange alla BLOB som börjar med ett prefix i **börjar med** selector, och ange det prefix som börjar med ett snedstreck '/'. Prefixet kan vara prefixet för behållarens namn, fullständig behållarens namn eller fullständig behållarens namn följt av prefixet för blob-namnet.
   
   I följande tabell visas exempel på giltiga blob-sökvägar:
   
   | Väljare | BLOB-sökväg | Beskrivning |
   | --- | --- | --- |
   | Börjar med |/ |Exporterar alla BLOB storage-konto |
   | Börjar med |/$root / |Exporterar alla blobbar i behållaren rot |
   | Börjar med |/Book |Exporterar alla blobbar i en behållare som börjar med prefixet **book** |
   | Börjar med |/Music/ |Exporterar alla blobbar i behållaren **musik** |
   | Börjar med |/ musik/kärlek |Exporterar alla blobbar i behållaren **musik** som börjar med prefixet **gillar** |
   | Lika med |$root/logo.bmp |Export blob **logo.bmp** i behållaren rot |
   | Lika med |videos/story.mp4 |Export blob **story.mp4** i behållaren **videor** |
   
   Du måste ange blob-sökvägar i giltigt format för att undvika fel under bearbetning, som visas i den här skärmbilden.
   
   ![Skapa exportjobb - steg3](./media/storage-import-export-service/export-job-03.png)

4. I steg 3 returnera leverans info, väljer du en operatör från den nedrullningsbara listrutan och ange en giltig operatör kontonummer som du har skapat med den operatör. Microsoft använder det här kontot för att leverera enheter tillbaka till dig när importjobbet har slutförts. Ange en fullständig och giltig kontaktperson, telefon, e-post, gatuadress, ort, zip, stat/proviince och land/region.
   
 5. På sidan Sammanfattning tillhandahålls leveransadress för Azure-DataCenter som ska användas för att leverera diskar till Azure-Domänkontrollant. Se till att Jobbnamn och fullständig adress nämns på etiketten leverans. 

6. Klicka på OK på sidan Sammanfattning för att importera jobbet Skapa

7. Efter leverans diskarna, gå tillbaka till den **Import/Export** sida på Azure-portalen a) navigera och klicka på importjobbet b) klickar du på **uppdatera jobbstatus och spåra information när enheter levereras**. 
     c) Välj kryssrutan ”Markera som levererats”-d) anger antalet operatör och spårning.
    
   Om antalet spårning inte har uppdaterats inom två veckor för att skapa jobbet jobbet upphör att gälla.
   
8. Du kan spåra jobbförloppet på portalens instrumentpanel. Se vad varje Jobbstatus i föregående avsnitt innebär av [visa status för din](#viewing-your-job-status).

   > [!NOTE]
   > Om blob exporteras används vid tidpunkten för kopiering till hårddisken, ta en ögonblicksbild av blob Azure Import/Export-tjänsten och kopiera ögonblicksbilden.
   > 
   > 
 
9. När du har fått enheterna med exporterade data kan du visa och kopiera BitLocker-nycklar som genereras av tjänsten för enheten. Navigera för att exportera jobb på Azure-portalen och klicka på fliken Import/Export. Välj din exportjobb i listan och klicka på alternativet för BitLocker-nycklar. BitLocker-nycklar visas enligt nedan:
   
   ![Visa BitLocker-nycklar för exportjobb](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Gå igenom avsnittet med vanliga frågor och svar nedan som det täcker de vanligaste frågorna uppstår när du använder den här tjänsten.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Kan jag kopiera Azure File storage med hjälp av tjänsten Azure Import/Export?**

Ja, stöder tjänsten Azure Import/Export import till Azure File Storage. Det stöder inte export av Azure-filer just nu.

**Finns tjänsten Azure Import/Export för CSP-prenumerationer?**

Azure Import/Export-tjänsten stöder CSP-prenumerationer.

**Kan jag hoppa över förberedelsen av enheten för ett importjobb eller kan jag förbereda en enhet utan att kopiera?**

Alla enheter som du vill leverera för import av data måste förberedas med verktyget Azure WAImportExport. Du måste använda verktyget WAImportExport för att kopiera data till enheten.

**Behöver jag utföra eventuella förberedelse av disk när du skapar ett exportjobb?**

Inledande kontroller i ingen, men vissa rekommenderas. Kontrollera antalet diskar som krävs med hjälp av verktyget WAImportExport PreviewExport kommando. Mer information finns i [Förhandsgranska enhet användning för ett jobb exportera](https://msdn.microsoft.com/library/azure/dn722414.aspx). Det hjälper dig att förhandsgranska diskanvändning för blobbar som du har valt, baserat på storleken på de enheter som du ska använda. Kontrollera också att du kan läsa från och skriva till hårddisken som ska levereras för exportjobbet.

**Vad händer om jag av misstag skickar en Hårddisk som inte överensstämmer med stöds kraven?**

Azure-datacentret returneras stöds kravet på att du den enhet som inte överensstämmer. Om endast några av enheterna i paketet uppfyller kraven för stöd dessa enheter kommer att bearbetas och de enheter som inte uppfyller kraven kommer att returneras till dig.

**Kan jag avbryts min?**

Du kan avbryta ett jobb när dess status skapades eller leverans.

**Hur länge kan visa statusen för slutförda jobb i Azure portal?**

Du kan visa statusen för slutförda jobb för upp till 90 dagar. Slutförda jobb tas bort efter 90 dagar.

**Vad gör jag om jag vill importera eller exportera fler än 10 enheter?**

En import eller exportjobb kan referera endast 10-enheter i ett enda jobb för tjänsten Import/Export. Om du vill leverera fler än 10 enheter kan du skapa flera jobb. Enheter som är associerade med samma jobb måste levereras tillsammans i samma paket.
Microsoft erbjuder vägledning och hjälp när datakapacitet sträcker sig över flera disk importera jobb. Kontakta bulkimport@microsoft.com för mer information

**Tjänsten formatera enheter innan du går tillbaka dem?**

Nej. Alla enheter krypteras med BitLocker.

**Kan jag köpa enheter för import/export av jobb från Microsoft?**

Nej. Behöver du levererar egna enheter för både import och export av jobb.

** Hur kan jag komma åt data som importeras av den här tjänsten **

Informationen under Azure storage-konto kan nås via Azure-portalen eller med ett fristående verktyg som kallas Lagringsutforskaren. https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**När importjobbet slutförs vad kommer Mina data ut i storage-konto? Min kataloghierarkin bevaras?**

När du förbereder en hårddisk på en importjobb anges målet av DstBlobPathOrPrefix-fält i datamängden CSV. Detta är målbehållare i storage-konto som kopieras data från hårddisken. I den här målbehållare virtuella kataloger har skapats för mappar från hårddisken och blobbar skapas för filer. 

**Om det finns filer som redan finns i mitt lagringskonto, tjänsten skriver över befintliga blobbar eller filer i mitt lagringskonto?**

När du förbereder enheten du kan ange om målfiler ska skrivas över eller ignoreras använda fält i datamängden CSV-fil som kallas Disposition: < Byt namn på | Nej skriva över | skriva över >. Som standard tjänsten kommer Byt namn på nya filer i stället skriva över befintliga blobbar eller filer.

**Är verktyget WAImportExport kompatibel med 32-bitars operativsystem?**
Nej. Verktyget WAImportExport är endast kompatibel med 64-bitars Windows-operativsystem. Mer information finns i avsnittet om operativsystem i den [förutsättningar](#pre-requisites) för en fullständig lista över operativsystemversioner som stöds.

**Bör jag ta något annat än hårddisken i min paketet?**

Kontrollera levereras endast dina hårddiskar. Inkludera inte ange strömkablar eller USB-kablar.

**Måste jag mina enheter som använder FedEx eller DHL levereras?**

Du kan leverera enheter till datacenter med några kända operatör som FedEx DHL, UPS eller oss postgång. För att leverera enheter tillbaka till dig från datacentret, måste du dock ange en FedEx kontonummer i USA och EU eller ett DHL kontonummer i Asien och Australien regioner.

**Finns det några begränsningar med leverans internationellt min enhet?**

Observera att fysiska media som du levererar kan behöva korsa internationella gränser. Du ansvarar för att säkerställa att din fysiska media och dina data importeras eller exporteras enligt tillämplig lagstiftning. Kontrollera med din rådgivare att kontrollera att dina media och data enligt lag kan levereras till identifierade datacentret innan levereras det fysiska mediet. Detta hjälper för att säkerställa att den når Microsoft inom rimlig tid.

**När du skapar ett jobb är en plats som skiljer sig från min lagringskontoplatsen leveransadressen. Vad ska jag göra?**

Vissa konto lagringsplatser mappas till alternativa leverans platser. Tidigare kan tillgängliga leverans platser också tillfälligt mappas till andra platser. Kontrollera alltid leveransadress tillhandahålls när jobbet skapas innan du levererar dina enheter.

**När leverans enheten begär en operatör data center adress och telefonnummer kontaktnumret. Vad bör jag ge?**

Telefonnummer och DC-adress anges som en del av jobbet skapas.

**Kan jag använda tjänsten Azure Import/Export för att kopiera PST postlådor och SharePoint-data till O365?**

Se [Importera PST-filer eller SharePoint-data i Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Kan jag använda tjänsten Azure Import/Export för att kopiera Mina säkerhetskopior offline till Azure Backup-tjänsten?**

Se [Offline säkerhetskopiering arbetsflöde i Azure Backup](../../backup/backup-azure-backup-import-export.md).

**Vad är det maximala antalet Hårddisk för i en leverans?**

Valfritt antal hårddiskar kan vara i en leverans och om diskarna tillhör flera jobb rekommenderas att en) diskar med motsvarande namn i jobbet. b) uppdatera jobb med flera spårning suffixet med -1,-2 osv.
  
**Vad är maximal Blockblob och sidstorlek Blob som stöds av Disk Import/Export?**

Max Blockblob är ungefär 4.768TB eller 5,000,000 MB.
Max Page Blob är 1TB.

**Import/Export-Disk som har stöd för AES 256-kryptering?**

Azure Import/Export-tjänsten som standard krypterar med AES 128 bitlocker-kryptering, men detta kan ökas AES 256 genom att manuellt krypteras med bitlocker innan data kopieras. 

Om du använder [WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), nedan visas ett exempel på kommando
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
Om du använder [WAImportExport verktyget](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) ange ”AlreadyEncrypted” och ange nyckeln i driveset CSV.
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget WAImportExport](storage-import-export-tool-how-to.md)
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* [Azure Import exportera REST API-exemplet](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

