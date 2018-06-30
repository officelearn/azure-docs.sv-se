---
title: Konfigurera diagnostik för virtuella datorer och Azure Cloud Services | Microsoft Docs
description: Lär dig hur du ställer in diagnostik för felsökning Azure cloude tjänster och virtuella maskiner (VMs) i Visual Studio.
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
ms.openlocfilehash: 2ff2a619dabd7dfabf89361172557efa4884ba12
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110495"
---
# <a name="set-up-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Konfigurera diagnostik för Azure-molntjänster och virtuella datorer
När du behöver felsöka ett Azure-molntjänst eller den virtuella datorn använder du Visual Studio enklare ställa in Azure-diagnostik. Diagnostik avbildar systemdata och loggningsdata på virtuella datorer och instanser för virtuella datorer som kör Molntjänsten. Diagnostikdata överförs till ett lagringskonto som du väljer. Mer information om diagnostik loggning i Azure, se [aktivera diagnostikloggning för Web Apps i Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

I den här artikeln hur vi du använder Visual Studio för att aktivera och konfigurera Azure-diagnostik både före och efter distributionen. Lär dig hur du ställer in diagnostik på Azure virtual machines, hur du väljer vilka typer av diagnostikinformation för att samla in och hur du visar informationen när den har samlats in.

Du kan använda något av följande alternativ för att ställa in Azure-diagnostik:

* Ändra inställningarna för webbprogramdiagnostik i den **diagnostik Configuration** dialogrutan i Visual Studio. Inställningarna sparas i en fil med namnet diagnostics.wadcfgx (i Azure SDK 2.4 och tidigare filen kallas diagnostics.wadcfg). Du också kan du direkt ändra konfigurationsfilen. Om du manuellt uppdatera filen configuration ändringar börja gälla nästa gång du distribuerar molnet till Azure-tjänst eller kör tjänsten i emulatorn.
* Använd Cloud Explorer eller Server Explorer i Visual Studio för att ändra diagnostikinställningarna för en molnbaserad tjänst eller en virtuell dator som körs.

## <a name="azure-sdk-26-diagnostics-changes"></a>Ändringar av Azure SDK 2.6 diagnostik
Följande ändringar tillämpas Azure SDK 2.6 och senare projekt i Visual Studio:

* Lokala emulatorn stöder nu diagnostik. Det innebär att du kan samla in diagnostikdata och kontrollera att ditt program skapar rätt spåren när du utvecklar och testar i Visual Studio. Anslutningssträngen `UseDevelopmentStorage=true` aktiverar diagnostik datainsamling när du kör cloud service-projekt i Visual Studio med hjälp av Azure storage-emulatorn. Alla diagnostikdata samlas in i Development lagring storage-konto.
* Diagnostik konto lagringsanslutningssträngen `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` lagras i tjänstekonfigurationsfilen (.cscfg). I Azure SDK 2.5 har diagnostik storage-konto angetts i filen diagnostics.wadcfgx.

Anslutningssträngen fungerar på olika sätt på vissa viktiga sätt i Azure SDK 2.6 och senare jämfört med Azure SDK 2.4 och tidigare:

* I Azure SDK 2.4 och tidigare används anslutningssträngen som en körning av diagnostik plugin-program för att hämta information om storage-konto för överföring av diagnostik loggar.
* I Azure SDK 2.6 eller senare, använder Visual Studio anslutningssträngen diagnostik för att ställa in Azure Diagnostics tillägg med lämplig lagring kontoinformationen vid publicering. Du kan använda anslutningssträngen för att definiera olika lagringskonton för olika konfigurationer som Visual Studio använder vid publicering. Men eftersom diagnostiken plugin-programmet inte är tillgänglig när Azure SDK 2,5 .cscfg-filen ensamt går inte att konfigurera diagnostik-tillägget. Du måste ställa in tillägget separat med hjälp av verktyg som Visual Studio eller PowerShell.
* För att förenkla processen för att konfigurera diagnostik-tillägget med hjälp av PowerShell innehåller paketet utdata från Visual Studio den offentliga konfigurationen-XML för diagnostik-tillägget för varje roll. Visual Studio använder anslutningssträngen diagnostik för att fylla kontoinformation för lagring i offentliga konfiguration. Offentliga config-filer som skapas i mappen tillägg. Den offentliga konfigurationsfiler använda namnmönstret PaaSDiagnostics. &lt;rollnamn\>. PubConfig.xml. PowerShell-baserade distributioner kan använda det här mönstret för att mappa varje konfiguration till en roll.
* Den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040) använder anslutningssträngen i .cscfg-filen för att komma åt diagnostikdata. Informationen som visas på den **övervakning** fliken. Anslutningssträngen krävs för att ställa in tjänsten för att visa utförliga övervakningsdata i portalen.

