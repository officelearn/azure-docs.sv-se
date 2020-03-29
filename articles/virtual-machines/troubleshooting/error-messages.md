---
title: Vanliga VM-felkoder i Azure | Microsoft-dokument
description: Förstå några av de vanliga felkoder som påträffades när du etablerar och hanterar virtuella datorer i Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543026"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Förstå vanliga felmeddelanden när du hanterar virtuella datorer i Azure

I den här artikeln beskrivs några av de vanligaste felkoderna och meddelanden som du kan stöta på när du skapar eller hanterar virtuella datorer i Azure.

>[!NOTE]
> Du kan lämna kommentarer på den här sidan för feedback eller via [Azure feedback](https://feedback.azure.com/forums/216843-virtual-machines) med #azerrormessage tagg.

## <a name="error-response-format"></a>Format för felsvar 
Virtuella Azure-datorer använder följande JSON-format för felsvar:

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

Ett felsvar innehåller alltid en statuskod och ett felobjekt. Varje felobjekt innehåller alltid en felkod och ett meddelande. Om den virtuella datorn skapas med en mall innehåller felobjektet också ett informationsavsnitt som innehåller en inre nivå av felkoder och meddelanden. Normalt är den mest inre nivån av felmeddelande rotfel.


## <a name="common-virtual-machine-management-errors"></a>Vanliga fel för hantering av virtuella datorer

I det här avsnittet visas de vanliga felmeddelanden som kan uppstå när du hanterar virtuella datorer:

|  Felkod  |  Felmeddelande  |  
|  :------| :-------------|  
|  Skaffa DiskLeaseFailed  |  Det gick inte att{0}hämta lånet när disken ' ' skapades med blob med URI {1}. Blob används redan.  |  
|  AllokeringMissen  |  Allokeringen misslyckades. Försök att minska den virtuella datorns storlek eller antal virtuella datorer, försök igen senare eller prova att distribuera till en annan tillgänglighetsuppsättning eller annan Azure-plats.  |  
|  AllokeringMissen  |  Den virtuella datornallokeringen misslyckades på grund av ett internt fel. Försök igen senare eller försök distribuera till en annan plats.  |
|  ArtefaktBlfound  |  Det gick inte{0}att hitta{1}VM-tillägget med utgivaren{2}' ' och typen ' ' ' ' .  |
|  ArtefaktBlfound  |  Det gick{0}inte att{1}hitta tillägget med utgivaren{2}' ', type ' ' och typhanterarversion ' ' ' i tilläggsarkivet.  |
|  ArtifactVersionIntens  |  Ingen version hittades i artefaktdatabasen som{0}uppfyller den begärda versionen ' '.  |
|  ArtifactVersionIntens  |  Ingen version hittades i artefaktdatabasen som{0}uppfyller den begärda{1}versionen '{2}' för VM-tillägget med utgivaren ' ' och skriv ' '.  |
|  BifogaDiskWhileBeingDetached  |  Det går inte{0}att koppla{1}datadisken till VM ' eftersom disken för närvarande tas bort. Vänta tills disken är helt avkopplad och försök sedan igen.  |
|  BadRequest  |  Justeringstillgänglighetsuppsättningar stöds ännu inte i den här regionen.  |
|  BadRequest  |  Tillägg av en virtuell dator med hanterade diskar i icke-hanterad tillgänglighetsuppsättning eller tillägg av en virtuell dator med blob-baserade diskar till hanterad tillgänglighetsuppsättning stöds inte. Skapa en tillgänglighetsuppsättning med egenskapen "managed" för att lägga till en virtuell dator med hanterade diskar i den.  |
|  BadRequest  |  Hanterade diskar stöds inte i den här regionen.  |
|  BadRequest  |  Flera VMExtensions per hanterare stöds{0}inte för OS-typen '. VMExtension{1}' ' med{2}hanterare ' ' redan tillagt eller angivet i indata.  |
|  BadRequest  |  Åtgärden{0}' ' stöds inte{1}på Resurs ' ' med hanterade diskar.  |
|  CertifikatÄrkorformformaterad  |  Hemlighetens JSON-representation som {0} hämtats från har ett datafält som inte är en korrekt formaterad PFX-fil, eller så avkodar inte lösenordet PFX-filen korrekt.  |
|  CertifikatÄrkorformformaterad  |  Data som hämtas från {0} kan inte deserializable till JSON.  |
|  Konflikt  |  Diskstorlek är endast tillåten när du skapar en virtuell dator eller när den virtuella datorn är frigörd.  |
|  MotstridigaAnvändarinput  |  Disk{0}' ' kan inte anslutas eftersom disken redan ägs av VM '{1}'.  |
|  MotstridigaAnvändarinput  |  Resursgrupper för källor och mål är desamma.  |
|  MotstridigaAnvändarinput  |  Käll- och mållagringskonton för disk {0} är olika.  |
|  BehållareAlreadyOnLease  |  Det finns redan ett lån på lagringsbehållaren {0}som innehåller bloben med URI .  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Begäran flytta resurser innehåller KeyVault-resurser som refereras {0}av en eller flera s i begäran. Detta stöds för närvarande inte i Cross-prenumeration flytta. Kontrollera felinformationen för KeyVault-resurs-ID:n.  |
|  DiagnostikFunktionInternalError  |  Ett internt fel uppstod vid {0}bearbetning av diagnostikprofil för vm .  |
|  DiskBlobAlreadyInUseByAntherDisk  |  Blob {0} används redan av en annan{1}disk som tillhör VM ' '. Du kan granska blobmetadata för diskreferensinformationen.  |
|  DiskBlobNotFound  |  Det gick inte att hitta VHD-blob med URI {0} för disk '.{1}  |
|  DiskBlobNotFound  |  Det gick inte att hitta VHD-blob med URI {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0}hemligheten inte har {1} taggarna. Uppdatera den hemliga versionen, lägg till nödvändiga taggar och försök igen.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Det gick inte {0} att {1} packa upp det hemliga värdet med hjälp av nyckeln.  |
|  DiskImageNotReady  |  {0} Diskavbildningen {1} är i läge. Försök igen när bilden är klar.  |
|  DiskPreparationError  |  Ett eller flera fel uppstod vid beredning av VM-diskar. Mer information finns i vyn diskinstans.  |
|  DiskProcessingError  |  Diskbearbetning stoppas eftersom den virtuella datorn har andra diskar i misslyckade diskar.  |
|  ImageBlobNotFound  |  Det gick inte att hitta VHD-blob med URI {0} för disk '.{1}  |
|  ImageBlobNotFound  |  Det gick inte att hitta VHD-blob med URI {0}.  |
|  FelaktigdiskBlobTyp  |  Diskblobar kan bara vara av typen sidblob. Blob {0} för{1}disk ' ' är av typen blockblob.  |
|  FelaktigdiskBlobTyp  |  Diskblobar kan bara vara av typen sidblob. Blob {0} är av{1}typen ' '.  |
|  IncorrectImageBlobType  |  Diskblobar kan bara vara av typen sidblob. Blob {0} för{1}disk ' ' är av typen blockblob.  |
|  IncorrectImageBlobType  |  Diskblobar kan bara vara av typen sidblob. Blob {0} är av{1}typen ' '.  |
|  InternoperationError  |  Det gick inte {0}att lösa lagringskontot . Kontrollera att den skapades via Storage Resource Provider på samma plats som beräkningsresursen.  |
|  InternoperationError  |  {0}målsökande uppgifter misslyckades.  |
|  InternoperationError  |  Det uppstod ett fel när{0}nätverksprofilen för den virtuella datorn valideras' '.  |
|  Ogiltigredovisningstyp  |  AccountType {0} är ogiltig.  |
|  Ogiltigparameter  |  Parametervärdet {0} är ogiltigt.  |
|  Ogiltigparameter  |  Det angivna administratörslösenordet är inte tillåtet.  |
|  Ogiltigparameter  |  "Det angivna lösenordet måste {0}{1} vara mellan - tecken {2} lång och måste uppfylla minst lösenord komplexitet krav från följande: <ol><li> Innehåller ett versaler</li><li>Innehåller ett gemener</li><li>Innehåller en numerisk siffra</li><li>Innehåller ett specialtecken.</li></ol>  |
|  Ogiltigparameter  |  Det angivna administratörsanvändarnamnet är inte tillåtet.  |
|  Ogiltigparameter  |  Det går inte att ansluta en befintlig OS-disk om den virtuella datorn skapas från en plattform eller en användaravbildning.  |
|  Ogiltigparameter  |  Behållarnamnet {0} är ogiltigt. Behållarnamnen måste vara 3-63 tecken långa och får endast innehålla gemener alfanumeriska tecken och bindestreck. Bindestreck måste föregås och följas av ett alfanumeriskt tecken.  |
|  Ogiltigparameter  |  Behållarnamnet {0} {1} i URL är ogiltigt. Behållarnamnen måste vara 3-63 tecken långa och får endast innehålla gemener alfanumeriska tecken och bindestreck. Bindestreck måste föregås och följas av ett alfanumeriskt tecken.  |
|  Ogiltigparameter  |  Blob-namnet i {0} URL:en innehåller ett snedstreck. Detta stöds för närvarande inte för diskar.  |
|  Ogiltigparameter  |  URI {0} ser inte ut att vara korrekt blob URI.  |
|  Ogiltigparameter  |  En disk{0}med namnet ' ' {1}använder redan samma LUN: .  |
|  Ogiltigparameter  |  Det finns{0}redan en disk med namnet ' ' .  |
|  Ogiltigparameter  |  Det går inte att ange åsidosättningar av användaravbildningar för en disk som redan har definierats i den angivna avbildningsreferensen.  |
|  Ogiltigparameter  |  En disk{0}med namnet ' ' {1}använder redan samma VHD-URL .  |
|  Ogiltigparameter  |  Det angivna antalet {0} feldomäner {1} måste {2}ligga i intervallet till .  |
|  Ogiltigparameter  |  Licenstypen {0} är ogiltig. Giltiga licenstyper är: Windows_Client eller Windows_Server, skiftlägeskänsliga.  |
|  Ogiltigparameter  |  Linux-värdnamn {0} får inte överskrida tecken {1}i längd eller innehålla följande tecken: .  |
|  Ogiltigparameter  |  Målsökvägen för Ssh-offentliga nycklar är {0} för närvarande begränsad till standardvärdet på grund av ett känt problem i Linux-etableringsagenten.  |
|  Ogiltigparameter  |  Det finns redan {0} en disk på LUN.  |
|  Ogiltigparameter  |  Prenumerationen {0} på begäran måste {1} matcha prenumerationen i det hanterade disk-ID:et.  |
|  Ogiltigparameter  |  Anpassade data i OSProfile måste finnas i Base64-kodning {0} och med en maximal teckenlängd.  |
|  Ogiltigparameter  |  Blob-namnet {0} i URL{1}måste sluta med ' ' extension.  |
|  Ogiltigparameter  |  {0}' är inte ett giltigt infångat VHD-blobnamnprefix. Ett giltigt prefix matchar{1}regex ' '.  |
|  Ogiltigparameter  |  Certifikat kan inte läggas till i den virtuella datorn om VM-agenten inte har etablerats.  |
|  Ogiltigparameter  |  Det finns redan {0} en disk på LUN.  |
|  Ogiltigparameter  |  Det gick inte att skapa {0} den virtuella datorn eftersom den begärda storleken inte är tillgänglig i klustret där tillgänglighetsuppsättningen för närvarande allokeras. De tillgängliga storlekarna {1}är: . Läs mer om VM storleksändring strategi på https://aka.ms/azure-resizevm.  |
|  Ogiltigparameter  |  Den begärda {0} vm-storleken är inte tillgänglig i den aktuella regionen. De storlekar som är tillgängliga {1}i den aktuella regionen är: . Läs mer om tillgängliga vm-storlekar i https://aka.ms/azure-regionsvarje region på .  |
|  Ogiltigparameter  |  Den begärda {0} vm-storleken är inte tillgänglig i den aktuella regionen. Läs mer om tillgängliga vm-storlekar i https://aka.ms/azure-regionsvarje region på .  |
|  Ogiltigparameter  |  Windows-administratörsanvändarnamn kan {0} inte vara längre än tecken, sluta med {1}en punkt(.) eller innehålla följande tecken: .  |
|  Ogiltigparameter  |  Windows-datornamn får {0} inte vara längre än tecken, vara helt {1}numeriskt eller innehålla följande tecken: .  |
|  MissingMoveDependentResources  |  Begäran om flyttresurser innehåller inte alla beroende resurser. Kontrollera felinformation för saknade resurs-ID: n.  |
|  FlyttaResurserHaveInvalidState  |  Begäran Flytta resurser innehåller virtuella datorer som är associerade med ogiltiga lagringskonton. Kontrollera information om dessa resurs-ID:er och refererade lagringskontonamn.  |
|  MoveResourcesHavePendingOperations  |  Begäran om flyttresurser innehåller resurser som en åtgärd väntar på. Kontrollera information om dessa resurs-ID:er. Försök igen när de väntande åtgärderna har slutförts.  |
|  MoveResourcesNotFound Flytta  |  Begäran om flyttresurser innehåller resurser som inte kan hittas. Kontrollera information om dessa resurs-ID:er.  |
|  NätverkInternalOperationError  |  Okänt nätverksallokeringsfel.  |
|  NätverkInternalOperationError  |  Okänt nätverksallokeringsfel  |
|  NätverkInternalOperationError  |  Ett internt fel uppstod vid bearbetning av nätverksprofil för den virtuella datorn.  |
|  NotFound  |  Det går {0} inte att hitta tillgänglighetsuppsättningen.  |
|  NotFound  |  Source Virtual{0}Machine ' ' som anges i begäran finns inte på den här Azure-platsen.  |
|  NotFound  |  Klienten med {0} id hittades inte.  |
|  NotFound  |  Det {0} går inte att hitta bilden.  |
|  Stöds inte  |  Licenstypen {0}är , men {1} bildbloben kommer inte från lokalt.  |
|  Åtgärden InteTilllämd  |  Tillgänglighetsuppsättningen {0} kan inte tas bort. Innan du tar bort en tillgänglighetsuppsättning ska du se till att den inte innehåller någon virtuell dator.  |
|  Åtgärden InteTilllämd  |  Det är inte tillåtet att ändra tillgänglighetsuppsättningen SKU från "Justerad" till "Klassisk".  |
|  Åtgärden InteTilllämd  |  Det går inte att ändra tillägg i den virtuella datorn när den virtuella datorn inte körs.  |
|  Åtgärden InteTilllämd  |  Infångningsåtgärden stöds bara på en virtuell dator med blob-baserade diskar. Använd resurs-API:erna "Image" för att skapa en avbildning från en hanterad virtuell dator.  |
|  Åtgärden InteTilllämd  |  Resursen {0} kan inte {1} skapas från Image förrän image har skapats.  |
|  Åtgärden InteTilllämd  |  Uppdateringar av krypteringInställningar är inte tillåtet när vm allokeras, Försök igen efter att den virtuella datorn har tilldelats  |
|  Åtgärden InteTilllämd  |  Tillägg av en hanterad disk till en virtuell dator med blob-baserade diskar stöds inte.  |
|  Åtgärden InteTilllämd  |  Det maximala antalet datadiskar som får kopplas till {0}en virtuell dator av den här storleken är .  |
|  Åtgärden InteTilllämd  |  Tillägg av en blob-baserad disk till virtuell dator med hanterade diskar stöds inte.  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte{1}tillåtet på Bild ' ' eftersom bilden är markerad för borttagning. Du kan bara försöka ta bort åtgärden igen (eller vänta tills en pågående åtgärd har slutförts).  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte{1}tillåten på den virtuella datorn ' eftersom den virtuella datorn är generaliserad.  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte tillåtet{1}eftersom Återställningspunktsamling ' ' har markerats för borttagning.  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte{1}tillåten i VM-tillägget ' ' eftersom den är markerad för borttagning. Du kan bara försöka ta bort åtgärden igen (eller vänta tills en pågående åtgärd har slutförts).  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte tillåten eftersom de virtuella{1}datorerna " etableras med hjälp av bilden "{2}'.  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte tillåten eftersom{1}den virtuella datorn{2}ScaleSet ' ' använder bilden ' '.  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte{1}tillåten på den virtuella datorn ' eftersom den virtuella datorn har markerats för borttagning. Du kan bara försöka ta bort åtgärden igen (eller vänta tills en pågående åtgärd har slutförts).  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte{1}tillåten på den virtuella datorn ' eftersom den virtuella datorn antingen är deallocated eller markerad för att deallocated.  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte{1}tillåten på den virtuella datorn ' eftersom den virtuella datorn körs. Stäng av den virtuella datorn om du stänger av den virtuella datorn inifrån gästoperativsystemet.  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte{1}tillåten på den virtuella datorn ' eftersom den virtuella datorn inte är deallocated.  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte{1}tillåten på{2}den virtuella datorn ' eftersom vm har anknytning ' ' i feltillstånd.  |
|  Åtgärden InteTilllämd  |  Åtgärden{0}' ' är inte{1}tillåten på den virtuella datorn ' eftersom en annan åtgärd pågår.  |
|  Åtgärden InteTilllämd  |  Åtgärden '{0}' kräver att{1}den virtuella datorn ' ' ska generaliseras.  |
|  Åtgärden InteTilllämd  |  Åtgärden kräver att den virtuella datorn körs (eller är inställd på att köras).  |
|  Åtgärden InteTilllämd  |  Disk med {0}storlek GB, som {1}är mindre än storleken GB för motsvarande disk i Bild, är inte tillåten.  |
|  Åtgärden InteTilllämd  |  VM Scale Set-tillägg av{0}hanterare ' kan endast läggas till när vm-skalningsuppsättningen skapas.  |
|  Åtgärden InteTilllämd  |  Vm Scale Set-tillägg av{0}hanteraren ' kan endast tas bort vid tidpunkten för borttagning av vm-skalningsuppsättning.  |
|  Åtgärden InteTilllämd  |  VM{0}' ' använder redan hanterade diskar.  |
|  Åtgärden InteTilllämd  |  VM{0}' ' tillhör tillgänglighetsuppsättningen{1}"Klassisk" '. Uppdatera tillgänglighetsuppsättningen för att använda SKU justerad och försök sedan igen med konverteringen.  |
|  Åtgärden InteTilllämd  |  Den virtuella datorn som skapats från avbildningen kan inte ha blobbaserade diskar. Alla diskar måste hanteras diskar.  |
|  Åtgärden InteTilllämd  |  Det går inte att slutföra hämtningsåtgärden eftersom den virtuella datorn inte är generaliserad.  |
|  Åtgärden InteTilllämd  |  Hanteringsåtgärder på{0}vm ' ' tillåts inte eftersom VM-diskar konverteras till hanterade diskar.  |
|  Åtgärden InteTilllämd  |  En pågående åtgärd ändrar {0} strömtillståndet för virtuell dator till {1}. Utför åtgärden {2} efter en tid.  |
|  Åtgärden InteTilllämd  |  Det går inte att lägga till eller uppdatera den virtuella datorn. Den begärda {0} VM-storleken kanske inte är tillgänglig i den befintliga allokeringsenheten. Läs mer om VM storleksändring strategi på https://aka.ms/azure-resizevm.  |
|  Åtgärden InteTilllämd  |  Det gick inte att ändra storlek {0} på den virtuella datorn eftersom den begärda storleken inte är tillgänglig i klustret där tillgänglighetsuppsättningen för närvarande allokeras. De tillgängliga storlekarna {1}är: . Läs mer om VM storleksändring strategi på https://aka.ms/azure-resizevm.  |
|  Åtgärden InteTilllämd  |  Det gick inte att ändra storlek {0} på den virtuella datorn eftersom den begärda storleken inte är tillgänglig i klustret där den virtuella datorn för närvarande allokeras. Om du vill ändra {1} storlek på den virtuella datorn så att du kan frigöra (det här är Stop-åtgärden i Azure-portalen) och prova åtgärden ändra storlek igen. Läs mer om VM storleksändring strategi på https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  OS-etablering misslyckades för{0}VM ' ' eftersom gästoperativsystemet för närvarande etableras.  |
|  OSProvisioningClientError  |  Os-etablering för{0}VM ' ' misslyckades. Felinformation: {1} Kontrollera att bilden har förberetts korrekt (generaliserats). <ul><li>Instruktioner för Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  SSH-värdnyckelgenerering misslyckades. Felinformation: {0}. LÃ¶s problemet ã¥fÃ¶r att lÃ¶sa om Linux-agenten har konfigurerats korrekt. <ul><li>Du kan läsa instruktionerna på:https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  Användarnamn som angetts för den virtuella datorn är ogiltigt för den här Linux-distributionen. Felinformation: {0}.  |
|  OSProvisioningInternalError  |  Os-etablering misslyckades för{0}VM ' ' på grund av ett internt fel.  |
|  OSProvisioningTimedOut  |  OS-etablering för{0}vm ' ' slutade inte på den tilldelade tiden. Den virtuella datorn kan fortfarande slutföra etableringen. Kontrollera etableringstillståndet senare.  |
|  OSProvisioningTimedOut  |  OS-etablering för{0}vm ' ' slutade inte på den tilldelade tiden. Den virtuella datorn kan fortfarande slutföra etableringen. Kontrollera etableringstillståndet senare. Kontrollera också att bilden har förberetts korrekt (generaliserats).   <ul><li>Instruktioner för Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruktioner för Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  OS-etablering för{0}vm ' ' slutade inte på den tilldelade tiden. Den virtuella datorns gästagent upptäcktes dock körs. Detta tyder på att gästoperativsystemet inte har förberetts korrekt för att användas som en VM-avbildning (med CreateOption=FromImage). LÃ¶s problemet genom att antingen anvÃ¤nde Ã¤1s den vhd som är med CreateOption=Attach eller förbereda den korrekt för anvÃ¤ndevning som bild:   <ul><li>Instruktioner för Windows:https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruktioner för Linux:https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Den nödvändiga VM-storleken är för närvarande inte tillgänglig på den valda platsen.  |
|  ResourceUpdateBlockedOnPlatformUppdera  |  Resursen kan inte uppdateras just nu på grund av pågående plattformsuppdatering. Försök igen senare.  |
|  LagringSredovisningLimitation  |  Lagringskonto{0}' ' stöder inte sidblobar som krävs för att skapa diskar.  |
|  LagringSredovisningLimitation  |  Lagringskontot '{0}' har överskridit den tilldelade kvoten.  |
|  LagringKontoPlatsMismatch  |  Det gick inte {0}att lösa lagringskontot . Kontrollera att den skapades via Storage Resource Provider på samma plats som beräkningsresursen.  |
|  LagringSkontoT Är integrundat  |  Det {0} gick inte att hitta lagringskontot. Kontrollera att lagringskontot inte tas bort och tillhör samma Azure-plats som den virtuella datorn.  |
|  StorageAccountNotRecognized  |  Använd ett lagringskonto som hanteras av Storage Resource Provider. Användning {0} av stöds inte.  |
|  LagringUträknadInternalError  |  Internt fel uppstod när {0}lagringskontot skulle komma åt .  |
|  LagringKontoAbonnemangMismatch  |  Lagringskontot {0} tillhör inte {1}prenumerationen .  |
|  LagringFörföretagande  |  Lagringskontot '{0}' är för upptaget för tillfället. Överväg att använda ett annat konto.  |
|  StorageAccountTypeInte Stöds  |  Disk {0} {1} använder som är ett Blob-lagringskonto. Försök igen med allmänt lagringskonto.  |
|  StorageAccountTypeInte Stöds  |  {0} Lagringskontot {1} är av typen. Boot Diagnostics {2} stöder lagringskontotyper.  <ul><li>Det här felet uppstår om du använder premiumlagringskontot för startdiagnostik. Mer information finns i [Så här använder du startdiagnostik](boot-diagnostics.md). </li></ul> |
|  PrenumerationAntecknas förimage  |  Prenumerationen är inte auktoriserad.  |
|  TargetDiskBlobAlreadyExister  |  Blob {0} finns redan. Ange en annan blob URI för att{1}skapa en ny tom datadisk ' '.  |
|  TargetDiskBlobAlreadyExister  |  Hämtningsåtgärden kan inte {0} fortsätta eftersom målbildblob redan finns och flaggan för att skriva över VHD-blobbar inte har angetts. Ta antingen bort blobben eller ange att flaggan ska skriva över VHD-blobbar och försöka igen.  |
|  TargetDiskBlobAlreadyExister  |  Hämtningsåtgärden kan inte {0} fortsätta eftersom målbildblob har ett aktivt lån på sig.   |
|  TargetDiskBlobAlreadyExister  |  Blob {0} finns redan. Ange en annan blob URI som{1}mål för disk ' '.  |
|  TooManyVMRedeploymentRequests  |  För många begäranden om omfördelning har{0}tagits emot för vm ' ' eller de virtuella datorerna i samma tillgänglighetsuppsättning med den här virtuella datorn. Försök igen senare.  |
|  VHDSizeInvalid  |  Det angivna diskstorleksvärdet {0} för{1}för disk {2} ' ' med blob är ogiltigt. Diskstorleken måste {3} {4}vara mellan och .  |
|  VMAgentStatusCommunicationError  |  VM{0}' har inte rapporterat status för VM-agent eller tillägg. Kontrollera att den virtuella datorn har en vm-agent som körs och kan upprätta utgående anslutningar till Azure-lagring.  |
|  VMArtifactRepositoryInternalError  |  Ett fel uppstod när artefaktdatabasen skulle kunna hämta artefaktinformation för virtuella datorer.  |
|  VMArtifactRepositoryInternalError  |  Ett internt fel uppstod när den virtuella artefaktdata hämtas från artefaktdatabasen.  |
|  VMExtensionHandlerNonTransientError  |  Handler{0}' ' har rapporterat{1}fel för VM{2}Extension ' '{3}med terminalfelkod ' ' och felmeddelande: ' '  |
|  VMExtensionManagementInternalError  |  Internt fel uppstod vid{0}bearbetning av vm-tillägg ' '.  |
|  VMExtensionManagementInternalError  |  Flera fel uppstod när vm-tilläggen skulle förberedas. Se vyn för tilläggstillägg för virtuell dator för mer information.  |
|  VMExtensionProvisioningError  |  Den virtuella datorn har rapporterat{0}ett fel vid bearbetning av tillägg ' '. Felmeddelande: "{1}".  |
|  VMExtensionProvisioningError  |  Flera VM-tillägg kunde inte etableras på den virtuella datorn. Mer information finns i vyn för tilläggstillägg för virtuell dator.  |
|  VMExtensionProvisioningTimeout  |  Etablering av VM-tillägg '{0}' har tagit time out. Tilläggsinstallationen kan ta för lång tid eller så gick det inte att hämta tilläggsstatus.  |
|  VMMarketplaceInvalidInput VMMarketplaceInvalidInput VMMarketplaceInvalidInput VMMarket  |  Att skapa en virtuell dator från en icke Marketplace-avbildning behöver inte planera information, ta bort planinformationen i begäran. OS-disknamn {0}är .  |
|  VMMarketplaceInvalidInput VMMarketplaceInvalidInput VMMarketplaceInvalidInput VMMarket  |  Inköpsinformationen matchar inte. Det går inte att distribuera från Marketplace-avbildningen. OS-disknamn {0}är .  |
|  VMMarketplaceInvalidInput VMMarketplaceInvalidInput VMMarketplaceInvalidInput VMMarket  |  För att skapa en virtuell dator från Marketplace-avbildning krävs planinformation i begäran. OS-disknamn {0}är .  |
|  VMNotFound  |  Det går{0}inte att hitta den virtuella datorn.  |
|  VMRedeploymentBetalt  |  Vm{0}' redeployment misslyckades på grund av ett internt fel. Försök igen senare.  |
|  VMRedeploymentTimedOut  |  Omfördelning av VM '{0}' slutade inte på den tilldelade tiden. Det kan avslutas framgångsrikt i någon gång. Annars kan du försöka igen begäran.  |
|  VMStartTimedOut  |  VM{0}' ' startade inte på den tilldelade tiden. Den virtuella datorn kan fortfarande starta. Kontrollera eltillståndet senare.  |


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
