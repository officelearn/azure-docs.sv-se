1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com) an. Sehen Sie sich die [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) bieten, wenn Sie noch kein Abonnement haben.

2. Klicken Sie auf der Befehlsleiste am unteren Rand des Fensters auf **Neu**.

3. Unter **Compute**, klicken Sie auf **virtuellen Computer**, und klicken Sie dann auf **aus Galerie**.

    ![Navigieren Sie auf der Befehlsleiste zu „Aus Katalog“.](./media/virtual-machines-create-WindowsVM/fromgallery.png)

4. Im ersten Bildschirm können Sie **Wählen Sie ein Bild** für den virtuellen Computer aus der Liste der verfügbaren Images. (Die verfügbaren Images können sich je nach dem von Ihnen verwendeten Abonnement unterscheiden.)

5. Im zweiten Bildschirm wählen Sie einen Computernamen, Größe sowie Benutzername und Passwort des Administrators aus. Andernfalls wählen Sie die benötigte Schicht und die Größe, um Ihre Anwendung oder Ihren Leistungsumfang auszuführen. Hier einige Tipps:

    - **Neuer Benutzername** bezieht sich auf das Administratorkonto, das Sie zum Verwalten des Servers verwenden. Geben Sie für dieses Konto ein individuelles Passwort an und stellen Sie sicher, dass Sie es nicht vergessen. **Sie benötigen den Benutzernamen und das Kennwort zum Anmelden auf dem virtuellen Computer**.

    - Die Größe eines virtuellen Computers wirkt sich auf seine Betriebskosten sowie auf die Konfigurationsoptionen aus (z. B. die Anzahl von Datenträgern, die Sie anschließen können). Weitere Informationen finden Sie unter [Größen für virtuelle Computer](../articles/virtual-machines-size-specs.md).

6. Auf dem dritten Bildschirm können Sie die Ressourcen für Netzwerk, Speicher und Verfügbarkeit konfigurieren. Hier einige Tipps:

    - Die **Cloud Service DNS Name** der globalen DNS-Name, der Teil des URIS wird, der zum Kontaktieren des virtuellen Computers verwendet wird. Sie müssen einen eigenen Namen für den Clouddienst eingeben, da dieser in Azure eindeutig sein muss. Clouddienste sind wichtig für Szenarien mit [mehrere virtuelle Computer](../articles/cloud-services-connect-virtual-machine.md).

    - Für **Region/Affinity Group/Virtual Network**, verwenden Sie eine Region aus, die für Ihren Standort angemessen ist. Sie können stattdessen ein virtuelles Netzwerk angeben.

    >[AZURE.NOTE] Wenn Sie eine virtuelle Maschine ein virtuelles Netzwerk verwenden möchten Sie **müssen** das virtuelle Netzwerk beim Erstellen des virtuellen Computers angeben. Sie können den virtuellen Computer nicht mit einem virtuellen Netzwerk verknüpfen, nachdem Sie den virtuellen Computer erstellt haben. Weitere Informationen finden Sie unter [virtuelle Netzwerke im Überblick](virtual-networks-overview.md).
    >
    > Weitere Informationen zum Konfigurieren von Endpunkten finden Sie unter [wie Einrichten von Endpunkten für einen virtuellen Computer](../articles/virtual-machines-set-up-endpoints.md).

7. Auf dem vierten Konfigurationsbildschirm können Sie den VM-Agent installieren und einige der verfügbaren Erweiterungen konfigurieren.

    >[AZURE.NOTE] Der VM-Agent stellt die Umgebung bereit zum Installieren von Erweiterungen, mit denen Sie interagieren oder den virtuellen Computer zu verwalten. Weitere Informationen finden Sie unter [der VM-Agent und Erweiterungen](virtual-machines-extensions-agent-about.md).  

8. Nachdem die virtuelle Maschine erstellt wurde, im Verwaltungsportal den neuen virtuellen Computer unter **virtuelle Computer**. In diesen Abschnitten werden auch der entsprechende Clouddienst und das Speicherkonto erstellt und aufgelistet. Sowohl der virtuelle Computer und Cloud-Dienst automatisch gestartet und deren Status als aufgeführt **unter**.

    ![Konfigurieren Sie den VM-Agent und die Endpunkte des virtuellen Computers.](./media/virtual-machines-create-WindowsVM/vmcreated.png)


