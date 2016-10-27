<properties
   pageTitle="Vanliga frågor och svar om Azure Backup | Microsoft Azure"
   description="Svar på vanliga frågor om säkerhetskopieringstjänsten, säkerhetskopieringsagenten, säkerhetskopiering och kvarhållning, återställning, säkerhet och andra vanliga frågor om säkerhetskopiering och haveriberedskap."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="säkerhetskopiering och katastrofåterställning, säkerhetskopieringstjänst"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="08/29/2016"
     ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>


# <a name="azure-backup-service--faq"></a>Vanliga frågor och svar om tjänsten Azure Backup

> [AZURE.SELECTOR]
- [Vanliga frågor om säkerhetskopiering för klassiskt läge](backup-azure-backup-faq.md)
- [Vanliga frågor om säkerhetskopiering för Resource Manager-läge](backup-azure-backup-ibiza-faq.md)

Den här artikeln innehåller en lista över vanliga frågor och svar om Azure Backup-tjänsten. Vår community svarar snabbt, och vanliga frågor publiceras i den här artikeln. Svaren på frågorna innehåller ofta referens- eller supportinformation. Du kan ställa frågor om Azure Backup i Disqus-rutan i den här eller en relaterad artikel. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).


## <a name="what-is-the-list-of-supported-operating-systems-from-which-i-can-back-up-to-azure-using-azure-backup?-<br/>"></a>Från vilka operativsystem kan jag säkerhetskopiera till Azure med hjälp av Azure Backup? <br/>
Azure Backup stöder operativsystemen i följande lista för säkerhetskopiering av filer, mappar och program med hjälp av Azure Backup Server och SCDPM. 

| Operativsystem        | Plattform           | SKU  |
| :------------- |-------------| :-----|
| Windows 8 och senaste Service Pack      | 64-bitars | Enterprise, Pro |
| Windows 7 och senaste Service Pack      | 64-bitars | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 och senaste Service Pack | 64-bitars      |    Enterprise, Pro |
| Windows 10      | 64-bitars | Enterprise, Pro, Home |
|Windows Server 2012 R2 och senaste Service Pack| 64-bitars| Standard, Datacenter, Foundation|
|Windows Server 2012 och senaste Service Pack|    64-bitars| Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 och senaste Service Pack  |64-bitars|    Standard, Workgroup|
|Windows Storage Server 2012 och senaste Service Pack |64-bitars |Standard, Workgroup
|Windows Server 2012 R2 och senaste Service Pack  |64-bitars|    Essential|
|Windows Server 2008 R2 SP1 |64-bitars|    Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2    |64-bitars|    Standard, Enterprise, Datacenter, Foundation|

Säkerhetskopiering av virtuell Azure-dator:

- **Linux**: Azure Backup stöder [en lista över distributioner som godkänts av Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md) med undantag för Core OS Linux.  Andra Bring-Your-Own-Linux-distributioner kan också fungera så länge som den virtuella datoragenten är tillgänglig på den virtuella datorn och stöd för Python finns.
- **Windows Server**: versioner som är äldre än Windows Server 2008 R2 stöds inte.

