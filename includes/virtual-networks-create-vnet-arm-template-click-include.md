## Bereitstellen der ARM-Vorlage per Klick

Sie können vordefinierte ARM-Vorlagen, die in ein von Microsoft verwaltetes und für die Community freigegebenes GitHub-Repository hochgeladen wurden, wiederverwenden. Diese Vorlagen können direkt aus GitHub heraus bereitgestellt oder heruntergeladen und an Ihre Bedürfnisse angepasst werden. Zum Bereitstellen einer Vorlage, die ein VNet mit zwei Subnetzen erstellt, führen Sie die unten aufgeführten Schritte aus.

1. Ein Browser, navigieren Sie zu [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Einen Bildlauf nach unten in der Liste der Vorlagen, und klicken Sie auf **101 zwei Subnetze**. Überprüfen Sie die **README.md** Datei, wie unten dargestellt.

    ![READEME.md-Datei in GitHub](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Klicken Sie auf **Bereitstellen in Azure**. Falls erforderlich, geben Sie Ihre Anmeldeinformationen für Azure ein. 
4. In der **Parameter** Blatt geben Sie die Werte zu verwenden, um Ihr neues VNet erstellen, und klicken Sie dann auf **OK**. Die folgende Abbildung zeigt die Werte für das vorliegende Szenario.

    ![ARM-Vorlagenparameter](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. Klicken Sie auf **Ressourcengruppe** und wählen Sie eine Ressourcengruppe zum Hinzufügen des VNet für, oder klicken Sie auf **neu erstellen** VNet eine neue Ressourcengruppe hinzu. Weitere Informationen zu Ressourcengruppen finden Sie unter [](). Die folgende Abbildung zeigt die Ressource die Einstellungen für eine neue Ressourcengruppe namens **TestRG**.

    ![Ressourcengruppe](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. Ändern Sie ggf. die **Abonnement** und **Speicherort** Einstellungen für Ihr VNet.
6. Wenn Sie nicht, finden in der VNet als Kachel in möchten der **im Startmenü**, deaktivieren Sie **an Startmenü anheften**.
5. Klicken Sie auf **Leagl Begriffe**, lesen Sie die rechtlichen Hinweise, und klicken Sie auf **kaufen** zustimmen. 
6. Klicken Sie auf **Erstellen** VNet zu erstellen.

    ![Senden der Bereitstellungskachel im Vorschauportal](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. Nachdem die Bereitstellung abgeschlossen ist, klicken Sie auf **TestVNet** > **Alle Einstellungen** > **Subnetze** zu den Eigenschaften für das Subnetz, wie unten dargestellt angezeigt.

    ![Erstellen von VNet im Vorschauportal](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)

