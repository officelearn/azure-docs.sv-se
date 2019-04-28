---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 6f0d2d59ed50c743adb19027c404bfa83a1886f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485618"
---
Beroende på din miljö och alternativ, kan skriptet Skapa hela klustret infrastrukturen, inklusive Azure-nätverk, lagringskonton, molntjänster, domänkontrollant, fjärrdatorer eller lokala SQL-databaser, huvudnod och ytterligare klusternoder. Skriptet kan också använda redan existerande Azure-infrastrukturen och skapa endast noderna för HPC-kluster.

Bakgrundsinformation om hur du planerar ett HPC Pack-kluster finns i den [produktutvärdering och planera](https://technet.microsoft.com/library/jj899596.aspx) och [komma igång](https://technet.microsoft.com/library/jj899590.aspx) innehåll i TechNet-biblioteket HPC Pack 2012 R2.

## <a name="prerequisites"></a>Nödvändiga komponenter
* **Azure-prenumeration**: Du kan använda en prenumeration i tjänsten Azure Global eller Azure Kina. Din prenumerationsbegränsningar påverkar antalet och typen av klusternoder som du kan distribuera. Mer information finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../articles/azure-subscription-service-limits.md).
* **Windows-klientdator med Azure PowerShell 0.8.10 eller senare installerat och konfigurerat**: Se [Kom igång med Azure PowerShell](/powershell/azureps-cmdlets-docs) installationsinstruktioner och stegen att ansluta till din Azure-prenumeration.
* **HPC Pack IaaS-distributionsskriptet**: Hämta och packa upp den senaste versionen av skriptet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Kontrollera vilken version av skriptet genom att köra `New-HPCIaaSCluster.ps1 –Version`. Den här artikeln är baserad på version 4.5.2 av skriptet.
* **Skriptet konfigurationsfilen**: Skapa en XML-fil som skriptet använder för att konfigurera HPC-kluster. Information och exempel finns i avsnitten senare i den här artikeln och filen Manual.rtf som medföljer distributionsskriptet.

## <a name="syntax"></a>Syntax
```powershell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> Kör skriptet som administratör.
> 
> 

### <a name="parameters"></a>Parametrar
* **ConfigFile**: Anger sökvägen till konfigurationsfilen för att beskriva HPC-kluster. Mer information om konfigurationsfil i det här avsnittet eller i filen Manual.rtf i mappen som innehåller skriptet.
* **AdminUserName**: Anger användarnamnet. Om domänskogen har skapats av skriptet, blir det lokalt administratörsanvändarnamn för alla virtuella datorer och domännamn för administratören. Om domänskogen redan finns anger detta domänanvändaren som användarnamn för lokal administratör för att installera HPC Pack.
* **AdminPassword**: Anger administratörens lösenord. Om inte anges på kommandoraden, uppmanas du att ange lösenordet i skriptet.
* **HPCImageName** (valfritt): Anger Avbildningsnamnet HPC Pack VM används för att distribuera HPC-kluster. Det måste vara en Microsoft HPC Pack-avbildning från Azure Marketplace. Om du inte har angetts (rekommenderas vanligtvis) skriptet väljer den senaste publicerade [HPC Pack 2012 R2-avbildning](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/). Den senaste avbildningen baseras på Windows Server 2012 R2 Datacenter med HPC Pack 2012 R2 uppdatering 3.
  
  > [!NOTE]
  > Distributionen misslyckas om du inte anger en giltig HPC Pack-bild.
  > 
  > 
* **LogFile** (valfritt): Anger sökvägen till distribution loggfilen. Om inte anges i skriptet skapar en loggfil i temp-katalogen på datorn som kör skriptet.
* **Framtvinga** (valfritt): Ignorerar alla bekräftelsemeddelanden.
* **NoCleanOnFailure** (valfritt): Anger att den virtuella Azure-datorer som inte har distribuerat inte tas bort. Ta bort de virtuella datorerna manuellt innan du köra skriptet för att fortsätta med distributionen eller distributionen misslyckas.
* **PSSessionSkipCACheck** (valfritt): För varje molntjänst med virtuella datorer som distribueras med det här skriptet ett självsignerat certifikat genereras automatiskt av Azure och alla de virtuella datorerna i Molntjänsten använder det här certifikatet som standardcertifikatet för Windows Remote Management (WinRM). För att distribuera HPC-funktioner i dessa virtuella datorer i Azure, skript som standard installeras tillfälligt certifikaten i den lokala datorn\\betrodda rotcertifikatutfärdare på klientdatorn att utelämna säkerhetsfel ”ej betrodd Certifikatutfärdare” under skriptkörningen. Certifikaten tas bort när skriptet är färdigt. Om den här parametern anges certifikat som inte har installerats på klientdatorn och visas inte säkerhetsvarningen.
  
  > [!IMPORTANT]
  > Den här parametern rekommenderas inte för distributioner av produktion.
  > 
  > 

### <a name="example"></a>Exempel
I följande exempel skapas ett HPC Pack-kluster med hjälp av konfigurationsfilen *MyConfigFile.xml*, och anger autentiseringsuppgifter för att installera klustret.

```powershell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Annat som är bra att tänka på
* Skriptet kan välja att jobböverföring via webbportalen HPC Pack eller HPC Pack REST API.
* Skriptet kan du kan också köra anpassade skript för före och efter konfigurationen på huvudnoden om du vill installera ytterligare programvara eller konfigurera andra inställningar.

## <a name="configuration-file"></a>Konfigurationsfil
Konfigurationsfilen för distributionsskriptet är en XML-fil. Schemafilen HPCIaaSClusterConfig.xsd är i skriptet för HPC Pack IaaS distributionsmappen. **IaaSClusterConfig** är rotelementet i konfigurationsfilen, som innehåller de underordnade elementen som beskrivs i detalj i filen Manual.rtf i skriptet distributionsmappen.

