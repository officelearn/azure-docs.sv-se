Azure filsynkronisering agenten uppdateras regelbundet kan lägga till nya funktioner och åtgärda problem. Vi rekommenderar att du konfigurerar Microsoft Update för att hämta uppdateringar för agenten Azure filsynkronisering som de är tillgängliga.

#### <a name="major-vs-minor-agent-versions"></a>Större eller mindre agentversioner
* Större agentversioner ofta innehåller nya funktioner och har allt fler som den första delen av versionsnumret. Till exempel: *2.\*.\**
* Mindre agentversioner kallas också ”korrigeringsfiler” och släpps oftare än större versioner. De innehåller ofta felkorrigeringar och förbättringar av mindre men inga nya funktioner. Till exempel:  *\*.3.\**

#### <a name="upgrade-paths"></a>Uppgraderingsvägar
Det finns tre godkända och testat sätt att installera Azure filsynkronisering agentuppdateringar. Dessa uppdatera sökvägar arbete för både högre och lägre versioner.
1. **(Rekommenderas) Konfigurera Microsoft Update för att automatiskt hämta och installera agentuppdateringar.**  
    Vi rekommenderar alltid tar alla Azure filsynkronisering uppdateringar så du får åtkomst till de senaste korrigeringarna för server-agent. Microsoft Update är den här processen sömlös, genom att automatiskt hämta och installera uppdateringar åt dig.
2. **Korrigering av en befintlig Azure filsynkronisering agent med hjälp av en korrigeringsfil för Microsoft Update eller en körbar .msp. Senaste Azure filsynkronisering uppdateringspaketet kan hämtas från den [Microsoft Update-katalogen](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Kör en körbara .msp uppgraderar installationen av Azure filsynkronisering med samma metod används automatiskt av Microsoft Update i den föregående sökvägen för uppgraderingen. Tillämpa en korrigering för Microsoft Update utför en uppgradering på plats av en Azure-filen Sync-installation.
3. **Hämta senaste Azure filsynkronisering installationsprogrammet för agenten från den [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Den installer-versionen är en Microsoft Installer-paket eller en körbar .msi.**  
    Uppgradera en befintlig Azure filsynkronisering agentinstallation, avinstalleras den tidigare versionen och installera den senaste versionen från installationsprogrammet. Server-registrering, synkroniseringsgrupper och andra inställningar underhålls av Azure filsynkronisering installer.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garanterar att agenten livscykel och ändringshantering
Azure filsynkronisering är en molnbaserad tjänst som gör att kontinuerligt införandet av nya funktioner. Det innebär att en viss version av Azure filsynkronisering agenten kan endast stöds för en begränsad tid. Följande regler för att garantera att du har tillräckligt med tid och meddelanden för agenten uppdateringar/uppgraderingar i din process för ändringshantering för att underlätta distributionen:

- Större agentversioner stöds för minst sex månader från datumet då den första versionen.
- Vi garanterar att det finns en överlappning av minst tre månader mellan stöd för större agentversioner. 
- Varningar utfärdas för registrerade servrar med hjälp av en har upphört att gälla snart-för-att-agent minst tre månader innan upphör att gälla. Du kan kontrollera om en registrerad server använder en äldre version av agenten under avsnittet registrerade servrar för lagring-synkroniseringstjänsten.
- Livslängden för en mindre agentversion är kopplad till den associera huvudversionen. Till exempel när agentversion 3.0 släpps, agentversioner 2. \* alla anges att gälla tillsammans.

> [!Note]
> Om du installerar en agentversion med ett förfallodatum varning visas en varning men lyckas. Försök att installera eller ansluta till en utgången agentversion stöds inte och kommer att blockeras.