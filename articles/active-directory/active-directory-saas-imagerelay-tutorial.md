<properties
    pageTitle="Tutorial: Azure Active Directory-Integration von ImageRelay | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ImageRelay konfigurieren."
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
    ms.date="11/16/2015"
    ms.author="markusvi"/>


# Tutorial: Azure Active Directory-Integration von ImageRelay

Dieses Lernprogramm wird gezeigt, wie ImageRelay mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD ImageRelay bietet Ihnen folgende Vorteile:

- Sie können in Azure AD steuern, wer auf ImageRelay Zugriff hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei ImageRelay anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal.

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen

Um die Azure AD-Integration von ImageRelay konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein ImageRelay-Abonnement, das für das einmalige Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/).


## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus zwei Hauptbausteinen:

1. Hinzufügen von ImageRelay aus dem Katalog

2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von ImageRelay aus dem Katalog
Zum Konfigurieren der Integration von ImageRelay in Azure AD müssen Sie ImageRelay aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um ImageRelay aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie in der Azure-Portal im linken Navigationsbereich auf **Active Directory**. <br><br>
![Active Directory][1]<br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.<br><br>
![Clientanwendungen][2]<br>
4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.<br><br>
![Clientanwendungen][3]<br>
5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.<br><br>
![Clientanwendungen][4]<br>
6. Geben Sie in das Suchfeld **ImageRelay**.<br><br>
![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)<br>
7. Wählen Sie im Ergebnisbereich **ImageRelay**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
<br><br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand einer Testbenutzerin namens „Britta Simon“ veranschaulicht werden, wie das einmalige Anmelden von Azure AD bei ImageRelay konfiguriert und getestet werden kann.

Damit das einmalige Anmelden funktioniert, benötigt Azure AD ein Benutzerkonto, das für einen entsprechenden Benutzer in ImageRelay steht.  Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ImageRelay eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** in ImageRelay.

Zum Konfigurieren und Testen des einmaligen Anmeldens mit Azure AD bei ImageRelay müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer ImageRelay](#creating-a-userecho-test-user)** – ein Gegenstück zur Britta Simon ImageRelay enthalten, die in Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts ist, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in der ImageRelay-Anwendung zu konfigurieren.


**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei ImageRelay die folgenden Schritte aus:**

1. In Azure AD-Portal auf der **ImageRelay** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden** Dialogfeld.

     ![Einmaliges Anmelden konfigurieren][6] <br>

2. Auf der **wie sollen sich Benutzer anmelden ImageRelay** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) <br>

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus:

     ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) <br>

    a. In der **-Anmelde-URL** Textfeld die URL ein, von den Benutzern Ihrer Anwendung ImageRelay anmelden (z. B.: *https://fabrikam.ImageRelay.com/*).

    b. Klicken Sie auf **Weiter**.

4. Auf der **ImageRelay einmaliges Anmelden konfigurieren** führen die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) <br>

    a. Klicken Sie auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.

5. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrer ImageRelay-Unternehmenswebsite als Administrator an.

    a. Klicken Sie in der Symbolleiste am oberen Rand auf die **Benutzer und Berechtigungen** Arbeitslast.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) <br>

    b. Klicken Sie auf **erstellen neue Berechtigung**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) <br>

    c. In der **Single Sign in Settings** Arbeitslast, wählen die **diese Gruppe kann nur melden Sie sich über SSO** und klicken Sie dann auf **Speichern**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) <br>

    d. Wechseln Sie zu **Konteneinstellungen**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) <br>

    e. Wechseln Sie zu der **Single Sign in Settings** Arbeitslast.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)<br>

    f. Füllen Sie das Formular wie angegeben, und klicken Sie dann auf **Speichern**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)<br>

    - **Anmelde-URL (SSO)**: Es ist der SSO-Dienst-URL aus dem Azure Active Directory.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_13.png)<br>

    - **Dienst-Abmelde-URL**: Es ist der Dienst für einmaliges Abmelden von Azure Active Directory.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_14.png)<br>

    - Unter **Name Id Format**, Option **Urn: Oasis: Namen: Tc: SAML: 1.1 NameID-Format: EmailAddress**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_15.png)<br>

    - Unter **Bindung Optionen für Anforderungen vom Dienstanbieter (Image Relay)**, Option **POST binden**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_16.png)<br>  

    - Unter **x. 509-Zertifikat**, klicken Sie auf **-Zertifikat aktualisieren**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)<br>

    - In Editor öffnen Sie das Zertifikat aus Azure Active Directory in Schritt 4 heruntergeladen und dann kopieren Sie den Inhalt des Zertifikats hier.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)<br>

    - In **Just-in-Time-Benutzerbereitstellung**, wählen die **Just-in-Time-Benutzerbereitstellung aktivieren** das Kontrollkästchen.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)<br>

    - Wählen Sie die Berechtigung aus (z. B. **SSO grundlegende**) die kann nur über einmaliges Anmelden.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)<br>

6. In Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**.

    ![Azure AD – einmaliges Anmelden][10]<br>

7. Auf der **Single Sign-On Bestätigung** auf **vollständig**.

    ![Azure AD – einmaliges Anmelden][11]


### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br>
Wählen Sie in der Liste Benutzer **Britta Simon**.<br><br>![Erstellen von Azure AD-Benutzer][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Portal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) <br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) <br>

4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**.<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) <br>

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus:<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) <br>

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.

    c. Klicken Sie auf **Weiter**.

6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus:<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) <br>

    a. In der **Vorname** Textfeld **Britta**.  

    b. In der **Nachname** geben, **Simon**.

    c. In der **Anzeigenamen** Textfeld **Britta Simon**.

    d. In der **Rolle** Liste **Benutzer**.

    e. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) <br>

8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) <br>

    a. Notieren Sie sich den Wert, der die **Neues Kennwort**.

    b. Klicken Sie auf **vollständige**.   



### Erstellen eines ImageRelay-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in ImageRelay.

**Um eine Benutzerin namens Britta Simon in ImageRelay zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer ImageRelay-Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Benutzer und Berechtigungen**    und wählen Sie **SSO-Benutzer erstellen**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) <br>

1. Geben Sie die **E-Mail**, **First Name**, **Nachname** und **Unternehmen** des Benutzers bereitzustellen, und wählen Sie die Berechtigung aus (z. B. SSO Basic) handelt, der nur über einmaliges, in anmelden können der Gruppe werden sollen.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) <br>

1. Klicken Sie auf **Erstellen**.

### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem ImageRelay keinen Zugriff erteilen.
<br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon zu ImageRelay zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.<br><br>![Weisen Sie Benutzer][201] <br>

2. Wählen Sie in der Anwendungsliste **ImageRelay**.<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) <br>

1. Klicken Sie im Menü am oberen Rand **Benutzer**.<br><br>![Weisen Sie Benutzer][203] <br>

1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.<br><br>![Weisen Sie Benutzer][205]


### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel „ImageRelay“ klicken, sollten Sie automatisch bei Ihrer ImageRelay-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png


