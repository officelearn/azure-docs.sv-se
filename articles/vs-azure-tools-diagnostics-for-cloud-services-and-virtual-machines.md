---
title: Konfigurera diagnostik för Azure Cloud Services och virtuella datorer | Microsoft Docs
description: Lär dig hur du ställer in diagnostik för felsökning Azure cloude tjänster och virtuella datorer (VM) i Visual Studio.
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: douge
editor: ''
ms.assetid: e70cd7b4-6298-43aa-adea-6fd618414c26
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/28/2018
ms.author: mikejo
ms.openlocfilehash: 8f32165a7a54ead06d57a3d8d1b4282498dca635
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969617"
---
# <a name="set-up-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Konfigurera diagnostik för Azure Cloud Services och virtuella datorer
När du behöver felsöka en Azure-molntjänst eller virtuell dator använder du Visual Studio att enkelt konfigurera Azure Diagnostics. Diagnostics samlar in systemdata och loggningsdata på virtuella datorer och instanser av virtuella datorer som kör din molntjänst. Diagnostics-data överförs till ett lagringskonto som du väljer. Läs mer om diagnostik loggning i Azure, i [aktivera diagnostikloggning för Webbappar i Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

I den här artikeln visar vi dig hur du använder Visual Studio för att aktivera och konfigurera Azure Diagnostics både före och efter distributionen. Lär dig hur du ställer in diagnostik på Azure virtual machines, så Välj typerna av diagnostikinformation att samla in och visa informationen när den har samlats in.

Du kan använda något av följande alternativ för att konfigurera Azure Diagnostics:

* Ändra inställningarna för startdiagnostik i den **Diagnostiky** dialogrutan i Visual Studio. Inställningarna sparas i en fil med namnet diagnostics.wadcfgx (i Azure SDK 2.4 och tidigare filen kallas diagnostics.wadcfg). Du också kan du direkt ändra konfigurationsfilen. Om du manuellt uppdatera filen configuration ändringarna gälla nästa gång du distribuerar molnet till Azure-tjänsten eller köra tjänsten i emulatorn.
* Använd Cloud Explorer eller Server Explorer i Visual Studio för att ändra inställningarna för startdiagnostik för en molntjänst eller virtuell dator som körs.

## <a name="azure-sdk-26-diagnostics-changes"></a>Azure SDK 2.6 diagnostik ändringar
Följande ändringar gäller för Azure SDK 2.6 och senare projekt i Visual Studio:

* Den lokala emulatorn har nu stöd för diagnostik. Det innebär att du kan samla in diagnostikdata och se till att ditt program skapar rätt spårningarna när du utvecklar och testar i Visual Studio. Anslutningssträngen `UseDevelopmentStorage=true` aktiverar diagnostik datainsamling när du kör cloud service-projekt i Visual Studio med hjälp av Azure storage-emulatorn. Alla diagnostics-data som samlas in i Utvecklingslagring storage-konto.
* Diagnostik konto lagringsanslutningssträngen `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` lagras i tjänstekonfigurationsfilen (.cscfg). Lagringskonto för startdiagnostik har angetts i filen diagnostics.wadcfgx i Azure SDK 2.5.

Anslutningssträngen fungerar på olika sätt på vissa sätt i Azure SDK 2.6 och senare jämfört med Azure SDK 2.4 och tidigare:

* I Azure SDK 2.4 och tidigare används anslutningssträngen som en körning av diagnostik plugin-programmet för att hämta information om storage-konto för att överföra diagnostikloggar.
* I Azure SDK 2.6 och senare, använder Visual Studio anslutningssträngen diagnostik för att konfigurera Azure Diagnostics-tillägget med lämpliga lagringskontoinformation vid publicering. Du kan använda anslutningssträngen för att definiera olika lagringskonton för olika konfigurationer som Visual Studio använder vid publicering. Men eftersom diagnostiken plugin-programmet inte är tillgänglig när du har Azure SDK 2.5 .cscfg-filen påverkar i sig kan inte konfigurera diagnostics-tillägg. Du måste ställa in tillägget separat med hjälp av verktyg som Visual Studio eller PowerShell.
* För att förenkla processen för att ställa in diagnostiktillägget med hjälp av PowerShell, innehåller paketet utdata från Visual Studio den offentliga konfigurationen-XML för diagnostics-tillägg för varje roll. Visual Studio använder anslutningssträngen diagnostik för att fylla i lagringskontoinformation i offentliga konfigurationen. Offentliga config-filer skapas i mappen tillägg. Offentliga config-filerna använder namngivningsmönstret PaaSDiagnostics. &lt;rollnamn\>. PubConfig.xml. Alla PowerShell-baserade distributioner kan använda det här mönstret för att mappa varje konfiguration tilldelas en roll.
* Den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040) använder anslutningssträngen i .cscfg-filen för att komma åt diagnostics-data. Informationen som visas på den **övervakning** fliken. Strängen som krävs för att ställa in tjänsten för att visa utförlig övervakningsdata i portalen.

