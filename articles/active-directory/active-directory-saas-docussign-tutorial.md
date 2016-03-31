<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit DocuSign | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und DocuSign konfigurieren."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2015"
    ms.author="markvi"/>


# Tutorial: Azure Active Directory-Integration mit DocuSign

In diesem Tutorial wird die Integration von Azure und DocuSign erläutert.
Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

- Ein gültiges Azure-Abonnement
- Einen Mandanten in DocuSign



Das in diesem Lernprogramm beschriebene Szenario besteht aus den folgenden Bausteinen:

1. [Aktivieren der Anwendungsintegration für DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Konfigurieren der einmaligen Anmeldung](#configuring-single-sign-on) 


3. [Konfigurieren der Kontobereitstellung](#configuring-account-provisioning) 


4. [Zuweisen von Benutzern](#assigning-users) 




<br><br>![Konfigurieren der einmaligen Anmeldung ][0]<br>


 

## Aktivieren der Anwendungsintegration für DocuSign

In diesem Abschnitt wird beschrieben, wie Sie die Anwendungsintegration für DocuSign aktivieren.

### Führen Sie die folgenden Schritte aus, um die Anwendungsintegration für DocuSign zu aktivieren:

1. Klicken Sie in der Azure-Verwaltungsportal im linken Navigationsbereich auf **Active Directory**.
<br><br>![Konfigurieren der einmaligen Anmeldung ][1]<br>

2. Wählen Sie in der Liste Verzeichnis das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.
<br><br>![Konfigurieren der einmaligen Anmeldung ][2]<br>

4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.
<br><br>![Clientanwendungen][3]<br>

5. Auf der Seite Was möchten Sie Dialogfeld führen, klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.
<br><br>![Konfigurieren der einmaligen Anmeldung ][4]<br>


6. Geben Sie in das Suchfeld **Docusign**.
<br><br>![Konfigurieren der einmaligen Anmeldung ][5]<br>

7. Wählen Sie im Ergebnisbereich **Docusign**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
<br><br>![Konfigurieren der einmaligen Anmeldung ][6]<br>




## Konfigurieren der einmaligen Anmeldung

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Azure AD-Konto bei DocuSign zu authentifizieren.


### So konfigurieren Sie einmaliges Anmelden

1. In Azure AD-Portal auf der **Anwendungsintegration für Docusign** auf **einmaliges Anmelden konfigurieren** um das Dialogfeld einmaliges Anmelden konfigurieren zu öffnen.
<br><br>![Konfigurieren der einmaligen Anmeldung ][7]<br>

2. Auf der **wie sollen sich Benutzer bei Docusign anmelden** Seite **Microsoft Azure AD Single Sign-On**, und klicken Sie dann auf Weiter.
<br><br>![Konfigurieren der einmaligen Anmeldung ][8]<br>

3. Auf der **App-URL konfigurieren** auf der Seite der **Docusign-Anmelde-URL** Textfeld Geben Sie die URL Ihres Docusign-Mandanten, und klicken Sie dann auf **Weiter**. 
Die URL weist das folgende Schema: *https://<yourcompanyname>.docusign.net/Member/MemberLogin.aspx?ssoname=<yourSSOInstanceName>*
<br><br>![Konfigurieren der einmaligen Anmeldung ][9]<br>


    > [AZURE.TIP] Wenn Sie nicht wissen, was Ihre app-URL für Ihren Mandanten ist, wenden Sie sich an Docusign über SSOSetup@Docusign.com vom Dienstanbieter initiierte SSO-URL für Ihren Mandanten abrufen.
 

4. Auf der **Konfigurieren Sie einmaliges Anmelden für docusign** auf **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.
<br><br>![Konfigurieren der einmaligen Anmeldung ][10]<br>


5. Melden Sie sich in einem Webbrowserfenster Ihre **Docusign** -Unternehmenswebsite als Administrator.


6. Erweitern Sie im Menü am oberen Rand des Benutzers im Menü, klicken Sie auf **Voreinstellungen**, und erweitern Sie dann im Navigationsbereich auf der linken Seite **Kontenverwaltung**, und klicken Sie dann auf **Funktionen**.
<br><br>![Konfigurieren der einmaligen Anmeldung ][11]<br>

7. Klicken Sie auf **SAML-Konfiguration**, und klicken Sie dann auf die **SAML-Konfiguration** Link.



8. In der **SAML 2.0-Konfiguration** führen die folgenden Schritte aus:
<br><br>![Konfigurieren der einmaligen Anmeldung ][13]<br>


    a. In the Azure portal, on the **Configure single sign-on at Docusign** dialogue page, copy the Issuer URL** value, and then paste it into the **Identity Provider Endpoint URL** textbox.

    > [AZURE.IMPORTANT] If this configuration option is unavailable, please contact your Docusign account manager or contact the SSO support team by email ([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com)).
 
    b. Click **Browse** to upload your downloaded certificate.


    c. Select **Enable first name, last name and email address**.


    d. Click **Save**.


9. Wählen Sie auf der Azure AD-Portal die **einmaliges Anmelden Bestätigung**, und klicken Sie dann auf **Weiter**.
<br><br>![Clientanwendungen][14]<br>

10. Auf der **Single Sign-On Bestätigung** auf **vollständig**.
<br><br>![Clientanwendungen][15]<br>


 

## Konfigurieren der Kontobereitstellung

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für DocuSign aktivieren.

### So konfigurieren Sie die Benutzerbereitstellung

1. In der **Azure-Verwaltungsportal**, auf die **Anwendungsintegration für DocuSign** auf **Bereitstellung konfigurieren** den Dialog Benutzerbereitstellung konfigurieren zu öffnen.
<br><br>![Konfigurieren der Bereitstellung][30]<br>
 

2. Auf der **Einstellungen und Administratoranmeldeinformationen** Blättern, um automatische benutzerbereitstellung aktivieren, geben Sie die Anmeldeinformationen eines DocuSign-Kontos mit ausreichenden rechten, und klicken Sie dann auf **Weiter**. 
<br><br>![Konfigurieren der Bereitstellung][31]<br>

3. Auf der **Verbindungstest** im Dialogfeld klicken Sie auf **Test starten**, und klicken Sie nach einem erfolgreichen Test auf **Weiter**.
<br><br>![Konfigurieren der Bereitstellung][32]<br>

3. Auf der **Bestätigung** auf **vollständig**.

<br><br>![Konfigurieren der Kontobereitstellung][33]<br>
 

## Zuweisen von Benutzern

Um Ihre Konfiguration zu testen, müssen Sie den Azure AD-Benutzern, denen Sie die Verwendung Ihrer Anwendung ermöglichen möchten, Zugriff auf die Anwendung gewähren. Weisen Sie dazu der Anwendung Benutzer zu.

### Führen Sie die folgenden Schritte aus, um DocuSign Benutzer zuzuweisen:

1. In der **Azure AD-Portal**, ein Testkonto erstellen.

2. Auf der **Anwendungsintegration für Docusign** auf **Zuweisen von Benutzern**.
<br><br>![Zuweisen von Benutzern][40]<br>
 

3. Wählen Sie Ihren Testbenutzer aus, klicken Sie auf **Zuweisen**, und klicken Sie dann auf **Ja** auf die Zuweisung zu bestätigen.

<br><br>![Zuweisen von Benutzern][41]<br>


Nach 10 Minuten können Sie überprüfen, ob das Konto mit DocuSign synchronisiert wurde.

Als ersten Überprüfungsschritt können Sie den Bereitstellungsstatus überprüfen, indem Sie auf Dashboard auf der anwendungsintegrationsseite für Docusign, im Azure-Verwaltungsportal.
<br><br>![Zuweisen von Benutzern][42]<br>

Ein erfolgreich abgeschlossener Benutzerbereitstellungszyklus wird durch einen entsprechenden Status angezeigt:
<br><br>![Zuweisen von Benutzern][43]<br>


Wenn Sie die SSO-Einstellungen testen möchten, öffnen Sie den Zugriffsbereich.

Weitere Informationen zum Zugriffsbereich finden Sie unter „Einführung in den Zugriffsbereich“.





## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_06.png
[11]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_07.png

[13]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_09.png
[14]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_18.png

