
Du kan lära dig om Azure Cosmos DB globala distribution i den här Azure fredag video med Scott Hanselman och huvudnamn tekniker Manager Karthik Raman.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

Mer information om hur globala databasreplikering fungerar i Azure Cosmos DB, finns [distribuera data globalt med Cosmos DB](../articles/cosmos-db/distribute-data-globally.md).

## <a id="addregion"></a>Lägga till den globala databasen områden med Azure-portalen
Azure Cosmos-DB är tillgänglig i alla [Azure-regioner] [ azureregions] över hela världen. När du har valt standardnivån för konsekvens för ditt konto, kan du associera en eller flera regioner (beroende på ditt val av standard konsekvenskontroll nivå och global distributionsplatsen behov).

1. I den [Azure-portalen](https://portal.azure.com/), i fältet till vänster klickar du på **Azure Cosmos DB**.
2. I den **Azure Cosmos DB** bladet väljer databasen konto för att ändra.
3. I bladet konto klickar du på **replikera data globalt** på menyn.
4. I den **replikera data globalt** bladet välj regioner att lägga till eller ta bort genom att klicka på regioner på kartan klicka sedan på **spara**. Det finns en kostnad för att lägga till regioner, se den [sida med priser](https://azure.microsoft.com/pricing/details/cosmos-db/) eller [distribuera data globalt med Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md) artikel för mer information.
   
    ![Klicka på regioner på kartan för att lägga till eller ta bort dem.][1]
    
När du lägger till en andra region i **manuell redundans** är aktiverat på den **replikera data globalt** blad i portalen. Du kan använda det här alternativet för att testa redundans processen eller ändra den primära write-regionen. När du lägger till en tredje region i **redundans prioriteter** är aktiverat på samma bladet så att du kan ändra ordning för växling vid fel för läsning.  

### <a name="selecting-global-database-regions"></a>Markera den globala databasen regioner
Det finns två vanliga scenarier för att konfigurera två eller flera områden:

1. Leverera låg latens åtkomst till data till slutanvändare oavsett var de befinner sig över hela världen
2. Lägga till regionala återhämtning för affärskontinuitet och haveriberedskap (BCDR)

För att leverera låg latens för slutanvändarna, rekommenderas att distribuera både programmet och lägga till Azure Cosmos-DB i områden som är motsvarar där programmets användare finns.

För BCDR, rekommenderas du lägger till regioner baserat på region-par som beskrivs i den [Business affärskontinuitet och haveriberedskap återställning (BCDR): parad Azure-regioner] [ bcdr] artikel.

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