## <a name="migrate-projects-to-azure-sdk-26-and-later"></a>Migrera projekt till Azure SDK 2.6 eller senare
När du migrerar från Azure SDK 2.5 Azure SDK 2.6 eller senare om du har en diagnostiklagringskonto som anges i filen .wadcfgx förblir storage-konto i den filen. Om du vill utnyttja möjlighet att använda olika lagringskonton för olika lagringskonfigurationer manuellt lägga till anslutningssträngen i projektet. Om du migrerar ett projekt från Azure SDK 2.4 eller tidigare till Azure SDK 2.6, bevaras anslutningssträngar diagnostik. Observera dock ändringar i hur anslutningssträngar behandlas i Azure SDK 2.6, beskrivs i föregående avsnitt.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hur Visual Studio avgör diagnostik storage-konto
* Om en anslutningssträng för diagnostik anges i .cscfg-filen, används Visual Studio för att ställa in diagnostik tillägget vid publicering och när den skapar XML-filerna offentliga konfiguration under paketering.
* Om en anslutningssträng för diagnostik inte har angetts i .cscfg-filen, faller Visual Studio tillbaka till med hjälp av det lagringskonto som har angetts i filen .wadcfgx att ställa in diagnostik-tillägg för publicering och för att generera den offentliga konfiguration-XML filer under paketering.
* Anslutningssträngen diagnostik i .cscfg-filen har företräde framför storage-konto i filen .wadcfgx. Om en anslutningssträng för diagnostik är anges i .cscfg-filen Visual Studio använder denna anslutningssträng och ignorerar storage-konto i .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-check-box-do"></a>Vad är kryssrutan ”Uppdatera development storage-anslutningssträngar...”?
Den **uppdatera development storage-anslutningssträngar för diagnostik- och cachelagring med Microsoft Azure lagringskontouppgifter vid publicering till Microsoft Azure** kryssrutan är ett bekvämt sätt att uppdatera all lagring som utveckling kontot anslutning strängar med Azure storage-konto som du anger under publiceringen.

Till exempel om du väljer den här kryssrutan och diagnostik anslutningssträngen anger `UseDevelopmentStorage=true`, när du publicerar projektet till Azure, Visual Studio uppdateras automatiskt anslutningssträngen diagnostik och lagringskontot som du angav i guiden Publicera. Men om en verklig lagringskonto har angetts som anslutningssträng diagnostik, används kontot i stället.

## <a name="diagnostics-functionality-differences-in-azure-sdk-24-and-earlier-vs-azure-sdk-25-and-later"></a>Diagnostik funktioner skillnader i Azure SDK 2.4 och jämfört med tidigare. Azure SDK 2,5 och senare
Om du uppgraderar ditt projekt från Azure SDK 2.4 och tidigare till Azure SDK 2.5 eller senare, Tänk på följande diagnostik funktioner skillnader:

* **Konfiguration av API: er är inaktuella**. Programkonfiguration av diagnostik är tillgängliga i Azure SDK 2.4 och tidigare, men är föråldrad i Azure SDK 2.5 och senare. Om konfigurationen av diagnostik för närvarande är definierad i koden, måste du konfigurera om inställningarna från grunden i migrerade projektet för diagnostik för att fortsätta arbeta. Konfigurationsfilen diagnostik för Azure SDK 2.4 är diagnostics.wadcfg. Konfigurationsfilen diagnostik för Azure SDK 2.5 och senare är diagnostics.wadcfgx.
* **Diagnostik för molnprogram för tjänsten kan konfigureras bara på rollnivå**. Azure SDK 2.5 och senare, kan du ställa in diagnostik för tjänsten molnprogram på instansnivå.
* **Varje gång du distribuerar appen, diagnostik konfiguration uppdateras**. Detta kan orsaka problem med paritet om du ändrar konfigurationen diagnostik från Visual Studio Server Explorer och distribuera din app.
* **Azure SDK 2.5 och senare, krascher Dumpar konfigureras i konfigurationsfilen diagnostik och inte i koden**. Om du har krascher Dumpar som konfigurerats i koden, måste du manuellt överföra konfigurationen från kod i konfigurationsfilen. Krashdumpar överförs inte under migreringen till Azure SDK 2.6.

