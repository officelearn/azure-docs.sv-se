---
title: Azure Stack 1710-uppdatering (Build 20171020.1) | Microsoft Docs
description: Lär dig mer om vad som finns i 1710 uppdateringen för Azure-stacken integrerat system, kända problem och var du kan hämta uppdateringen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mabrigg
ms.openlocfilehash: 8c7c39ecdc332c994e5c00f8415462f208e7d20b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-1710-update-build-201710201"></a>Azure Stack 1710-uppdatering (Build 20171020.1)

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver förbättringarna och korrigeringar i det här uppdateringspaketet kända problem för den här versionen och var du kan hämta uppdateringen. Kända problem är indelade i kända problem direkt relaterad till uppdateringen och kända problem med build (efter installationen).

> [!IMPORTANT]
> Det här uppdateringspaketet gäller bara för Azure-stacken integrerat system. Det här uppdateringspaketet gäller inte Azure Stack Development Kit.

## <a name="improvements-and-fixes"></a>Förbättringar och korrigeringar

Den här uppdateringen innehåller följande förbättringar av kvalitet och korrigeringar för Azure-stacken.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Förbättringar i Windows Server 2016 och korrigeringar

- Uppdateringar för Windows Server 2016: den 10 oktober 2017 – KB4041691 (OS-version 14393.1770. Se [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) för mer information.

### <a name="additional-quality-improvements-and-fixes"></a>Förbättringar av ytterligare kvalitet och korrigeringar

- Lägga till Privilegierade endpoint PowerShell-cmdlets för att felsöka och uppdatera Network Time Protocol (NTP)-server.
- Stöd har lagts till för att uppdatera Privilegierade slutpunkt bara tillräckligt Administration JEA () slutpunkt moduler och cmdlet godkända. 
- Fast lokala språket fel i Privilegierade slutpunkten.
- Lägga till möjligheten att rotera gateway-autentiseringsuppgifter.
- Ta bort det lokala administratörskontot för CBLocalAdmin. 
- Fast pulsslag avisering mallinnehållet att se till att pulsslag aviseringar fungerar korrekt efter en uppdatering.
- Fast Fabric-resursprovidern hantera tidsgränser under FRU-åtgärder. 
- Lägga till möjligheten för molnet utvecklare att använda Azure Resource Manager API profiler på Azure-stacken.
- Inaktivera Windows Update-tjänsten på distribution av virtuell dator (DVM). 
- Ta bort nod power på/av åtgärder från användargränssnittet.
- Olika prestanda och stabilitet korrigeringar. 
 
## <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

Det här avsnittet innehåller kända problem som kan uppstå under installationen av uppdateringen 1710.

> [!IMPORTANT]
> Om uppdateringen misslyckas när du senare försöker fortsätta uppdateringen måste du använda den `Resume-AzureStackUpdate` cmdlet från Privilegierade slutpunkten. Återuppta inte uppdateringen med hjälp av administratörsportalen. (Detta är ett känt problem i den här versionen.) Mer information finns i [övervaka uppdateringar i Azure-stacken använder Privilegierade slutpunkten](azure-stack-monitor-update.md).

| Symptom  | Orsak  | Lösning |
|---------|---------|---------|
|När du utför en uppdatering, ett fel som liknar följande<br>kan uppstå under steget ”värdar starta om lagring lagringsnod”<br> för handlingsplan uppdateringen.<br><br>**{”name”: ”starta om lagring värdar”, ”beskrivning” ”: starta om<br> lagring värdar.” ”, errorMessage” ”: Skriv 'Restart' rollen<br> 'BareMetal” utlöses ett undantag: \n\n datorn<br> HostName-05 hoppas över. Det gick inte att hämta dess LastBootUpTime<br> via WMI-tjänsten med följande felmeddelande:<br> RPC-servern är inte tillgänglig.<br> (Undantag från HRESULT: 0x800706BA). \nat omstart-värd** | Det här problemet orsakas av en möjlig felaktig drivrutin som finns i vissa konfigurationer. | 1. Logga in på webbgränssnittet baseboard management controller (BMC) och starta om värden som anges i felmeddelandet.<br><br>2. Återuppta uppdateringen med hjälp av Privilegierade slutpunkten. |
| När du utför en uppdatering kan verkar uppdateringen stopp<br> och inte se förloppet efter steget ”steg: kör steg 2.4 - installation<br> Uppdatera ”för handlingsplan uppdateringen.<br><br>Det här steget sedan följt av en serie kopieringsstegen av .nupkg<br> filer till den interna infrastrukturen filresurser. Exempel:<br><br>**Kopiera 1 filer från content\PerfCollector\VirtualMachines till <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Eller meddelandet:<br><br>**WarningMessage:Task: Anrop av gränssnitt 'LiveUpdate'<br> av rollen 'Cloud\Fabric\VirtualMachines' misslyckades:<br> typ 'LiveUpdate' av rollen 'VirtualMachines' aktiveras en<br> undantag: det finns inte tillräckligt med utrymme på disken .**  | Problemet orsakas av loggfiler fyller upp diskarna på en infrastruktur för virtuell dator och ett problem i Windows Server skalbar filserver (SOFS) som distribueras i en kommande uppdatering. | Kontakta Microsofts kundservice och Support (CSS) för att få hjälp. | 
| När du utför en uppdatering, ett fel som liknar följande<br> kan uppstå under steget ”steg: kör steg 2.13.2 - uppdatering<br> *VM_Name*”av åtgärdsplan för uppdateringen. (Den virtuella datorn<br> namnet kan variera.)<br><br>**ActionPlanInstanceWarning FN/MachineName:<br> WarningMessage:Task: anrop av gränssnitt 'LiveUpdate' av<br> rollen 'Cloud\Fabric\WAS' misslyckades: typen 'LiveUpdate' för rollen<br> '' uppstod ett undantag: ett fel UPPSTOD vid lagring<br> initieringen: ett fel uppstod vid försök att göra en API<br> anrop till tjänsten Microsoft Storage: {”meddelandet” ”: tidsgränsen<br> uppstod vid kommunikation med Service Fabric.<br> Undantagstyp: TimeoutException.<br> Undantagsmeddelande: Tidsgränsen uppnåddes ”.}**  | Problemet orsakas av ett i/o-tidsgräns i Windows Server som kommer att åtgärdas i en kommande uppdatering. | Kontakta Microsoft CSS om du behöver hjälp.
| När du utför en uppdatering, ett fel som liknar följande<br> kan uppstå under steget ”steg i 21 starta om SQL server-datorer”.<br><br>**Typen 'LiveUpdateRestart' för rollen ”VirtualMachines” utlöses ett<br> undantag: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> frågar efter VM MachineName-Sql01. - 10/13/2017 17:11:50: 00 VerboseMessage: [virtuella datorer: LiveUpdateRestart]<br> VM har markerats som HighlyAvailable. – 10/13/2017 17:11:50: 00<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart] vid<br>MS. Internal.ServerClusters.ExceptionHelp.Build på<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(booleskt force) på Microsoft.FailoverClusters.PowerShell.<br> StopClusterResourceCommand.BeginTimedOperation() på <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() på Microsoft.FailoverClusters.PowerShell.<br> FCCmdlet.ProcessRecord() - 10/13/2017 5:11:50 PM varning<br>meddelande: uppgiften: anrop av gränssnitt 'LiveUpdateRestart' för<br> rollen 'Cloud\Fabric\VirtualMachines' misslyckades:** | Det här problemet kan inträffa om den virtuella datorn kunde inte startas om. | Kontakta Microsoft CSS om du behöver hjälp.
| När du utför en uppdatering, kan det uppstå ett fel som liknar följande:<br><br>**2017-10-22T01:37:37.5369944Z typ 'Avstängning' av rollen 'SQL'<br> utlöste ett undantagsfel: ett fel uppstod noden<br> 's45r1004 Sql01'.at Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 på<br> Avstängning, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>program \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: rad 50 vid < ScriptBlock&#62;,<br> <No file>: rad 18 vid < ScriptBlock&#62;, < Ingen fil&#62;: rad 16** | Det här problemet kan inträffa om den virtuella datorn inte kan placeras i ett pausat tillstånd att tömma rollerna. | Kontakta Microsoft CSS om du behöver hjälp.
| När du utför en uppdatering kan något av följande fel uppstå:<br><br>**Typen 'Verifiera' av rollen 'ADFS' utlöste ett undantagsfel: verifiering<br> för AD FS/diagram misslyckades med felet: Kontrollera ADFS<br> avsökning endpoint *endpoint_URI*: undantag anropar<br> ” GetResponse ”med” 0 ”argument”: fjärrservern<br> returnerade ett fel: (503) Server inte tillgänglig ”. vid Invoke -<br>ADFSGraphValidation**<br><br>**Typen 'Verifiera' av rollen 'ADFS' utlöste ett undantagsfel: verifiering<br> för AD FS/diagram misslyckades med felet: gick inte att hämta<br> ADFS egenskaper: kunde inte ansluta till <br>NET.TCP://localhost: 1 500-princip. Anslutningsförsöket varade<br> för en tidsrymd för 00:00:02.0498923. TCP-felkod<br> 10061: ingen anslutning kan göras eftersom målet<br> datorn aktivt nekade det. 127.0.0.1:1500.<br> på Invoke-ADFSGraphValidation** | Plan för update-åtgärder kan inte verifiera hälsotillstånd för Active Directory Federation Services (AD FS). | Kontakta Microsoft CSS om du behöver hjälp.

## <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

Det här avsnittet innehåller efter installationen kända problem med att skapa 20171020.1.

### <a name="portal"></a>Portalen

- Det kanske inte går att visa beräkning eller lagring resurser i administratörsportalen. Detta anger att ett fel inträffade under installationen av uppdateringen och att uppdateringen felaktigt rapporterades som slutförd. Om det här problemet inträffar kan du kontakta Microsoft CSS om hjälp.
- Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du växeln-ikonen i det övre högra hörnet av portalen och välj sedan **återställa standardinställningarna**.
- När du visar egenskaperna för en resursgrupp i **flytta** är inaktiverat. Det är förväntat. Flytta resursgrupper mellan prenumerationer stöds inte för närvarande.
- För alla arbetsflöden där du väljer en prenumeration, resursgrupp eller plats i en listrutan, kan en eller flera av följande problem uppstå:

   - Du kan se en tom rad längst upp i listan. Du måste fortfarande kunna markera ett objekt som förväntat.
   - Om listan över objekt i den nedrullningsbara listrutan är kort kan kanske du inte visa något av objektnamn.
   - Om du har flera prenumerationer för användaren, får den nedrullningsbara resurslistan vara tom. 

   Du kan skriva namnet på den prenumeration eller resursgrupp (om du vet att den) Undvik de två sista problem, eller kan du använda PowerShell i stället.
- Ta bort användaren prenumerationer resulterar i frånkopplade resurser. Först ta bort resurser för användare eller hela resursgruppen som en lösning och sedan ta bort prenumerationer för användaren.
- Det går inte att visa behörighet till din prenumeration med hjälp av Azure Stack-portaler. Som en tillfällig lösning kan kontrollera du behörigheterna med hjälp av PowerShell.
- Den **tjänstens hälsa** bladet inte laddas. När du öppnar bladet tjänstens hälsa i administratören eller användaren portal, Azure stacken visas ett fel och läses inte in information. Detta är förväntat. Även om du kan markera och öppna tjänstens hälsa, funktionen är inte tillgänglig ännu, men de kommer att genomföras i en framtida version av Azure-stacken.
 

### <a name="backup"></a>Backup

- Aktivera inte infrastrukturen för säkerhetskopiering på den **infrastruktur säkerhetskopiering** bladet.

### <a name="health-and-monitoring"></a>Hälsa och övervakning

- Om du startar om en infrastruktur för rollinstansen får ett meddelande som anger att omstarten misslyckades. Men omstarten faktiskt lyckades.

### <a name="marketplace"></a>Marketplace
- När du försöker lägga till objekt i stacken för Azure marketplace med hjälp av den **Lägg till från Azure** alternativet inte alla objekt kan vara synlig för hämtning.
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa element som planer och erbjudanden. Objekten är icke-fungerande till användare.

### <a name="compute"></a>Compute
- Användare möjlighet att skapa en virtuell dator med geo-redundant lagring. Den här konfigurationen gör att skapa en virtuell dator misslyckas.
- Du kan konfigurera en virtuell dator tillgänglighetsuppsättning endast med en feldomän på en och en uppdateringsdomän i en.
- Det finns inga marketplace-upplevelse för att skapa virtuella datorer. Du kan skapa en skala som anges med hjälp av en mall.
- Inställningarna för skalning för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader mellan versioner PowerShell måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.
 
### <a name="networking"></a>Nätverk
- Du kan inte skapa en belastningsutjämnare med en offentlig IP-adress med hjälp av portalen. Som en tillfällig lösning kan använda du PowerShell för att skapa belastningsutjämnaren.
- När du skapar en Utjämning av nätverksbelastning måste du skapa en regel för network adress translation (NAT). Om du inte får ett fel när du försöker lägga till en NAT-regel när belastningsutjämnaren har skapats.
- Du kan koppla en offentlig IP-adress från en virtuell dator (VM) när den virtuella datorn har skapats och associerats med den IP-adressen. Disassociation visas ska fungera, men tidigare tilldelade offentliga IP-adressen är kopplad till den ursprungliga virtuella datorn. Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som vanligtvis kallas ett *VIP-växling*). Alla framtida försöker att ansluta till IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till en ny. För närvarande måste du bara använda nya offentliga IP-adresser för att skapa en ny virtuell dator.
 
### <a name="sqlmysql"></a>SQL/MySQL
- Det kan ta upp till en timme innan klienter kan skapa databaser i en ny SQL eller MySQL SKU. 
- Skapande av objekt direkt på SQL- och MySQL som värd för servrar som inte utförs av resursprovidern stöds inte och kan resultera i ett felaktigt tillstånd.
 
### <a name="app-service"></a>App Service
- Användaren måste registrera lagringsresursprovidern innan de skapar sin första Azure-funktion i prenumerationen.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Fältet utbytbara enhet (FRU) procedurer

- Under uppdateringskörningen, korrigeras offlineavbildningar inte. Om du behöver ersätta en scale unit nod fungera med maskinvaruleverantören OEM-noden ersatta har den senaste korrigeringsnivå.

## <a name="download-the-update"></a>Hämta uppdateringen

Du kan ladda ned uppdateringspaketet 1710 [här](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Nästa steg

- En översikt över uppdateringshantering i Azure-stacken, se [hantera uppdateringar i Azure-stacken översikt](azure-stack-updates.md).
- Information om hur du installerar uppdateringar finns i [tillämpa uppdateringar i Azure-stacken](azure-stack-apply-updates.md).
