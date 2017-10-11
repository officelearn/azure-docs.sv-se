



Beroende på din miljö och alternativ, kan skriptet Skapa hela klustret infrastrukturen, inklusive Azure-nätverk, storage-konton, molntjänster, domänkontrollant, fjärrdatorer eller lokala SQL-databaser, huvudnod och ytterligare klusternoder. Skriptet kan också använda befintlig Azure-infrastrukturen och skapa endast noderna för HPC-kluster.

Bakgrundsinformation om hur du planerar ett HPC Pack-kluster finns i [produktutvärdering och planera](https://technet.microsoft.com/library/jj899596.aspx) och [komma igång](https://technet.microsoft.com/library/jj899590.aspx) innehåll i TechNet-biblioteket HPC Pack 2012 R2.

## <a name="prerequisites"></a>Krav
* **Azure-prenumeration**: du kan använda en prenumeration i Azure Global eller Azure Kina service. Din prenumerationsbegränsningar påverkar antalet och typen av klusternoder som du kan distribuera. Mer information finns i [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../articles/azure-subscription-service-limits.md).
* **Windows-klientdator med Azure PowerShell 0.8.10 eller senare installerat och konfigurerat**: se [Kom igång med Azure PowerShell](/powershell/azureps-cmdlets-docs) installationsinstruktioner och anvisningar att ansluta till din Azure-prenumeration.
* **HPC Pack IaaS distributionsskriptet**: hämta och packa upp den senaste versionen av skriptet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Kontrollera vilken version av skriptet genom att köra `New-HPCIaaSCluster.ps1 –Version`. Den här artikeln baserat på version 4.5.2 av skriptet.
* **Konfigurationsfilen**: skapa en XML-fil som skriptet använder för att konfigurera HPC-kluster. Mer information och exempel finns i avsnitt längre fram i den här artikeln och filen Manual.rtf som medföljer skriptet för distribution.

## <a name="syntax"></a>Syntax
```PowerShell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> Kör skriptet som administratör.
> 
> 

### <a name="parameters"></a>Parametrar
* **ConfigFile**: Anger sökvägen till filen i konfigurationsfilen för att beskriva HPC-kluster. Läs mer om konfigurationsfil i det här avsnittet eller i filen Manual.rtf i mappen som innehåller skriptet.
* **AdminUserName**: Anger användarnamnet. Om domänskogen har skapats av skriptet, blir det lokalt administratörsanvändarnamn för alla virtuella datorer och administratören domännamn. Om domänskogen redan anger domänanvändaren som användarnamn för lokal administratör för att installera HPC Pack.
* **AdminPassword**: Anger administratörslösenord. Om inte anges på kommandoraden, uppmanas du att ange lösenordet i skriptet.
* **HPCImageName** (valfritt): Anger HPC Pack VM avbildning används för att distribuera HPC-kluster. Det måste vara en Microsoft HPC Pack-avbildning från Azure Marketplace. Om inget annat anges (rekommenderas vanligtvis) skriptet väljer det senaste publicerade [HPC Pack 2012 R2-avbildningen](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/). Senaste avbildningen baseras på Windows Server 2012 R2 Datacenter med HPC Pack 2012 R2 uppdatering 3.
  
  > [!NOTE]
  > Distributionen misslyckas om du inte anger en giltig HPC Pack-bild.
  > 
  > 
* **LogFile** (valfritt): Anger sökvägen till distributionen loggfilen. Om inte har angetts för skriptet skapar en loggfil i temp-katalogen på datorn som kör skriptet.
* **Framtvinga** (valfritt): förhindrar att alla bekräftelse-meddelanden.
* **NoCleanOnFailure** (valfritt): Anger att den virtuella Azure-datorer som inte distribueras har inte tagits bort. Ta bort dessa virtuella datorer manuellt innan du köra skriptet för att fortsätta med distributionen eller distributionen misslyckas.
* **PSSessionSkipCACheck** (valfritt): för varje molntjänst med virtuella datorer som distribueras med det här skriptet, skapas automatiskt ett självsignerat certifikat av Azure och alla virtuella datorer i Molntjänsten använda det här certifikatet som standard Windows Remote Certifikat Management (WinRM). Om du vill distribuera HPC-funktioner i dessa virtuella datorer i Azure, skriptet som standard installeras tillfälligt certifikaten i den lokala datorn\\betrodda rotcertifikatutfärdare på klientdatorn för att undertrycka säkerhetsfel ”inte betrodd Certifikatutfärdare” under körning av skript. Certifikat tas bort när skriptet har slutförts. Om den här parametern anges certifikaten har installerats inte på klientdatorn och visas inte säkerhetsvarningen.
  
  > [!IMPORTANT]
  > Den här parametern rekommenderas inte för Produktionsdistribution.
  > 
  > 

### <a name="example"></a>Exempel
I följande exempel skapas ett HPC Pack kluster som använder konfigurationsfilen *MyConfigFile.xml*, och anger administratörsautentiseringsuppgifter som för att installera klustringen.

```PowerShell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Annat som är bra att tänka på
* Skriptet kan också aktivera jobbet via webbportalen HPC Pack eller HPC Pack REST API.
* Skriptet kan du köra skript före och efter konfigurationen på huvudnoden om du vill installera ytterligare programvara eller konfigurera andra inställningar.

## <a name="configuration-file"></a>Konfigurationsfilen
Konfigurationsfilen för distributionsskriptet är en XML-fil. Schemafilen HPCIaaSClusterConfig.xsd finns i mappen HPC Pack IaaS distribution skript. **IaaSClusterConfig** har rotelementet i en konfigurationsfil som innehåller de underordnade elementen som beskrivs i detalj i filen Manual.rtf i skriptet distributionsmappen.

