<!--author=SharS last changed: 1/14/2016 -->

> [!NOTE]
> När du ändrar StorSimple-kortet för SharePoint RBS konfigurationen måste du vara inloggad med ett konto som tillhör gruppen Domänadministratörer. Dessutom måste du komma åt konfigurationssidan från en webbläsare som körs på samma värddator som Central Administration.
> 
> 

#### <a name="to-configure-rbs"></a>Så här konfigurerar du RBS
1. Öppna sidan Central Administration av SharePoint och bläddra till **systeminställningar**. 
2. I den **Azure StorSimple** klickar du på **konfigurera StorSimple nätverkskort**.
   
    ![Konfigurera StorSimple-kort](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. På den **konfigurera StorSimple nätverkskort** sidan:
   
   1. Se till att den **Aktivera redigering sökväg** är markerad.
   2. I rutan skriver du Universal Naming Convention (UNC)-sökvägen till blobstore.
      
      > [!NOTE]
      > BLOB store volym måste finnas på en iSCSI-volymen som konfigurerats på StorSimple-enheten.

   3. Klicka på den **aktivera** knapp under varje innehållsdatabaser som du vill konfigurera för Fjärrlagring.
      
      > [!NOTE]
      > BLOB-arkivet måste vara delad och kan nås av alla frontend (WFE) webbservrar och det användarkonto som har konfigurerats för SharePoint-servergruppen måste ha åtkomst till resursen.
      
      ![Aktivera RBS-provider](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      När du aktiverar eller inaktiverar RBS dessutom visas följande meddelande.
      
      ![Konfigurera StorSimple nätverkskort aktivera inaktivera](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Klicka på den **uppdatering** för att tillämpa konfigurationen. När du klickar på den **uppdatering** knappen Konfigurationsstatus RBS kommer att uppdateras på alla WFE-servrar och hela servergruppen kommer att RBS-aktiverade. Följande meddelande visas.
      
      ![Kortet configuration meddelande](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Om du konfigurerar RBS för en SharePoint-grupp med ett mycket stort antal databaser (större än 200), kan sidan Central Administration av SharePoint timeout. Om det inträffar kan du uppdatera sidan. Detta påverkar inte konfigurationen.

4. Kontrollera konfigurationen:
   
   1. Logga in på webbplatsen för Central Administration av SharePoint och bläddra till den **konfigurera StorSimple nätverkskort** sidan.
   2. Kontrollera konfigurationsinformation för att se till att de matchar de inställningar som du angett. 
5. Kontrollera att RBS fungerar korrekt:
   
   1. Överför ett dokument till SharePoint. 
   2. Bläddra till UNC-sökvägen som du har konfigurerat. Se till att katalogstrukturen RBS har skapats och att den innehåller överförda objektet.
6. (Valfritt) Du kan använda Microsoft RBS `Migrate()` PowerShell-cmdlet som ingår i SharePoint för att migrera befintliga BLOB-innehåll till StorSimple-enhet. Mer information finns i [migrerar innehåll till eller från RBS i SharePoint 2013] [ 6] eller [migrerar innehåll till eller från RBS (SharePoint Foundation 2010)] [7].
7. (Valfritt) På test-installationer kan du kontrollera att Blobbarna har flyttats utanför innehållsdatabasen på följande sätt: 
   
   1. Starta SQL Management Studio.
   2. Kör frågan ListBlobsInDB_2010.sql eller ListBlobsInDB_2013.sql på följande sätt.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Om RBS är korrekt konfigurerad, visas ett nullvärde i kolumnen SizeOfContentInDB för alla objekt som har överförts och har externalized med RBS.
8. (Valfritt) När du konfigurerar RBS och flytta alla BLOB-innehåll till StorSimple-enhet kan flytta du innehållsdatabasen till enheten. Om du väljer att flytta innehållsdatabasen rekommenderar vi att du konfigurerar innehållsdatabasen lagring på enheten som en primär volym. Använd upprätta sedan SQL Server bästa praxis för att migrera innehållsdatabasen till StorSimple-enhet. 
   
   > [!NOTE]
   > Flytta innehållsdatabasen till enheten har endast stöd för StorSimple 8000-serien (den inte stöds för 5000 och 7000-serien).
   
   Om du sparar Blobbar och innehållsdatabasen i separata volymer på StorSimple-enheten, rekommenderar vi att du konfigurerar dem i samma volymbehållare. Detta säkerställer att de ska säkerhetskopieras tillsammans.
   
   > [!WARNING]
   > Om du inte har aktiverat RBS rekommenderar vi inte flytta innehållsdatabasen till StorSimple-enhet. Detta är en otestade konfiguration.
   
9. Gå till nästa steg: [konfigurera skräpinsamling](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
