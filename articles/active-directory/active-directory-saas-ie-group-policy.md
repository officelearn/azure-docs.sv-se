<properties
   pageTitle="How to Deploy the Access Panel Extension for Internet Explorer using Group Policy (Bereitstellen der Zugriffsbereichserweiterung für Internet Explorer mit der Gruppenrichtlinie; in englischer Sprache) | Microsoft Azure"
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
   ms.date="11/18/2015"
   ms.author="liviodlc"/>

#How to Deploy the Access Panel Extension for Internet Explorer using Group Policy (Bereitstellen der Zugriffsbereichserweiterung für Internet Explorer mit der Gruppenrichtlinie; in englischer Sprache)

In diesem Tutorial wird erläutert, wie Sie mithilfe von Gruppenrichtlinien die Zugriffsbereichserweiterung für Internet Explorer per Remotezugriff auf den Computern Ihrer Benutzer installieren. Diese Erweiterung ist erforderlich, damit Benutzer von Internet Explorer, die Anmeldung bei apps, die mithilfe von konfiguriert werden müssen [kennwortbasierte einmalige](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

Administratoren wird empfohlen, die Bereitstellung dieser Erweiterung zu automatisieren. Andernfalls müssen Benutzer die Erweiterung selbst herunterladen und installieren. Diese Vorgehensweise ist jedoch fehleranfällig; zudem sind Administratorberechtigungen erforderlich. In diesem Tutorial wird eine Methode zum Automatisieren von Softwarebereitstellungen mithilfe von Gruppenrichtlinien erläutert. [Weitere Informationen zu Gruppenrichtlinien](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Die zugriffsbereichserweiterung steht auch für [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) und [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), da diese keine Administratorberechtigungen zum Installieren.

##Voraussetzungen

- Sie haben eingerichtet [Active Directory-Domänendienste](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), und Sie müssen den Computern Ihrer Benutzer mit der Domäne verknüpft.
- Zum Bearbeiten von Gruppenrichtlinienobjekten benötigen Sie die Berechtigung „Einstellungen bearbeiten“. Standardmäßig verfügen Mitglieder der folgenden Sicherheitsgruppen über diese Berechtigung: Domänenadministratoren, Organisationsadministratoren und Richtlinien-Ersteller-Besitzer. [Weitere Informationen.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##Schritt 1: Erstellen des Verteilungspunkts

Zunächst müssen Sie das Installationspaket an einem Speicherort im Netzwerk ablegen, auf den von allen Computern aus zugegriffen werden kann, auf denen Sie die Erweiterung per Remotezugriff installieren möchten. Gehen Sie dazu folgendermaßen vor:

1. Melden Sie sich beim Server als Administrator an.

2. In der **Server-Manager** Wechseln Sie zu **Dateien und Speicherdienste**.

    ![Dateien und Speicherdienste öffnen](./media/active-directory-saas-ie-group-policy/files-services.png)

3. Wechseln Sie zu der **Freigaben** Registerkarte. Klicken Sie dann auf **Aufgaben** > **Neue Freigabe...**

    ![Dateien und Speicherdienste öffnen](./media/active-directory-saas-ie-group-policy/shares.png)

4. Führen Sie den **Assistenten für neue Freigaben** und Festlegen von Berechtigungen, um sicherzustellen, dass es von den Computern Ihrer Benutzer zugegriffen werden kann. [Weitere Informationen zu Freigaben](https://technet.microsoft.com/library/cc753175.aspx)

5. Die folgenden Microsoft Windows Installer-Paket (MSI-Datei) herunterladen: [Zugriff Bereich Extension.msi] (https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Bereich Extension.msi)

6. Kopieren Sie das Installationspaket an den gewünschten Speicherort auf der Freigabe.

    ![Kopieren Sie die MSI-Datei auf Ihre Freigabe.](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. Stellen Sie sicher, dass von den Clientcomputern aus auf das Installationspaket auf der Freigabe zugegriffen werden kann. 

##Schritt 2: Erstellen des Gruppenrichtlinienobjekts

1. Melden Sie sich bei dem Server mit den Active Directory-Domänendiensten (Active Directory Domain Services, AD DS) an.

2. Im Server-Manager, wechseln Sie zu **Tools** > **Group Policy Management**.

    ![Klicken Sie auf „Extras“ > „Gruppenrichtlinienverwaltung“.](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. Im linken Bereich von der **Group Policy Management** Fenster der Hierarchie der Organisationseinheit (OU) anzuzeigen und zu bestimmen, welcher Bereich der Gruppenrichtlinie angewendet werden soll. Sie können beispielsweise eine kleine Organisationseinheit auswählen, die einigen wenigen Benutzern zu Testzwecken bereitgestellt wird, oder eine Organisationseinheit der obersten Ebene festlegen, die im gesamten Unternehmen bereitgestellt wird.

    > [AZURE.NOTE] Wenn Sie möchten, erstellen oder Bearbeiten Ihrer Organisationseinheiten (OUs), wechseln Sie zurück an den Server-Manager und wechseln zu **Tools** > **Active Directory-Benutzer und-Computer**.

4. Wenn Sie eine Organisationseinheit ausgewählt haben, mit der rechten Maustaste darauf, und wählen Sie **in dieser Domäne ein Gruppenrichtlinienobjekt erstellen und verknüpfen Sie es hier...**

    ![Neues Gruppenrichtlinienobjekt erstellen](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. In der **Gruppenrichtlinienobjekt** dazu aufgefordert werden, geben Sie einen Namen für das neue Gruppenrichtlinienobjekt.

    ![Neues Gruppenrichtlinienobjekt benennen](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. Mit der rechten Maustaste auf das Gruppenrichtlinienobjekt, das Sie gerade erstellt haben, und wählen Sie **Bearbeiten**.

    ![Neues Gruppenrichtlinienobjekt bearbeiten](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##Schritt 3: Zuweisen des Installationspakets

1. Bestimmen, ob Sie die Erweiterung auf der Grundlage bereitstellen möchten **Computerkonfiguration** oder **Benutzerkonfiguration**. Bei Verwendung [Computerkonfiguration](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), auf dem Computer, unabhängig davon, welche Benutzer melden Sie sich an die Erweiterung installiert werden. Auf der anderen Seite mit [Benutzerkonfiguration](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), Benutzer haben die Erweiterung installiert, die Sie unabhängig davon, welche Computer sie melden Sie sich auf.

2. Im linken Bereich von der **Gruppenrichtlinienverwaltungs-Editor** Wechseln Sie auf die folgenden Ordnerpfade, je nachdem, welche Konfiguration Sie ausgewählt haben:
    - `Computer Configuration/Policies/Software Settings/`
    - `User Configuration/Policies/Software Settings/`

3. Mit der rechten Maustaste auf **Softwareinstallation**, und wählen Sie dann **Neu** > **Paket...**

    ![Neues Paket für die Softwareinstallation erstellen](./media/active-directory-saas-ie-group-policy/new-package.png)

4. Klicken Sie auf den freigegebenen Ordner, der das Installationspaket von enthält [Schritt 1: Erstellen der Verteilungspunkt](#step-1-create-the-distribution-point), wählen Sie die MSI-Datei, und klicken Sie auf **Öffnen**.

    > [AZURE.IMPORTANT] Wenn die Freigabe auf dem gleichen Server befindet, stellen Sie sicher, dass Sie die MSI-Datei über den lokalen Dateipfad, anstatt den Netzwerkpfad für die Datei zugreifen.

    ![Wählen Sie das Installationspaket im freigegebenen Ordner.](./media/active-directory-saas-ie-group-policy/select-package.png)

5. In der **Bereitstellen von Software** auffordern, wählen **zugewiesene** für Ihre Bereitstellungsmethode. Klicken Sie dann auf **OK**.

    ![Wählen Sie „Zugewiesen“, und klicken Sie dann auf „OK“.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

Die Erweiterung wird nun für die ausgewählte Organisationseinheit bereitgestellt. [Weitere Informationen zur Gruppenrichtlinien-Softwareinstallation](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##Schritt 4: Automatisches Aktivieren der Erweiterung für Internet Explorer 

Zusätzlich zur Ausführung des Installationsprogramms muss jede Erweiterung für Internet Explorer vor ihrer Verwendung explizit aktiviert werden. Gehen Sie folgendermaßen vor, um die Zugriffsbereichserweiterung mithilfe von Gruppenrichtlinien zu aktivieren:

1. In der **Gruppenrichtlinienverwaltungs-Editor** Wechseln Sie zu einem der folgenden Pfade, je nachdem, welche Konfiguration Sie in ausgewählt haben [Schritt 3: Zuweisen des Installationspakets](#step-3-assign-the-installation-package):
    - `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. Mit der rechten Maustaste auf **Add-on-Liste**, und wählen Sie **Bearbeiten**.
    ![Add-on-Liste zu bearbeiten.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. In der **Add-on-Liste** Wählen Sie im Fenster **aktiviert**. Klicken Sie unter der **Optionen** auf **anzeigen...**.

    ![Klicken Sie auf „Aktivieren“ und anschließend auf „Anzeigen...“.](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. In den **Inhalt anzeigen** Fenster die folgenden Schritte ausführen:

    1. Für die erste Spalte (die **Wertname** Feld), kopieren Sie die folgenden Klassen-ID: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

    2. Für die zweite Spalte (der **Wert** Feld), geben Sie den folgenden Wert: `1`

    3. Klicken Sie auf **OK** Schließen die **Inhalt anzeigen** Fenster.

    ![Füllen Sie die Werte wie oben angegeben aus.](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. Klicken Sie auf **OK** zu übernehmen, und schließen Sie die **Add-on-Liste** Fenster.

Die Erweiterung sollte jetzt für die Computer in der ausgewählten Organisationseinheit aktiviert sein. [Erfahren Sie mehr über die Verwendung von Gruppenrichtlinien zum Aktivieren oder Deaktivieren von Add-Ons für Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

##Schritt 5 (optional): Deaktivieren der Frage nach der Kennwortspeicherung

Wenn sich Benutzer unter Verwendung der Zugriffsbereichserweiterung bei Websites anmelden, kann Internet Explorer fragen, ob der Benutzer sein Kennwort speichern möchte.

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Falls diese Frage nicht angezeigt werden soll, führen Sie die folgenden Schritte aus, um das Speichern von Kennwörtern durch AutoVervollständigen zu unterbinden:

1. In der **Gruppenrichtlinienverwaltungs-Editor** Wechseln Sie in den unten aufgeführten Pfad. Beachten Sie, dass diese Einstellung nur unter **Benutzerkonfiguration**.
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`

2. Suchen Sie die Einstellung mit dem Namen **aktivieren die Funktion AutoVervollständigen für Benutzernamen und Kennwörter in Formularen**.

    > [AZURE.NOTE] Frühere Versionen von Active Directory können diese Einstellung mit dem Namen Liste **Automatische Vervollständigung Speichern von Kennwörtern nicht zulassen**. Die Konfiguration für diese Einstellung unterscheidet sich von der in diesem Tutorial beschriebenen Einstellung.

    ![Diese finden Sie in den Benutzereinstellungen.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)

3. Klicken Sie mit der rechten Maustaste auf die obige Einstellung, und wählen Sie **Bearbeiten**.

4. Im Fenster **aktivieren die Funktion AutoVervollständigen für Benutzernamen und Kennwörter in Formularen**, Option **deaktiviert**.

    ![Wählen Sie „Deaktivieren“ aus.](./media/active-directory-saas-ie-group-policy/disable-passwords.png)

5. Klicken Sie auf **OK** diese Änderungen anwenden und schließen Sie das Fenster.

Daraufhin können Benutzer ihre Anmeldeinformationen nicht mehr speichern oder mithilfe von AutoVervollständigen auf zuvor gespeicherte Anmeldeinformationen zugreifen. Für andere Arten von Formularfeldern (etwa Suchfelder) kann AutoVervollständigen dagegen weiter verwendet werden.

> [AZURE.WARNING] Wenn diese Richtlinie aktiviert ist, nachdem Benutzer ausgewählt haben, speichern Sie einige Anmeldeinformationen, die dieser Richtlinie wird *nicht* Deaktivieren Sie die Anmeldeinformationen, die bereits gespeichert wurden.

##Schritt 6: Testen der Bereitstellung

Gehen Sie folgendermaßen vor, um zu überprüfen, ob die Erweiterungsbereitstellung erfolgreich war:

1. Wenn Sie mit **Computerkonfiguration**, melden Sie sich bei einem Clientcomputer, die mit der Organisationseinheit, die im ausgewählten gehört [Schritt2: Erstellen Sie das Gruppenrichtlinienobjekt](#step-2-create-the-group-policy-object). Wenn Sie mit **Benutzerkonfiguration**, vergewissern Sie sich als Benutzer anmelden, der an diese Organisationseinheit angehört.

2. Möglicherweise werden die Gruppenrichtlinienänderungen auf dem Computer erst durch mehrmaliges Anmelden vollständig aktualisiert. Um die Aktualisierung zu erzwingen, öffnen Sie eine **Command Prompt** Fenster und führen Sie den folgenden Befehl aus: `gpupdate /force`

3. Der Computer muss neu gestartet werden, damit die Installation ausgeführt werden kann. Das Starten kann während der Installation der Erweiterung erheblich länger dauern als gewöhnlich.

4. Öffnen Sie nach dem Neustart **Internet Explorer**. Klicken Sie auf der oberen rechten Ecke des Fensters auf **Tools** (Zahnradsymbol), und wählen Sie dann **Add-ons verwalten**.

    ![Klicken Sie auf „Extras“ > „Add-Ons verwalten“.](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. In der **Add-ons verwalten** Fenster, überprüfen Sie, ob die **Zugriffsbereichserweiterung** installiert wurde und seine **Status** festgelegt wurde **aktiviert**.

    ![Stellen Sie sicher, dass die Zugriffsbereichserweiterung installiert und aktiviert wurde.](./media/active-directory-saas-ie-group-policy/verify-install.png)

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

