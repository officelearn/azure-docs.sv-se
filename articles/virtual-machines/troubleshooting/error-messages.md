---
title: Vanliga fel koder för virtuella datorer i Azure | Microsoft Docs
description: Förstå några av de vanliga fel koderna som påträffades när du etablerar och hanterar virtuella datorer i Azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: f5639d1cf94c77d699dc6de9841698b045ac1f96
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "76543026"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Förstå vanliga felmeddelanden när du hanterar virtuella datorer i Azure

I den här artikeln beskrivs några av de vanligaste fel koderna och meddelanden som du kan stöta på när du skapar eller hanterar virtuella datorer i Azure.

>[!NOTE]
> Du kan lämna kommentarer på den här sidan för feedback eller via [Azure feedback](https://feedback.azure.com/forums/216843-virtual-machines) med #azerrormessage-tagg.

## <a name="error-response-format"></a>Format för fel svar 
Virtuella Azure-datorer använder följande JSON-format för fel svar:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner level error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Ett fel svar innehåller alltid en status kod och ett fel objekt. Varje fel objekt innehåller alltid en felkod och ett meddelande. Om den virtuella datorn skapas med en mall innehåller felobjektet även ett informations avsnitt som innehåller en inre nivå av felkoder och meddelande. Normalt är den mest inre nivån av fel meddelande rotens fel.


## <a name="common-virtual-machine-management-errors"></a>Vanliga fel vid hantering av virtuella datorer

Det här avsnittet innehåller vanliga fel meddelanden som du kan stöta på när du hanterar virtuella datorer:

|  Felkod  |  Felmeddelande  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Det gick inte att hämta lånet vid skapande {0} av disk med BLOB med URI {1} . Blobben används redan.  |  
|  AllocationFailed  |  Tilldelningen misslyckades. Försök att minska storleken på den virtuella datorn eller antalet virtuella datorer, försök igen senare eller försök att distribuera till en annan tillgänglighets uppsättning eller en annan Azure-plats.  |  
|  AllocationFailed  |  Det gick inte att allokera den virtuella datorn på grund av ett internt fel. Försök igen senare eller försök att distribuera till en annan plats.  |
|  ArtifactNotFound  |  Det gick inte att hitta VM-tillägget med utgivaren {0} och typen {1} på platsen {2} .  |
|  ArtifactNotFound  |  {0}Det gick inte att hitta tillägget med utgivaren, typen {1} och typ hanterarens version {2} i tilläggs lagrings platsen.  |
|  ArtifactVersionNotFound  |  Det gick inte att hitta någon version i artefakt lagrings platsen som uppfyller den begärda versionen {0} .  |
|  ArtifactVersionNotFound  |  Det gick inte att hitta någon version i artefakt lagrings platsen som uppfyller den begärda versionen {0} för VM-tillägget med utgivaren {1} och typen {2} .  |
|  AttachDiskWhileBeingDetached  |  Det går inte att koppla data disken {0} till den virtuella datorn {1} eftersom disken för närvarande är frånkopplad. Vänta tills disken är helt frånkopplad och försök sedan igen.  |
|  BadRequest  |  Justerade tillgänglighets uppsättningar stöds ännu inte i den här regionen.  |
|  BadRequest  |  Det finns inte stöd för att lägga till en virtuell dator med Managed disks till icke-hanterad tillgänglighets uppsättning eller tillägg av en virtuell dator med BLOB-baserade diskar till hanterad tillgänglighets uppsättning. Skapa en tillgänglighets uppsättning med egenskapen hanterad inställd för att lägga till en virtuell dator med hanterade diskar.  |
|  BadRequest  |  Managed Disks stöds inte i den här regionen.  |
|  BadRequest  |  Flera VMExtensions per hanterare stöds inte för OS-typen {0} . VMExtension {1} med hanterare har {2} redan lagts till eller angetts i indatamängden.  |
|  BadRequest  |  Åtgärden {0} stöds inte för resursen {1} med Managed disks.  |
|  CertificateImproperlyFormatted  |  Hemlighetens JSON-representation som hämtades från {0} har ett data fält som inte är en korrekt FORMATERAD PFX-fil, eller så är det angivna lösen ordet inte att avkoda PFX-filen på rätt sätt.  |
|  CertificateImproperlyFormatted  |  Data som hämtats från {0} kan inte deserialiseras till JSON.  |
|  Konflikt  |  Storleks ändring av disken tillåts bara när du skapar en virtuell dator eller när den virtuella datorn frigörs.  |
|  ConflictingUserInput  |  {0}Det går inte att ansluta disken eftersom disken redan ägs av den virtuella datorn {1} .  |
|  ConflictingUserInput  |  Käll-och mål resurs grupper är desamma.  |
|  ConflictingUserInput  |  Käll-och mål lagrings kontona för disken {0} skiljer sig åt.  |
|  ContainerAlreadyOnLease  |  Det finns redan ett lån på lagrings behållaren som innehåller blobben med URI {0} .  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Begäran om att flytta resurser innehåller nyckel Valvs resurser som refereras till av en eller flera {0} s i begäran. Det finns inte stöd för att flytta mellan prenumerationer. Läs fel informationen om ID för nyckel Valvs resurs.  |
|  DiagnosticsOperationInternalError  |  Ett internt fel uppstod när den virtuella datorns diagnostiska profil bearbetades {0} .  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Blobben {0} används redan av en annan disk som tillhör den virtuella datorn {1} . Du kan kontrol lera BLOB-metadata för diskens referens information.  |
|  DiskBlobNotFound  |  Det gick inte att hitta VHD-blobben med URI {0} för disken {1} .  |
|  DiskBlobNotFound  |  Det gick inte att hitta VHD-blobben med URI {0} .  |
|  DiskEncryptionKeySecretMissingTags  |  {0}hemligheten har inte {1} taggarna. Uppdatera den hemliga versionen, Lägg till de nödvändiga taggarna och försök igen.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  {0}Det gick inte att packa upp det hemliga värdet med nyckeln {1} .  |
|  DiskImageNotReady  |  Disk avbildningen {0} har {1} statusen. Försök igen när avbildningen är klar.  |
|  DiskPreparationError  |  Ett eller flera fel uppstod när VM-diskar skulle förberedas. Mer information finns i vyn disk instans.  |
|  DiskProcessingError  |  Disk bearbetningen har stoppats eftersom den virtuella datorn har andra diskar på felaktiga diskar.  |
|  ImageBlobNotFound  |  Det gick inte att hitta VHD-blobben med URI {0} för disken {1} .  |
|  ImageBlobNotFound  |  Det gick inte att hitta VHD-blobben med URI {0} .  |
|  IncorrectDiskBlobType  |  Disk-blobbar kan endast vara av typen Page blob. Blobb {0} för disken {1} är av typen Block-Blob.  |
|  IncorrectDiskBlobType  |  Disk-blobbar kan endast vara av typen Page blob. BLOB {0} är av typen ' {1} '.  |
|  IncorrectImageBlobType  |  Disk-blobbar kan endast vara av typen Page blob. Blobb {0} för disken {1} är av typen Block-Blob.  |
|  IncorrectImageBlobType  |  Disk-blobbar kan endast vara av typen Page blob. BLOB {0} är av typen ' {1} '.  |
|  InternalOperationError  |  Det gick inte att matcha lagrings kontot {0} . Kontrol lera att den har skapats via Storage Resource Provider på samma plats som beräknings resursen.  |
|  InternalOperationError  |  {0}måls sökning av aktiviteter misslyckades.  |
|  InternalOperationError  |  Ett fel uppstod när nätverks profilen för den virtuella datorn verifierades {0} .  |
|  InvalidAccountType  |  Start@ {0} är ogiltig.  |
|  InvalidParameter  |  Parameterns värde {0} är ogiltigt.  |
|  InvalidParameter  |  Det angivna administratörs lösen ordet är inte tillåtet.  |
|  InvalidParameter  |  "Det angivna lösen ordet måste vara mellan {0} - {1} tecken långt och måste uppfylla minst {2} lösen ords komplexitets krav från följande: <ol><li> Innehåller ett versal steg</li><li>Innehåller ett gemener</li><li>Innehåller en numerisk siffra</li><li>Innehåller ett specialtecken.</li></ol>  |
|  InvalidParameter  |  Det angivna administratörs användar namnet är inte tillåtet.  |
|  InvalidParameter  |  Det går inte att koppla en befintlig OS-disk om den virtuella datorn skapas från en plattform eller användar avbildning.  |
|  InvalidParameter  |  Container namnet {0} är ogiltigt. Behållar namn måste vara 3-63 tecken långt och får bara innehålla gemena alfanumeriska tecken och bindestreck. Bindestreck måste föregås och följas av ett alfanumeriskt tecken.  |
|  InvalidParameter  |  Container namnet {0} i webb adressen {1} är ogiltigt. Behållar namn måste vara 3-63 tecken långt och får bara innehålla gemena alfanumeriska tecken och bindestreck. Bindestreck måste föregås och följas av ett alfanumeriskt tecken.  |
|  InvalidParameter  |  BLOB-namnet i URL: {0} en innehåller ett snedstreck. Det finns för närvarande inte stöd för diskar.  |
|  InvalidParameter  |  URI: n {0} ser inte ut korrekt som en BLOB-URI.  |
|  InvalidParameter  |  En disk med namnet ' {0} ' använder redan samma LUN: {1} .  |
|  InvalidParameter  |  Det finns redan en disk med namnet {0} .  |
|  InvalidParameter  |  Det går inte att ange åsidosättningar för användar avbildning för en disk som redan har definierats i den angivna bild referensen.  |
|  InvalidParameter  |  En disk med namnet ' {0} ' använder redan samma VHD-URL {1} .  |
|  InvalidParameter  |  Det angivna antalet fel domäner {0} måste ligga inom intervallet {1} till {2} .  |
|  InvalidParameter  |  Licens typen {0} är ogiltig. Giltiga licens typer är: Windows_Client eller Windows_Server, SKIFT läges känsligt.  |
|  InvalidParameter  |  Linux-värdnamnet får inte överskrida {0} tecken långa eller innehålla följande tecken: {1} .  |
|  InvalidParameter  |  Mål Sök vägen för offentliga SSH-nycklar är för närvarande begränsad till sitt standardvärde {0} på grund av ett känt problem i Linux-etablerings agenten.  |
|  InvalidParameter  |  Det finns redan en disk på LUN {0} .  |
|  InvalidParameter  |  Prenumerationen {0} på begäran måste matcha den prenumeration {1} som finns i ID för hanterad disk.  |
|  InvalidParameter  |  Anpassade data i OSProfile måste vara i base64-kodning och får bestå av högst {0} tecken.  |
|  InvalidParameter  |  BLOB-namnet i URL: en {0} måste sluta med {1} tillägget.  |
|  InvalidParameter  |  {0}' är inte ett giltigt prefix för avbildning av avbildnings-VHD-blob. Ett giltigt prefix matchar regex {1} .  |
|  InvalidParameter  |  Det går inte att lägga till certifikat i den virtuella datorn om VM-agenten inte har tillhandahållits.  |
|  InvalidParameter  |  Det finns redan en disk på LUN {0} .  |
|  InvalidParameter  |  Det gick inte att skapa den virtuella datorn eftersom den begärda storleken {0} inte är tillgänglig i klustret där tillgänglighets uppsättningen för närvarande är allokerad. Tillgängliga storlekar: {1} . Läs mer om strategi för storleks ändring av virtuella datorer på https://aka.ms/azure-resizevm .  |
|  InvalidParameter  |  Den begärda virtuella dator storleken {0} är inte tillgänglig i den aktuella regionen. De storlekar som är tillgängliga i den aktuella regionen är: {1} . Ta reda på mer om tillgängliga VM-storlekar i varje region på https://aka.ms/azure-regions .  |
|  InvalidParameter  |  Den begärda virtuella dator storleken {0} är inte tillgänglig i den aktuella regionen. Ta reda på mer om tillgängliga VM-storlekar i varje region på https://aka.ms/azure-regions .  |
|  InvalidParameter  |  Användar namnet för Windows-administratören får inte vara mer än {0} tecken långt, sluta med en punkt (.) eller innehålla följande tecken: {1} .  |
|  InvalidParameter  |  Windows-datornamnet får inte vara mer än {0} tecken långt, vara helt numeriskt eller innehålla följande tecken: {1} .  |
|  MissingMoveDependentResources  |  Begäran om att flytta resurser innehåller inte alla beroende resurser. Läs fel informationen om saknade resurs-ID: n.  |
|  MoveResourcesHaveInvalidState  |  Begäran om att flytta resurser innehåller virtuella datorer som är associerade med ogiltiga lagrings konton. Kontrol lera informationen för dessa resurs-ID: n och refererade lagrings konto namn.  |
|  MoveResourcesHavePendingOperations  |  Begäran om att flytta resurser innehåller resurser för vilka en åtgärd väntar. Kontrol lera informationen för dessa resurs-ID: n. Försök igen när de väntande åtgärderna har slutförts.  |
|  MoveResourcesNotFound  |  Begäran om att flytta resurser innehåller resurser som inte går att hitta. Kontrol lera informationen för dessa resurs-ID: n.  |
|  NetworkingInternalOperationError  |  Okänt nätverks tilldelnings fel.  |
|  NetworkingInternalOperationError  |  Okänt nätverks tilldelnings fel  |
|  NetworkingInternalOperationError  |  Ett internt fel uppstod vid bearbetning av nätverks profilen för den virtuella datorn.  |
|  NotFound  |  Det går inte att hitta tillgänglighets uppsättningen {0} .  |
|  NotFound  |  Den virtuella käll datorn {0} som anges i begäran finns inte på den här Azure-platsen.  |
|  NotFound  |  Det gick inte att hitta innehavaren med ID {0} .  |
|  NotFound  |  Det {0} går inte att hitta avbildningen.  |
|  NotSupported  |  Licens typen är {0} , men avbildnings-bloben {1} är inte från lokal plats.  |
|  OperationNotAllowed  |  {0}Det går inte att ta bort tillgänglighets uppsättningen. Innan du tar bort en tillgänglighets uppsättning ser du till att den inte innehåller någon virtuell dator.  |
|  OperationNotAllowed  |  Det är inte tillåtet att ändra SKU för tillgänglighets uppsättning från "justerad" till "klassisk".  |
|  OperationNotAllowed  |  Det går inte att ändra tillägg på den virtuella datorn när den virtuella datorn inte körs.  |
|  OperationNotAllowed  |  Avbildnings åtgärden stöds endast på en virtuell dator med BLOB-baserade diskar. Använd "image"-resurs-API: er för att skapa en avbildning från en hanterad virtuell dator.  |
|  OperationNotAllowed  |  Det {0} går inte att skapa resursen från avbildningen {1} förrän avbildningen har skapats.  |
|  OperationNotAllowed  |  Uppdateringar av encryptionSettings tillåts inte när en virtuell dator allokeras, försök igen när den virtuella datorn har frigjorts  |
|  OperationNotAllowed  |  Det finns inte stöd för att lägga till en hanterad disk till en virtuell dator med BLOB-baserade diskar.  |
|  OperationNotAllowed  |  Det maximala antalet data diskar som får anslutas till en virtuell dator av den här storleken är {0} .  |
|  OperationNotAllowed  |  Det finns inte stöd för att lägga till en BLOB-baserad disk till en virtuell dator med hanterade diskar.  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte på bilden {1} eftersom bilden har marker ATS för borttagning. Du kan endast försöka ta bort igen (eller vänta tills en pågående åtgärd slutförts).  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte på den virtuella datorn {1} eftersom den virtuella datorn är generaliserad.  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte eftersom återställnings punkt samlingen {1} har marker ATS för borttagning.  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte för det virtuella dator tillägget {1} eftersom det har marker ATS för borttagning. Du kan endast försöka ta bort igen (eller vänta tills en pågående åtgärd slutförts).  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte eftersom Virtual Machinesen {1} har allokerats med avbildningen {2} .  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte eftersom den virtuella datorn ScaleSet {1} använder avbildningen {2} .  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte på den virtuella datorn {1} eftersom den virtuella datorn har marker ATS för borttagning. Du kan endast försöka ta bort igen (eller vänta tills en pågående åtgärd slutförts).  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte på den virtuella datorn {1} eftersom den virtuella datorn antingen har frigjorts eller marker ATS för att bli fribelagd.  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte på den virtuella datorn {1} eftersom den virtuella datorn körs. Stäng av explicit om du stänger av den virtuella datorn inifrån gäst operativ systemet.  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte på den virtuella datorn {1} eftersom den virtuella datorn inte har frigjorts.  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte på den virtuella datorn {1} eftersom den virtuella datorn har tillägget i ett {2} felaktigt tillstånd.  |
|  OperationNotAllowed  |  Åtgärden {0} tillåts inte på den virtuella datorn {1} eftersom en annan åtgärd pågår.  |
|  OperationNotAllowed  |  Åtgärden kräver att {0} den virtuella datorn {1} är generaliserad.  |
|  OperationNotAllowed  |  Åtgärden kräver att den virtuella datorn körs (eller har angetts för körning).  |
|  OperationNotAllowed  |  Disk med storlek {0} GB, vilket är mindre än storleken på {1} GB för motsvarande disk i avbildningen är inte tillåten.  |
|  OperationNotAllowed  |  Tillägg för skalnings uppsättning för virtuella datorer i hanteraren {0} kan bara läggas till vid tidpunkten för skapandet av VM Scale set.  |
|  OperationNotAllowed  |  Tillägg för skalnings uppsättning för virtuella datorer i hanteraren {0} kan bara tas bort när VM Scale set tas bort.  |
|  OperationNotAllowed  |  Den virtuella datorn {0} använder redan hanterade diskar.  |
|  OperationNotAllowed  |  Den virtuella datorn {0} tillhör den klassiska tillgänglighets uppsättningen {1} . Uppdatera tillgänglighets uppsättningen så att den använder "justerad" SKU och försök sedan att konvertera igen.  |
|  OperationNotAllowed  |  Den virtuella datorn som skapas från avbildningen kan inte ha BLOB-baserade diskar. Alla diskar måste vara hanterade diskar.  |
|  OperationNotAllowed  |  Det går inte att slutföra avbildnings åtgärden eftersom den virtuella datorn inte är generaliserad.  |
|  OperationNotAllowed  |  Hanterings åtgärder på den virtuella datorn {0} tillåts inte eftersom VM-diskar konverteras till hanterade diskar.  |
|  OperationNotAllowed  |  En pågående åtgärd ändrar energi statusen för den virtuella datorn {0} till {1} . Utför åtgärden {2} efter en stund.  |
|  OperationNotAllowed  |  Det går inte att lägga till eller uppdatera den virtuella datorn. Den begärda virtuella dator storleken är {0} kanske inte tillgänglig i den befintliga allokeringsenheten. Läs mer om strategi för storleks ändring av virtuella datorer på https://aka.ms/azure-resizevm .  |
|  OperationNotAllowed  |  Det går inte att ändra storlek på den virtuella datorn eftersom den begärda storleken {0} inte är tillgänglig i klustret där tillgänglighets uppsättningen för närvarande är allokerad. Tillgängliga storlekar: {1} . Läs mer om strategi för storleks ändring av virtuella datorer på https://aka.ms/azure-resizevm .  |
|  OperationNotAllowed  |  Det går inte att ändra storlek på den virtuella datorn eftersom den begärda storleken {0} inte är tillgänglig i klustret där den virtuella datorn för närvarande är allokerad. Om du vill ändra storlek på den virtuella datorn för att {1} frigöra (det här är en åtgärd i Azure Portal) och försök sedan att ändra storlek igen. Läs mer om strategi för storleks ändring av virtuella datorer på https://aka.ms/azure-resizevm .  |
|  OSProvisioningClientError  |  OS-etableringen misslyckades för den virtuella datorn {0} eftersom gäst operativ systemet håller på att tillhandahållas.  |
|  OSProvisioningClientError  |  OS-etableringen för den virtuella datorn {0} misslyckades. Fel information: {1} kontrol lera att avbildningen har förberetts på rätt sätt (generaliseras). <ul><li>Instruktioner för Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Det gick inte att skapa SSH-nyckeln. Fel information: {0} . Lös problemet genom att kontrol lera om Linux-agenten är korrekt konfigurerad. <ul><li>Du kan kontrol lera anvisningarna på:https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  Det angivna användar namnet för den virtuella datorn är ogiltigt för den här Linux-distributionen. Fel information: {0} .  |
|  OSProvisioningInternalError  |  OS-etableringen misslyckades för den virtuella datorn {0} på grund av ett internt fel.  |
|  OSProvisioningTimedOut  |  OS-etableringen för den virtuella datorn {0} kunde inte slutföras inom den angivna tiden. Den virtuella datorn kanske fortfarande har slutfört etableringen. Kontrol lera etablerings statusen senare.  |
|  OSProvisioningTimedOut  |  OS-etableringen för den virtuella datorn {0} kunde inte slutföras inom den angivna tiden. Den virtuella datorn kanske fortfarande har slutfört etableringen. Kontrol lera etablerings statusen senare. Kontrol lera också att avbildningen har förberetts (generaliseras).   <ul><li>Instruktioner för Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruktioner för Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  OS-etableringen för den virtuella datorn {0} kunde inte slutföras inom den angivna tiden. Den virtuella datorns gästa Gent upptäcktes dock. Detta föreslår att gäst operativ systemet inte har förberetts för att användas som en VM-avbildning (med CreateOption = FromImage). Lös problemet genom att antingen använda den virtuella hård disken som är med CreateOption = Anslut eller förbereda den korrekt för användning som en avbildning:   <ul><li>Instruktioner för Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruktioner för Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Den nödvändiga VM-storleken är för närvarande inte tillgänglig på den valda platsen.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Det går inte att uppdatera resursen just nu på grund av en pågående plattforms uppdatering. Försök igen senare.  |
|  StorageAccountLimitation  |  Lagrings kontot {0} har inte stöd för sid blobbar som krävs för att skapa diskar.  |
|  StorageAccountLimitation  |  Lagrings kontot {0} har överskridit den tilldelade kvoten.  |
|  StorageAccountLocationMismatch  |  Det gick inte att matcha lagrings kontot {0} . Kontrol lera att den har skapats via Storage Resource Provider på samma plats som beräknings resursen.  |
|  StorageAccountNotFound  |  Lagrings kontot {0} hittades inte. Se till att lagrings kontot inte har tagits bort och tillhör samma Azure-plats som den virtuella datorn.  |
|  StorageAccountNotRecognized  |  Använd ett lagrings konto som hanteras av Storage Resource Provider. {0}Det finns inte stöd för användning av.  |
|  StorageAccountOperationInternalError  |  Ett internt fel uppstod vid åtkomst till lagrings kontot {0} .  |
|  StorageAccountSubscriptionMismatch  |  Lagrings kontot {0} tillhör inte prenumerationen {1} .  |
|  StorageAccountTooBusy  |  Lagrings kontot {0} är för upptaget. Överväg att använda ett annat konto.  |
|  StorageAccountTypeNotSupported  |  Disken {0} använder {1} ett Blob Storage-konto. Försök igen med lagrings kontot för generell användning.  |
|  StorageAccountTypeNotSupported  |  Lagrings kontot {0} är av {1} typen. Startdiagnostik stöder {2} lagrings konto typer.  <ul><li>Det här felet uppstår om du använder Premium Storage-kontot för startdiagnostik. Mer information finns i [så här använder du startdiagnostik](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Prenumerationen är inte auktoriserad.  |
|  TargetDiskBlobAlreadyExists  |  Det {0} finns redan en blob. Ange en annan BLOB-URI för att skapa en ny tom data disk {1} .  |
|  TargetDiskBlobAlreadyExists  |  Avbildnings åtgärden kan inte fortsätta eftersom mål avbildnings-bloben {0} redan finns och flaggan för att skriva över VHD-blobar inte har angetts. Ta antingen bort blobben eller ange flaggan för att skriva över VHD-blobbar och försök igen.  |
|  TargetDiskBlobAlreadyExists  |  Avbildnings åtgärden kan inte fortsätta eftersom mål avbildnings-BLOB {0} har ett aktivt lån.   |
|  TargetDiskBlobAlreadyExists  |  Det {0} finns redan en blob. Ange en annan BLOB-URI som mål för disken {1} .  |
|  TooManyVMRedeploymentRequests  |  För många omdistributions begär Anden har tagits emot för den virtuella datorn {0} eller de virtuella datorerna i samma availabilityset med den här virtuella datorn. Försök igen senare.  |
|  VHDSizeInvalid  |  Det angivna disk storlek svärdet {0} för disken {1} med BLOB {2} är ogiltigt. Disk storleken måste vara mellan {3} och {4} .  |
|  VMAgentStatusCommunicationError  |  Den virtuella datorn {0} har inte rapporterat status för VM-agenten eller-tillägg. Kontrol lera att den virtuella datorn har en virtuell dator agent som körs och kan upprätta utgående anslutningar till Azure Storage.  |
|  VMArtifactRepositoryInternalError  |  Ett fel uppstod vid kommunikation med artefakt lagrings platsen för att hämta information om virtuell dator artefakt.  |
|  VMArtifactRepositoryInternalError  |  Ett internt fel inträffade när den virtuella datorns artefakt data hämtades från artefakt lagret.  |
|  VMExtensionHandlerNonTransientError  |  Hanteraren {0} rapporterade ett fel för det virtuella dator tillägget " {1} " med Terminal-felkoden " {2} " och fel meddelande: " {3} "  |
|  VMExtensionManagementInternalError  |  Ett internt fel uppstod vid bearbetning av det virtuella dator tillägget {0} .  |
|  VMExtensionManagementInternalError  |  Flera fel uppstod när VM-tilläggen skulle förberedas. Mer information finns i instans vyn för VM-tillägg.  |
|  VMExtensionProvisioningError  |  Ett problem har rapporter ATS för den virtuella datorn under bearbetning av tillägget {0} . Fel meddelande: " {1} ".  |
|  VMExtensionProvisioningError  |  Det gick inte att tillhandahålla flera VM-tillägg på den virtuella datorn. Mer information finns i instans vyn för VM-tillägg.  |
|  VMExtensionProvisioningTimeout  |  Tids gränsen nåddes för etablering av VM-tillägget {0} . Det kan ta för lång tid att installera tillägg, eller så gick det inte att hämta tilläggets status.  |
|  VMMarketplaceInvalidInput  |  Att skapa en virtuell dator från en icke-Marketplace-avbildning behöver inte planera information, ta bort plan informationen i begäran. OS-diskens namn är {0} .  |
|  VMMarketplaceInvalidInput  |  Inköps informationen stämmer inte överens. Det går inte att distribuera från Marketplace-avbildningen. OS-diskens namn är {0} .  |
|  VMMarketplaceInvalidInput  |  Om du skapar en virtuell dator från Marketplace-avbildningen måste du planera information i begäran. OS-diskens namn är {0} .  |
|  VMNotFound  |  Det går inte att hitta den virtuella datorn {0} .  |
|  VMRedeploymentFailed  |  {0}Det gick inte att distribuera om den virtuella datorn på grund av ett internt fel. Försök igen senare.  |
|  VMRedeploymentTimedOut  |  Omdistributionen av den virtuella datorn {0} kunde inte slutföras inom den angivna tiden. Det kan slutföras i en stund. Annars kan du försöka utföra begäran igen.  |
|  VMStartTimedOut  |  Den virtuella datorn startade {0} inte inom den angivna tiden. Den virtuella datorn kan fortfarande starta. Kontrol lera energi spar läget senare.  |


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
