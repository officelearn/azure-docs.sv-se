<properties
    pageTitle="Verwalten von Verbundzertifikaten in Azure AD | Microsoft Azure"
    description="Erfahren Sie, wie Sie das Ablaufdatum für Verbundzertifikate anpassen und Zertifikate erneuern, die in Kürze ablaufen."
    services="active-directory"
    documentationCenter=""
    authors="liviodlc"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2015"
    ms.author="liviodlc"/>

#Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory

Dieser Artikel enthält häufige gestellte Fragen im Zusammenhang mit den Zertifikaten, die Azure Active Directory erstellt, um die einmalige Verbundanmeldung für Ihre SaaS-Anwendungen einzurichten.

Dieser Artikel ist nur relevant für apps, die für die Verwendung konfiguriert **Azure AD Single Sign-On**, wie im folgenden Beispiel gezeigt:

![Azure AD – einmaliges Anmelden](./media/active-directory-sso-certs/fed-sso.PNG)

##Anpassen des Ablaufdatums für das Verbundzertifikat

Standardmäßig wird für Zertifikate eine Gültigkeitsdauer von zwei Jahren festgelegt. Mit folgenden Schritten können Sie ein anderes Ablaufdatum für Ihr Zertifikat auswählen. In den Screenshots wurde Salesforce als Beispiel verwendet, diese Schritte können jedoch für jede SaaS-Verbund-App angewendet werden.

1. Klicken Sie in Azure Active Directory auf der Seite "Schnellstart" für Ihre Anwendung auf **einmaliges Anmelden konfigurieren**.

    ![Öffnen des SSO-Konfigurations-Assistenten](./media/active-directory-sso-certs/config-sso.png)

2. Wählen Sie **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

3. Geben Sie in der **Anmelde-URL** Ihrer Anwendung, und aktivieren Sie das Kontrollkästchen **das Zertifikat zum Verbund einmaliges Anmelden konfigurieren**. Klicken Sie dann auf **Weiter**.

    ![Azure AD – einmaliges Anmelden](./media/active-directory-sso-certs/new-app-config-sso.PNG)

4. Wählen Sie auf der nächsten Seite **ein neues Zertifikat generiert**, und wählen Sie, wie lange das Zertifikat gültig sein soll. Klicken Sie dann auf **Weiter**.

    ![Generieren eines neues Zertifikats](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Klicken Sie anschließend auf **Zertifikat herunterladen**. Informationen zum Hochladen des Zertifikats in Ihre spezielle SaaS-app, klicken Sie auf **-konfigurationsanweisungen anzeigen**.

    ![Herunterladen und Hochladen des Zertifikats](./media/active-directory-sso-certs/new-app-config-app.PNG)

6. Das Zertifikat wird erst aktiviert, wenn Sie das Kontrollkästchen "Bestätigung" am unteren Rand des Dialogfelds aktivieren und auf "Senden" klicken.

##Vorgehensweise zum Erneuern eines in Kürze ablaufenden Zertifikats

Im Idealfall sollte die unten gezeigten Schritte zur Erneuerung keine wesentliche Ausfallzeiten für Ihre Benutzer verursachen. In den Screenshots in diesem Abschnitt wurde Salesforce als Beispiel verwendet, diese Schritte können jedoch für jede SaaS-Verbund-App angewendet werden.

1. Klicken Sie in Azure Active Directory auf der Seite "Schnellstart" für Ihre Anwendung auf **Konfigurieren Sie einmaliges Anmelden**.

    ![Öffnen des SSO-Konfigurations-Assistenten](./media/active-directory-sso-certs/renew-sso-button.PNG)

2. Auf der ersten Seite des Dialogfelds **Azure AD Single Sign-On** sollte bereits ausgewählt ist, klicken Sie auf **Weiter**.

3. Auf der zweiten Seite, aktivieren Sie das Kontrollkästchen **das Zertifikat zum Verbund einmaliges Anmelden konfigurieren**. Klicken Sie dann auf **Weiter**.

    ![Azure AD – einmaliges Anmelden](./media/active-directory-sso-certs/renew-config-sso.PNG)

4. Wählen Sie auf der nächsten Seite **ein neues Zertifikat generiert**, und wählen Sie, wie lange das neue Zertifikat gültig sein soll. Klicken Sie dann auf **Weiter**.

    ![Generieren eines neues Zertifikats](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Klicken Sie auf **Zertifikat herunterladen**. Um Ihr Zertifikat zu verlängern, führen Sie die folgenden beiden Schritte aus:

    - Laden Sie das neue Zertifikat im Bestätigungsbildschirm der einmaligen Anmeldung der SaaS-App hoch. Vorgehensweise für Ihre spezielle SaaS-app finden Sie unter **-konfigurationsanweisungen anzeigen**.

    - Wählen Sie in Azure AD das Kontrollkästchen zur Bestätigung unten im Dialogfeld, um das neue Zertifikat zu aktivieren, und klicken Sie dann auf **Weiter** übermitteln.

    > [AZURE.IMPORTANT] Einmaliges Anmelden für die app wird deaktiviert einen Moment dieser beiden Schritte abgeschlossen ist, aber es wird wieder aktiviert, sobald der zweite Schritt abgeschlossen ist. Um daher Ausfallzeiten so gering wie möglich zu halten, bereiten Sie sich darauf vor, beide Schritte möglichst schnell nacheinander auszuführen.

    ![Herunterladen und Hochladen des Zertifikats](./media/active-directory-sso-certs/renew-config-app.PNG)

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]


