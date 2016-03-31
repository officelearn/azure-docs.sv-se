## Herunterladen, Installieren und Registrieren des Azure Backup-Agents

Nach dem Erstellen des Azure Backup-Tresors sollte auf jedem Windows-Computer (Windows Server, Windows-Client oder System Center Data Protection Manager-Server oder Azure Backup Server-Computer) ein Agent installiert werden, der das Sichern von Daten und Anwendungen in Azure ermöglicht.

1. Melden Sie sich bei der [-Verwaltungsportal](https://manage.windowsazure.com/)

2. Klicken Sie auf **Recovery Services**, wählen Sie den sicherungstresor aus, die Sie bei einem Server registrieren möchten. Die Seite "Schnellstart" für diesen Sicherungstresor wird angezeigt.

    ![Schnellstart](./media/backup-install-agent/quickstart.png)

3. Klicken Sie auf der Seite "Schnellstart" auf die **für Windows Server oder System Center Data Protection Manager oder Windows-Clients** option **Agent herunterladen**. Klicken Sie auf **Speichern** in den lokalen Computer zu kopieren.

    ![Agent speichern](./media/backup-install-agent/agent.png)

4. Sobald der Agent installiert ist, doppelklicken Sie auf „MARSAgentInstaller.exe“, um die Installation des Azure Backup-Agents zu starten. Wählen Sie den Installationsordner und den Ablageordner aus, der für den Agent erforderlich ist. Der verfügbare Speicherplatz am angegebenen Speicherort muss mindestens 5 % der Sicherungsdaten betragen.

5.  Wenn Sie einen Proxyserver für die Verbindung mit dem Internet in die **Proxykonfiguration** Bildschirm, geben Sie die Details des Proxyservers. Wenn Sie einen authentifizierten Proxy verwenden, geben Sie in diesem Bildschirm die Informationen zum Benutzernamen und zum Kennwort ein.

6.  Der Azure Backup-Agent installiert .NET Framework 4.5 und Windows PowerShell (falls noch nicht verfügbar), um die Installation abzuschließen.

7.  Nachdem der Agent installiert ist, klicken Sie auf die **mit Registrierung Fortfahren** Schaltfläche, um den Workflow fortzusetzen.

    ![Registrieren](./media/backup-install-agent/register.png)

8. Suchen Sie im Bildschirm für die Tresoranmeldeinformationen nach der zuvor heruntergeladenen Datei mit den Tresoranmeldeinformationen, und wählen Sie sie aus.

    ![Tresoranmeldeinformationen](./media/backup-install-agent/vc.png)

    Die Datei mit den Tresoranmeldeinformationen gilt nur für 48 Stunden (nachdem sie aus dem Portal heruntergeladen wurde). Wenn in diesem Bildschirm Fehler auftreten (z. B. "Datei mit Tresoranmeldeinformationen abgelaufen"), melden Sie sich beim Azure-Portal an, und laden Sie die Datei mit den Tresoranmeldeinformationen erneut herunter.

    Stellen Sie sicher, dass die Datei mit den Tresoranmeldeinformationen an einem Speicherort verfügbar ist, der für die Setupanwendung zugänglich ist. Wenn Zugriffsfehler auftreten, kopieren Sie die Datei mit den Tresoranmeldeinformationen in einen temporären Speicherort auf diesem Computer, und wiederholen Sie den Vorgang.

    Wenn ein Fehler wegen ungültiger Tresoranmeldeinformationen angezeigt wird (z. B. "Ungültige Tresoranmeldeinformationen angegeben"), ist die Datei entweder beschädigt oder enthält nicht die aktuellen Anmeldeinformationen, die dem Wiederherstellungsdienst zugeordnet sind. Wiederholen Sie den Vorgang, nachdem Sie eine neue Datei mit Tresoranmeldeinformationen vom Portal heruntergeladen haben. Dieser Fehler tritt in der Regel klickt der Benutzer auf die **tresoranmeldedaten herunterladen** -Option in der Azure-Portal in schneller Folge. In diesem Fall ist nur die zweite Datei mit Tresoranmeldeinformationen gültig.

9. In der **verschlüsselungseinstellung** Bildschirm können Sie eine Passphrase generieren oder eine Passphrase bereitstellen (mindestens 16 Zeichen). Vergessen Sie nicht, die Passphrase an einem sicheren Speicherort zu speichern.

    ![Verschlüsselung](./media/backup-install-agent/encryption.png)

    > [AZURE.WARNING] Wenn die Passphrase verloren geht oder vergessen wird, Microsoft kann nicht bei der Wiederherstellung der Sicherungsdaten helfen. Der Endbenutzer besitzt die Verschlüsselungspassphrase, und Microsoft kann nicht auf die vom Endbenutzer verwendete Passphrase zugreifen. Speichern Sie die Datei an einem sicheren Speicherort, da sie bei einem Wiederherstellungsvorgang benötigt wird.

10. Nach dem Klicken auf die **Fertig stellen** gewählt haben, der Computer erfolgreich beim Tresor registriert, und Sie können nun mit der Sicherung in Microsoft Azure beginnen.

11. Bei Verwendung von Microsoft Azure Backup eigenständige können, ändern Sie die Einstellungen, die während des registrierungsworkflows angegeben werden, indem Sie auf die **Eigenschaften ändern** -Option in der Azure Backup-Mmc-Snap-in.

    ![Eigenschaften ändern](./media/backup-install-agent/change.png)

    Auch wenn Data Protection Manager zu verwenden, können Sie ändern die Einstellungen, die während des registrierungsworkflows angegeben werden, indem Sie auf die **konfigurieren** Option dazu **Online** unter der **Management** Registerkarte.

    ![Konfigurieren von Azure Backup](./media/backup-install-agent/configure.png)


