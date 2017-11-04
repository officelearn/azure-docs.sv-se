>[!NOTE]
> Du kan lämna kommentarer på den här sidan för feedback eller via [Azure feedback](https://feedback.azure.com/forums/216843-virtual-machines) med #azerrormessage tagg.

## <a name="error-response-format"></a>Fel svarsformat 
Virtuella Azure-datorer kan du använda följande JSON-format för felsvar:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Ett felsvar innehåller alltid en statuskod och ett fel uppstod när objektet. Varje felobjekt innehåller alltid en felkod och ett meddelande. Om den virtuella datorn har skapats med en mall, innehåller felobjektet också ett information avsnitt som innehåller en inre nivå av felkoder och meddelandet. Normalt är den mest inre säkerhetsnivån felmeddelande roten fel.


## <a name="common-virtual-machine-management-errors"></a>Vanliga virtuella management fel

Det här avsnittet innehåller vanliga felmeddelanden som kan uppstå när du hanterar virtuella datorer:

|  Felkod  |  Felmeddelande  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Det gick inte att låna medan disken {0}-med blobben med URI {1}. BLOB används redan.  |  
|  AllocationFailed  |  Det gick inte att allokera. Försök att minska den Virtuella datorstorleken eller antalet virtuella datorer, försök igen senare eller försök att distribuera till en annan Tillgänglighetsuppsättning eller olika Azure-plats.  |  
|  AllocationFailed  |  Det gick inte att allokera VM på grund av ett internt fel. Försök igen senare eller försök att distribuera till en annan plats.  |
|  ArtifactNotFound  |  Det Virtuella datortillägget med utgivaren: {0} och typ '{1}' hittades inte på platsen '{2}'.  |
|  ArtifactNotFound  |  Tillägg med utgivare {0}-, Skriv '{1}' och typhanteringsversion '{2}' kunde inte hittas i tilläggscentrallagret.  |
|  ArtifactVersionNotFound  |  Det gick inte att hitta någon version i artefaktcentrallagret som motsvarar den efterfrågade versionen {0}.  |
|  ArtifactVersionNotFound  |  Ingen version hittades i artefaktcentrallagret som motsvarar den efterfrågade versionen '{0}' för VM-tillägg med utgivare '{1}' och typen '{2}'.  |
|  AttachDiskWhileBeingDetached  |  Det går inte att ansluta datadisk: {0} till VM '{1}' eftersom disken är bortkopplad. Vänta tills disken är helt frånkopplad och försök sedan igen.  |
|  BadRequest  |  Justerad ' Tillgänglighetsuppsättningar ännu inte stöds i den här regionen.  |
|  BadRequest  |  Lägga till en virtuell dator med hanterade diskar till icke-hanterad Tillgänglighetsuppsättning eller lägga till en virtuell dator med blob baserat diskar till hanterade Tillgänglighetsuppsättning stöds inte. Skapa en Tillgänglighetsuppsättning med ”hanterad” värdet för egenskapen du vill lägga till en virtuell dator med hanterade diskar.  |
|  BadRequest  |  Hanterade diskar stöds inte i den här regionen.  |
|  BadRequest  |  Flera VMExtensions per hanterare stöds inte för OS Skriv: {0}. VMExtension '{1}' med hanteraren '{2}' har redan lagts till eller angetts i indata.  |
|  BadRequest  |  Åtgärden: {0} stöds inte på resursen '{1}' med hanterade diskar.  |
|  CertificateImproperlyFormatted  |  Hemlighetens JSON-representation som hämtades från {0} har ett datafält som inte är en korrekt formaterad PFX-fil, eller så kan det tillhandahållna lösenordet inte avkoda PFX-filen korrekt.  |
|  CertificateImproperlyFormatted  |  De data som hämtades från {0} går inte att deserialisera till JSON.  |
|  Konflikt  |  Det går bara att ändra storlek på en disk när en virtuell dator skapas eller när den virtuella datorn frigörs.  |
|  ConflictingUserInput  |  Disk {0}' kan inte bifogas eftersom disken är redan ägs av VM-{1}'.  |
|  ConflictingUserInput  |  Käll- och målresursgrupperna är desamma.  |
|  ConflictingUserInput  |  Käll- och mållagringskontona för disken {0} är olika.  |
|  ContainerAlreadyOnLease  |  Det finns redan ett lån på den lagringsbehållare som innehåller blobben med URI {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Begäran om att flytta resurser innehåller KeyVault-resurser som refereras av en eller flera {0} s i begäran. Detta stöds inte för närvarande i mellan prenumerationer Move. Kontrollera felinformationen för KeyVault-resursen i ID: N.  |
|  DiagnosticsOperationInternalError  |  Ett internt fel inträffade när diagnostikprofilen för den virtuella datorn {0} bearbetades.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  BLOB {0} används redan av en annan disk som hör till VM '{1}'. Du kan undersöka blobmetadata för referensinformation disk.  |
|  DiskBlobNotFound  |  Det gick inte att hitta VHD-blobben med URI {0} för disken '{1}'.  |
|  DiskBlobNotFound  |  Det gick inte att hitta VHD-blobben med URI {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} hemlighet saknar {1}-taggar. Uppdatera den hemliga versionen, Lägg till taggarna som krävs och försök igen.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Unwrap av hemliga {0}-värde med hjälp av viktiga {1} misslyckades.  |
|  DiskImageNotReady  |  Disk image {0} är i {1} tillstånd. Försök igen när avbildningen är klar.  |
|  DiskPreparationError  |  Ett eller flera fel inträffade vid förberedelse av Virtuella diskar. I avsnittet disk instans visa mer information.  |
|  DiskProcessingError  |  Disk bearbetningen stannas som den virtuella datorn har andra diskar i felaktiga diskar.  |
|  ImageBlobNotFound  |  Det gick inte att hitta VHD-blobben med URI {0} för disken '{1}'.  |
|  ImageBlobNotFound  |  Det gick inte att hitta VHD-blobben med URI {0}.  |
|  IncorrectDiskBlobType  |  Diskblobbar kan bara vara av typen sidblob. BLOB {0} för disken '{1}' är av typen blockblob.  |
|  IncorrectDiskBlobType  |  Diskblobbar kan bara vara av typen sidblob. BLOB {0} är av typen '{1}'.  |
|  IncorrectImageBlobType  |  Diskblobbar kan bara vara av typen sidblob. BLOB {0} för disken '{1}' är av typen blockblob.  |
|  IncorrectImageBlobType  |  Diskblobbar kan bara vara av typen sidblob. BLOB {0} är av typen '{1}'.  |
|  InternalOperationError  |  Det gick inte att matcha lagringskontot {0}. Kontrollera att den skapades med Lagringsresursleverantören på samma plats som beräkningsresursen.  |
|  InternalOperationError  |  {0} målsökningsuppgifter misslyckades.  |
|  InternalOperationError  |  Det uppstod ett fel vid verifieringen av nätverksprofilen för den virtuella datorn {0}.  |
|  InvalidAccountType  |  AccountType {0} är ogiltig.  |
|  InvalidParameter  |  Värdet för parametern {0} är ogiltigt.  |
|  InvalidParameter  |  Det specificerade administratörslösenordet är inte tillåtet.  |
|  InvalidParameter  |  ”Det tillhandahållna lösenordet måste vara mellan {0}-\  tecken och måste uppfylla minst {2} av kraven på lösenordskomplexitet från följande: <ol><li> Innehåller en versal bokstav</li><li>Innehåller en gemen bokstav</li><li>Innehåller ett numeriskt värde</li><li>Innehåller ett specialtecken.</li></ol>  |
|  InvalidParameter  |  Det specificerade administratörsanvändarnamnet är inte tillåtet.  |
|  InvalidParameter  |  Det går inte att bifoga en befintlig OS-disk om den virtuella datorn skapas från en plattform eller användaravbildning.  |
|  InvalidParameter  |  Behållaren {0} är ogiltig. Behållarnamn måste innehålla 3-63 tecken långt och får bara innehålla gemena alfanumeriska tecken och bindestreck. Bindestreck måste föregås och följas av alfanumeriska tecken.  |
|  InvalidParameter  |  Behållaren {0} i URL: en {1} är ogiltig. Behållarnamn måste innehålla 3-63 tecken långt och får bara innehålla gemena alfanumeriska tecken och bindestreck. Bindestreck måste föregås och följas av alfanumeriska tecken.  |
|  InvalidParameter  |  Blobbnamnet i URL-Adressen {0} innehåller ett snedstreck. Detta är för närvarande inte stöd för diskar.  |
|  InvalidParameter  |  URI {0} ser inte ut att vara en korrekt blob-URI.  |
|  InvalidParameter  |  En disk med namnet {0} redan i LUN: {1}.  |
|  InvalidParameter  |  En disk med namnet {0} redan finns.  |
|  InvalidParameter  |  Det går inte att ange användarens avbildningsåsidosättanden för en disk som redan har definierats i den angivna avbildningsreferensen.  |
|  InvalidParameter  |  En disk med namnet {0} redan använder samma VHD URL {1}.  |
|  InvalidParameter  |  {0} angivna feldomänen domän count måste ligga i intervallet {1} till {2}.  |
|  InvalidParameter  |  Licens typen {0} är ogiltig. Giltiga licenstyper är: Windows_Client eller Windows_Server, skiftlägeskänsligt.  |
|  InvalidParameter  |  Linux-värdnamnet får inte överskrida {0} tecken eller innehålla följande tecken: {1}.  |
|  InvalidParameter  |  Målsökvägen för offentliga SSH-nycklar är för tillfället begränsad till standardvärdet {0} pga ett känt problem i Linux-etableringsagenten.  |
|  InvalidParameter  |  Det finns redan en disk på LUN {0}.  |
|  InvalidParameter  |  Prenumerationen {0} för begäran måste matcha den prenumeration {1} i hanterade disk-id.  |
|  InvalidParameter  |  Anpassade data i OSProfile måste vara Base64-kodade och bestå av högst {0} tecken.  |
|  InvalidParameter  |  Blobbnamnet i URL-Adressen {0} måste avslutas med tillägget '{1}'.  |
|  InvalidParameter  |  {0}' är inte ett giltigt avbildade VHD blob namnprefix. Ett giltigt prefix matchar regex '{1}'.  |
|  InvalidParameter  |  Det går inte att lägga till certifikat till din virtuella dator om VM-agenten inte har tillhandahållits.  |
|  InvalidParameter  |  Det finns redan en disk på LUN {0}.  |
|  InvalidParameter  |  Det gick inte att skapa den virtuella datorn eftersom den begärda storleken {0} inte finns i klustret där tillgänglighetsuppsättning är allokerat för tillfället. Tillgängliga värden är: {1}. Läs mer på VM storleksändring strategi på https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  Den begärda VM-storlek {0} finns inte i det aktuella området. Storlekarna som finns i det aktuella området är: {1}. Lär dig mer om tillgängliga VM-storlekar i varje region på https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Den begärda VM-storlek {0} finns inte i det aktuella området. Lär dig mer om tillgängliga VM-storlekar i varje region på https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Windows-administratörsanvändarnamnet får inte innehålla fler än {0} tecken långa, sluta med en period(.) eller innehålla följande tecken: {1}.  |
|  InvalidParameter  |  Windows-datornamn får inte innehålla fler än {0} tecken långa, vara helt numeriskt eller innehålla följande tecken: {1}.  |
|  MissingMoveDependentResources  |  Begäran om att flytta resurser innehåller inte alla beroende resurser. Kontrollera felinformationen för resurs-ID: n som saknas.  |
|  MoveResourcesHaveInvalidState  |  Begäranden om att flytta resurser innehåller virtuella datorer som är associerade med ogiltig storage-konton. Kontrollera information om dessa resurs-ID och refererade lagringskontonamn.  |
|  MoveResourcesHavePendingOperations  |  Begäran om att flytta resurser innehåller resurser som en åtgärd väntar. Kontrollera information om dessa resurs-ID: n. Försök igen när de väntande åtgärderna har slutfört.  |
|  MoveResourcesNotFound  |  Begäran om att flytta resurser innehåller resurser som inte kan hittas. Kontrollera information om dessa resurs-ID: n.  |
|  NetworkingInternalOperationError  |  Okänt nätverkstilldelningsfel.  |
|  NetworkingInternalOperationError  |  Okänt nätverkstilldelningsfel  |
|  NetworkingInternalOperationError  |  Det uppstod ett internt fel vid bearbetningen av den virtuella datorns nätverksprofil.  |
|  notFound  |  Det går inte att hitta tillgänglighetsuppsättningen {0}.  |
|  notFound  |  Virtuella källdatorn '{0}' som anges i begäran finns inte i den här Azure-plats.  |
|  notFound  |  Det gick inte att hitta klienten med ID:t {0}.  |
|  notFound  |  Det går inte att hitta avbildningen {0}.  |
|  NotSupported  |  Licenstypen är {0}, men avbildningen blob {1} är inte lokal.  |
|  OperationNotAllowed  |  Tillgänglighet Set {0} kan inte tas bort. Innan du tar bort en Tillgänglighetsuppsättning se till att den inte innehåller någon virtuell dator.  |
|  OperationNotAllowed  |  Ändra tillgänglighetsuppsättning är SKU från 'Justerad' till 'Klassiskt' inte tillåtet.  |
|  OperationNotAllowed  |  Det går inte att ändra tillägg i den virtuella datorn när den inte körs.  |
|  OperationNotAllowed  |  Capture-åtgärd stöds endast på en virtuell dator med blob baserat diskar. Använd '' avbildningsresursen API: er för att skapa en avbildning från en hanterad virtuell dator.  |
|  OperationNotAllowed  |  Resursen {0} kan inte skapas från avbildningen {1} tills bilden har skapats.  |
|  OperationNotAllowed  |  Det går inte att uppdatera encryptionSettings när den virtuella datorn är tilldelad. Försök igen när den har frigjorts.  |
|  OperationNotAllowed  |  Det finns inte stöd för att lägga till en hanterad disk i en virtuell dator med blobbaserade diskar.  |
|  OperationNotAllowed  |  Det maximala antalet datadiskar som kan kopplas till en virtuell dator av den här storleken är {0}.  |
|  OperationNotAllowed  |  Det finns inte stöd för att lägga till en blobbaserad disk i en virtuell dator med hanterade diskar.  |
|  OperationNotAllowed  |  Åtgärden: {0} är inte tillåten på bilden {1} eftersom bilden har markerats för borttagning. Du kan bara försök ta bort (eller vänta tills en pågående att slutföra).  |
|  OperationNotAllowed  |  Åtgärden: {0} är inte tillåten på den virtuella datorn '{1}' eftersom den virtuella datorn är generaliserad.  |
|  OperationNotAllowed  |  Åtgärden '{0}' tillåts inte som återställning punkt samlingen '{1}' är markerad för borttagning.  |
|  OperationNotAllowed  |  Åtgärden '{0}' tillåts inte för VM-tillägget '{1}' eftersom den har markerats för borttagning. Du kan bara försök ta bort (eller vänta tills en pågående att slutföra).  |
|  OperationNotAllowed  |  Åtgärden '{0}' tillåts inte eftersom de virtuella datorerna '{1}' som tillhandahålls med bild '{2}'.  |
|  OperationNotAllowed  |  Åtgärden: {0} är inte tillåten eftersom den virtuella datorn ScaleSet '{1}' avbildningen '{2}'.  |
|  OperationNotAllowed  |  Åtgärden: {0} är inte tillåten på den virtuella datorn '{1}' eftersom den virtuella datorn har markerats för borttagning. Du kan bara försök ta bort (eller vänta tills en pågående att slutföra).  |
|  OperationNotAllowed  |  Åtgärden: {0} är inte tillåten på den virtuella datorn '{1}' eftersom den virtuella datorn antingen har frigjorts eller markerats för att frigöras.  |
|  OperationNotAllowed  |  Åtgärden: {0} är inte tillåten på den virtuella datorn '{1}' eftersom den virtuella datorn körs. Du måste ange power ut om du stänger av den virtuella datorn i gästoperativsystemet.  |
|  OperationNotAllowed  |  Åtgärden: {0} är inte tillåten på den virtuella datorn '{1}' eftersom den inte har frigjorts.  |
|  OperationNotAllowed  |  Åtgärden: {0} är inte tillåten på den virtuella datorn '{1}' eftersom VM har tillägget '{2}' i ett feltillstånd.  |
|  OperationNotAllowed  |  Åtgärden: {0} är inte tillåten på den virtuella datorn '{1}' eftersom en annan åtgärd pågår.  |
|  OperationNotAllowed  |  Åtgärden: {0} kräver att den virtuella datorn '{1}' ska vara generaliserad.  |
|  OperationNotAllowed  |  Den virtuella datorn måste köras (eller vara inställd på körning) för att åtgärden ska kunna utföras.  |
|  OperationNotAllowed  |  Disken med storlek {0} GB, vilket är mindre än storleken {1}GB av motsvarande disk i bild är inte tillåtet.  |
|  OperationNotAllowed  |  VM Scale Set-tillägg för hanteraren {0} kan bara läggas till när VM Scale Set skapas.  |
|  OperationNotAllowed  |  VM Scale Set-tillägg för hanteraren {0} kan bara tas bort när VM Scale Set tas bort.  |
|  OperationNotAllowed  |  VM: {0} används redan av hanterade diskar.  |
|  OperationNotAllowed  |  VM: {0} tillhör {1}' 'Klassiskt' tillgänglighet set'. Uppdatera tillgänglighetsuppsättning använda 'Justerad' SKU: N och försök sedan utföra konverteringen.  |
|  OperationNotAllowed  |  VM som skapas från avbildningen kan inte ha blob baserat diskar. Alla diskar måste vara hanterade diskar.  |
|  OperationNotAllowed  |  Avbildningsåtgärden kan inte slutföras eftersom den virtuella datorn inte har generaliserats.  |
|  OperationNotAllowed  |  Hanteringsåtgärder på VM: {0} tillåts inte eftersom VM-diskarna omvandlas till hanterade diskar.  |
|  OperationNotAllowed  |  En pågående åtgärd ändrar energisparläge för virtuell dator {0} till {1}. Utför åtgärden {2} efter en stund.  |
|  OperationNotAllowed  |  Det går inte att lägga till eller uppdatera den virtuella datorn. Den begärda VM-storlek {0} får inte vara tillgängliga i den befintliga allokeringsenheten. Läs mer på VM storleksändring strategi på https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Det går inte att ändra storlek på den virtuella datorn eftersom den begärda storleken {0} inte finns i klustret där tillgänglighetsuppsättning är allokerat för tillfället. Tillgängliga värden är: {1}. Läs mer på VM storleksändring strategi på https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Det går inte att ändra storlek på den virtuella datorn eftersom den begärda storleken {0} inte finns i klustret där den virtuella datorn för närvarande allokeras. Ändra storlek på den virtuella datorn till {1} du frigöra (detta är Stop-åtgärd i Azure portal) och försök att ändra storlek igen. Läs mer på VM storleksändring strategi på https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Etablering av operativsystem misslyckades för den virtuella datorn {0} eftersom gästoperativsystemet håller på att etableras.  |
|  OSProvisioningClientError  |  Det gick inte att OS-etableringen för VM: {0}. Information om felet: {1} Kontrollera att avbildningen har förberetts på rätt (generaliserats). <ul><li>Instruktioner för Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Det gick inte att nyckelgenerering för SSH-värden. Felinformation: {0}. Lös problemet kontrollera om Linux-agenten är korrekt konfigurerad. <ul><li>Du kan kontrollera anvisningarna på: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/ </li></ul> |
|  OSProvisioningClientError  |  Användarnamnet som angetts för den virtuella datorn är ogiltigt för den här Linux-distribution. Felinformation: {0}.  |
|  OSProvisioningInternalError  |  Operativsystemsetableringen misslyckades för den virtuella datorn {0} pga ett internt fel.  |
|  OSProvisioningTimedOut  |  Etablering av operativsystem för den virtuella datorn {0}-slutfördes inte inom den tilldelade tiden. Den virtuella datorn kan fortfarande slutföra etableringen. Kontrollera etableringsstatusen senare.  |
|  OSProvisioningTimedOut  |  Etablering av operativsystem för den virtuella datorn {0}-slutfördes inte inom den tilldelade tiden. Den virtuella datorn kan fortfarande slutföra etableringen. Kontrollera etableringsstatusen senare. Kontrollera också att avbildningen har förberetts på rätt (generaliserats).   <ul><li>Instruktioner för Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruktioner för Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Etablering av operativsystem för den virtuella datorn {0}-slutfördes inte inom den tilldelade tiden. Virtuella datorns gästagent upptäcktes dock körs. Detta tyder på gästoperativsystemet inte har rätt förberedda som ska användas som en VM-avbildning (med CreateOption = FromImage). Lös problemet, använda den virtuella Hårddisken som är med CreateOption = bifoga eller korrekt förbereda den för användning som en bild:   <ul><li>Instruktioner för Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruktioner för Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Den efterfrågade virtuella datorstorleken är för tillfället inte tillgänglig på den valda platsen.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Resursen kan inte uppdateras just nu på grund av en pågående plattformsuppdatering. Försök igen senare.  |
|  StorageAccountLimitation  |  Lagringskontot {0} stöder inte sidblobbar som krävs för att skapa diskar.  |
|  StorageAccountLimitation  |  Lagringskontot {0} har överskridit sin tilldelade kvot.  |
|  StorageAccountLocationMismatch  |  Det gick inte att matcha lagringskontot {0}. Kontrollera att den skapades med Lagringsresursleverantören på samma plats som beräkningsresursen.  |
|  StorageAccountNotFound  |  Storage-kontot {0} hittades inte. Se till att lagringskontot inte har tagits bort och som tillhör samma Azure-plats som den virtuella datorn.  |
|  StorageAccountNotRecognized  |  Använd ett lagringskonto som hanteras av Lagringsresursprovidern. Användning av {0} stöds inte.  |
|  StorageAccountOperationInternalError  |  Ett internt fel uppstod vid åtkomst till lagringskontot {0}.  |
|  StorageAccountSubscriptionMismatch  |  Storage-kontot {0} tillhör inte prenumerationen {1}.  |
|  StorageAccountTooBusy  |  Storage-konto: {0} är för upptagen för närvarande. Överväg att använda ett annat konto.  |
|  StorageAccountTypeNotSupported  |  Disk {0} använder {1} som är ett Blob storage-konto. Försök igen med ett allmänt lagringskonto.  |
|  StorageAccountTypeNotSupported  |  Storage-kontot {0} är av typen {1}. Inga Startdiagnostikinställningar har stöd för {2} lagringskontotyper.  |
|  SubscriptionNotAuthorizedForImage  |  Prenumerationen saknar behörighet.  |
|  TargetDiskBlobAlreadyExists  |  Det finns redan en BLOB {0}. Ange en annan blob-URI för att skapa en ny tom data disk '{1}'.  |
|  TargetDiskBlobAlreadyExists  |  Avbilda åtgärden kan inte fortsätta eftersom mål avbildningen blob {0} redan finns och flaggan för att skriva över VHD-blobbar har inte angetts. Ta bort blobben eller Ställ in flaggan Skriv över VHD-blobbar och försök sedan igen.  |
|  TargetDiskBlobAlreadyExists  |  Avbildningen kan inte fortsätta eftersom målavbildningsblobben {0} har en aktiv livslängd.   |
|  TargetDiskBlobAlreadyExists  |  Det finns redan en BLOB {0}. Ange en annan blob-URI som mål för disken '{1}'.  |
|  TooManyVMRedeploymentRequests  |  För många omdistributionsförfrågningar har tagits emot för VM: {0} eller de virtuella datorerna i samma tillgänglighetsuppsättning med den här virtuella datorn. Försök igen senare.  |
|  VHDSizeInvalid  |  Den angivna diskstorleksvärdet {0} för disken {1} om du med blob {2} är ogiltig. Diskstorleken måste vara mellan {3} och {4}.  |
|  VMAgentStatusCommunicationError  |  VM: {0} har inte rapporterat status för VM-agenten eller tillägg. Kontrollera den virtuella datorn har en virtuell datoragent som körs och kan upprätta utgående anslutningar till Azure-lagring.  |
|  VMArtifactRepositoryInternalError  |  Det uppstod ett fel under kommunikationen med artefaktcentrallagret när det skulle hämtas VM-artefaktdata.  |
|  VMArtifactRepositoryInternalError  |  Ett internt fel inträffade när den virtuella datorns artefaktdata hämtades från artefaktlagret.  |
|  VMExtensionHandlerNonTransientError  |  Hanterare: {0} har rapporterat fel för VM-tillägget '{1}' med terminal felkoden '{2}' och felmeddelande: '{3}'  |
|  VMExtensionManagementInternalError  |  Ett internt fel inträffade vid bearbetningen av VM-tillägget {0}.  |
|  VMExtensionManagementInternalError  |  Flera fel uppstod under förberedelserna av VM-tillägg. VM-instansvyn mer information finns i.  |
|  VMExtensionProvisioningError  |  VM har rapporterat ett fel vid bearbetningen av tillägget: {0}. Felmeddelande: ”{1}”.  |
|  VMExtensionProvisioningError  |  Det gick inte att flera VM-tillägg som ska etableras på den virtuella datorn. VM-instansvyn mer information finns i.  |
|  VMExtensionProvisioningTimeout  |  Etablering av VM-tillägget '{0}' har gått ut. Installationen av tillägget tar kanske för lång eller tillståndets status kunde inte hämtas.  |
|  VMMarketplaceInvalidInput  |  Skapar en virtuell dator från en icke-Marketplace-avbildning behövs inte Planinformation, ta bort planinformationen i förfrågan. OS-disknamnet är {0}.  |
|  VMMarketplaceInvalidInput  |  Köpinformationen stämmer inte överens. Det går inte att distribuera från Marketplace-avbildning. OS-disknamnet är {0}.  |
|  VMMarketplaceInvalidInput  |  Skapa en virtuell dator från Marketplace-avbildning måste planinformation i förfrågan. OS-disknamnet är {0}.  |
|  VMNotFound  |  Den virtuella datorn {0}' kan inte hittas.  |
|  VMRedeploymentFailed  |  VM: {0} Omdistributionen misslyckades på grund av ett internt fel. Försök igen senare.  |
|  VMRedeploymentTimedOut  |  Omdistributionen av VM: {0} avslutades inte inom den tilldelade tiden. Den kan slutföras i senare. Annars kan du försöka begäran.  |
|  VMStartTimedOut  |  VM: {0} startade inte inom den tilldelade tiden. Den virtuella datorn kan fortfarande starta korrekt. Kontrollera energinivån senare.  |


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.