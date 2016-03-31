<!--author=SharS last changed: 12/1/2015-->

#### So installieren Sie Update 1.2 über das klassische Azure-Portal

1. Wechseln Sie im klassischen Azure-Portal, um die **Geräte** Seite und wählen Sie Ihr Gerät.
 
2. Navigieren Sie zu **Geräte** > **konfigurieren**. 

3. Unter **Netzwerkschnittstellen**, zuerst sicher, dass Sie mindestens eine Netzwerkschnittstelle, die iSCSI-aktiviert ist. Suchen Sie dann die Netzwerkschnittstelle (nicht DATA 0), der ein Gateway zugewiesen wurde. 

4. Deaktivieren Sie die Netzwerkschnittstelle, der ein Gateway zugeordnet ist, und speichern Sie die geänderte Konfiguration. Beachten Sie, dass die Netzwerkschnittstellen-Einstellungen beibehalten werden. Wenn Sie also diese Netzwerkschnittstelle später wieder aktivieren, stellt das Portal wieder die ursprünglichen Einstellungen her.

7. Sie können jetzt [klassische Azure-Portal verwenden, installieren Sie Update 1.2](#install-update-12-via-the-azure-portal). Befolgen Sie die Anleitung beginnend mit Schritt 3 dieses Verfahrens. Nachdem Sie alle Updates installiert haben, können Sie die deaktivierte Netzwerkschnittstelle wieder aktivieren. 






