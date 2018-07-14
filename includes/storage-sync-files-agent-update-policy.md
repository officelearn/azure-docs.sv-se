Azure File Sync-agenten uppdateras regelbundet att lägga till nya funktioner och för att lösa problem. Vi rekommenderar att du konfigurerar Microsoft Update för att hämta uppdateringar för Azure File Sync-agenten när de är tillgängliga.

#### <a name="major-vs-minor-agent-versions"></a>Större eller mindre agentversioner
* Större agentversioner ofta innehåller nya funktioner och har allt fler som den första delen av versionsnumret. Till exempel: *2.\*.\**
* Mindre agentversioner kallas även ”korrigeringar” och släpps oftare än högre versioner. De innehåller ofta felkorrigeringar och förbättringar av mindre men inga nya funktioner. Till exempel:  *\*.3.\**

#### <a name="upgrade-paths"></a>Sökvägar för uppgradering
Det finns tre godkända och testats sätt att installera uppdateringar för Azure File Sync-agenten. Dessa uppdatera sökvägar arbete för både stora och den lägre versionen.
1. **(Rekommenderas) Konfigurera Microsoft Update för att automatiskt hämta och installera agentuppdateringar.**  
    Vi rekommenderar alltid var Azure File Sync-update för att säkerställa att du har åtkomst till de senaste korrigeringarna för server-agenten. Microsoft Update är den här processen sömlös, genom att automatiskt hämta och installera uppdateringar åt dig.
2. **Korrigera ett befintligt Azure File Sync-agenten med hjälp av en korrigeringsfil för Microsoft Update eller en körbar .msp. Det senaste uppdateringspaketet för Azure File Sync kan laddas ned från den [Microsoft Update-katalogen](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Köra en körbar .msp uppgraderar Azure File Sync-installationen med samma metod som används automatiskt av Microsoft Update i den föregående uppgraderingsvägen. Tillämpa en korrigering för Microsoft Update utför en uppgradering på plats av en Azure File Sync-installation.
3. **Hämta installationsprogrammet för den senaste Azure File Sync-agenten från den [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Installationsprogrammet hämtningen är en Microsoft Installer-paket eller en körbar .msi.**  
    Uppgradera en befintlig Azure File Sync-agentinstallation, avinstalleras den äldre versionen och installera den senaste versionen från det nedladdade installationsprogrammet. Registrera servern synkroniseringsgrupper och eventuella andra inställningar underhålls av Azure File Sync-installationsprogrammet.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Livscykel och ändra agenthantering garanterar
Azure File Sync är en molnbaserad tjänst som gör att kontinuerligt med nya funktioner och funktionalitet. Det innebär att en specifik version av Azure File Sync-agenten kan bara användas under en begränsad tid. Följande regler för att garantera att du har tillräckligt med tid och ett meddelande för agenten uppdateringar/uppgraderingar i din process för ändringshantering för att underlätta distributionen:

- Större agentversioner som stöds för minst sex månader från dagen för första versionen.
- Vi garanterar att det finns en överlappning av minst tre månader mellan stöd för större agentversioner. 
- Varningar har utfärdats för registrerade servrar med hjälp av en har upphört att gälla snart tas agenten minst tre månader före förfallodatumet. Du kan kontrollera om en registrerad server använder en äldre version av agenten under avsnittet registrerade servrar för en Lagringssynkroniseringstjänst.
- Livslängden för en mindre agentversionen är kopplad till den associera huvudversionen. Till exempel när agent-version 3.0 släpps, agentversioner 2. \* alla upphör att gälla tillsammans.

> [!Note]
> Om du installerar en agentversion med ett förfallodatum varning visas en varning, men lyckas. Försök att installera eller ansluta till en har upphört att gälla agentversion stöds inte och kommer att blockeras.