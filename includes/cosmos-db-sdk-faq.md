**1. Hur kunder meddelas om retiring SDK?**

Microsoft ger 12 månad avisering i förväg till slutet av stödet för retiring SDK för att underlätta en smidig övergång till en SDK som stöds. Dessutom kunder meddelas via olika kommunikation kanaler – Azure-hanteringsportalen, Developer Center blogginlägget och direkt kommunikation tilldelade tjänstadministratörer.

**2. Kunder kan skriva program med hjälp av en ”ska” tillbakadragna Azure Cosmos DB SDK under en period för 12 månader?** 

Ja, kunder har fullständig behörighet att skapa, distribuera och ändra program med hjälp av ”ska” tillbakadragna Azure Cosmos DB SDK under respittiden tolv månader. Under respittiden 12 månad bäst kunder att migrera till en nyare version som stöds av Azure Cosmos DB SDK efter behov.

**3. Kan kunderna skapa och ändra program med hjälp av en pensionerad Azure Cosmos DB SDK när perioden 12 månaden?**

När perioden 12 månad ska SDK tas bort. All åtkomst till Azure Cosmos DB av ett program med hjälp av en pensionerad SDK tillåts inte av Azure DB som Cosmos-plattformen. Microsoft ger dessutom ingen teknisk support om tillbakadragna SDK.

**4. Vad händer med kunden kör program som använder Azure Cosmos DB SDK-versionen stöds inte?**

Alla försök görs att ansluta till tjänsten Azure Cosmos DB med en pensionerad SDK-version återställs. 

**5. Nya funktioner och funktionalitet tillämpas på alla ej dras tillbaka SDK: er?**

Nya funktioner kommer bara lägga till nya versioner. Om du använder en gammal, icke-dras tillbaka, version av SDK dina begäranden till Azure Cosmos DB fortsätter att fungera som tidigare men har du inte åtkomst till några nya funktioner.  

**6. Vad gör jag om jag inte kan uppdatera Mina program innan ett slutdatum?**

Vi rekommenderar att du uppgraderar till den senaste SDK: N så snart som möjligt. När en SDK har taggats för pensionering har du 12 månader att uppdatera ditt program. Om du av någon anledning inte kan du slutföra din programuppdateringen inom denna period sedan kontakta den [Cosmos DB Team](mailto:askcosmosdb@microsoft.com) och begära deras hjälp före sista datumet.

