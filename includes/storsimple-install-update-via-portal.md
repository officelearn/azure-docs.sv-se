<!--author=SharS last changed: 11/16/15-->

#### So installieren Sie Update 1.2 über das Azure-Portal

1. Wählen Sie auf der Seite des StorSimple-Diensts Ihr Gerät aus. Navigieren Sie zu **Geräte** > **Wartung**.

2. Klicken Sie unten auf der Seite auf **Updates suchen**. Für die Suche nach verfügbaren Updates wird ein Auftrag erstellt. Sie werden benachrichtigt, wenn der Auftrag erfolgreich abgeschlossen wurde.

3. In der **Softwareupdates** Abschnitt auf derselben Seite sehen Sie, dass neue Softwareupdates verfügbar sind. Es wird empfohlen, die Versionshinweise zu lesen, bevor Sie Update 1.2 auf Ihrem Gerät installieren.

    ![Softwareupdates installieren](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)

4. Klicken Sie unten auf der Seite auf **Updates installieren**.

5. Sie werden aufgefordert, diesen Schritt zu bestätigen. Klicken Sie auf **OK**.

6. Ein **Updates installieren** Dialogfeld wird angezeigt. Ihr Gerät sollte die in diesem Dialogfeld aufgeführten Voraussetzungen erfüllen. Diese Schritte wurden vor dem Update ausgeführt. Wählen Sie **ich den oben aufgeführten Anforderungen gelesen und bin bereit, das Gerät zu aktualisieren**. Klicken Sie auf das Häkchensymbol.

    ![Bestätigungsmeldung](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)

7. Eine Reihe von automatischen Vorabüberprüfungen wird jetzt gestartet. Diese umfassen:

    - **Controller integritätsprüfungen** um sicherzustellen, dass beide Gerätecontroller fehlerfrei und online sind.
    
    - **Integritätsprüfungen für Hardware-Komponente** zu überprüfen, ob alle Hardwarekomponenten im StorSimple-Gerät fehlerfrei sind.
    
    - **DATA 0-Prüfungen** stellen Sie sicher, dass DATA 0 auf Ihrem Gerät aktiviert ist. Wenn diese Schnittstelle nicht aktiviert ist, müssen Sie sie aktivieren, und es dann erneut versuchen.
    
    - **DATA 2- und DATA 3-Prüfungen** stellen Sie sicher, dass DATA 2- und DATA 3-Netzwerkschnittstellen nicht aktiviert sind. Wenn diese Schnittstellen aktiviert sind, müssen Sie deaktiviert werden, bevor Sie Ihr Gerät aktualisieren. Diese Prüfung wird nur bei der Aktualisierung auf einem Gerät durchgeführt, auf dem GA-Software ausgeführt wird. Bei Geräten, auf denen Versionen von 0.1, 0.2 oder 0.3 ausgeführt werden, ist diese Prüfung nicht erforderlich.
    
    - **Gateway-Prüfung** auf jedem Gerät eine Version vor Update 1. Diese Prüfung wird auf Geräten durchgeführt, auf denen Software zur Vorabprüfung 1 ausgeführt wird, aber auf Geräten fehlschlägt, auf denen ein Gateway für eine andere Netzwerkschnittstelle als DATA 0 konfiguriert ist.
 
    Update 1.2 wird nur angewendet, wenn alle obigen vor dem Update durchgeführten Prüfungen erfolgreich abgeschlossen wurden. Sie erhalten die Benachrichtigung, dass vor dem Update Überprüfungen ausgeführt werden.
  
    ![Benachrichtigung zur Vorabprüfung](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)

    Nachfolgend sehen Sie ein Beispiel, in dem die Überprüfung vor dem Upgrade nicht erfolgreich war. Sie müssen sicherstellen, dass beide Gerätecontroller fehlerfrei und online sind. Sie müssen außerdem den Zustand der Hardwarekomponenten überprüfen. In diesem Beispiel erfordern Controller 0- und Controller 1-Komponenten Ihre Aufmerksamkeit. Möglicherweise müssen Sie den Microsoft Support kontaktieren, wenn Sie diese Probleme nicht selbst beheben können.

     ![Fehler bei der Vorabprüfung](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

    > [AZURE.NOTE] Nachdem Sie Update 1.2 im StorSimple-Gerät angewendet haben, DATA 2 und DATA 3-Prüfungen und die Gateway-Prüfung nicht mehr wird für die zukünftige Updates erforderlich. Die anderen Vorabprüfungen sind noch immer erforderlich.


8. Wenn die Überprüfungen vor dem Upgrade erfolgreich abgeschlossen wurden, wird ein Updateauftrag erstellt. Sie werden benachrichtigt, wenn der Updateauftrag erfolgreich erstellt wurde.
 
    ![Erstellen eines Updateauftrags](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)

    Das Update wird dann auf Ihr Gerät angewendet.
 
9. Klicken Sie zum Überwachen des Fortschritts des updateauftrags **Auftrag anzeigen**. Auf der **Aufträge** Seite sehen Sie den Status der Aktualisierung. 

    ![Status des Updateauftrags](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)

10. Das Update kann einige Stunden dauern. Sie können die Details des Auftrags jederzeit anzeigen.

    ![Details zum Updateauftrag](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)

11. Nachdem der Auftrag abgeschlossen ist, navigieren Sie zu der **Wartung** Seite und einen Bildlauf nach unten zum **Softwareupdates**.

12. Stellen Sie sicher, dass Ihr Gerät läuft **StorSimple 8000-Serie Update 1.2 (6.3.9600.17584)**. Die **Datum der letzten Aktualisierung** muss ebenfalls geändert werden.

    ![Seite "Wartung"](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)

13. Sie sehen nun, dass Wartungsmodus-Updates verfügbar sind. Diese Updates sind störende Updates, die zu Ausfallzeiten beim Gerät führen und nur über die Windows PowerShell-Schnittstelle Ihres Geräts angewendet werden können. Führen Sie die Schritte [Installieren Sie Wartungsmodus-Updates](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple) diese Updates über Windows PowerShell für StorSimple installieren.

> [AZURE.NOTE] In bestimmten Fällen kann die Meldung bezüglich des Wartungsmodus-Updates verfügbar sind von 24 Stunden, nachdem der Wartungsmodus-Updates auf dem Gerät erfolgreich angewendet wurden, angezeigt.  




