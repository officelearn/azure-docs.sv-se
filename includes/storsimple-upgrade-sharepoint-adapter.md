<!--author=SharS last changed: 9/17/15-->

### Upgraden von SharePoint 2010 auf SharePoint 2013 und Installieren des StorSimple-Adapters für SharePoint

>[AZURE.IMPORTANT] Alle Dateien, die zuvor über RBS in einem externen Speicher verschoben wurden wird nicht, bis die Aktualisierung abgeschlossen ist und die RBS-Funktion erneut aktiviert wird. Führen Sie Aktualisierungen oder Neuinstallationen während eines geplanten Wartungsfensters durch, um die Auswirkungen für Benutzer gering zu halten.

#### So führen Sie ein Upgrade von SharePoint 2010 auf SharePoint 2013 mit anschließender Adapterinstallation aus

1. Notieren Sie sich in der SharePoint 2010-Farm den BLOB-Speicherpfad für die externalisierten BLOBs und die Inhaltsdatenbanken, für die RBS aktiviert ist. 

2. Installieren und konfigurieren Sie die neue SharePoint 2013-Farm. 

3. Verschieben Sie Datenbanken, Anwendungen und Websitesammlungen aus der SharePoint 2010-Farm in die neue SharePoint 2013-Farm. Eine Anleitung hierzu finden Sie unter [Überblick über den Upgradeprozess für SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).

4. Installieren Sie in der neuen Farm den StorSimple-Adapter für SharePoint. Wechseln Sie zu [des StorSimple-Adapters für SharePoint installieren](#install-the-storsimple-adapter-for-sharepoint) Verfahren.

5. Aktivieren Sie RBS unter Verwendung der Informationen, die Sie in Schritt 1 notiert haben, für die gleichen Inhaltsdatenbanken, und geben Sie den BLOB-Speicherpfad an, der auch in der SharePoint 2010-Installation verwendet wurde. Wechseln Sie zu [Konfigurieren von RBS](#configure-rbs) Verfahren. Nach Abschluss dieses Schritts kann von der neuen Farm aus auf zuvor externalisierte Dateien zugegriffen werden. 

### Upgraden des StorSimple-Adapters für SharePoint

>[AZURE.IMPORTANT] Planen Sie das Upgrade während eines geplanten Wartungsfensters aus den folgenden Gründen auftreten:
>
>- Zuvor externalisierte Inhalte sind erst nach der Neuinstallation des Adapters wieder verfügbar.
>
>- Alle Inhalte, die nach der Deinstallation der vorherigen Version des StorSimple-Adapters für SharePoint und vor der Installation der neuen Version an die Website hochgeladen werden, werden in der Inhaltsdatenbank gespeichert. Diese Inhalte müssen nach dem Installieren des neuen Adapters auf das StorSimple-Gerät verschoben werden. Sie können das Microsoft PowerShell-Cmdlet ` RBS Migrate()` aus SharePoint für das Migrieren der Inhalte verwenden. Weitere Informationen finden Sie unter [Migrieren von Inhalt in oder aus RBS](https://technet.microsoft.com/library/ff628255.aspx). 


#### So führen Sie ein Upgrade des StorSimple-Adapters für SharePoint aus 

1. Deinstallieren Sie die vorherige Version des StorSimple-Adapters für SharePoint.

    >[AZURE.NOTE] Dadurch wird RBS automatisch auf die Inhaltsdatenbanken deaktiviert. Bereits vorhandene BLOBs bleiben allerdings auf dem StorSimple-Gerät erhalten. Da RBS deaktiviert ist und die BLOBs nicht wieder in die Inhaltsdatenbanken migriert wurden, sind Anforderungen für diese BLOBs nicht erfolgreich. 
 
2. Installieren Sie den StorSimple-Adapter für SharePoint. Der neue Adapter erkennt automatisch die Inhaltsdatenbanken, die zuvor mit RBS verwendet wurden, und verwendet die vorherigen Einstellungen.


