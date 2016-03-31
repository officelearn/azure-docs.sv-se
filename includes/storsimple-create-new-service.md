<!--author=alkohli last changed: 12/1/15-->


#### So erstellen Sie einen neuen Dienst

1. Mithilfe Ihrer Microsoft-Kontoanmeldeinformationen, melden Sie sich beim klassischen Azure-Portal unter dieser URL: [http://azure.microsoft.com/](http://azure.microsoft.com/).

2. Klicken Sie in der klassischen Azure-Portal auf **Neu** > **Data Services** > **StorSimple Manager** > **Schnellerfassung**.

3. Gehen Sie im angezeigten Formular folgendermaßen vor:
  1. Geben Sie einen eindeutigen **Namen** für Ihren Dienst. Dies ist ein Anzeigename, der zum Identifizieren des Diensts verwendet werden kann. Der Name kann zwischen 2 und 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.
  2. Geben Sie einen **Speicherort** für Ihren Dienst. Üblicherweise sollten Sie den Standort auswählen, welcher der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten liegt. Berücksichtigen Sie ggf. auch Folgendes: 
     
        - Falls Sie vorhandene Workloads in Azure haben, die Sie auch mit Ihrem StorSimple-Gerät bereitstellen möchten, sollten Sie das Datencenter nutzen.
        - Der StorSimple Manager-Dienst und der Azure-Speicher können sich an zwei verschiedenen Standorten befinden. In einem solchen Fall müssen Sie das StorSimple Manager- und das Azure-Speicherkonto separat erstellen. Um ein Speicherkonto zu erstellen, wechseln Sie zu Azure Storage-Diensts im klassischen Azure-Portal, und führen Sie die Schritte in [Erstellen von Azure-Speicherkonten](storage-create-storage-account.md#create-a-storage-account). Nach der Erstellung dieses Kontos hinzufügen zu StorSimple Manager-Dienst mithilfe der Schritte in [Konfigurieren eines neuen Speicherkontos für den Dienst](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service).
         
  3. Wählen Sie eine **Abonnement** aus der Dropdown-Liste. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. Dieses Feld wird nicht angezeigt, wenn Sie nur ein Abonnement besitzen.
  4. Wählen Sie **erstellen ein neues Speicherkonto** automatisch ein Speicherkonto mit dem Dienst erstellt. Dieses Speicherkonto erhält einen speziellen Namen wie "storsimplebwv8c6dcnf". Wenn Ihre Daten an einem anderen Speicherort gespeichert werden sollen, deaktivieren Sie dieses Kontrollkästchen. 
  5. Klicken Sie auf **StorSimple-Manager erstellen** zum Erstellen des Diensts.

   ![StorSimple-Manager erstellen](./media/storsimple-create-new-service/HCS_CreateAService-include.png)

  Sie werden weitergeleitet werden, um die **Service** Landing Page wird angezeigt. Die Diensterstellung dauert einige Minuten. Nachdem der Dienst erfolgreich erstellt wurde, Sie entsprechend benachrichtigt werden und der Status des Diensts sich in ändert **Active**.
 
   ![Diensterstellung](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

![Video verfügbar](./media/storsimple-create-new-service/Video_icon.png) **Video verfügbar**

Um ein Video ansehen, die veranschaulicht, wie Sie einen neuen StorSimple-Manager-Dienst zu erstellen, klicken Sie auf [hier](http://azure.microsoft.com/documentation/videos/create-a-storsimple-manager-service/).