## <a name="turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them"></a>Aktivera diagnostik i molntjänstprojekt innan du distribuerar dem.
I Visual Studio kan du samla in diagnostikdata för roller som körs i Azure när du kör tjänsten i emulatorn före distributionen. Alla ändringar av inställningarna för webbprogramdiagnostik i Visual Studio sparas i konfigurationsfilen diagnostics.wadcfgx. Dessa inställningar anger det lagringskonto där diagnostikdata sparas när du distribuerar din tjänst i molnet.

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-turn-on-diagnostics-in-visual-studio-before-deployment"></a>Aktivera diagnostik i Visual Studio före distributionen

1. Välj på snabbmenyn för rollen **egenskaper**. I rollen **egenskaper** dialogrutan markerar du den **Configuration** fliken.
2. I den **diagnostik** avsnittet, se till att den **aktivera diagnostik** är markerad.
   
    ![Åtkomst till alternativet Aktivera diagnostik](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. Välj knappen med ellipstecknet (...) för att ange lagringskontot för diagnostikdata.
   
    ![Ange lagringskontot för att använda](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. I den **skapa Lagringsanslutningssträng** dialogrutan Ange om du vill ansluta med hjälp av Azure storage-emulatorn en Azure-prenumeration eller autentiseringsuppgifterna anges manuellt.
   
    ![Dialogrutan för Storage-konto](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * Om du väljer **Microsoft Azure storage-emulatorn**, anslutningssträngen har angetts till `UseDevelopmentStorage=true`.
   * Om du väljer **prenumerationen**, du kan välja de Azure-prenumeration som du vill använda och ange ett kontonamn. För att hantera dina Azure-prenumerationer, Välj **hantera konton**.
   * Om du väljer **autentiseringsuppgifterna anges manuellt**, ange namnet och nyckeln för det Azure-konto som du vill använda.
5. Visa den **diagnostik configuration** dialogrutan **konfigurera**. Undantag för **allmänna** och **loggen kataloger**, varje flik representerar en diagnostik-datakälla som du kan samla in. Standard **allmänna** fliken erbjuder följande diagnostik alternativ för samlingen: **endast fel**, **All information**, och **anpassade plan**. Standard **endast fel** alternativet använder den minsta mängden lagring, eftersom den inte överföra varningar eller spårning av meddelanden. Den **All information** överför mycket mer information, använder mest lagring och därför är alternativet dyraste.

   > [!NOTE]
   > Minsta storlek som stöds för ”Disk kvot i MB” är 4GB. Men om du samlar in minnesdumpar öka detta till ett högre värde som 10GB.
   >
  
    ![Aktivera Azure-diagnostik och konfiguration](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. Det här exemplet väljer du den **anpassade plan** alternativ, så du kan anpassa insamlade data.
7. I den **diskkvot i MB** kan du ange hur mycket utrymme för att allokera i ditt lagringskonto för diagnostikdata. Du kan ändra eller acceptera standardvärdet.
8. På varje flik diagnostikdata som du vill samla in, markera den **Aktivera överföring av \<logga typen\>**  kryssrutan. Om du vill samla in Programloggar, till exempel den **programloggarna** väljer den **Aktivera överföring av programloggar** kryssrutan. Dessutom ange annan information som krävs för varje datatyp för diagnostik. Konfigurationsinformation för varje flik, finns i avsnittet **ställa in diagnostik datakällor** senare i den här artikeln. 
9. När du har aktiverat insamling av all diagnostikdata som du vill markera **OK**.
10. Kör ditt Azure cloud service-projekt i Visual Studio som vanligt. När du använder programmet sparas logginformation som du har aktiverat Azure storage-konto som du angav.

## <a name="turn-on-diagnostics-on-azure-virtual-machines"></a>Aktivera diagnostik på virtuella Azure-datorer
I Visual Studio kan du samla in diagnostikdata för virtuella Azure-datorer.

### <a name="to-turn-on-diagnostics-on-azure-virtual-machines"></a>Aktivera diagnostik på virtuella Azure-datorer

1. Välj noden Azure i Server Explorer och ansluta till din Azure-prenumeration om du inte redan är ansluten.
2. Expandera den **virtuella datorer** nod. Du kan skapa en ny virtuell dator eller välj en befintlig nod.
3. På snabbmenyn för den virtuella datorn som du vill markera **konfigurera**. Dialogrutan för konfiguration av virtuell dator visas.
   
    ![Konfigurera en virtuell Azure-dator](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Om den inte redan är installerat kan du lägga till filnamnstillägget Microsoft Monitoring Agent-diagnostik. Du kan använda det här tillägget för att samla in diagnostikdata för den virtuella Azure-datorn. Under **installerat tillägg**i den **markerar du tillägget tillgängliga** nedrullningsbara listrutan, Välj **Microsoft Monitoring Agent-diagnostik**.
   
    ![Installera ett tillägg för virtuell Azure-dator](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
    > [!NOTE]
   > Andra diagnostik tillägg är tillgängligt för virtuella datorer. Mer information finns i [tillägg för virtuell dator och funktioner i Windows](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).
   > 
   > 
5. Lägga till tillägget och visa dess **diagnostik configuration** dialogrutan **Lägg till**.
6. Välj för att ange ett lagringskonto **konfigurera**, och välj sedan **OK**.
   
    Varje flik (förutom för **allmänna** och **loggen kataloger**) representerar en diagnostik-datakälla som du kan samla in.
   
    ![Aktivera Azure-diagnostik och konfiguration](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    Fliken standard **allmänna**, finns följande diagnostik data samling alternativ: **endast fel**, **All information**, och **anpassade plan**. Standardalternativet **endast fel**, tar den minsta mängden lagringsutrymme eftersom den inte överföra varningar eller spårning av meddelanden. Den **All information** alternativet överför mest information och är därför alternativet dyraste vad gäller lagring.
7. Det här exemplet väljer du den **anpassade plan** alternativet så att du kan anpassa data som samlas in.
8. Den **diskkvot i MB** ruta anger hur mycket utrymme som du vill allokera i ditt lagringskonto för diagnostikdata. Du kan ändra standardvärdet om du vill.
9. På varje flik diagnostikdata som du vill samla in, Välj dess **Aktivera överföring av \<logga typen\>**  kryssrutan.
   
    Om du vill samla in programloggar till exempel väljer den **Aktivera överföring av programloggar** kryssrutan på den **programloggarna** fliken. Dessutom ange annan information som krävs för varje datatyp för diagnostik. Konfigurationsinformation för varje flik, finns i avsnittet **ställa in diagnostik datakällor** senare i den här artikeln.
10. När du har aktiverat insamling av all diagnostikdata som du vill markera **OK**.
11. Spara det uppdaterade projektet.
    
    Ett meddelande i den **Microsoft Azure-aktivitetsloggen** fönster visar att den virtuella datorn har uppdaterats.

## <a name="set-up-diagnostics-data-sources"></a>Konfigurera diagnostik-datakällor
När du aktiverar datainsamling för diagnostik, kan du välja exakt vilka datakällor som du vill samla in och vilken information som samlas in. I nästa avsnitt beskrivs flikarna i den **diagnostik configuration** dialogrutan och innebär att varje konfigurationsalternativet.

### <a name="application-logs"></a>Programloggar
Programloggarna har diagnostikinformation som produceras av ett webbprogram. Om du vill samla in Programloggar, väljer du den **Aktivera överföring av programloggar** kryssrutan. Om du vill öka eller minska tidsintervallet mellan överföring av programloggar till ditt lagringskonto, ändra den **överföring Period (min)** värde. Du kan även ändra mängden information som anges i loggen genom att ange den **Certifikatutfärdarnivå** värde. Välj exempelvis **utförlig** vill ha mer information eller välj **kritisk** att avbilda endast kritiska fel. Om du har en särskild diagnostik-provider som genererar programloggarna kan du fånga in loggarna genom att lägga till leverantörens GUID i den **Provider-GUID** rutan.

  ![Programloggar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

Läs mer om programloggarna [aktivera diagnostikloggning för Web Apps i Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

### <a name="windows-event-logs"></a>Windows-händelseloggar
För att avbilda Windows-händelseloggar, Välj den **Aktivera överföring av Windows-händelseloggar** kryssrutan. Öka eller minska tidsintervallet mellan överföringen till ditt lagringskonto för händelseloggar genom att ändra den **överföring Period (min)** värde. Markera kryssrutorna för typerna av händelser som du vill spåra.

![Händelseloggar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Om du använder Azure SDK 2.6 eller senare och du vill ange en anpassad datakälla, anger du det i den **\<datakällnamn\>** textruta och välj sedan **Lägg till**. Datakällan har lagts till filen diagnostics.cfcfg.

Om du använder Azure SDK 2.5 och vill ange en anpassad datakälla, du kan lägga till den till den `WindowsEventLog` avsnitt i diagnostics.wadcfgx-fil, som i följande exempel:

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Prestandaräknare
Prestandaräknaren som kan hjälpa dig hitta system flaskhalsar och finjustera system- och programprestanda. Mer information finns i [skapa och använda prestandaräknare i ett Azure-program](https://msdn.microsoft.com/library/azure/hh411542.aspx). Om du vill samla in prestandaräknare, Välj den **Aktivera överföring av prestandaräknare** kryssrutan. Öka eller minska tidsintervallet mellan överföringen till ditt lagringskonto för händelseloggar genom att ändra den **överföring Period (min)** värde. Markera kryssrutorna för de prestandaräknare som du vill spåra.

![Prestandaräknare](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Ange prestandaräknaren med hjälp av föreslagna syntax för att spåra en prestandaräknare som inte finns. och välj sedan **Lägg till**. Operativsystemet på den virtuella datorn avgör vilka prestandaräknare som du kan följa. Mer information om syntaxen finns [ange en räknarsökvägen](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Infrastruktur-loggar
Infrastruktur loggar har information om Azure-diagnostik infrastrukturen, RemoteAccess-modulen och RemoteForwarder-modulen. Om du vill samla in information om infrastruktur loggar, Välj den **Aktivera överföring av infrastrukturen loggar** kryssrutan. Om du vill öka eller minska tidsintervallet mellan överföring av infrastruktur-loggar till ditt lagringskonto, ändra den **överföring Period (min)** värde.

![Diagnostik infrastruktur loggar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

Mer information finns i [samla in loggningsdata med hjälp av Azure-diagnostik](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="log-directories"></a>Loggen kataloger
Loggen kataloger har data som samlas in från loggen kataloger för Internet Information Services (IIS)-begäranden, misslyckade begäranden eller mappar som du väljer. Om du vill samla in loggen kataloger, Välj den **Aktivera överföring av loggen kataloger** kryssrutan. Om du vill öka eller minska tidsintervallet mellan överföring av loggar till ditt lagringskonto, ändra den **överföring Period (min)** värde.

Markera kryssrutorna för de loggar som du vill samla in, t.ex **IIS-loggar** och **kunde inte begära** loggar. Standard lagring behållarnamn finns, men du kan ändra namn.

Du kan samla in loggar från en mapp. Ange sökvägen i den **loggen från absoluta Directory** avsnittet och väljer sedan **Lägg till katalog**. Loggarna samlas i de angivna behållarna.

![Loggen kataloger](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>ETW-loggar
Om du använder [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (ETW) och vill avbilda ETW loggar, Välj den **Aktivera överföring av ETW-loggar** kryssrutan. Om du vill öka eller minska tidsintervallet mellan överföring av loggar till ditt lagringskonto, ändra den **överföring Period (min)** värde.

Händelserna som har hämtats från händelsekällor och händelsemanifest som du anger. Ange en händelsekälla i den **händelsekällor** , ange ett namn och välj sedan **lägga till händelsekällan**. På liknande sätt kan du ange ett manifest för händelsen i den **händelse visar** avsnittet och väljer sedan **Lägg till händelse Manifest**.

![ETW-loggar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

ETW-ramverket stöds i ASP.NET via klasser i den [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) namnområde. Namnområdet Microsoft.WindowsAzure.Diagnostics som ärver från och utökar standard [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) klasser, kan du använda [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) som en loggning ramverk i Azure-miljön. Mer information finns i [ta kontroll över loggning och spårning i Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) och [aktivera diagnostik i Azure-molntjänster och virtuella datorer](cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Kraschdumpar
Om du vill samla in information om när en rollinstans kraschar, Välj den **Aktivera överföring av krascher Dumpar** kryssrutan. (Eftersom ASP.NET hanterar de flesta undantag, detta är vanligtvis bara användbar för arbetsroller.) Om du vill öka eller minska procentandelen av lagringsutrymme för krascher Dumpar, ändra den **Directory kvoten (%)** värde. Du kan ändra lagringsbehållaren där krascher minnesdumpar lagras, och välj om du vill avbilda en **fullständig** eller **Mini** dumpen.

Processer som för närvarande spåras visas i nästa skärmbilden. Markera kryssrutorna för de processer som du vill samla in. Om du vill lägga till en annan process i listan, ange processens namn och välj sedan **lägga till processen**.

![Kraschdumpar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

Mer information finns i [ta kontroll över loggning och spårning i Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) och [Microsoft Azure Diagnostics del 4: Anpassad loggning komponenter och Azure Diagnostics 1.3 ändringar](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## <a name="view-the-diagnostics-data"></a>Visa diagnostikdata
När du har samlat in diagnostikdata för en molnbaserad tjänst eller en virtuell dator, kan du visa den.

### <a name="to-view-cloud-service-diagnostics-data"></a>Visa data i molnet diagnostik
1. Distribuera din molntjänst som vanligt och sedan köra den.
2. Du kan visa diagnostikdata i en rapport som Visual Studio genererar eller i tabeller i ditt lagringskonto. Att visa data i en rapport, öppna Cloud Explorer eller Server Explorer, öppna snabbmenyn som visas i noden för den roll som du vill använda och välj sedan **visa diagnostikdata**.
   
    ![Visa diagnostik-data](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    En rapport som visar tillgängliga data visas.
   
    ![Microsoft Azure-diagnostik rapporten i Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    Om de senaste data inte visas kan behöva du vänta tills överföringen tid ska gå.
   
    För att omedelbart uppdatera data, Välj den **uppdatera** länk. Om du vill att data uppdateras automatiskt, väljer du ett intervall i den **uppdatera** nedrullningsbara listrutan. Om du vill exportera Feldata för, Välj den **exportera till CSV** för att skapa en CSV-fil som du kan öppna Excel-kalkylblad.
   
    Öppna storage-konto som är associerad med distributionen i Cloud Explorer eller Server Explorer.
3. Öppna tabellerna diagnostik i visningsprogrammet för tabellen och granska de data som du samlade in. Du kan öppna en blob-behållare för IIS-loggar och anpassade loggar. I följande tabell visas de tabeller eller blob-behållare som innehåller data för de olika loggfilerna. Förutom de data som loggfilen posterna i registret innehåller **EventTickCount**, **DeploymentId**, **rollen**, och **RoleInstance** , som hjälper dig att identifiera vilka virtuella datorer och rollen genereras data, och när. 
   
   | Diagnostikdata | Beskrivning | Plats |
   | --- | --- | --- |
   | Programloggar |Loggar som genereras av din kod genom att anropa metoder i den **System.Diagnostics.Trace** klass. |WADLogsTable |
   | Händelseloggar |Data från Windows-händelseloggarna på de virtuella datorerna. Windows lagrar information i dessa loggar, men program och tjänster också använda loggfilerna för att rapportera fel eller logga information. |WADWindowsEventLogsTable |
   | Prestandaräknare |Du kan samla in data på alla prestandaräknare som är tillgängliga på den virtuella datorn. Operativsystemet tillhandahåller prestandaräknare som innehåller många statistik, t.ex. minne användnings- och tid. |WADPerformanceCountersTable |
   | Infrastruktur-loggar |Loggar som genereras från den diagnostik-infrastrukturen. |WADDiagnosticInfrastructureLogsTable |
   | IIS-loggar |Loggar som registrerar webbegäranden. Om din molntjänst hämtar en stor mängd trafik, kan dessa loggar vara tidskrävande. Det är en bra idé att samla in och lagra dessa data endast när du behöver den. |Du kan hitta misslyckades begäran loggas i blob-behållare under bomullstuss-iis-failedreqlogs under en sökväg för den distribution, roll och instans. Du kan hitta klar loggar under bomullstuss-iis-loggfiler. För varje fil skapas transaktioner i tabellen WADDirectories. |
   | Kraschdumpar |Visar binära avbildningar av din molntjänst processen (vanligtvis en arbetsroll). |bomullstuss-crush-Dumpar blob-behållare |
   | Anpassade loggfiler |Loggar av data som du fördefinierade. |I koden kan du ange platsen för anpassade loggfiler i ditt lagringskonto. Du kan till exempel ange en anpassad blob-behållare. |
4. Om data av valfri typ trunkeras kan du öka bufferten för dessa data typ eller förkorta intervallet mellan överföringar av data från den virtuella datorn till ditt lagringskonto.
5. (Valfritt) Ta bort data från storage-konto ibland att minska övergripande kostnader för lagring.
6. När du gör en fullständig distribution diagnostics.cscfg filen (.wadcfgx för Azure SDK 2.5) uppdateras i Azure och din molntjänst hämtar ändringar i konfigurationen av diagnostik. Om du uppdaterar en befintlig distribution i stället, uppdateras inte .cscfg-filen i Azure. Du kan fortfarande ändra diagnostikinställningarna, men genom att följa stegen i nästa avsnitt. Mer information om hur du utför en fullständig distribution uppdaterar en befintlig distribution finns [Publiceringsguiden för Azure-program](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Visa diagnostik-data för virtuell dator
1. På snabbmenyn för den virtuella datorn, Välj **visa diagnostikdata**.
   
    ![Visa diagnostik-data i en virtuell Azure-dator](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    Den **diagnostik sammanfattning** dialogrutan visas.
   
    ![Virtuell dator i Azure diagnostics sammanfattning](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    Om de senaste data inte visas kan behöva du vänta tills överföringen tid ska gå.
   
    För att omedelbart uppdatera data, Välj den **uppdatera** länk. Om du vill att data uppdateras automatiskt, väljer du ett intervall i den **uppdatera** nedrullningsbara listrutan. Om du vill exportera Feldata för, Välj den **exportera till CSV** för att skapa en CSV-fil som du kan öppna Excel-kalkylblad.

## <a name="set-up-cloud-service-diagnostics-after-deployment"></a>Konfigurera cloud service diagnostik efter distribution
Om du vill veta problem med en molnbaserad tjänst som redan körs, kanske du vill samla in data som du inte har angett innan du ursprungligen distribuerat rollen. I det här fallet kan du börja samla in data genom att ändra inställningarna i Server Explorer. Du kan ställa in diagnostik för en enda instans eller för alla instanser i en roll, beroende på om du öppnar den **diagnostik Configuration** dialogrutan på snabbmenyn för instansen eller för rollen. Om du konfigurerar rollen noden gäller alla ändringar du gör för alla instanser. Om du konfigurerar noden instans, gäller alla ändringar du gör endast för den instansen.

### <a name="to-set-up-diagnostics-for-a-running-cloud-service"></a>Konfigurera diagnostik för en molnbaserad tjänst som körs
1. I Server Explorer expanderar den **molntjänster** nod, och sedan expandera listan över noder att hitta rollen eller instansen (eller båda) som du vill undersöka.
   
    ![Konfigurera diagnostiken](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. Välj på snabbmenyn för en instans noden eller rollen **uppdatera diagnostikinställningar**, och välj sedan de diagnostiska inställningar som du vill samla in.
   
    Information om inställningarna i avsnittet **ställa in diagnostik datakällor** i den här artikeln. Information om hur du visar diagnostikdata finns i avsnittet **visa diagnostikdata** i den här artikeln.
   
    Om du ändrar datainsamling i Server Explorer gäller ändringarna tills du distribuerar fullständigt Molntjänsten. Om du använder standard Publiceringsinställningar kan ändringarna att skrivas över inte. Standard publicera inställningen är att uppdatera den befintliga distributionen, i stället för att göra en fullständig omdistributionen. För att säkerställa att inställningarna avmarkerar vid tidpunkten för distribution, gå till den **avancerade inställningar** i guiden Publicera och avmarkerar sedan den **uppdatering av programdistribution** kryssrutan. När du distribuerar med den här kryssrutan avmarkerad inställningarna återgå till dem i filen .wadcfgx (eller .wadcfg) som angetts via den **egenskaper** Redigeraren för rollen. Om du uppdaterar din distribution, behåller Azure de tidigare inställningarna.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Felsökning av problem med Azure cloud service
Om du får problem med ditt molntjänstprojekt som en roll som hämtar fastnat i statusen ”upptagen” upprepade gånger återanvänds eller genererar ett internt serverfel har verktyg och tekniker som du kan använda för att diagnostisera och åtgärda problemet. Exempel på vanliga problem och lösningar, samt en översikt över de koncept och verktyg som du kan använda för att diagnostisera och åtgärda dessa fel, se [PaaS Azure compute diagnostikdata](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Frågor och svar
**Vad är buffertstorleken och hur stor ska den vara?**

På varje virtuell dator-instans begränsa kvoter hur mycket diagnostikdata kan lagras på det lokala filsystemet. Dessutom kan ange du en buffertstorlek för varje typ av diagnostikdata som är tillgängliga. Den här buffertstorlek fungerar som en individuell kvot för den typ av data. Finns längst ned i dialogrutan för datatypen diagnostik för att fastställa den övergripande kvoten och mängden minne som finns kvar. Om du anger större buffertar eller fler typer av data ska du hanterar den övergripande kvoten. Du kan ändra den övergripande kvoten genom att ändra diagnostics.wadcfg eller .wadcfgx konfigurationsfil. Diagnostikdata lagras på samma filsystem som dina programdata. Om programmet använder en stor mängd diskutrymme, bör du öka kvoten för övergripande diagnostik.

**Vad är överföring period och hur lång tid ska den vara?**

Överföringsperioden är tidsperiod som förflutit mellan data samlar in. Efter varje överföring period flyttas data från det lokala filsystemet på en virtuell dator till tabeller i ditt lagringskonto. Om mängden data som samlas in överskrider kvoten före överföringen utgång, ignoreras äldre data. Om du förlorar data eftersom dina data överskrider buffertstorleken eller övergripande kvoten, kanske du vill minska överföringsperioden.

**Vilken tidszon är tidsstämplar i?**

Tidsstämplar finns i den lokala tidszonen för datacenter som är värd för Molntjänsten. Följande tre tidsstämpel kolumner i tabellerna i används:

* **PreciseTimeStamp**: det ETW tidsstämpel för händelsen. Det vill säga den tid som händelsen loggas från klienten.
* **TIDSSTÄMPEL**: värdet för **PreciseTimeStamp** avrundade nedåt överför frekvens gräns. Om din överföringsfrekvensen är 5 minuter och händelsen tid 00:17:12, är TIDSTÄMPEL 00:15:00.
* **Tidsstämpel**: tidsstämpeln som entiteten skapades i Azure-tabellen.

**Hur hanterar jag kostnader vid insamling av diagnostikinformation?**

Standardinställningarna (**Certifikatutfärdarnivå** inställd på **fel**, och **överföring period** inställd på **1 minut**) är utformade för att minimera kostnaderna. Compute-kostnaderna ökar när du samlar in mer diagnostikdata eller om du minskar överföringsperioden. Samla inte in mer data än vad du behöver och Glöm inte att inaktivera datainsamling när du inte längre behöver. Du kan alltid aktivera det igen, även vid körning, enligt beskrivningen tidigare i den här artikeln.

**Hur samlar misslyckades begäran loggar från IIS?**

Som standard IIS inte samla in loggar misslyckades-begäran. Du kan konfigurera IIS att samla in loggar misslyckades begäran genom att redigera filen web.config för webbrollen.

**Jag får inte spårningsinformation från RoleEntryPoint metoder som OnStart. Vad är fel?**

Metoder för **RoleEntryPoint** anropas i kontexten för WAIISHost.exe inte i IIS. Konfigurationsinformationen i web.config som normalt inte gäller aktiverar spårning. Lös problemet, Lägg till en .config-fil i din webbrollsprojektet och namn på filen så att den matchar utdata sammansättningen som innehåller den **RoleEntryPoint** kod. Namnet på .config-filen ska vara WAIISHost.exe.config i webbrollsprojektet standard. Lägg till följande rader i den här filen:

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

I den **egenskaper** fönstret kan du ange den **kopiera till utdatakatalog** egenskapen **Kopiera alltid**.

## <a name="next-steps"></a>Nästa steg
Mer information om loggning i Azure diagnostics finns [aktivera diagnostik i Azure-molntjänster och virtuella datorer](cloud-services/cloud-services-dotnet-diagnostics.md) och [aktivera diagnostikloggning för Web Apps i Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

