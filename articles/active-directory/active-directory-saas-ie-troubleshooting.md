<properties
   pageTitle="Problembehandlung in der Zugriffsbereichserweiterung für Internet Explorer | Microsoft Azure"
   description="So stellen Sie das Internet Explorer-Add-On für das Portal "Meine Apps" mithilfe von Gruppenrichtlinien bereit"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/28/2015"
   ms.author="liviodlc"/>

#Problembehandlung in der Zugriffsbereichserweiterung für Internet Explorer

Dieser Artikel hilft Ihnen bei der Behandlung der folgenden Probleme:

- Während der Verwendung von Internet Explorer ist nicht möglich, über das Portal "Meine Apps" auf Ihre Apps zuzugreifen.
- Die Meldung "Software installieren" wird angezeigt, obwohl Sie die Software bereits installiert haben.

Wenn Sie Administrator sind, siehe auch: [Zugriffsbereichserweiterung für Internet Explorer mithilfe der Gruppenrichtlinie bereitstellen](active-directory-saas-ie-group-policy.md)

##Ausführen des Diagnoseprogramms

Sie können Probleme bei der Installation mithilfe der Zugriffsbereichserweiterung diagnostizieren, indem Sie das Diagnoseprogramm "Zugriffsbereich" herunterladen und ausführen:

1. [Klicken Sie hier, um das Diagnoseprogramm herunterzuladen.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Öffnen Sie die Datei, und drücken Sie die **Alle extrahieren** Schaltfläche.

    ![Auf "Alle extrahieren" klicken](./media/active-directory-saas-ie-troubleshooting/extract1.png)

3. Drücken Sie dann die **extrahieren** um fortzufahren.

    ![Auf "Extrahieren" klicken](./media/active-directory-saas-ie-troubleshooting/extract2.png)

4. Zum Ausführen des Tools Maustaste die Datei mit dem Namen **AccessPanelExtensionDiagnosticTool**, und wählen Sie dann **mit öffnen > Microsoft Windows-basierten Skripthost**.

    ![Öffnen mit > Microsoft Windows Script Host](./media/active-directory-saas-ie-troubleshooting/open_tool.png)

5. Anschließend wird das folgende Diagnosefenster angezeigt, in dem die möglichen Probleme bei der Installation angezeigt werden.

    ![Ein Beispiel für das Diagnosefenster](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)

6. Klicken Sie auf "**Ja**" können Sie das Programm, das Problem zu beheben, die gefunden wurden.

7. Schließen Sie alle Internet Explorer-Fenster, und öffnen Sie Internet Explorer erneut, um diese Änderungen zu speichern.<br />Wenn Sie weiterhin Ihre apps zugreifen können, versuchen Sie die folgenden Schritte aus.

##Überprüfen, ob die Zugriffsbereichserweiterung aktiviert ist

So überprüfen Sie, ob die Zugriffsbereichserweiterung in Internet Explorer aktiviert ist:

1. Klicken Sie in Internet Explorer auf die **Zahnradsymbol** in der oberen rechten Ecke des Fensters. Wählen Sie dann **Internetoptionen**.<br />(In älteren Versionen von Internet Explorer Sie finden Sie unter **Extras > Internetoptionen**.

    ![Auf "Extras" > "Internetoptionen" klicken](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)

2. Klicken Sie auf die **Programme** Registerkarte, und klicken Sie dann auf die **Verwalten von Add-ons** Schaltfläche.

    ![Auf "Add-Ons verwalten" klicken](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)

3. Wählen Sie in diesem Dialogfeld **Zugriffsbereichserweiterung** und klicken Sie dann auf die **aktivieren** Schaltfläche.

    ![Auf "Aktivieren" klicken](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)

4. Um diese Änderungen zu speichern, schließen Sie alle Internet Explorer-Fenster, und öffnen Sie Internet Explorer dann erneut.

##Aktivieren von Erweiterungen für InPrivate-Browsen

Gehen Sie wie folgt vor, wenn Sie den Modus "InPrivate-Browsen" verwenden:

1. Klicken Sie in Internet Explorer auf die **Zahnradsymbol** in der oberen rechten Ecke des Fensters. Wählen Sie dann **Internetoptionen**.<br />(In älteren Versionen von Internet Explorer Sie finden Sie unter **Extras > Internetoptionen**.

    ![Ein Beispiel für das Diagnosefenster](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)

2. Wechseln Sie zu der **Datenschutz** Registerkarte, klicken Sie dann **Deaktivieren Sie** das Kontrollkästchen **Deaktivieren von Symbolleisten und Erweiterungen beim InPrivate-Browsen starten**</p>

    !["Symbolleisten und Erweiterungen beim Starten des InPrivate-Browsens deaktivieren" deaktivieren](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)

3. Um diese Änderungen zu speichern, schließen Sie alle Internet Explorer-Fenster, und öffnen Sie Internet Explorer dann erneut.

##Deinstallieren der Zugriffsbereichserweiterung

So deinstallieren Sie die Zugriffsbereichserweiterung auf Ihrem Computer:

1. Drücken Sie auf der Tastatur die **Windows-Taste** im Startmenü zu öffnen. Wenn das Menü geöffnet ist, können Sie beliebige Begriffe für die Suche eingeben. Geben Sie "Systemsteuerung", und öffnen Sie dann die **Systemsteuerung** Wenn es in den Suchergebnissen angezeigt wird.

    ![Systemsteuerung suchen](./media/active-directory-saas-ie-troubleshooting/search_sm.png)

2. In der oberen rechten Ecke der Systemsteuerung, ändern Sie die **anzeigen, indem Sie** option **große Symbole**. Suchen und klicken Sie auf die **Programme und Funktionen** Schaltfläche.

    ![Ansicht in große Symbole ändern](./media/active-directory-saas-ie-troubleshooting/control_panel.png)

3. Wählen Sie aus der Liste **Zugriffsbereichserweiterung**, und klicken auf die **Deinstallieren** Schaltfläche.

    ![Auf "Deinstallieren" klicken](./media/active-directory-saas-ie-troubleshooting/uninstall.png)

4. Sie können dann versuchen, die Erweiterung erneut zu installieren, um festzustellen, ob das Problem behoben wurde.

Wenn beim Deinstallieren der Erweiterung Probleme auftreten, können Sie auch entfernen sie mithilfe der [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) Tool.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]


