<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit UserEcho | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und UserEcho konfigurieren."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="prasannas"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit UserEcho

Dieses Lernprogramm wird gezeigt, wie UserEcho mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD UserEcho bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer auf UserEcho Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei UserEcho anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im klassischen Azure-Portal.

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit UserEcho konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein UserEcho-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus zwei Hauptbausteinen:

1. Hinzufügen von UserEcho aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von UserEcho aus dem Katalog
Zum Konfigurieren der Integration von UserEcho in Azure AD müssen Sie UserEcho aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um UserEcho aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. In der **klassischen Azure-Portal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. <br><br>
![Active Directory][1]<br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.<br><br>
![Clientanwendungen][2]<br>
4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.<br><br>
![Clientanwendungen][3]<br>
5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.<br><br>
![Clientanwendungen][4]<br>
6. Geben Sie in das Suchfeld **UserEcho**.<br><br>
![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_01.png)<br>
7. Wählen Sie im Ergebnisbereich **UserEcho**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
<br><br>
![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_02.png)<br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens „Britta Simon“ veranschaulicht werden, wie das einmalige Anmelden von Azure AD in UserEcho konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in UserEcho als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in UserEcho eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** in UserEcho.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei UserEcho müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer UserEcho](#creating-a-userecho-test-user)** – ein Gegenstück zur Britta Simon UserEcho enthalten, die in Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts ist, das einmalige Anmelden von Azure AD im klassischen Azure-Portal zu aktivieren und das einmalige Anmelden in der UserEcho-Anwendung zu konfigurieren. 




**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in UserEcho die folgenden Schritte aus:**

1. In der klassischen Azure-Portal auf der **UserEcho** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.
<br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Auf der **wie sollen sich Benutzer anmelden UserEcho** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.
<br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_03.png) <br>

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus:.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_04.png) <br>

    a. In der **-Anmelde-URL** Textfeld die URL ein, von den Benutzern Ihrer Anwendung UserEcho anmelden (z. B.: *https://fabrikam.UserEcho.com/*).

    b. Klicken Sie auf **Weiter**.
 
 
4. Auf der **UserEcho einmaliges Anmelden konfigurieren** führen die folgenden Schritte aus:
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_05.png) <br>

    a. Klicken Sie auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.


1. Melden Sie sich in einem anderen Webbrowserfenster bei der UserEcho-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der Symbolleiste am oberen Rand auf Ihren Benutzernamen, um das Menü zu erweitern, und klicken Sie dann auf **Setup**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) <br>

1. Klicken Sie auf **Integrationen**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) <br>


1. Klicken Sie auf **Website**, und klicken Sie dann auf **einmaliges Anmelden (SAML2)**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) <br>


1. Auf der **einmaliges Anmelden (SAML)** führen die folgenden Schritte aus:
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png) <br>

    a. Als **SAML-fähigen**, Option **Ja**. 

    b. In der klassischen Azure-Portal auf der **UserEcho einmaliges Anmelden konfigurieren** Kopieren der **Single Sign-On-Dienst-URL** Wert, und fügen Sie ItOpen das heruntergeladene Zertifikat in Editor, kopieren Sie den Inhalt und fügen Sie ihn in die **SAML-SSO-URL** Textfeld. 

    c. In der klassischen Azure-Portal auf der **UserEcho einmaliges Anmelden konfigurieren** Kopieren der **Remote-Abmelde-URL** -Wert aus, und fügen Sie ihn in die **Remote Logoout URL** Textbox. 

    d. Öffnen Sie das heruntergeladene Zertifikat in Editor, kopieren Sie den Inhalt und fügen Sie ihn in die **x. 509-Zertifikat** Textfeld.    

    e. Klicken Sie auf **Speichern**.


6. Im klassischen Azure-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**. 
<br><br>![Azure AD Single Sign-On][10]<br>

7. Auf der **Single Sign-On Bestätigung** auf **vollständig**.  
  <br><br>![Azure AD Single Sign-On][11]




### Erstellen eines Azure AD-Testbenutzers
In diesem Abschnitt wird im klassischen Azure-Portal eine Testbenutzerin namens Britta Simon erstellt.<br>
Wählen Sie in der Liste Benutzer **Britta Simon**.<br><br>![Erstellen von Azure AD-Benutzer][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **klassischen Azure-Portal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-userecho-tutorial/create_aaduser_09.png) <br> 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) <br>
 
4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) <br>

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus: 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-userecho-tutorial/create_aaduser_05.png) <br> 

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.

    c. Klicken Sie auf **Weiter**.

6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-userecho-tutorial/create_aaduser_06.png) <br>
 
    a. In der **Vorname** Textfeld **Britta**.  

    b. In der **Nachname** geben, **Simon**.

    c. In der **Anzeigenamen** Textfeld **Britta Simon**.

    d. In der **Rolle** Liste **Benutzer**.
    e. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-userecho-tutorial/create_aaduser_07.png) <br>
 
8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-userecho-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie sich den Wert, der die **Neues Kennwort**.

    b. Klicken Sie auf **vollständige**.   

  
 
### Erstellen eines UserEcho-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in UserEcho.

**Um einen Benutzer namens Britta Simon in UserEcho zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei der UserEcho-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der Symbolleiste am oberen Rand auf Ihren Benutzernamen, um das Menü zu erweitern, und klicken Sie dann auf **Setup**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) <br>

1. Klicken Sie auf **Benutzer**, erweitern die **Benutzer** Abschnitt.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png) <br>

1. Klicken Sie auf **Benutzer**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png) <br>

1. Klicken Sie auf **einen neuen Benutzer einladen**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png) <br>


1. Auf der **einen neuen Benutzer einladen** im Dialogfeld die folgenden Schritte ausführen:
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png) <br>

    a. In den **Namen** Textfeld **Britta Simon**.

    b. In der **E-Mail** Textfeld Geben Sie die e-Mail-Adresse des Britta im klassischen Azure-Portal.

    c. Klicken Sie auf **einladen**.

Eine Einladung wird an Britta gesendet, die ihr Zugriff auf UserEcho gewährt. 



### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem UserEcho keinen Zugriff erteilen.
<br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon UserEcho zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie auf dem Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.
<br><br>![Weisen Sie Benutzer][201] <br>

2. Wählen Sie in der Anwendungsliste **UserEcho**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_50.png) <br>

1. Klicken Sie im Menü am oberen Rand **Benutzer**.
<br><br>![Weisen Sie Benutzer][203] <br>

1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel „UserEcho“ klicken, sollten Sie automatisch in Ihrer UserEcho-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_205.png