## <a name="where-can-i-download-the-latest-azure-backup-agent?-<br/>"></a>Var kan jag hämta den senaste Azure Backup-agenten? <br/>
Du kan hämta den senaste agenten för säkerhetskopiering av Windows Server, System Center DPM eller Windows-klienten [här](http://aka.ms/azurebackup_agent). Om du vill säkerhetskopiera en virtuell dator använder du VM-agenten (som automatiskt installerar rätt tillägg). VM-agenten finns redan på virtuella datorer som skapats från Azure-galleriet.

## <a name="which-version-of-scdpm-server-is-supported?-<br/>"></a>Vilken version av SCDPM-servern stöds? <br/>
Vi rekommenderar att du installerar den [senaste](http://aka.ms/azurebackup_agent) Azure Backup-agenten med den senaste samlade uppdateringen av SCDPM (UR11 från augusti 2016)

## <a name="when-configuring-the-azure-backup-agent,-i-am-prompted-to-enter-the-vault-credentials.-do-vault-credentials-expire?"></a>När jag konfigurerar Azure Backup-agenten uppmanas jag att ange valvautentiseringsuppgifter. Slutar valvautentiseringsuppgifterna att gälla?
Ja, valvautentiseringsuppgifterna upphör att gälla efter 48 timmar. Om filen går ut loggar du in på Azure-portalen och laddar ned filerna med valvautentiseringsuppgifterna från ditt valv. 

## <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription?-<br/>"></a>Finns det någon gräns för antalet valv som kan skapas i varje Azure-prenumeration? <br/>
Ja. Från september 2016 så kan du skapa 25 säkerhetskopieringsvalv per prenumeration. Du kan skapa upp till 25 Recovery Services-valv för varje Azure Backup-region som stöds per prenumeration. Om du behöver flera valv skapar du en ny prenumeration.

## <a name="are-there-any-limits-on-the-number-of-servers/machines-that-can-be-registered-against-each-vault?-<br/>"></a>Finns det några begränsningar för hur många servrar/datorer som kan registreras mot varje valv? <br/>
Ja, du kan registrera upp till 50 datorer per valv. För virtuella Azure IaaS-datorer är gränsen 200 virtuella datorer per valv. Om du behöver registrera fler datorer skapar du ett nytt valv.

## <a name="how-do-i-register-my-server-to-another-datacenter?<br/>"></a>Hur registrerar jag min server till ett annat datacenter?<br/>
Säkerhetskopierade data skickas till datacentret för det valv som det har registrerats för. Det enklaste sättet att ändra datacentret är att avinstallera och installera om agenten och registrera ett nytt valv som tillhör det önskade datacentret.

## <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azure?<br/>"></a>Vad händer om jag byter namn på en Windows-server som säkerhetskopierar data till Azure?<br/>
När du byter namn på en server stoppas alla säkerhetskopieringar som är konfigurerade för närvarande.
Du måste registrera det nya namnet på servern med Backup-valvet. När du skapar en ny registrering är den första säkerhetskopieringen en fullständig säkerhetskopiering, inte en inkrementell säkerhetskopiering. Om du behöver återställa data som tidigare har säkerhetskopierats till valvet med det gamla servernamnet kan du återställa dessa data med hjälp av alternativet [**En annan server**](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) i guiden **Återställ data**.

## <a name="what-types-of-drives-can-i-backup-files-and-folders-from?-<br/>"></a>Från vilka typer av enheter kan jag säkerhetskopiera filer och mappar? <br/>
Följande uppsättning enheter/volymer kan inte säkerhetskopieras:

- Flyttbart medium: Enheten måste rapporteras som fast för att kunna användas som källa för säkerhetskopior.
- Skrivskyddade volymer: Volymen måste vara skrivbar för att VSS-tjänsten (Volume Shadow Copy) ska fungera.
- Offlinevolymer: Volymen måste vara online för att VSS ska fungera.
- Nätverksresurs: Volymen måste vara lokal på servern för att kunna säkerhetskopieras med onlinesäkerhetskopiering.
- BitLocker-skyddade volymer: Volymen måste vara upplåst innan säkerhetskopieringen kan utföras.
- Filsystemidentifiering: NTFS är det enda filsystemet som stöds för den här versionen av onlinesäkerhetskopieringstjänsten.

## <a name="what-file-and-folder-types-can-i-back-up-from-my-server?<br/>"></a>Vilka typer av filer och mappar kan jag säkerhetskopiera från servern?<br/>
Följande typer stöds:

- Krypterade
- Komprimerade
- Utspridda
- Komprimerade + utspridda
- Hårda länkar: Stöds inte, hoppas över
- Referenspunkt: Stöds inte, hoppas över
- Krypterade + komprimerade: Stöds inte, hoppas över
- Krypterade + utspridda: Stöds inte, hoppas över
- Komprimerad dataström: Stöds inte, hoppas över
- Utspridd dataström: Stöds inte, hoppas över

## <a name="what's-the-minimum-size-requirement-for-the-cache-folder?-<br/>"></a>Vilken är den minsta nödvändiga storleken på cachelagringsmappen? <br/>
Storleken på cachelagringsmappen avgör mängden data som säkerhetskopieras. Cachelagringsmappens storlek bör vara 5 % av det utrymme som krävs för att lagra data.

## <a name="if-my-organization-has-one-vault,-how-can-i-isolate-one-server's-data-from-another-server-when-restoring-data?<br/>"></a>Min organisation har ett valv. Hur kan jag isolera en servers data från en annan server när jag återställer data?<br/>
Alla servrar som är registrerade för samma valv kan återställa data som säkerhetskopierats av andra servrar *som använder samma lösenfras*. Om du vill isolera säkerhetskopierade data på en server från andra servrar i organisationen kan du använda en annan lösenfras för dessa servrar. HR-servrarna kan till exempel använda en krypteringslösenfras, redovisningsservrarna en annan och lagringsservrar en tredje.

## <a name="can-i-“migrate”-my-backup-data-or-vault-between-subscriptions?-<br/>"></a>Kan jag ”migrera” mina säkerhetskopierade data eller mitt säkerhetskopierade valv mellan prenumerationer? <br/>
Nej. Valvet skapas på prenumerationsnivå och kan inte tilldelas en annan prenumeration när det har skapats.

## <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication?-<br/>"></a>Fungerar Azure Backup Agent på en server som använder Windows Server 2012-deduplicering? <br/>
Ja. Agenttjänsten konverterar deduplicerade data till vanliga data när den förbereder säkerhetskopieringen. Därefter optimerar tjänsten data för säkerhetskopiering, krypterar dessa data och skickar dem till onlinesäkerhetskopieringstjänsten.

## <a name="if-i-cancel-a-backup-job-once-it-has-started,-is-the-transferred-backup-data-deleted?-<br/>"></a>Tas säkerhetskopierade data som redan har överförts bort om jag avbryter ett säkerhetskopieringsjobb som redan har startat? <br/>
Nej. Säkerhetskopieringsvalvet lagrar säkerhetskopierade data som har överförts fram till den punkt då jobbet avbröts. Azure Backup använder en kontrollpunktsmekanism för att då och då lägga till kontrollpunkter till säkerhetskopierade data under säkerhetskopieringen. Eftersom det finns kontrollpunkter i säkerhetskopian kan nästa säkerhetskopiering validera filernas integritet. Nästa säkerhetskopiering är en inkrementell säkerhetskopiering som körs mot tidigare säkerhetskopierade data. En inkrementell säkerhetskopiering utnyttjar bandbredden bättre, så att du inte behöver överföra samma data flera gånger.

När det gäller säkerhetskopiering av virtuella Azure-datorer så ignoreras överförda data när jobbet avbryts och en ny säkerhetskopiering överför inkrementella data från ett tidigare lyckat säkerhetskopieringsjobb. 

## <a name="why-am-i-seeing-the-warning-"azure-backups-have-not-been-configured-for-this-server"-even-though-i-had-scheduled-regular-backups-previously?-<br/>"></a>Varför visas en varning om att inga Azure-säkerhetskopieringar har konfigurerats för servern, trots att jag har schemalagt regelbundna säkerhetskopieringar tidigare? <br/>
Den här varningen visas om inställningarna för säkerhetskopieringsschemat på den lokala servern inte är samma som inställningarna som lagras i säkerhetskopieringsvalvet. Om servern eller inställningarna har återställts till ett fungerande tillstånd kan synkroniseringen av säkerhetskopieringsscheman brytas. Om den här varningen visas [omkonfigurerar du säkerhetskopieringspolicyn](backup-azure-manage-windows-server.md) och väljer **Kör säkerhetskopiering nu** för att synkronisera om den lokala servern med Azure.

## <a name="what-firewall-rules-should-be-configured-for-azure-backup?-<br/>"></a>Vilka brandväggsregler ska jag konfigurera för Azure Backup? <br/>
För sömlöst skydd av ”lokala till Azure”-data och ”arbetsbelastning till Azure”-data rekommenderar vi att du tillåter att brandväggen kommunicerar med följande URL:er:

- www.msftncsi.com
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

##<a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension?-<br/>"></a>Kan jag installera Azure Backup-agenten på en virtuell dator i Azure som redan har säkerhetskopierats av Azure Backup-tjänsten med hjälp av VM-tillägget? <br/>
Absolut. Azure Backup stöder säkerhetskopiering på VM-nivå för virtuella datorer i Azure med hjälp av VM-tillägget. Du kan installera Azure Backup-agenten i ett Windows-gästoperativsystem för att skydda filer och mappar i gästoperativsystemet.

## <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm?-<br/>"></a>Kan jag installera Azure Backup-agenten på en virtuell dator i Azure om jag vill säkerhetskopiera filer och mappar med tillfällig lagring på den virtuella Azure-datorn? <br/>
Du kan installera Azure Backup-agenten i Windows-gästoperativsystemet och säkerhetskopiera filer och mappar till ett tillfälligt lagringsutrymme. Observera dock att säkerhetskopieringen misslyckas om du rensar data i tillfällig lagring. Om data i tillfällig lagring har tagits bort kan du bara återställa till beständig lagring.

## <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders.-can-i-now-install-scdpm-to-work-with-azure-backup-agent-to-protect-on-premises-application/vm-workloads-to-azure?-<br/>"></a>Jag har installerat Azure Backup-agenten för att skydda mina filer och mappar. Kan jag nu installera SCDPM för användning med Azure Backup-agenten om jag vill skydda lokala program/VM-arbetsbelastningar i Azure? <br/>
Om du vill använda Azure Backup med SCDPM rekommenderar vi att du installerar SCDPM först och sedan installerar Azure Backup-agenten. Detta säkerställer en smidig integrering av Azure Backup-agenten med SCDPM och gör att du kan skydda filer/mappar, program och arbetsbelastningar och virtuella datorer i Azure direkt från SCDPM-hanteringskonsolen. Installera inte SCDPM efter det att du har installerat Azure Backup-agenten för ovan nämnda ändamål. Detta varken rekommenderas eller stöds.

## <a name="what-is-the-length-of-file-path-that-can-be-specified-as-part-of-azure-backup-policy-using-azure-backup-agent?-<br/>"></a>Vilken längd på filsökvägar stöds i Azure Backup-principen när Azure Backup-agenten används? <br/>  
Azure Backup-agenten använder NTFS. [Specifikationen för filsökvägarnas längd begränsas av Windows-API:et](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Om du behöver säkerhetskopiera filer med sökvägar som är längre än vad som tillåts av Windows-API:et kan du välja att säkerhetskopiera den överordnade mappen eller diskenheten där filerna finns.  

## <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent?-<br>"></a>Vilka tecken tillåts i filsökvägar i Azure Backup-principen när Azure Backup-agenten används? <br>  
 Azure Backup-agenten använder NTFS. [Tecken som stöds av NTFS](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) stöds enligt filspecifikationen.  

## <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-(bmr)-backup-for-a-physical-server?-<br/>"></a>Kan jag använda Azure Backup Server för att skapa en BMR-säkerhetskopia (Bare Metal Recovery) för en fysisk server? <br/>
Ja.

## <a name="can-i-configure-the-backup-service-to-send-mail-if-a-backup-job-fails?-<br/>"></a>Kan jag konfigurera Backup-tjänsten så att ett e-postmeddelande skickas om ett säkerhetskopieringsjobb misslyckas? <br/>
Ja, Backup-tjänsten har flera händelsebaserade aviseringar som du kan använda med PowerShell-skript. En fullständig beskrivning finns i [Varningsmeddelanden](backup-azure-manage-vms.md#alert-notifications)

## <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up?-<br/>"></a>Finns det någon storleksgräns för en datakälla som säkerhetskopieras? <br/>
Även om det på valvnivå inte finns någon gräns för den mängd data som du kan säkerhetskopiera, så tillämpar Azure Backup en begränsning när det gäller datakällans maximala storlek (men för alla praktiska syften har gränsen satts mycket högt). Från och med augusti 2015 är den största storleken på datakällor för operativsystem som stöds:

|Nr | Operativsystem |  Största storlek på datakälla |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 eller senare| 54 400 GB|
|2| Windows 8 eller senare| 54 400 GB|
|3| Windows Server 2008, Windows Server 2008 R2 | 1 700 GB|
|4| Windows 7 | 1 700 GB|

Följande tabell beskriver hur datakällans storlek bestäms.

|   Datakälla  |   Detaljer |
| :-------------: |:-------------|
|Volym |Mängden data som säkerhetskopieras från en enskild volym på en server eller klientdator|
|Virtuell Hyper-V-dator | Summan av data på alla virtuella hårddiskar på den virtuella datorn som säkerhetskopieras|
|Microsoft SQL Server-databas | Storleken på en enskild SQL-databas som säkerhetskopieras |
|Microsoft SharePoint |Summan av innehållet och konfigurationsdatabaserna i en SharePoint-servergrupp som säkerhetskopieras|
|Microsoft Exchange |Summan av alla Exchange-databaser på en Exchange-server som säkerhetskopieras|
|BMR/systemtillstånd |Varje enskild kopia av BMR eller systemtillstånd på datorn som säkerhetskopieras|

## <a name="are-there-limits-on-the-number-of-times-a-backup-job-can-be-scheduled-per-day?<br/>"></a>Finns det någon gräns för hur många gånger ett säkerhetskopieringsjobb kan schemaläggas per dag?<br/>
Ja, du kan köra säkerhetskopieringsjobb på Windows Server eller Windows-klienten upp till tre gånger per dag. Du kan köra säkerhetskopieringsjobb i System Center DPM upp till två gånger om dagen. Du kan köra ett säkerhetskopieringsjobb för virtuella IaaS-datorer en gång om dagen.

## <a name="is-there-a-difference-between-the-scheduling-policy-for-dpm-and-windows-server-(i.e.-on-windows-server-without-dpm)?-<br/>"></a>Finns det någon tidsskillnad mellan schemaläggningsprincipen för DPM och Windows Server (dvs. på Windows Server utan DPM)? <br/>
Ja. Med DPM kan du definiera dags-, vecko-, månads- och årsscheman. Med Windows Server (utan DPM) kan du bara definiera dags- och veckoscheman.

## <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-server/client-(i.e.-on-windows-server-without-dpm)?<br/>"></a>Finns det någon skillnad mellan bevarandeprincipen för DPM och Windows Server/Windows-klienten (dvs. på Windows Server utan DPM)?<br/>
Ingen, både DPM och Windows Server/Windows-klienten har dagliga, veckovisa, månatliga och årliga bevarandeprinciper.

## <a name="can-i-configure-my-retention-policies-selectively-–-i.e.-configure-weekly-and-daily-but-not-yearly-and-monthly?<br/>"></a>Kan jag konfigurera mina bevarandeprinciper selektivt, dvs. konfigurera veckovisa och dagliga men inte årliga och månatliga?<br/>
Ja, bevarandestrukturen i Azure Backup är mycket flexibel och du kan definiera bevarandeprincipen efter dina behov.

## <a name="can-i-“schedule-a-backup”-at-6pm-and-specify-“retention-policies”-at-a-different-time?<br/>"></a>Kan jag ”schemalägga en säkerhetskopiering” kl. 18:00 och ange ”bevarandeprinciper” vid en annan tidpunkt?<br/>
Nej. Bevarandeprinciper kan bara användas med säkerhetskopieringspunkter. I följande bild har bevarandeprincipen angetts för säkerhetskopieringar som körs kl. 12:00 och 18:00. <br/>

![Schemalägg säkerhetskopiering och kvarhållning](./media/backup-azure-backup-faq/Schedule.png)
<br/>

## <a name="is-an-incremental-copy-transferred-for-the-retention-policies-scheduled?-<br/>"></a>Överförs en inkrementell kopia för de schemalagda bevarandeprinciperna? <br/>
Nej, den inkrementella kopian skickas baserat på tiden som anges på sidan för säkerhetskopieringsschemat. Vilka punkter som kan bevaras avgörs baserat på bevarandeprincipen.

## <a name="if-a-backup-is-retained-for-a-long-duration,-does-it-take-more-time-to-recover-an-older-data-point?-<br/>"></a>Tar det längre tid att återställa en äldre datapunkt om en säkerhetskopia bevaras under lång tid? <br/>
 Nej. Det tar lika lång tid att återställa den äldsta och den senaste punkten. Varje återställningspunkt beter sig som en fullständig punkt.

## <a name="if-each-recovery-point-is-like-a-full-point,-does-it-impact-the-total-billable-backup-storage?<br/>"></a>Om varje återställningspunkt fungerar som en fullständig punkt, påverkas i så fall den totalt fakturerbara lagringen av säkerhetskopior?<br/>
Typiska produkter för långsiktiga kvarhållningspunkter lagrar säkerhetskopierade data som fullständiga punkter. De fullständiga punkterna är *ineffektiva* ur lagringssynvinkel men är enklare och snabbare att återställa. Inkrementella kopior är *lagringseffektiva* men kräver att du återställer en kedja med data som påverkar återställningstiden. Azure Backup-lagringsarkitekturen ger dig det bästa av två världar genom att lagra data för snabb återställning till låga lagringskostnader. Den här datalagringsmetoden säkerställer att den in- och utgående bandbredden används effektivt. Både mängden datalagring och tiden som krävs för att återställa data hålls nere på ett minimum. Läs mer om hur [inkrementell säkerhetskopiering](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) är effektiv. 

## <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created?<br/>"></a>Finns det någon gräns för antalet återställningspunkter som kan skapas?<br/>
Nej. Vi har tagit bort gränserna för återställningspunkter. Du kan skapa så många återställningspunkter du vill.

## <a name="why-is-the-amount-of-data-transferred-in-backup-not-equal-to-the-amount-of-data-i-backed-up?<br/>"></a>Varför är mängden data som överförs under en säkerhetskopiering inte samma som mängden säkerhetskopierade data?<br/>
 Alla de data som säkerhetskopieras från Azure Backup Agent, SCDPM eller Azure Backup Server komprimeras och krypteras innan de överförs. När komprimering och kryptering tillämpats är storleken på data i säkerhetskopieringsvalvet 30–40 % mindre.

## <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-service?<br/>"></a>Kan jag justera mängden bandbredd som används av Backup-tjänsten?<br/>
 Ja, använd alternativet **Ändra egenskaper** i Backup-agenten om du vill justera bandbredden. Justera mängden bandbredd och tiderna då du använder den bandbredden. Mer information finns i [Nätverksbegränsning](../backup-configure-vault.md#enable-network-throttling).

## <a name="my-internet-bandwidth-is-limited-for-the-amount-of-data-i-need-to-back-up.-is-there-a-way-i-can-move-data-to-a-certain-location-with-a-large-network-pipe-and-push-that-data-into-azure?-<br/>"></a>Min Internetbandbredd är begränsad för den mängd data som jag behöver säkerhetskopiera. Kan jag flytta data till en plats med en stor nätverkspipeline och skicka dessa data till Azure? <br/>
Du kan säkerhetskopiera data till Azure via standardprocessen för säkerhetskopiering online eller använda tjänsten Azure Import/Export för att överföra data till Blob Storage i Azure. Det finns inga andra sätt att skicka säkerhetskopierade data till Azure-lagring. Information om hur du använder tjänsten Azure Import/Export med Azure Backup finns i artikeln [Arbetsflöde för säkerhetskopiering offline](backup-azure-backup-import-export.md).

## <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azure?<br/>"></a>Hur många återställningar kan jag göra av data som säkerhetskopierats till Azure?<br/>
Det finns ingen gräns för antalet återställningar från Azure Backup.

## <a name="do-i-have-to-pay-for-the-egress-traffic-from-azure-data-center-during-recoveries?<br/>"></a>Måste jag betala för utgående trafik från Azure-datacentret under återställningar?<br/>
 Nej. Återställningarna är gratis och du debiteras inte för den utgående trafiken.

## <a name="is-the-data-sent-to-azure-encrypted?-<br/>"></a>Krypteras informationen som skickas till Azure? <br/>
Ja. Data krypteras på den lokala server-/klient-/SCDPM-datorn med hjälp av AES256 och informationen skickas via en säker HTTPS-anslutning.

## <a name="is-the-backup-data-on-azure-encrypted-as-well?<br/>"></a>Är säkerhetskopierade data i Azure också krypterade?<br/>
 Ja. Data som skickas till Azure förblir krypterade (i vila). Microsoft dekrypterar aldrig dina säkerhetskopierade data. När det gäller säkerhetskopiering av virtuella Azure-datorer så använder sig Azure Backup av kryptering av den virtuella datorn. Det innebär att om din virtuella dator har krypterats med Azure Disk Encryption eller någon annan krypteringsteknik, så Azure Backup den krypteringen för att skydda dina data. 

## <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data?-<br/>"></a>Vilken är den minsta längden på krypteringsnyckeln som används för att kryptera säkerhetskopierade data? <br/>
 Krypteringsnyckeln ska vara minst 16 tecken.

## <a name="what-happens-if-i-misplace-the-encryption-key?-can-i-recover-the-data-(or)-can-microsoft-recover-the-data?-<br/>"></a>Vad händer om jag tappar bort krypteringsnyckeln? Kan jag (eller Microsoft) återställa mina data? <br/>
Nyckeln som används för att kryptera säkerhetskopierade data finns bara hos kunden. Microsoft sparar ingen kopia i Azure och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan inte Microsoft återställa dina säkerhetskopierade data.

## <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agent?<br/>"></a>Hur ändrar jag cachelagringsplatsen för Azure Backup-agenten?<br/>
 Gå igenom punktlistan nedan en punkt i taget om du vill ändra plats för cachelagringen.
- Stoppa Backup-motorn genom att köra följande kommando från en upphöjd kommandotolk:

  ```PS C:\> Net stop obengine```

- Flytta inte filerna. Kopiera i stället cachelagringsmappen till en annan enhet med tillräckligt med utrymme. Du kan ta bort det ursprungliga cachelagringsutrymmet när du har bekräftat att säkerhetskopieringarna fungerar med den nya cachelagringsplatsen.

- Uppdatera följande registerposter med sökvägen till den nya cachelagringsmappen.<br/>

|Sökväg i registret | Registernyckel | Värde |
| ------ | ------- | ------|
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | *Ny plats för cachemappen* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | *Ny plats för cachemappen* |

- Starta om Backup-motorn genom att köra följande kommando från en upphöjd kommandotolk:

  ```PS C:\> Net start obengine```

  När säkerhetskopian har skapats på den nya cachelagringsplatsen kan du ta bort den ursprungliga cachelagringsmappen.

## <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expected?<br/>"></a>Var kan jag placera cachelagringsmappen så att Azure Backup Agent fungerar korrekt?<br/>
Följande platser rekommenderas inte för cachelagringsmappen:

- Nätverksresurs eller flyttbart medium: Cachelagringsmappen måste vara lokal på servern som ska säkerhetskopieras med onlinesäkerhetskopiering. Nätverksplatser eller flyttbara medier som USB-enheter stöds inte.
- Offlinevolymer: Cachelagringsmappen måste vara online för säkerhetskopiering med Azure Backup Agent.

## <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supported?<br/>"></a>Finns det några attribut för cachelagringsmappen som inte stöds?<br/>
 Följande attribut eller deras kombinationer stöds inte för cachelagringsmappen:

- Krypterade
- Deduplicerade
- Komprimerade
- Utspridda
- Referenspunkt

Vi rekommenderar att du inte använder attributen ovan för cachelagringsmappen eller för den virtuella hårddisken med metadata för att Azure Backup-agenten ska fungera korrekt.



<!--HONumber=Oct16_HO3-->


