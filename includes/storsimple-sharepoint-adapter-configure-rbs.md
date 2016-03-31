<!--author=SharS last changed: 9/17/15-->

>[AZURE.NOTE] Wenn Sie mit dem StorSimple-Adapter für SharePoint-RBS-Konfiguration ändern, müssen Sie mit einem Benutzerkonto angemeldet sein, zu der Gruppe der Domänenadministratoren gehört. Darüber hinaus müssen Sie die Konfigurationsseite in einem Browser öffnen, der auf dem gleichen Host ausgeführt wird wie die Zentraladministration.

#### So konfigurieren Sie RBS

1. Öffnen Sie die Seite SharePoint-Zentraladministration, und navigieren Sie zu **Systemeinstellungen**. 

2. In der **Azure StorSimple** auf **StorSimple-Adapter konfigurieren**.

    ![Konfigurieren des StorSimple-Adapters](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 

3. Auf der **StorSimple-Adapter konfigurieren** Seite:

    1. Stellen Sie sicher, dass die **Pfad bearbeiten aktivieren** das Kontrollkästchen aktiviert ist.

    2. Geben Sie im Textfeld den UNC-Pfad des BLOB-Speichers ein.

          >[AZURE.NOTE] Der BLOB-Speichervolume muss auf einem iSCSI-Volume auf dem StorSimple-Gerät konfiguriert gehostet werden.

    3. Klicken Sie auf die **aktivieren** Schaltfläche unter jeder Inhaltsdatenbank, die Sie für Remotespeicher konfigurieren möchten.

          >[AZURE.NOTE] Der BLOB-Speicher muss von allen Web-Front-End (WFE)-Server freigegeben und erreichbar sein, und das Benutzerkonto, das für die SharePoint-Serverfarm konfiguriert ist, muss Zugriff auf die Freigabe haben.

          ![Aktivieren des RBS-Anbieters](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)

           When you enable or disable RBS, you will also see the following message.

          ![Konfigurieren des StorSimple-Adapters (aktivieren/deaktivieren)](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

    4. Klicken Sie auf die **Update** Schaltfläche, um die Konfiguration zu übernehmen. Beim Klicken auf die **Update** gewählt haben, wird der Status der RBS-Konfiguration auf allen WFE-Servern aktualisiert, und die gesamte Farm ist, RBS aktiviert. Die folgende Meldung wird angezeigt.

           ![Adapter configuration message](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)

           >[AZURE.NOTE] If you are configuring RBS for a SharePoint farm with a very large number of databases (greater than 200), the SharePoint Central Administration web page might time out. If that occurs, refresh the page. This does not affect the configuration process.
 
4. Überprüfen Sie die Konfiguration:

    1. Melden Sie sich bei der SharePoint-Zentraladministration-Website, und navigieren Sie zu der **StorSimple-Adapter konfigurieren** Seite.

    2. Vergewissern Sie sich, dass die Konfigurationsdetails den eingegebenen Einstellungen entsprechen. 

5. Vergewissern Sie sich, dass RBS ordnungsgemäß funktioniert:

    1. Laden Sie ein Dokument an SharePoint hoch. 

    2. Navigieren Sie zum konfigurierten UNC-Pfad. Vergewissern Sie sich, dass die RBS-Verzeichnisstruktur erstellt wurde und das hochgeladene Objekt enthält.

6. (Optional) Mit dem in SharePoint enthaltenen Microsoft RBS-PowerShell-Cmdlet `Migrate()` können Sie vorhandene BLOB-Inhalte zum StorSimple-Gerät migrieren. Weitere Informationen finden Sie unter [Migrieren von Inhalt in oder aus RBS in SharePoint 2013][6] oder [Migrieren von Inhalt in oder aus RBS (SharePoint Foundation 2010)][7].

7. (Optional) In Testinstallationen können Sie sich wie folgt vergewissern, dass die BLOBs aus der Inhaltsdatenbank verschoben wurden: 

    1. Starten Sie SQL Management Studio.

    2. Führen Sie die Abfrage „ListBlobsInDB_2010.sql“ oder „ListBlobsInDB_2013.sql“ wie folgt aus:

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

     Wenn RBS ordnungsgemäß konfiguriert wurde, enthält die Spalte „SizeOfContentInDB“ für jedes hochgeladene Objekt, das erfolgreich mit RBS externalisiert wurde, einen NULL-Wert.

8. (Optional) Nachdem Sie RBS konfiguriert und alle BLOB-Inhalte auf das StorSimple-Gerät verschoben haben, können Sie die Inhaltsdatenbank auf das Gerät verschieben. Wenn Sie die Inhaltsdatenbank verschieben möchten, empfehlen wir, den Inhaltsdatenbankspeicher auf dem Gerät als primäres Volume zu konfigurieren. Wenden Sie dann die bewährten Methoden für die SQL Server-Migration an, um die Inhaltsdatenbank zum StorSimple-Gerät zu migrieren. 

     >[AZURE.NOTE] Verschieben die Inhaltsdatenbank auf das Gerät wird nur für die StorSimple 8000-Serie unterstützt (es ist nicht für die Serie 5000 oder 7000 unterstützt).
 
     Wenn Sie BLOBs und die Inhaltsdatenbank auf dem StorSimple-Gerät in separaten Volumes speichern, sollten Sie diese im gleichen Volumecontainer konfigurieren. Dadurch wird sichergestellt, dass sie gemeinsam gesichert werden.

       >[AZURE.WARNING] Wenn Sie RBS nicht aktiviert haben, empfohlen nicht die Inhaltsdatenbank zum StorSimple-Gerät verschieben. Dies ist eine nicht getestete Konfiguration.
 
9. Wechseln Sie zum nächsten Schritt: [Konfigurieren der Garbagecollection](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx


