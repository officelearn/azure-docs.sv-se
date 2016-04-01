#Erstellen eines benutzerdefinierten virtuellen Computers

Ein *benutzerdefinierte* virtueller Computer ist mit einem virtuellen Computer, erstellen Sie mithilfe, der **aus Galerie** Methode da Sie weitere Optionen konfigurieren können die **Quick Create** Methode. Die Optionen umfassen:

- Mehr Imageoptionen zum Erstellen des virtuellen Computers (VM)
- Verbinden des virtuellen Computers mit einem virtuellen Netzwerk
- Hinzufügen des virtuellen Computers zu einem vorhandenen Cloud-Dienst
- Hinzufügen des virtuellen Computers zu einer Verfügbarkeitsgruppe

> [AZURE.IMPORTANT] Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden, damit Sie direkt anhand des Hostnamens oder Einrichten von standortübergreifenden Verbindungen herstellen können sollen, stellen Sie sicher, dass Sie das virtuelle Netzwerk angeben, wenn Sie den virtuellen Computer erstellen. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Weitere Informationen zu virtuellen Netzwerken finden Sie unter [virtuelle Netzwerke im Überblick](http://go.microsoft.com/fwlink/p/?LinkID=294063).

1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com) an.

2. Klicken Sie auf der Befehlsleiste auf **Neu**.

3. Klicken Sie auf **berechnen**, klicken Sie auf **virtuellen Computer**, und klicken Sie dann auf **aus Galerie**.

4. Wählen Sie das Image aus, das Sie verwenden möchten, und klicken Sie dann auf den Pfeil, um den Vorgang fortzusetzen.

5. Wenn mehrere Versionen des Bilds in der sind **Version Release Date**, wählen Sie die Version, die Sie verwenden möchten.

6. In **Name des virtuellen Computers**, geben Sie den Namen, die Sie für den virtuellen Computer verwenden möchten.

7. Verwendung **Ebene** und **Größe** die geeignete Größe für den virtuellen Computer aus. Die ausgewählte Größe wirkt sich auf die maximale Konfiguration des virtuellen Computer sowie auf die Kosten aus. Details zur Konfiguration finden Sie unter [Größen virtueller Computer und Cloud-Dienst für Azure](http://go.microsoft.com/fwlink/p/?LinkID=389844).

8. In **Neuer Benutzername**, geben Sie einen Namen für das Administratorkonto, das Sie zum Verwalten des Servers verwenden möchten.

9. In **Neues Kennwort**, geben Sie ein sicheres Kennwort für das Administratorkonto ein. In **Kennwort bestätigen**, das gleiche Kennwort erneut ein.

10. Klicken Sie auf den Pfeil, um fortzufahren.

11. In **Cloud-Dienst**, führen Sie eine der folgenden:

    - Wenn dies der erste oder einzige virtuelle Computer im Cloud-Dienst ist, wählen Sie **erstellen einen neuen Cloud-Dienst**. Klicken Sie dann im **Cloud Service DNS Name**, geben Sie einen Namen ein, der zwischen 3 und 24 Kleinbuchstaben und Zahlen. Dieser Name wird zu einem Teil des URI, mit dessen Hilfe der virtuelle Computer über den Clouddienst kontaktiert wird.
    - Wenn dieser virtuelle Computer zu einem Clouddienst hinzugefügt wird, wählen Sie ihn in der Liste aus.

    > [AZURE.NOTE] Weitere Informationen zur Platzierung virtueller Computer im selben Cloud-Dienst finden Sie unter [wie verbinden virtueller Computer in einem Clouddienst](http://www.windowsazure.com/manage/windows/how-to-guides/connect-to-a-cloud-service/).

12. In **Region/Affinity Group/Virtual Network**, wählen Sie Region, Affinitätsgruppe oder virtuelles Netzwerk, das Sie für den virtuellen Computer verwenden möchten. Weitere Informationen zu affinitätsgruppen finden Sie unter [Informationen zu Affinitätsgruppen für virtuelle Netzwerke](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

13. In **Speicherkonto**, wählen Sie ein vorhandenes Speicherkonto für die VHD-Datei oder ein automatisch generiertes Speicherkonto verwenden. Es wird nur ein Speicherkonto pro Region automatisch erstellt. Alle weiteren virtuellen Computer, die Sie mit dieser Einstellung erstellen, befinden sich in diesem Speicherkonto. Es sind maximal 20 Speicherkonten möglich.

14. Wenn Sie möchten, dass der virtuelle Computer in einer verfügbarkeitsgruppe gehören **Availability Set**, auf **verfügbarkeitsgruppe erstellen** oder einer vorhandenen verfügbarkeitsgruppe hinzu.

    **Hinweis**: virtuelle Maschinen in einer verfügbarkeitsgruppe in verschiedenen Fehlerdomänen bereitgestellt werden. Die Platzierung mehrerer virtueller Computer in einer Verfügbarkeitsgruppe sorgt dafür, dass Ihre Anwendung auch bei Netzwerkausfällen, bei Ausfällen der lokalen Hardware und während geplanter Ausfallzeiten verfügbar ist.

15.  Unter **Endpunkte**, überprüfen Sie die neuen Endpunkte, die erstellt werden, um Verbindungen mit dem virtuellen Computer über Remotedesktop oder einen Secure Shell (SSH)-Client zu ermöglichen. Sie können entweder jetzt Endpunkte hinzufügen oder diese später erstellen. Anleitung für die sie später erstellen, finden Sie unter [wie Einrichten von Endpunkten für einen virtuellen Computer](../articles/virtual-machines/virtual-machines-set-up-endpoints.md).

16.  Unter **VM-Agent**, entscheiden Sie, ob der VM-Agent zu installieren. Dieser Agent stellt die Umgebung bereit, in der Sie Erweiterungen installieren können, die Ihnen bei der Interaktion mit dem virtuellen Computer helfen können. Weitere Informationen finden Sie unter [Verwalten von Erweiterungen](http://go.microsoft.com/FWLink/p/?LinkID=390493).

17. Klicken Sie auf den Pfeil, um den virtuellen Computer zu erstellen.

    ![Benutzerdefinierter virtueller Computer erfolgreich erstellt](./media/howto-custom-create-vm/VMSuccessWindows.png)

##Nächste Schritte##
Nachdem der virtuelle Computer erstellt wurde, wird er automatisch gestartet. Wenn im Portal der Status "Wird ausgeführt" angezeigt wird, können Sie sich beim virtuellen Computer anmelden. Weitere Anweisungen finden Sie in den folgenden Artikeln:

- [Anmelden bei einem mit Linux betriebenen virtuellen Computer](../articles/virtual-machines/virtual-machines-linux-how-to-log-on.md)
- [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird](../articles/virtual-machines/virtual-machines-log-on-windows-server.md)