## <a name="migrate-projects-to-azure-sdk-26-and-later"></a>Migrera projekt till Azure SDK 2.6 och senare
När du migrerar från Azure SDK 2.5 Azure SDK 2.6 eller senare om du hade en diagnostiklagringskonto som anges i filen .wadcfgx kvar storage-konto i filen. Om du vill dra nytta av flexibiliteten i att använda olika konton för olika lagringskonfigurationer, manuellt lägga till anslutningssträngen i projektet. Om du migrerar ett projekt från Azure SDK 2.4 eller tidigare till Azure SDK 2.6, bevaras anslutningssträngar för diagnostik. Observera dock att ändras i hur anslutningssträngar behandlas i Azure SDK 2.6, som beskrivs i föregående avsnitt.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hur Visual Studio anger lagringskonto för diagnostik
* Om en anslutningssträng för diagnostik har angetts i .cscfg-filen använder den för att ställa in diagnostiktillägget vid publicering och när den skapar XML-filer för offentliga konfiguration under paketering i Visual Studio.
* Om en anslutningssträng för diagnostik inte har angetts i .cscfg-filen, återgår Visual Studio till att använda det lagringskonto som anges i filen .wadcfgx att ställa in diagnostiktillägget för publicering samt för att generera den offentliga konfigurationen-XML filer under paketering.
* Diagnostik-anslutningssträngen i .cscfg-filen har företräde framför storage-konto i filen .wadcfgx. Om en anslutningssträng för diagnostik är anges i .cscfg-filen Visual Studio använder denna anslutningssträng och ignorerar lagringskontot i .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-check-box-do"></a>Vad gör kryssrutan ”Uppdatera utveckling storage-anslutningssträngar...”?
Den **uppdatera utveckling storage-anslutningssträngar för diagnostik- och cachelagring med autentiseringsuppgifterna för Microsoft Azure storage-konto när du publicerar till Microsoft Azure** kryssrutan är ett praktiskt sätt att uppdatera alla utvecklingslagring anslutning till strängar med Azure storage-kontot som du anger under publiceringen.

Till exempel om du väljer den här kryssrutan och diagnostik anslutningssträngen anger `UseDevelopmentStorage=true`, när du publicerar projektet till Azure, Visual Studio automatiskt uppdaterar anslutningssträngen diagnostik med storage-konto som du angav i guiden Publicera. Men om en verklig storage-konto har angetts som anslutningssträng diagnostik används kontot i stället.

## <a name="diagnostics-functionality-differences-in-azure-sdk-24-and-earlier-vs-azure-sdk-25-and-later"></a>Diagnostik funktioner skillnaderna i Azure SDK 2.4 och jämfört med tidigare. Azure SDK 2.5 och senare
Om du uppgraderar ditt projekt från Azure SDK 2.4 och tidigare till Azure SDK 2.5 eller senare, Tänk på följande sätt för diagnostik-funktioner:

* **Konfiguration av API: er är inaktuella**. Programkonfiguration av diagnostik är tillgängliga i Azure SDK 2.4 och tidigare, men är inaktuell i Azure SDK 2.5 och senare. Om din diagnostik-konfiguration för närvarande har definierats i koden, måste du konfigurera om dessa inställningar från början i migrerade projektet för diagnostik kan fortsätta att arbeta. Diagnostik-konfigurationsfilen för Azure SDK 2.4 är diagnostics.wadcfg. Konfigurationsfilen diagnostik för Azure SDK 2.5 och senare är diagnostics.wadcfgx.
* **Diagnostik för molnprogram för tjänsten kan konfigureras bara på rollnivå**. I Azure SDK 2.5 och senare, kan inte du konfigurera diagnostik för tjänsten molnprogram på instansnivå.
* **Varje gång som du har distribuerat din app diagnostikkonfigurationen uppdateras**. Om du ändrar konfigurationen diagnostik från Visual Studio Server Explorer och distribuera om din app kan det medföra problem för paritet.
* **I Azure SDK 2.5 och senare, kraschdumpar är konfigurerade i konfigurationsfilen för diagnostik och inte i kod**. Om du har kraschdumpar som konfigurerats i koden kan överför du manuellt konfigurationen från kod till konfigurationsfilen. Kraschdumpar överförs inte under migreringen till Azure SDK 2.6.

