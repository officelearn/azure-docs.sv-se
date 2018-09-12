---
title: Azure Stack 1710 Update (version 20171020.1) | Microsoft Docs
description: Lär dig mer om vad som finns i 1710 uppdateringen för Azure Stack integrerade system, kända problem och var du kan hämta uppdateringen.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: cf870551a3dbd9b5ea0ef6f886dc6451e43b2c25
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377198"
---
# <a name="azure-stack-1710-update-build-201710201"></a>Azure Stack 1710 Update (version 20171020.1)

*Gäller för: integrerade Azure Stack-system*

Den här artikeln beskrivs förbättringarna och korrigeringar i den här uppdateringen, kända problem för den här versionen och var du kan hämta uppdateringen. Kända problem är indelade i kända problem direkt relaterade till uppdateringsprocessen och kända problem med build (efter installationen).

> [!IMPORTANT]
> Det här uppdateringspaketet är endast för integrerade Azure Stack-system. Gäller inte det här uppdateringspaketet för Azure Stack Development Kit.

## <a name="improvements-and-fixes"></a>Förbättringar och korrigeringar

Den här uppdateringen innehåller följande förbättringar i kvalitet och korrigeringar för Azure Stack.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Windows Server 2016 förbättringar och korrigeringar

- Uppdateringar för Windows Server 2016: den 10 oktober 2017 – KB4041691 (OS-version 14393.1770. Se [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) för mer information.

### <a name="additional-quality-improvements-and-fixes"></a>Ytterligare kvalitet förbättringar och korrigeringar

- Lägga till privilegierad slutpunkt PowerShell-cmdletar för att felsöka och uppdatera Network Time Protocol (NTP)-servern.
- Stöd har lagts till för att uppdatera privilegierad slutpunkt tillräckligt Administration JEA ()-slutpunkten moduler och cmdlet: en lista över tillåtna. 
- Fast språk fel i den privilegierade slutpunkten.
- Tillagd möjlighet att rotera autentiseringsuppgifter för gateway.
- Ta bort det lokala administratörskontot för CBLocalAdmin. 
- Fast pulsslag avisering mall-innehåll att se till att pulsslag aviseringar fungerar korrekt efter en uppdatering.
- Fasta resursprovidern Fabric behöver bry dig om timeout-fel under FRU-åtgärder. 
- Har lagts till i molnet ger utvecklarna möjlighet att använda Azure Resource Manager API-profiler i Azure Stack.
- Inaktiverat Windows Update-tjänsten på distribution av virtuell dator (DVM). 
- Ta bort noden power på/av åtgärder från användargränssnittet.
- Olika andra prestanda och stabilitet korrigeringar. 
 
## <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

Det här avsnittet innehåller kända problem som kan uppstå under installationen av uppdateringen 1710.

> [!IMPORTANT]
> Om uppdateringen misslyckas vid senare försök att återuppta uppdateringen måste du använda den `Resume-AzureStackUpdate` cmdlet från Privilegierade slutpunkten. Fortsätt inte uppdateringen med hjälp av administratörsportalen. (Detta är ett känt problem i den här versionen.) Mer information finns i [övervakar uppdateringarna i Azure Stack med hjälp av privilegierad slutpunkt](azure-stack-monitor-update.md).

| Symtom  | Orsak  | Upplösning |
|---------|---------|---------|
|När du utför en uppdatering, ett fel som liknar följande<br>kan uppstå under steget ”Storage värdar starta om Lagringsnoden”<br> av uppdatering åtgärdsplan.<br><br>**{”name”: ”starta om Storage värdar”, ”beskrivning” ”: starta om<br> Storage värdar.” ”, errorMessage” ”: Skriv 'Restart' i rollen<br> ” BareMetal ”utlöses ett undantag: \n\n datorn<br> HostName-05 hoppas över. Det gick inte att hämta dess LastBootUpTime<br> via WMI-tjänsten med följande felmeddelande visas:<br> RPC-servern är inte tillgänglig.<br> (Undantag från HRESULT: 0x800706BA). \nat omstart-värd** | Det här problemet orsakas av en potentiell felaktig drivrutin som finns i vissa konfigurationer. | 1. Logga in till webbgränssnittet för baseboard management controller (BMC) och starta om den värd som har identifierats i felmeddelandet.<br><br>2. Återuppta uppdateringen genom att använda privilegierad slutpunkt. |
| När du utför en uppdatering visas uppdateringsprocessen uppehåll<br> och gör inte förloppet efter steget ”steg: kör steg 2.4 - installation<br> Uppdatera ”för handlingsplan uppdatering.<br><br>Det här steget sedan följt av en serie kopieringsstegen av .nupkg<br> filer till den interna infrastrukturen filresurser. Exempel:<br><br>**Kopiera 1 filer från content\PerfCollector\VirtualMachines till <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Alternativt kan du se meddelandet:<br><br>**WarningMessage:Task: Anrop av gränssnittet 'LiveUpdate'<br> av rollen 'Cloud\Fabric\VirtualMachines' misslyckades:<br> typ 'LiveUpdate' av rollen 'VirtualMachines' har aktiverats en<br> undantag: det finns inte tillräckligt med utrymme på disken .**  | Problemet orsakas av loggfiler fyller upp diskarna på en virtuell dator i infrastrukturen och ett problem i Windows Server skalbar filserver (SOFS) som distribueras i en efterföljande uppdatering. | Kontakta Microsofts kundservice och Support (CSS) för hjälp. | 
| När du utför en uppdatering, ett fel som liknar följande<br> kan uppstå under steget ”steg: kör steg 2.13.2 - uppdatering<br> *VM_Name*”av uppdatering åtgärdsplan. (Den virtuella datorn<br> namnet kan variera.)<br><br>**ActionPlanInstanceWarning FN/MachineName:<br> WarningMessage:Task: anrop av gränssnitt 'LiveUpdate' av<br> rollen 'Cloud\Fabric\WAS' misslyckades: Skriv ”LiveUpdate' i rollen<br> 'Var' utlöste ett undantag: ett fel UPPSTOD vid Storage<br> initieringen: ett fel uppstod vid försök att göra ett API<br> anrop till Microsoft Storage-tjänst: {”meddelande” ”: en tidsgräns<br> inträffade under kommunikationen med Service Fabric.<br> Undantagstyp: TimeoutException.<br> Undantagsmeddelande: Tidsgränsen nåddes för åtgärden ”.}**  | Problemet orsakas av ett i/o-timeout i Windows Server som kommer att åtgärdas i en efterföljande uppdatering. | Kontakta Microsofts CSS för hjälp.
| När du utför en uppdatering, ett fel som liknar följande<br> kan uppstå under steget ”steg i 21 starta om SQL server-datorer”.<br><br>**Skriv ”LiveUpdateRestart” av rollen 'VirtualMachines' har aktiverats en<br> undantag: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> fråga efter VM MachineName-Sql01. - 10/13/2017 17:11:50: 00 VerboseMessage: [virtuella datorer: LiveUpdateRestart]<br> datorn har markerats som HighlyAvailable. – 10/13/2017 17:11:50: 00<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart] vid<br>MS. Internal.ServerClusters.ExceptionHelp.Build på<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(booleskt force) på Microsoft.FailoverClusters.PowerShell.<br> StopClusterResourceCommand.BeginTimedOperation() på <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() på Microsoft.FailoverClusters.PowerShell.<br> FCCmdlet.ProcessRecord() - 10/13/2017 17:11:50: 00 varning<br>meddelande: uppgift: anrop av gränssnitt 'LiveUpdateRestart' av<br> rollen ”Cloud\Fabric\VirtualMachines” misslyckades:** | Det här problemet kan inträffa om den virtuella datorn kunde inte startas om. | Kontakta Microsofts CSS för hjälp.
| När du utför en uppdatering kan det uppstå ett fel som liknar följande:<br><br>**2017-10-22T01:37:37.5369944Z rolltyp 'Avstängning' 'SQL'<br> utlöste ett undantag: ett fel uppstod noden<br> 's45r1004 Sql01'.at Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 på<br> Stäng av, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>program \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: rad 50 på < ScriptBlock&#62;,<br> <No file>: rad 18 på < ScriptBlock&#62;, < Ingen fil&#62;: rad 16** | Det här problemet kan inträffa om den virtuella datorn inte försättas i ett väntetillstånd att tömma rollerna. | Kontakta Microsofts CSS för hjälp.
| När du utför en uppdatering kan något av följande fel uppstå:<br><br>**Rolltyp 'Verifiera' 'ADFS' utlöste ett undantag: verifiering<br> för AD FS/Graph-roll misslyckades med felet: fel vid sökning ADFS<br> avsökning endpoint *endpoint_URI*: undantag anropa<br> ” GetResponse ”med” 0 ”argument”: fjärrservern<br> returnerade ett fel: (503) Server är inte tillgänglig ”. vid Invoke -<br>ADFSGraphValidation**<br><br>**Rolltyp 'Verifiera' 'ADFS' utlöste ett undantag: verifiering<br> för AD FS/Graph-roll misslyckades med felet: gick inte att hämta<br> ADFS egenskaper: Det gick inte att ansluta till <br>NET.TCP://localhost: 1500/policy. Anslutningsförsöket varat<br> för ett tidsintervall för 00:00:02.0498923. TCP-felkod<br> 10061: Det gick att upprätta någon anslutning eftersom målet<br> datorn aktivt nekade det. 127.0.0.1:1500.<br> på Invoke-ADFSGraphValidation** | Uppdatera åtgärdsplan kan inte verifiera hälsotillstånd för Active Directory Federation Services (AD FS). | Kontakta Microsofts CSS för hjälp.

## <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

Det här avsnittet innehåller efter installation kända problem med att skapa 20171020.1.

### <a name="portal"></a>Portal

- Det kanske inte möjligt att visa resurser för beräkning eller lagring i administratörsportalen. Detta anger att ett fel uppstod under installationen av uppdateringen och att uppdateringen felaktigt har rapporterats som slutförd. Om det här problemet inträffar kan du kontakta Microsofts CSS för att få hjälp.
- Du kan se en tom instrumentpanel i portalen. Om du vill återställa instrumentpanelen, väljer du kugghjulsikonen i det övre högra hörnet i portalen och välj sedan **Återställ standardinställningarna**.
- När du visar egenskaperna för en resursgrupp, den **flytta** knappen är inaktiverad. Det här beteendet är förväntat. Flytta resursgrupper mellan prenumerationer stöds inte för närvarande.
- För alla arbetsflöden där du väljer en prenumeration, resursgrupp eller plats i en nedrullningsbar listruta, kan en eller flera av följande problem uppstå:

   - Du kan se en tom rad överst i listan. Du bör fortfarande att kunna markera ett objekt som förväntat.
   - Om listan över objekt i den nedrullningsbara listan är kort kan du kanske inte visa någon av Artikelnamnen.
   - Om du har flera prenumerationer för användare, kan grupp resurs för listrutan vara tom. 

   Undvik de två sista problem, du kan skriva namnet på en prenumeration eller resursgrupp (om du känner till den) eller du kan använda PowerShell i stället.
- Tar bort användaren prenumerationer resulterar i överblivna resurser. Som en lösning kan du först ta bort användarresurser eller hela resursgruppen och tar bort användarprenumerationer.
- Det går inte att visa behörigheter till din prenumeration med hjälp av Azure Stack-portaler. Som en lösning kan kontrollera du behörighet med hjälp av PowerShell.
- Den **Service Health** bladet inte kan läsas in. När du öppnar bladet Tjänstehälsa i administratören eller användaren portal, Azure Stack visar ett fel och läses inte in information. Detta är förväntat. Även om du kan välja och öppna Tjänstehälsa, den här funktionen är inte tillgänglig ännu, men de kommer att implementeras i en framtida version av Azure Stack.
 

### <a name="backup"></a>Backup

- Aktivera inte infrastruktur för säkerhetskopiering på den **infrastruktur för säkerhetskopiering** bladet.

### <a name="health-and-monitoring"></a>Hälsa och övervakning

- Om du startar om en rollinstans infrastruktur får ett meddelande om att omstarten misslyckades. Men omstarten faktiskt har slutförts.

### <a name="marketplace"></a>Marketplace
- När du försöker lägga till objekt i Azure Stack Marketplace genom att använda den **Lägg till från Azure** alternativet, alla objekt inte kan vara synlig för hämtning.
- Användare kan bläddra till den fullständiga marknadsplatsen utan en prenumeration och kan se administrativa objekt och planer och erbjudanden. De här objekten är icke-funktionella till användare.

### <a name="compute"></a>Compute
- Användare har möjlighet att skapa en virtuell dator med geo-redundant lagring. Den här konfigurationen gör att skapa en virtuell dator misslyckas.
- Du kan konfigurera en VM-tillgänglighetsuppsättning endast med en feldomän av en och en uppdateringsdomän i en.
- Det finns inga marketplace-upplevelse för att skapa VM-skalningsuppsättningar. Du kan skapa en skalningsuppsättning med hjälp av en mall.
- Skalningsinställningarna för skalningsuppsättningar för virtuella datorer är inte tillgängliga i portalen. Som en tillfällig lösning kan du använda [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). På grund av skillnader i PowerShell-version, måste du använda den `-Name` parameter i stället för `-VMScaleSetName`.
 
### <a name="networking"></a>Nätverk
- Du kan inte skapa en belastningsutjämnare med en offentlig IP-adress med hjälp av portalen. Som en lösning kan använda du PowerShell för att skapa belastningsutjämnaren.
- När du skapar en Utjämning av nätverksbelastning måste du skapa en regel för network adress translation (NAT). Om du inte får ett fel när du försöker lägga till en NAT-regel när belastningsutjämnaren har skapats.
- Du kan inte kopplingen mellan en offentlig IP-adress från en virtuell dator (VM) när den virtuella datorn har skapats och associerats med IP-adress. Avassociationsåtgärden verkar fungera, men den tidigare tilldelade offentliga IP-adressen förblir kopplad till den ursprungliga virtuella datorn. Detta inträffar även om du omtilldelar IP-adress till en ny virtuell dator (som ofta kallas en *VIP-växling*). Alla framtiden försöker ansluta via IP-adress resultatet i en anslutning till den ursprungligen associerade virtuella datorn och inte till den nya servern. Du måste för närvarande kan bara använda nya offentliga IP-adresser för att skapa en ny virtuell dator.
 
### <a name="sqlmysql"></a>SQL/MySQL
- Det kan ta upp till en timme innan klienter kan skapa databaser i en ny SQL eller MySQL SKU. 
- Skapandet av objekt direkt på SQL- och MySQL som är värd för servrar som inte utförs av resursprovidern stöds inte och kan resultera i ett felaktigt tillstånd.
 
### <a name="app-service"></a>App Service
- En användare måste registrera lagringsresursprovidern innan de skapar sina första Azure-funktion i prenumerationen.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Fältet replaceable enhet (FRU) procedurer

- Under uppdateringskörningen, korrigerade offlineavbildningar inte. Om du vill ersätta en skala enhet nod kan du arbeta med leverantören OEM och kontrollera att noden ersatta har den senaste korrigeringsnivån.

## <a name="download-the-update"></a>Hämta uppdateringen

Du kan ladda ned uppdateringspaketet 1710 [här](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Nästa steg

- En översikt över uppdateringshantering i Azure Stack finns i [hantera uppdateringar i Azure Stack – översikt](azure-stack-updates.md).
- Information om hur du installerar uppdateringar finns i [tillämpa uppdateringar i Azure Stack](azure-stack-apply-updates.md).
