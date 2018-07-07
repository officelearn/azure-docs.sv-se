---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/25/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 00cb6cb59f8144782fdacd8c2f9763c2f7036880
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37906844"
---
>[!NOTE]
> Du kan lämna kommentarer på den här sidan för feedback eller via [feedback om Azure](https://feedback.azure.com/forums/216843-virtual-machines) med #azerrormessage tagg.

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

Ett felsvar omfattar alltid statuskoden och ett fel uppstod när objektet. Varje felobjekt innehåller alltid en felkod och ett meddelande. Om den virtuella datorn skapas med en mall, innehåller felobjektet också ett avsnitt för information som innehåller en inre nivå av felkoder och meddelanden. Normalt är mest inre andelen felmeddelande rot-fel.


## <a name="common-virtual-machine-management-errors"></a>Vanliga fel för hantering av virtuell dator

Det här avsnittet innehåller vanliga felmeddelanden som kan uppstå när du hanterar virtuella datorer:

|  Felkod  |  Felmeddelande  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Det gick inte att låna medan skapar disken ”{0}' med blobben med URI {1}. Blobben används redan.  |  
|  AllocationFailed  |  Tilldelningen misslyckades. Försök att minska den Virtuella datorstorleken eller antalet virtuella datorer, försök igen senare eller försök att distribuera till en annan Tillgänglighetsuppsättning eller olika Azure-plats.  |  
|  AllocationFailed  |  Tilldelningen av virtuella datorer misslyckades pga ett internt fel. Försök igen senare eller försök att distribuera till en annan plats.  |
|  ArtifactNotFound  |  Det Virtuella datortillägget med utgivaren '{0}”och typen”{1}'kunde inte hittas på platsen'{2}'.  |
|  ArtifactNotFound  |  Tillägg med utgivare ”{0}', typ”{1}”, och typhanteringsversion '{2}' hittades inte i tilläggscentrallagret.  |
|  ArtifactVersionNotFound  |  Ingen version hittades i artefaktcentrallagret som motsvarar den efterfrågade versionen ”{0}”.  |
|  ArtifactVersionNotFound  |  Ingen version hittades i artefaktcentrallagret som motsvarar den efterfrågade versionen ”{0}” för det Virtuella datortillägget med utgivaren'{1}”och typen”{2}”.  |
|  AttachDiskWhileBeingDetached  |  Det går inte att bifoga datadisk '{0}”till VM-{1}' eftersom disken håller på att frånkopplas. Vänta tills disken har frånkopplats helt och försök sedan igen.  |
|  BadRequest  |  Justerad ”Tillgänglighetsuppsättningar ännu inte stöds i den här regionen.  |
|  BadRequest  |  Lägga till en virtuell dator med hanterade diskar till icke-hanterad Tillgänglighetsuppsättning eller lägga till en virtuell dator med blobbaserade diskar till hanterad Tillgänglighetsuppsättning stöds inte. Skapa en Tillgänglighetsuppsättning med ”hanterad”-egenskapen som angetts för att lägga till en virtuell dator med hanterade diskar.  |
|  BadRequest  |  Hanterade diskar stöds inte i den här regionen.  |
|  BadRequest  |  Flera VMExtensions per hanterare som inte stöds för OS-typ '{0}'. VMExtension-{1}'med hanteraren'{2}' redan lagts till eller angetts i indata.  |
|  BadRequest  |  Åtgärden '{0}'stöds inte på resursen'{1}' med hanterade diskar.  |
|  CertificateImproperlyFormatted  |  Hemlighetens JSON-representation som hämtades från {0} har ett datafält som inte är en korrekt formaterad PFX-fil, eller det angivna lösenordet inte avkoda PFX-filen korrekt.  |
|  CertificateImproperlyFormatted  |  Data som hämtats från {0} går inte att deserialisera till JSON.  |
|  Konflikt  |  Det går bara att ändra storlek på en disk när en virtuell dator skapas eller när den virtuella datorn frigörs.  |
|  ConflictingUserInput  |  Disk ”{0}'kan inte kopplas eftersom disken redan ägs av den virtuella datorn'{1}'.  |
|  ConflictingUserInput  |  Käll- och målresursgrupperna är desamma.  |
|  ConflictingUserInput  |  Käll- och storage-konton för disken {0} är olika.  |
|  ContainerAlreadyOnLease  |  Det finns redan ett lån på den lagringsbehållare som innehåller blobben med URI {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Begäran om att flytta resurser innehåller KeyVault-resurser som refereras av en eller flera {0}s i begäran. Detta stöds inte för närvarande mellan prenumerationer flytten. Kontrollera felinformationen för KeyVault-resurs-ID.  |
|  DiagnosticsOperationInternalError  |  Ett internt fel uppstod under bearbetning av diagnostikprofilen för den virtuella datorn {0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  BLOB {0} används redan av en annan disk som hör till VM-{1}”. Du kan granska blob-metadata för referensinformation för disken.  |
|  DiskBlobNotFound  |  Det går inte att hitta VHD-blobben med URI {0} för disken '{1}'.  |
|  DiskBlobNotFound  |  Det går inte att hitta VHD-blobben med URI {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} hemligheten har inte den {1} taggar. Uppdatera den hemliga versionen, Lägg till taggarna som krävs och försök igen.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Packa upp av hemlighet {0} värde med hjälp av nyckeln {1} misslyckades.  |
|  DiskImageNotReady  |  Diskavbildning {0} i {1} tillstånd. Försök igen när avbildningen är klar.  |
|  DiskPreparationError  |  Ett eller flera fel inträffade under förberedelserna av VM-diskar. Se disk Instansvy för mer information.  |
|  DiskProcessingError  |  Diskbearbetningen har stoppats eftersom den virtuella datorn har andra diskar i misslyckade diskar.  |
|  ImageBlobNotFound  |  Det går inte att hitta VHD-blobben med URI {0} för disken '{1}'.  |
|  ImageBlobNotFound  |  Det går inte att hitta VHD-blobben med URI {0}.  |
|  IncorrectDiskBlobType  |  Diskblobar får bara vara av typen sidblob. BLOB {0} för disken '{1}' är av typen blockblob.  |
|  IncorrectDiskBlobType  |  Diskblobar får bara vara av typen sidblob. BLOB {0} är av typen '{1}'.  |
|  IncorrectImageBlobType  |  Diskblobar får bara vara av typen sidblob. BLOB {0} för disken '{1}' är av typen blockblob.  |
|  IncorrectImageBlobType  |  Diskblobar får bara vara av typen sidblob. BLOB {0} är av typen '{1}'.  |
|  InternalOperationError  |  Det gick inte att lösa lagringskontot {0}. Kontrollera att det skapades med Lagringsresursprovidern på samma plats som beräkningsresursen.  |
|  InternalOperationError  |  {0} målsökningsuppgifter misslyckades.  |
|  InternalOperationError  |  Fel uppstod vid verifieringen av nätverksprofilen för virtuell dator ”{0}”.  |
|  InvalidAccountType  |  AccountType {0} är ogiltig.  |
|  InvalidParameter  |  Värdet för parametern {0} är ogiltig.  |
|  InvalidParameter  |  Det specificerade administratörslösenordet är inte tillåtet.  |
|  InvalidParameter  |  ”Det angivna lösenordet måste vara mellan {0}-{1} tecken och uppfylla minst {2} av kraven på lösenordskomplexitet från följande: <ol><li> Innehåller en versal</li><li>Innehåller en gemen bokstav</li><li>Innehåller en numerisk siffra</li><li>Innehåller ett specialtecken.</li></ol>  |
|  InvalidParameter  |  Det specificerade administratörsanvändarnamnet är inte tillåtet.  |
|  InvalidParameter  |  Det går inte att bifoga en befintlig OS-disk om den virtuella datorn skapas från en plattform eller användaravbildning.  |
|  InvalidParameter  |  Behållarnamn {0} är ogiltig. Behållarnamn måste vara mellan 3-63 tecken långt och får bara innehålla gemena alfanumeriska tecken och bindestreck. Bindestreck måste föregås och följas av alfanumeriska tecken.  |
|  InvalidParameter  |  Behållarnamn {0} i URL: en {1} är ogiltig. Behållarnamn måste vara mellan 3-63 tecken långt och får bara innehålla gemena alfanumeriska tecken och bindestreck. Bindestreck måste föregås och följas av alfanumeriska tecken.  |
|  InvalidParameter  |  Blobnamnet i URL: en {0} innehåller ett snedstreck. Det finns för närvarande inte stöd för diskar.  |
|  InvalidParameter  |  URI: N {0} ser inte ut för att vara korrekt blob-URI.  |
|  InvalidParameter  |  En disk med namnet ”{0}' redan i LUN: {1}.  |
|  InvalidParameter  |  En disk med namnet '{0}' finns redan.  |
|  InvalidParameter  |  Det går inte att ange användarens avbildningsåsidosättanden för en disk som redan har definierats i den angivna avbildningsreferensen.  |
|  InvalidParameter  |  En disk med namnet ”{0}' redan använder samma VHD URL {1}.  |
|  InvalidParameter  |  Den angivna feldomänsantal {0} måste ligga i intervallet {1} till {2}.  |
|  InvalidParameter  |  Licenstypen {0} är ogiltig. Giltiga licenstyper är: Windows_Client eller Windows_Server, skiftlägeskänsligt.  |
|  InvalidParameter  |  Linux-värdnamnet får inte överskrida {0} tecken långt eller innehålla följande tecken: {1}.  |
|  InvalidParameter  |  Målsökvägen för offentliga Ssh-nycklar är för närvarande begränsat till dess standardvärde {0} på grund av ett känt problem i Linux-etableringsagenten.  |
|  InvalidParameter  |  En disk på LUN {0} finns redan.  |
|  InvalidParameter  |  Prenumeration {0} för begäran måste överensstämma med prenumerationen {1} i hanterade diskens id.  |
|  InvalidParameter  |  Anpassade data i OSProfile måste vara Base64-kodade och med en maximal längd på {0} tecken.  |
|  InvalidParameter  |  Blobnamnet i URL: en {0} måste sluta med ”{1}-tillägget.  |
|  InvalidParameter  |  {0}' är inte en giltig hämtade VHD prefix för blobbnamn. Ett giltigt prefix matchar regex '{1}'.  |
|  InvalidParameter  |  Certifikat kan inte läggas till den virtuella datorn om den Virtuella datoragenten inte har etablerats.  |
|  InvalidParameter  |  En disk på LUN {0} finns redan.  |
|  InvalidParameter  |  Det går inte att skapa den virtuella datorn eftersom den begärda storleken {0} är inte tillgänglig i det kluster där tillgänglighetsuppsättningen för närvarande allokeras. Tillgängliga storlekar är: {1}. Läs mer om virtuella datorer ändra storlek på https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  Den begärda storleken {0} är inte tillgänglig i den aktuella regionen. Storlekarna som är tillgängliga i den aktuella regionen är: {1}. Ta reda på mer om de tillgängliga storlekarna i varje region på https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Den begärda storleken {0} är inte tillgänglig i den aktuella regionen. Ta reda på mer om de tillgängliga storlekarna i varje region på https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Windows-användarnamn för administratör får inte vara mer än {0} tecken långt, sluta med en period(.) eller innehålla följande tecken: {1}.  |
|  InvalidParameter  |  Windows-datornamnet får inte vara mer än {0} tecken långt, vara helt numeriskt eller innehålla följande tecken: {1}.  |
|  MissingMoveDependentResources  |  Begäran om att flytta resurser innehåller inte alla beroende resurser. Kontrollera saknade resurs-ID: n.  |
|  MoveResourcesHaveInvalidState  |  Begäran om att flytta resurser innehåller virtuella datorer som är kopplade till ogiltiga lagringskonton. Läs informationen om dessa resurs-ID: n och refererade lagringskontonamn.  |
|  MoveResourcesHavePendingOperations  |  Begäran om att flytta resurser innehåller resurser som en åtgärd väntar. Läs informationen om dessa resurs-ID: n. Försök igen när de väntande åtgärderna har slutförts.  |
|  MoveResourcesNotFound  |  Begäran om att flytta resurser innehåller resurser som inte kan hittas. Läs informationen om dessa resurs-ID: n.  |
|  NetworkingInternalOperationError  |  Okänt nätverkstilldelningsfel.  |
|  NetworkingInternalOperationError  |  Okänt nätverkstilldelningsfel  |
|  NetworkingInternalOperationError  |  Det uppstod ett internt fel vid bearbetningen av den virtuella datorns nätverksprofil.  |
|  NotFound  |  Tillgänglighetsuppsättningen {0} går inte att hitta.  |
|  NotFound  |  Käll-VM ”{0}” anges i begäran finns inte i den här Azure-plats.  |
|  NotFound  |  Innehavaren med id {0} hittades inte.  |
|  NotFound  |  Avbildningen {0} går inte att hitta.  |
|  NotSupported  |  Licenstypen är {0}, men avbildningsbloben {1} är inte från en lokal plats.  |
|  OperationNotAllowed  |  Tillgänglighetsuppsättning {0} kan inte tas bort. Innan du tar bort en Tillgänglighetsuppsättning se till att den inte innehåller alla virtuella datorer.  |
|  OperationNotAllowed  |  Ändra tillgänglighetsuppsättning är SKU från ”justerad” till ”klassisk” inte tillåtet.  |
|  OperationNotAllowed  |  Det går inte att ändra tillägg i den virtuella datorn när den inte körs.  |
|  OperationNotAllowed  |  Åtgärden för att avbilda stöds bara på en virtuell dator med blobbaserade diskar. Använd ”Image” resursen API: er för att skapa en avbildning från en hanterad virtuell dator.  |
|  OperationNotAllowed  |  Resursen {0} går inte att skapa från avbildning {1} tills avbildningen har skapats.  |
|  OperationNotAllowed  |  Uppdateringar av encryptionSettings tillåts inte när VM har allokerats, försök igen när virtuella dator har frigjorts  |
|  OperationNotAllowed  |  Det finns inte stöd för att lägga till en hanterad disk i en virtuell dator med blobbaserade diskar.  |
|  OperationNotAllowed  |  Det maximala antalet datadiskar som kopplas till en virtuell dator av den här storleken är {0}.  |
|  OperationNotAllowed  |  Det finns inte stöd för att lägga till en blobbaserad disk i en virtuell dator med hanterade diskar.  |
|  OperationNotAllowed  |  Åtgärden '{0}'är inte tillåten på bild'{1}' eftersom avbildningen har markerats för borttagning. Du kan bara försöka göra om borttagningen (eller vänta tills den pågående att slutföra).  |
|  OperationNotAllowed  |  Åtgärden '{0}'tillåts inte på den virtuella datorn'{1}' eftersom den virtuella datorn är generaliserad.  |
|  OperationNotAllowed  |  Åtgärden '{0}'tillåts inte eftersom återställningspunktsamlingen'{1}' har markerats för borttagning.  |
|  OperationNotAllowed  |  Åtgärden '{0}'tillåts inte på VM-tillägget'{1}' eftersom den har markerats för borttagning. Du kan bara försöka göra om borttagningen (eller vänta tills den pågående att slutföra).  |
|  OperationNotAllowed  |  Åtgärden '{0}'tillåts inte eftersom de virtuella datorerna'{1}'har etablerats med avbildningen ”{2}”.  |
|  OperationNotAllowed  |  Åtgärden '{0}'tillåts inte eftersom den virtuella datorns ScaleSet'{1}'för närvarande använder avbildningen ”{2}”.  |
|  OperationNotAllowed  |  Åtgärden '{0}'tillåts inte på den virtuella datorn'{1}' eftersom den virtuella datorn har markerats för borttagning. Du kan bara försöka göra om borttagningen (eller vänta tills den pågående att slutföra).  |
|  OperationNotAllowed  |  Åtgärden '{0}'tillåts inte på den virtuella datorn'{1}' eftersom den virtuella datorn antingen har frigjorts eller markerats för att frigöras.  |
|  OperationNotAllowed  |  Åtgärden '{0}'tillåts inte på den virtuella datorn'{1}' eftersom Virtuellt datorn körs. Du måste ange power ut om du stänger av den virtuella datorn från inuti gästoperativsystemet.  |
|  OperationNotAllowed  |  Åtgärden '{0}'tillåts inte på den virtuella datorn'{1}' eftersom den virtuella datorn inte har frigjorts.  |
|  OperationNotAllowed  |  Åtgärden '{0}'tillåts inte på den virtuella datorn'{1}'Eftersom den Virtuella datorns tillägg ”{2}” i ett felaktigt tillstånd.  |
|  OperationNotAllowed  |  Åtgärden '{0}'tillåts inte på den virtuella datorn'{1}' eftersom en annan åtgärd pågår.  |
|  OperationNotAllowed  |  Åtgärden '{0}”måste den virtuella datorn'{1}' generaliserats.  |
|  OperationNotAllowed  |  Den virtuella datorn måste köras (eller vara inställd på körning) för att åtgärden ska kunna utföras.  |
|  OperationNotAllowed  |  Disken med storleken {0}GB, vilket är mindre än storleken {1}GB för motsvarande disk i avbildningen, tillåts inte.  |
|  OperationNotAllowed  |  VM Scale Set-tillägg för hanteraren '{0}' kan bara läggas till VM Scale Set skapas.  |
|  OperationNotAllowed  |  VM Scale Set-tillägg för hanteraren '{0}' kan tas bort bara vid tidpunkten för VM Scale Set tas bort.  |
|  OperationNotAllowed  |  VM-{0}' redan använder hanterade diskar.  |
|  OperationNotAllowed  |  VM-{0}'tillhör tillgänglighetsuppsättningen ”klassisk”'{1}'. Uppdatera tillgänglighetsuppsättningen för att använda SKU ”justerad” och försök sedan utföra konverteringen.  |
|  OperationNotAllowed  |  Virtuell dator som skapats från avbildningen får inte ha blobbaserade diskar. Alla diskar måste vara hanterade diskar.  |
|  OperationNotAllowed  |  Avbildningsåtgärden kan inte slutföras eftersom den virtuella datorn inte har generaliserats.  |
|  OperationNotAllowed  |  Hanteringsåtgärder på den virtuella datorn '{0}' tillåts inte eftersom VM-diskarna konverteras till hanterade diskar.  |
|  OperationNotAllowed  |  En pågående åtgärd ändrar energinivån för den virtuella datorn {0} till {1}. Utför åtgärden {2} efter en stund.  |
|  OperationNotAllowed  |  Det går inte att lägga till eller uppdatera den virtuella datorn. Den begärda storleken {0} kanske inte tillgänglig i den befintliga allokeringsenheten. Läs mer om virtuella datorer ändra storlek på https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Det går inte att ändra storlek på den virtuella datorn eftersom den begärda storleken {0} är inte tillgänglig i det kluster där tillgänglighetsuppsättningen för närvarande allokeras. Tillgängliga storlekar är: {1}. Läs mer om virtuella datorer ändra storlek på https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Det går inte att ändra storlek på den virtuella datorn eftersom den begärda storleken {0} är inte tillgänglig i det kluster där den virtuella datorn för närvarande allokeras. Ändra storlek på den virtuella datorn ska {1} Frigör (detta är Stop-åtgärd i Azure portal) och försök sedan ändra storlek på igen. Läs mer om virtuella datorer ändra storlek på https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Operativsystemsetableringen misslyckades för den virtuella datorn '{0}' eftersom gästoperativsystemet håller på att etableras.  |
|  OSProvisioningClientError  |  För den virtuella datorn '{0}' misslyckades. Felinformation: {1} Kontrollera att avbildningen har förberetts ordentligt (generaliserats). <ul><li>Instruktioner för Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Det gick inte att skapa SSH-värd-nyckel. Felinformation: {0}. För att lösa det här problemet kontrollerar du om Linux-agenten är korrekt konfigurerad. <ul><li>Du kan läsa anvisningarna på: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  Användarnamnet som angetts för den virtuella datorn är ogiltigt för den här Linux-distribution. Felinformation: {0}.  |
|  OSProvisioningInternalError  |  Operativsystemsetableringen misslyckades för den virtuella datorn '{0}' på grund av ett internt fel.  |
|  Osprovisioningtimedout har uppstått  |  För den virtuella datorn '{0}' slutfördes inte inom avsedd tid. Den virtuella datorn kan fortfarande slutföras. Kontrollera installationstillståndet senare.  |
|  Osprovisioningtimedout har uppstått  |  För den virtuella datorn '{0}' slutfördes inte inom avsedd tid. Den virtuella datorn kan fortfarande slutföras. Kontrollera installationstillståndet senare. Kontrollera också att avbildningen har förberetts ordentligt (generaliserats).   <ul><li>Instruktioner för Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Anvisningar för Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  Osprovisioningtimedout har uppstått  |  För den virtuella datorn '{0}' slutfördes inte inom avsedd tid. Men upptäcktes virtuella datorns gästagent, som körs. Det tyder på gästoperativsystemet inte har korrekt förberedd som ska användas som en VM-avbildning (med CreateOption = FromImage). För att lösa problemet, använda den virtuella Hårddisken skick med CreateOption = Attach eller förbereda den korrekt för användning som en bild:   <ul><li>Instruktioner för Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Anvisningar för Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Den efterfrågade virtuella datorstorleken är för tillfället inte tillgänglig på den valda platsen.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Resursen kan inte uppdateras på grund av en pågående plattformsuppdatering. Försök igen senare.  |
|  StorageAccountLimitation  |  Storage-konto '{0}' stöder inte sidblobbar som krävs för att skapa diskar.  |
|  StorageAccountLimitation  |  Storage-konto '{0}' har överskridit sin tilldelade kvot.  |
|  StorageAccountLocationMismatch  |  Det gick inte att lösa lagringskontot {0}. Kontrollera att det skapades med Lagringsresursprovidern på samma plats som beräkningsresursen.  |
|  StorageAccountNotFound  |  Storage-konto {0} hittades inte. Kontrollera att lagringskontot inte tas bort och tillhör samma Azure-plats som den virtuella datorn.  |
|  StorageAccountNotRecognized  |  Använd ett lagringskonto som hanteras av Storage Resource Provider. Användning av {0} stöds inte.  |
|  StorageAccountOperationInternalError  |  Internt fel inträffade vid åtkomst till lagringskontot {0}.  |
|  StorageAccountSubscriptionMismatch  |  Storage-konto {0} tillhör inte prenumerationen {1}.  |
|  StorageAccountTooBusy  |  Storage-konto '{0}' är för upptagen för närvarande. Överväg att använda ett annat konto.  |
|  StorageAccountTypeNotSupported  |  Disk {0} använder {1} vilket är ett Blob storage-konto. Försök igen med allmänt lagringskonto.  |
|  StorageAccountTypeNotSupported  |  Storage-konto {0} är av {1} typen. Starta diagnostik stöder {2} storage-kontotyper.  <ul><li>Det här felet uppstår om du använder premium-lagringskonto för startdiagnostik. Mer information finns i [hur du använder startdiagnostik](../articles/virtual-machines/windows/boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Prenumerationen saknar behörighet.  |
|  TargetDiskBlobAlreadyExists  |  BLOB {0} finns redan. Ange en annan blob-URI för att skapa en ny tom datadisk '{1}'.  |
|  TargetDiskBlobAlreadyExists  |  Avbilda kan inte fortsätta eftersom målet bild blob {0} finns redan och flagga för att skriva över VHD-blobbar har inte angetts. Ta bort blobben eller Ställ in flaggan att skriva över VHD-blobbar och försök igen.  |
|  TargetDiskBlobAlreadyExists  |  Avbilda kan inte fortsätta eftersom målet bild blob {0} har ett aktivt lån på den.   |
|  TargetDiskBlobAlreadyExists  |  BLOB {0} finns redan. Ange en annan blob-URI som mål för disken '{1}'.  |
|  TooManyVMRedeploymentRequests  |  För många omdistributionsförfrågningar har tagits emot för den virtuella datorn '{0}' eller de virtuella datorerna i samma tillgänglighetsuppsättning med den här virtuella datorn. Försök igen senare.  |
|  VHDSizeInvalid  |  Den angivna diskstorleksvärdet {0} för disken ”{1}' med blob {2} är ogiltig. Diskstorleken måste vara mellan {3} och {4}.  |
|  VMAgentStatusCommunicationError  |  VM-{0}' har inte rapporterat status för VM-agenten och tillägg. Kontrollera den virtuella datorn har en virtuell datoragent som körs och kan upprätta utgående anslutningar till Azure storage.  |
|  VMArtifactRepositoryInternalError  |  Det uppstod ett fel under kommunikationen med artefaktcentrallagret när det skulle hämtas VM-artefaktdata.  |
|  VMArtifactRepositoryInternalError  |  Ett internt fel inträffade när den virtuella datorns artefaktdata hämtades från artefaktlagret.  |
|  VMExtensionHandlerNonTransientError  |  Hanteraren '{0}'har rapporterat fel för VM-tillägg ”{1}'med terminalfelkoden'{2}' och felmeddelande”:{3}”  |
|  VMExtensionManagementInternalError  |  Internt fel inträffade vid bearbetning av VM-tillägg ”{0}”.  |
|  VMExtensionManagementInternalError  |  Flera fel uppstod under förberedelserna av VM-tillägg. Se VM-instansvyn för information.  |
|  VMExtensionProvisioningError  |  Den virtuella datorn rapporterade ett fel vid bearbetningen av tillägget '{0}'. Felmeddelande ”:{1}”.  |
|  VMExtensionProvisioningError  |  Flera VM-tillägg kunde inte etableras på den virtuella datorn. VM-instansvyn mer information finns på.  |
|  VMExtensionProvisioningTimeout  |  Etablering av VM-tillägget '{0}' har nått sin tidsgräns. Installera tillägget tar kanske för lång eller Tilläggsstatus kunde inte hämtas.  |
|  VMMarketplaceInvalidInput  |  Skapa en virtuell dator från en icke Marketplace-avbildning behöver inte Planinformation, ta bort planinformationen i förfrågan. OS-disknamnet är {0}.  |
|  VMMarketplaceInvalidInput  |  Köpinformationen stämmer inte överens. Det går inte att distribuera från Marketplace-avbildning. OS-disknamnet är {0}.  |
|  VMMarketplaceInvalidInput  |  Skapa en virtuell dator från Marketplace-avbildning kräver planinformation i förfrågan. OS-disknamnet är {0}.  |
|  VMNotFound  |  Den virtuella datorn '{0}' kan inte hittas.  |
|  VMRedeploymentFailed  |  VM-{0}' omdistribution misslyckades på grund av ett internt fel. Försök igen senare.  |
|  VMRedeploymentTimedOut  |  Omdistribution av den virtuella datorn '{0}' avslutades inte inom den tilldelade tiden. Den kan slutföras utan problem i en stund. Annars kan kan du försöka begäran.  |
|  VMStartTimedOut  |  VM-{0}' startade inte inom avsedd tid. Den virtuella datorn kan fortfarande startas. Kontrollera energinivån senare.  |


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du kontakta Azure-experter på [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.