## <a name="turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them"></a>Slå på diagnostik i molntjänstprojekt innan du distribuerar dem.
I Visual Studio kan du samla in diagnostikdata för roller som körs i Azure när du kör tjänsten i emulatorn före distributionen. Alla ändringar i inställningarna för startdiagnostik i Visual Studio har sparats i konfigurationsfilen diagnostics.wadcfgx. Dessa inställningar anger det lagringskonto där diagnostics-data sparas när du distribuerar din molntjänst.

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-turn-on-diagnostics-in-visual-studio-before-deployment"></a>Aktivera diagnostik i Visual Studio före distributionen

1. På snabbmenyn för rollen, Välj **egenskaper**. I rollen **egenskaper** dialogrutan den **Configuration** fliken.
2. I den **diagnostik** avsnittet, se till att den **aktivera diagnostik** kryssrutan är markerad.
   
    ![Komma åt alternativet Aktivera diagnostik](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. Välj knappen med tre punkter (...) för att ange lagringskontot för diagnostikdata.
   
    ![Ange konto för att använda](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. I den **skapa Lagringsanslutningssträng** dialogrutan anger du om du vill ansluta med hjälp av Azure storage-emulatorn, en Azure-prenumeration eller angett autentiseringsuppgifterna manuellt.
   
    ![Dialogrutan för Storage-konto](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * Om du väljer **Microsoft Azure storage-emulatorn**, anslutningssträngen har angetts till `UseDevelopmentStorage=true`.
   * Om du väljer **prenumerationen**, kan du ange den prenumeration som du vill använda och ange ett kontonamn. För att hantera dina Azure-prenumerationer, Välj **hantera konton**.
   * Om du väljer **angett autentiseringsuppgifterna manuellt**, ange namnet och nyckeln för Azure-konto som du vill använda.
5. Visa den **diagnostiky** dialogrutan **konfigurera**. Undantag för **Allmänt** och **Loggkatalogerna**, varje flik representerar en diagnostik-datakälla som du kan samla in. Standard **Allmänt** fliken erbjuder följande diagnostiken samling Dataalternativ: **endast fel**, **All information**, och **anpassad plan**. Standard **endast fel** alternativet använder minsta möjliga storage, eftersom det inte överföra varningar och spårning av meddelanden. Den **All information** alternativet överför mycket mer information, använder de lagring och därför är det dyraste alternativet.

   > [!NOTE]
   > Minsta storlek som stöds för ”Disk kvoten i MB” är 4GB. Men om du kan samla in minnesdumpar, öka det till ett högre värde som 10GB.
   >
  
    ![Aktivera Azure-diagnostik och konfiguration](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. Det här exemplet väljer du den **anpassad plan** alternativ, så du kan anpassa insamlade data.
7. I den **diskkvot i MB** kan du ange hur mycket utrymme för att allokera i ditt storage-konto för diagnostikdata. Du kan ändra eller godkänner du standardvärdet.
8. På varje flik till diagnostikdata som du vill samla in, väljer den **Aktivera överföring av \<loggtyp\>**  markerar du kryssrutan. Exempel: Om du vill samla in Programloggar, på den **programloggar** fliken den **överföring av programloggar** markerar du kryssrutan. Dessutom ange annan information som krävs av varje datatyp för diagnostik. Konfigurationsinformation för varje flik, finns i avsnittet **konfigurera diagnostik datakällor** senare i den här artikeln. 
9. När du har aktiverat insamling av all diagnostikdata som du vill välja **OK**.
10. Kör Azure cloud service-projekt i Visual Studio som vanligt. När du använder programmet sparas logginformation som du har aktiverat Azure storage-kontot som du har angett.

## <a name="turn-on-diagnostics-on-azure-virtual-machines"></a>Slå på diagnostik på Azure virtual machines
I Visual Studio kan du samla in diagnostikdata för Azure-datorer.

### <a name="to-turn-on-diagnostics-on-azure-virtual-machines"></a>Aktivera diagnostik i Azure virtual machines

1. Välj Azure-nod i Server Explorer och sedan ansluta till din Azure-prenumeration om du inte redan är ansluten.
2. Expandera den **virtuella datorer** noden. Du kan skapa en ny virtuell dator eller välj en befintlig nod.
3. På snabbmenyn för den virtuella datorn som du vill välja **konfigurera**. Dialogrutan för konfiguration av virtuell dator visas.
   
    ![Konfigurera en Azure virtuell dator](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Om den inte redan är installerad lägger du till Microsoft Monitoring Agent Diagnostics-tillägg. Du kan använda det här tillägget för att samla in diagnostikdata för Azure-dator. Under **installerat tillägg**i den **markerar du tillägget tillgängliga** nedrullningsbara listrutan Välj **Microsoft Monitoring Agent diagnostik**.
   
    ![Installera ett tillägg för virtuell Azure-dator](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
    > [!NOTE]
   > Det finns andra diagnostics-tillägg för dina virtuella datorer. Mer information finns i [VM-tillägg och funktioner i Windows](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).
   > 
   > 
5. Att lägga till tillägget och visa dess **diagnostiky** dialogrutan **Lägg till**.
6. Om du vill ange ett lagringskonto, Välj **konfigurera**, och välj sedan **OK**.
   
    Varje flik (förutom för **Allmänt** och **Loggkatalogerna**) representerar en diagnostik-datakälla som du kan samla in.
   
    ![Aktivera Azure-diagnostik och konfiguration](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    Fliken standard **Allmänt**, finns följande diagnostik data collection alternativ: **endast fel**, **All information**, och **anpassad plan**. Standardalternativet **endast fel**, tar den minsta möjliga storage eftersom det inte överföra varningar och spårning av meddelanden. Den **All information** alternativet överför mycket mer information och därför är det dyraste alternativet när det gäller lagring.
7. Det här exemplet väljer du den **anpassad plan** alternativet så att du kan anpassa data som samlas in.
8. Den **diskkvot i MB** ruta anger hur mycket utrymme som du vill tilldela i ditt storage-konto för diagnostikdata. Du kan ändra standardvärdet om du vill.
9. I varje flik till diagnostikdata som du vill samla in, Välj dess **Aktivera överföring av \<loggtyp\>**  markerar du kryssrutan.
   
    Till exempel om du vill samla in Programloggar, väljer den **överföring av programloggar** kryssrutan på den **programloggar** fliken. Dessutom ange annan information som krävs för varje datatyp för diagnostik. Konfigurationsinformation för varje flik, finns i avsnittet **konfigurera diagnostik datakällor** senare i den här artikeln.
10. När du har aktiverat insamling av all diagnostikdata som du vill välja **OK**.
11. Spara det uppdaterade projektet.
    
    Ett meddelande i den **Microsoft Azure-aktivitetsloggen** fönster visar att den virtuella datorn har uppdaterats.

## <a name="set-up-diagnostics-data-sources"></a>Konfigurera diagnostik-datakällor
När du har aktiverat insamling av diagnostik kan du välja exakt vilka datakällor som du vill samla in och vilken information som samlas in. I nästa avsnitt beskrivs flikarna i den **diagnostiky** dialogrutan och vad varje konfigurationsalternativet innebär.

### <a name="application-logs"></a>Programloggar
Programloggar har diagnostikinformation som produceras av ett webbprogram. Om du vill samla in Programloggar, väljer du den **överföring av programloggar** markerar du kryssrutan. Om du vill öka eller minska tidsintervallet mellan överföringen av programloggar till ditt storage-konto, ändra den **överföring Period (min)** värde. Du kan även ändra hur mycket information som anges i loggen genom att ange den **Certifikatutfärdarnivå** värde. Välj exempelvis **utförlig** att få mer information eller välj **kritisk** avbilda endast kritiska fel. Om du har en programspecifik diagnos-provider som genererar programloggarna kan du samla in loggarna genom att lägga till leverantörens GUID som finns i den **Provider-GUID** box.

  ![Programloggar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

Läs mer om Programloggar, [aktivera diagnostikloggning för Webbappar i Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

### <a name="windows-event-logs"></a>Windows-händelseloggar
Om du vill samla in Windows-händelseloggar, Välj den **överföring av Windows-händelseloggar** markerar du kryssrutan. Om du vill öka eller minska tidsintervallet mellan överföringen av händelseloggar till ditt storage-konto, ändra den **överföring Period (min)** värde. Markera kryssrutorna för typerna av händelser som du vill spåra.

![Händelseloggar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Om du använder Azure SDK 2.6 eller senare och du vill ange en anpassad datakälla, anger du den i den **\<datakällnamn\>** textrutan och välj sedan **Lägg till**. Datakällan har lagts till filen diagnostics.cfcfg.

Om du använder Azure SDK 2.5 och vill ange en anpassad datakälla, du kan lägga till den till den `WindowsEventLog` delen av diagnostics.wadcfgx fil, som i följande exempel:

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Prestandaräknare
Prestandaräknarinformationen kan hjälpa dig hitta systemets flaskhalsar och finjustera systemets och programmets prestanda. Mer information finns i [skapa och använda prestandaräknare i Azure-program](https://msdn.microsoft.com/library/azure/hh411542.aspx). Om du vill samla in prestandaräknare, Välj den **överföring av prestandaräknare** markerar du kryssrutan. Om du vill öka eller minska tidsintervallet mellan överföringen av händelseloggar till ditt storage-konto, ändra den **överföring Period (min)** värde. Markera kryssrutorna för de prestandaräknare som du vill spåra.

![Prestandaräknare](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Ange prestandaräknaren med hjälp av föreslagna syntax för att spåra en prestandaräknare som inte visas. Välj sedan **Lägg till**. Operativsystemet på den virtuella datorn avgör vilka prestandaräknare som du kan spåra. Mer information om syntaxen finns i [anger en räknarsökvägen](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Protokoly infrastruktury
Protokoly infrastruktury har information om Azure-diagnostik infrastrukturen, RemoteAccess-modulen och RemoteForwarder-modulen. Om du vill samla in information om infrastrukturloggar, Välj den **överföring av Infrastrukturloggar** markerar du kryssrutan. Om du vill öka eller minska tidsintervallet mellan överföringen av infrastructure-loggar till ditt storage-konto, ändra den **överföring Period (min)** värde.

![Diagnostikinfrastrukturloggar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

Mer information finns i [samla in loggningsdata med hjälp av Azure Diagnostics](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="log-directories"></a>Loggkatalogerna
Loggkatalogerna som har data som samlas in från loggkatalogerna för Internet Information Services (IIS)-begäranden, misslyckade begäranden eller mappar som du väljer. Om du vill samla in logg kataloger, Välj den **överföring av Loggkatalogerna** markerar du kryssrutan. Om du vill öka eller minska tidsintervallet mellan överföring av loggar till ditt storage-konto, ändra den **överföring Period (min)** värde.

Markera kryssrutorna för loggarna som du vill samla in, till exempel **IIS-loggar** och **det gick inte begära** loggar. Standard storage-behållarnamn anges, men du kan ändra namn.

Du kan samla in loggar från valfri mapp. Ange sökvägen i den **loggen från absolut Directory** avsnittet och välj sedan **Lägg till katalog**. Loggarna samlas i de angivna behållarna.

![Loggkatalogerna](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>ETW-loggar
Om du använder [Event Tracing för Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (ETW) och vill samla in ETW-loggar, Välj den **överföring av ETW-loggar** markerar du kryssrutan. Om du vill öka eller minska tidsintervallet mellan överföring av loggar till ditt storage-konto, ändra den **överföring Period (min)** värde.

Händelserna som avbildas från händelsekällor och händelsemanifest som du anger. Ange en händelsekälla i den **händelsekällor** , ange ett namn och väljer sedan **lägga till händelsekällan**. På samma sätt kan du ange en händelsemanifest i den **Händelsemanifest** avsnittet och välj sedan **Lägg till händelse Manifest**.

![ETW-loggar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

ETW-ramverket stöds i ASP.NET via klasser i den [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) namnområde. Microsoft.WindowsAzure.Diagnostics-namnområde som ärver från och utökar standard [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) klasser, kan du använda [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) som en loggar ramverk i Azure-miljön. Mer information finns i [ta kontroll över loggning och spårning i Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) och [aktivera diagnostik i Azure Cloud Services och virtuella datorer](cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Kraschdumpar
Om du vill samla in information om när en rollinstans kraschar, Välj den **överföring av krascha minnesdumpar** markerar du kryssrutan. (Eftersom ASP.NET hanterar de flesta undantag, är det här brukar vara användbara endast för worker-roller.) Om du vill öka eller minska procentandelen lagringsutrymmet tilldela kraschdumpar, ändra den **Directory kvot (%)** värde. Du kan ändra behållaren där kraschdumpar lagras, och välj om du vill samla in en **fullständig** eller **Mini** dump.

De processer som spåras visas på nästa skärmbild. Markera kryssrutorna för de processer som du vill samla in. Om du vill lägga till en annan process i listan, ange processens namn och välj sedan **lägga till processen**.

![Kraschdumpar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

Mer information finns i [ta kontroll över loggning och spårning i Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) och [Microsoft Azure-diagnostik del 4: Anpassad loggning komponenter och Azure Diagnostics 1.3 ändringar](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## <a name="view-the-diagnostics-data"></a>Visa diagnostikdata
När du har samlat in diagnostikdata för en molntjänst eller virtuell dator, kan du visa den.

### <a name="to-view-cloud-service-diagnostics-data"></a>Visa cloud service diagnostics-data
1. Distribuera din molntjänst som vanligt och sedan köra den.
2. Du kan visa diagnostikdata i en rapport som Visual Studio genererar eller i tabeller i ditt lagringskonto. Att visa data i en rapport, öppen Cloud Explorer eller Server Explorer, öppna snabbmenyn för noden för den roll som du vill och välj sedan **visa diagnostikdata**.
   
    ![Visa diagnostikdata](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    En rapport som visar tillgängliga data visas.
   
    ![Microsoft Azure Diagnostics-rapport i Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    Om de senaste data inte visas, kan du behöva vänta under överföringen aktiviteten ska köras.
   
    För att omedelbart uppdatera data, Välj den **uppdatera** länk. Om du vill att data uppdateras automatiskt, Välj ett intervall i den **automatisk uppdatering** nedrullningsbara listrutan. Om du vill exportera Feldata, Välj den **exportera till CSV** för att skapa en CSV-fil som kan öppnas i ett Excel-kalkylblad.
   
    Öppna storage-konto som är associerat med distributionen i Cloud Explorer eller Server Explorer.
3. Öppna tabellerna diagnostik i visningsprogrammet för tabell och granska sedan de data som du samlat in. Du kan öppna en blob-behållare för IIS-loggar och anpassade loggar. I följande tabell visas de tabeller eller blob-behållare som innehåller data för de olika loggfilerna. Förutom de data för den här loggfilen innehåller posterna i registret **EventTickCount**, **DeploymentId**, **rollen**, och **Rollinstans** , för att identifiera vilken virtuell dator och rollen genereras data, och när. 
   
   | Diagnostikdata | Beskrivning | Plats |
   | --- | --- | --- |
   | Programloggar |Loggar som genereras av din kod genom att anropa metoder i den **System.Diagnostics.Trace** klass. |WADLogsTable |
   | Händelseloggar |Data från Windows-händelseloggarna på de virtuella datorerna. Windows lagrar information i dessa loggar, men program och tjänster också använda loggar att rapportera fel eller logga information. |WADWindowsEventLogsTable |
   | Prestandaräknare |Du kan samla in data på alla prestandaräknare som är tillgänglig på den virtuella datorn. Operativsystemet tillhandahåller prestandaräknare som innehåller många statistik, t.ex. minne användnings- och processor tid. |WADPerformanceCountersTable |
   | Protokoly infrastruktury |Loggarna som genereras från diagnostik infrastrukturen själva. |WADDiagnosticInfrastructureLogsTable |
   | IIS-loggar |Loggar som registrerar webbegäranden. Om din molntjänst får en betydande mängd trafik kan kan dessa loggar vara långa. Det är en bra idé att samla in och lagra dessa data när de behövs. |Du hittar misslyckades begäran loggas i blob-behållare under wad-iis-failedreqlogs under en sökväg för den distribution, rollen och instans. Du hittar en fullständig loggar under wad-iis-loggfiler. Poster för varje fil görs i tabellen WADDirectories. |
   | Kraschdumpar |Innehåller binära avbildningar av din molntjänst-processen (vanligtvis en arbetsroll). |wad-efter-Dumpar blob-behållare |
   | Anpassade loggfiler |Loggarna för data som du fördefinierade. |I koden kan du ange platsen för anpassade loggfiler i lagringskontot. Du kan till exempel ange en anpassad blob-behållare. |
4. Om alla typer av data trunkeras, kan du öka buffertstorleken för dessa data typ eller korta intervall mellan överföring av data från den virtuella datorn till ditt lagringskonto.
5. (Valfritt) Rensa data från storage-kontot ibland för att minska sina sammanlagda lagringskostnader.
6. När du gör en fullständig distribution diagnostics.cscfg-fil (.wadcfgx för Azure SDK 2.5) uppdateras i Azure och din molntjänst hämtar alla ändringar i konfigurationen av diagnostik. Om du uppdaterar en befintlig distribution i stället, uppdateras inte .cscfg-filen i Azure. Du kan fortfarande ändra inställningarna för startdiagnostik, men genom att följa stegen i nästa avsnitt. Mer information om hur du utför en fullständig distribution uppdaterar en befintlig distribution finns i [Publiceringsguiden för Azure Application](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Visa VM diagnostics-data
1. På snabbmenyn för den virtuella datorn och väljer **visa diagnostikdata**.
   
    ![Visa diagnostikdata i Azure-datorer](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    Den **souhrn Diagnostiky** dialogrutan visas.
   
    ![Souhrn diagnostiky för Azure-dator](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    Om de senaste data inte visas, kan du behöva vänta under överföringen aktiviteten ska köras.
   
    För att omedelbart uppdatera data, Välj den **uppdatera** länk. Om du vill att data uppdateras automatiskt, Välj ett intervall i den **automatisk uppdatering** nedrullningsbara listrutan. Om du vill exportera Feldata, Välj den **exportera till CSV** för att skapa en CSV-fil som kan öppnas i ett Excel-kalkylblad.

## <a name="set-up-cloud-service-diagnostics-after-deployment"></a>Konfigurera molntjänstdiagnostik efter distributionen
Om du undersöker ett problem med en molnbaserad tjänst som redan körs kanske du vill samla in data som du inte angav innan du ursprungligen började använda rollen. I det här fallet kan du börja samla in dessa data genom att ändra inställningarna i Server Explorer. Du kan ställa in diagnostik för en enskild instans eller för alla instanser i en roll, beroende på om du öppnar den **Diagnostiky** dialogrutan på snabbmenyn för instansen eller för rollen. Om du konfigurerar rollen noden tillämpas alla ändringar du gör på alla instanser. Om du konfigurerar noden instans, gäller alla ändringar du gör bara den instansen.

### <a name="to-set-up-diagnostics-for-a-running-cloud-service"></a>Konfigurera diagnostik för en aktiv cloud-tjänst
1. I Server Explorer expanderar du den **molntjänster** noden och sedan expandera listan över noder att hitta rollen eller instans (eller båda) som du vill undersöka.
   
    ![Konfigurera diagnostiken](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. På snabbmenyn för en instans noden eller roll, Välj **uppdatera diagnostikinställningar**, och välj sedan de diagnostiska inställningar som du vill samla in.
   
    Information om inställningarna finns i avsnittet **konfigurera diagnostik datakällor** i den här artikeln. Information om hur du visar diagnostics-data finns i avsnittet **visa diagnostics-data** i den här artikeln.
   
    Om du ändrar datainsamling i Server Explorer kan gäller ändringarna tills du fullständigt distribuera om din molntjänst. Om du använder standard Publiceringsinställningar kan ändringarna skrivs över inte. Standard publicera inställningen är att uppdatera den befintliga distributionen, snarare än att göra distribueras på nytt. För att säkerställa att inställningarna avmarkerar vid tidpunkten för distribution, går du till den **avancerade inställningar** fliken i guiden Publicera och avmarkerar sedan den **uppdatering av programdistribution** markerar du kryssrutan. När du distribuerar om med den kryssrutan avmarkerad inställningarna återgå till dem i filen .wadcfgx (eller .wadcfg) som angetts via den **egenskaper** Redigeraren för rollen. Om du uppdaterar din distribution, behåller tidigare inställningar i Azure.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Felsökning av problem med Azure cloud service
Om det uppstår problem med ditt molntjänstprojekt som en roll som fastnar i statusen ”upptagen” flera gånger återanvänds eller genererar ett internt serverfel, finns det verktyg och tekniker som du kan använda för att diagnostisera och åtgärda problemet. Exempel på vanliga problem och lösningar, samt en översikt över de koncept och verktyg som du kan använda för att diagnostisera och åtgärda dessa fel, se [diagnostikdata för Azure PaaS-beräkning](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Frågor och svar
**Vad är buffertstorleken och hur stor ska den vara?**

På varje virtuell datorinstans kvoter som begränsar hur mycket diagnostics-data kan lagras på det lokala filsystemet. Dessutom kan ange du buffertstorleken för varje typ av diagnostikdata som är tillgänglig. Den här buffertstorleken fungerar som en individuell kvot för den typ av data. För att fastställa den övergripande kvoten och mängden minne som finns kvar, se längst ned i dialogrutan för diagnostik-datatyp. Om du anger större buffertar eller fler typer av data, kommer du itu med den övergripande kvoten. Du kan ändra den övergripande kvoten genom att ändra konfigurationsfilen diagnostics.wadcfg eller .wadcfgx. Diagnostics-data lagras på samma filsystem som dina programdata. Om ditt program använder en stor mängd diskutrymme, inte bör du öka övergripande diagnostik kvot.

**Vad är överföring period och hur lång tid ska detta ske?**

Överföringsperioden är hur lång tid som förflutit mellan data samlar in. Efter varje överföringsperioden flyttas data från det lokala filsystemet på en virtuell dator till tabeller i ditt storage-konto. Om mängden data som samlas in överskrider kvoten innan en utgång överföring, ignoreras äldre data. Om du förlorar data eftersom dina data överskrider buffertstorleken eller övergripande kvot, kanske du vill minska överföringsperioden.

**Vilken tidszon är tidsstämplar i?**

Tidsstämplar finns i den lokala tidszonen för det datacenter som är värd för din molntjänst. Följande tre tidsstämpel kolumner i tabellerna i används:

* **PreciseTimeStamp**: The ETW tidsstämpeln för händelsen. Det vill säga den tid som händelsen loggas från klienten.
* **TIDSSTÄMPEL**: värdet för **PreciseTimeStamp** avrundade nedåt uppladdning frekvens gräns. Om din överföringsfrekvensen är 5 minuter och händelsen tid 00:17:12, är TIDSSTÄMPELN 00:15:00.
* **Tidsstämpel**: tidsstämpeln då entiteten skapades i Azure-tabellen.

**Hur hanterar jag kostnader vid insamling av diagnostikinformation?**

Standardinställningarna (**Certifikatutfärdarnivå** inställd **fel**, och **överföring period** inställd **1 minut**) är utformade för att minimera kostnaderna. Dina beräkningskostnader ökar när du samlar in mer diagnostics-data eller om du minskar överföring period. Inte samla in mer data än vad du behöver och Glöm inte att inaktivera insamling av data när du inte längre behöver den. Du kan alltid aktivera den igen, även vid körningstillfället, enligt beskrivningen tidigare i den här artikeln.

**Hur jag för att samla in det gick inte begära loggar från IIS?**

Som standard IIS samlar inte in det gick inte begära loggar. Du kan konfigurera IIS att samla in det gick inte begära loggar genom att redigera filen web.config för webbrollen.

**Jag får inte spårningsinformation från RoleEntryPoint metoder som OnStart. Vad är fel?**

Metoderna i **RoleEntryPoint** anropas i kontexten för WAIISHost.exe inte i IIS. Konfigurationsinformationen i web.config som vanligtvis inte gäller aktiverar spårning. För att lösa problemet, lägga till en .config-filen till din webbrollsprojektet och namnge filen så att den matchar utdata sammansättningen som innehåller den **RoleEntryPoint** kod. Namnet på .config-filen ska vara WAIISHost.exe.config i standard-webbrollsprojektet. Lägg till följande rader i den här filen:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

I den **egenskaper** ställer den **kopiera till utdatakatalog** egenskap **Kopiera alltid**.

## <a name="next-steps"></a>Nästa steg
Läs mer om diagnostik loggning i Azure i [aktivera diagnostik i Azure Cloud Services och virtuella datorer](cloud-services/cloud-services-dotnet-diagnostics.md) och [aktivera diagnostikloggning för Webbappar i Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

