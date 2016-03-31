<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit Moxtra | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Moxtra konfigurieren."
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
    ms.date="12/01/2015"
    ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit Moxtra

Dieses Lernprogramm wird gezeigt, wie Moxtra mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD Moxtra bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer Zugriff auf Moxtra hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Moxtra anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit Moxtra konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Moxtra-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus zwei Hauptbausteinen:

1. Hinzufügen von Moxtra aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von Moxtra aus dem Katalog
Zum Konfigurieren der Integration von Moxtra in Azure AD müssen Sie Moxtra aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Moxtra aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. <br><br>
![Active Directory][1]<br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.<br><br>
![Clientanwendungen][2]<br>
4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.<br><br>
![Clientanwendungen][3]<br>
5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.<br><br>
![Clientanwendungen][4]<br>
6. Geben Sie in das Suchfeld **Moxtra**.<br><br>
![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_01.png)<br>
7. Wählen Sie im Ergebnisbereich **Moxtra**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
<br><br>
![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_02.png)<br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens Britta Simon veranschaulicht werden, wie das einmalige Anmelden von Azure AD in Moxtra konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Moxtra als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Moxtra eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** in Moxtra.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Moxtra müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer Moxtra](#creating-a-moxtra-test-user)** – um eine Entsprechung Britta Simon in Moxtra haben, der Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts besteht darin, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in Ihrer Moxtra-Anwendung zu konfigurieren. 

Die Moxtra-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:
<br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_09.png) <br>



**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Moxtra die folgenden Schritte aus:**

1. In Azure AD-Portal auf der **Moxtra** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.
<br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Auf der **wie sollen sich Benutzer anmelden Moxtra** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.
<br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_03.png) <br>

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus:.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_04.png) <br>

    a. In der **-Anmelde-URL** Textfeld Geben Sie die folgende URL: **https://www.moxtra.com/service/#login**.

    b. Klicken Sie auf **Weiter**.
 
 
4. Auf der **Moxtra einmaliges Anmelden konfigurieren** führen die folgenden Schritte aus:
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_05.png) <br>

    a. Klicken Sie auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.


1. Melden Sie sich in einem anderen Webbrowserfenster bei der Moxtra-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der Symbolleiste auf der linken Seite auf **Admin Console > SAML Single Sign-On**, und klicken Sie dann **Neu**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) <br>


1. Auf der **SAML** führen die folgenden Schritte aus:
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png) <br>

    a. In den **Namen** Geben einen Namen für die Konfiguration (z. B.: *SAML*). 

    b. In Azure-Portal auf der **Moxtra einmaliges Anmelden konfigurieren** Kopieren der **Entitäts-ID** -Wert aus, und fügen Sie ihn in die **IdP-Entitäts-ID** Textfeld. 

    c. In Azure-Portal auf der **Moxtra einmaliges Anmelden konfigurieren** Kopieren der **Remote-Anmelde-URL** -Wert aus, und fügen Sie ihn in die **-Anmelde-URL** Textfeld. 

    d. In der **AuthnContextClassRef** Textfeld Tyoe **Urn: Oasis: Namen: Tc: SAML:2.0:ac:classes:Password**.

    e. In Azure-Portal auf der **Moxtra einmaliges Anmelden konfigurieren** Kopieren der **Namensbezeichnerformat** -Wert aus, und fügen Sie ihn in die **NameID Format** Textfeld. 

    f. Öffnen Sie das heruntergeladene Zertifikat in Editor, kopieren Sie den Inhalt und fügen Sie ihn in das **Zertifikat** Textfeld.    

    g. Geben Sie im Textfeld für die SAML-E-Mail-Domäne Ihre SAML-E-Mail-Domäne ein.    
    > [AZURE.NOTE] Die Schritte für die Domäne zu überprüfen, klicken Sie auf die "**ich**" unten.


    h. Click **Update**.


6. In Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**. 
<br><br>![Azure AD Single Sign-On][10]<br>

7. Auf der **Single Sign-On Bestätigung** auf **vollständig**.  
  <br><br>![Azure AD Single Sign-On][11]

1. Benutzerdefinierte attributzuordnungen hinzufügen, Ihrer Konfiguration des Saml-token-Attribute, im Menü am oberen Rand, klicken Sie auf **Attribute** zum Öffnen der **SAML-Token-Attribute** Dialogfeld. 
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_general_80.png) <br>



1. Führen Sie für jede Datenzeile in der Tabelle unten die folgenden Schritte aus:

  	| Attributname | Attributwert |
  	| ---            | ---             |
  	| firstname      | givenname       |
  	| lastname       | surname         |
  	| idpid          | *\ < die **Entitäts-ID** Wert aus der **Moxtra einmaliges Anmelden konfigurieren** Dialogfeld im Azure-Portal \ >* |

 
    a. Click add user attribute <br><br>![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_general_81.png) <br>

    b. On the **Add User Attribute** dialog, type the attribute name and attribute value shown for that row in the table. <br><br>![Configure Single Sign-On](./media/active-directory-saas-moxtra-tutorial/tutorial_general_82.png) <br>

    c. Click **Complete**.



1. Klicken Sie auf **Ändern**. 
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_general_84.png) <br>








### Erstellen einen Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br>
Wählen Sie in der Liste Benutzer **Britta Simon**.<br><br>![Erstellen von Azure AD-Benutzer][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-moxtra-tutorial/create_aaduser_09.png) <br> 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) <br>
 
4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) <br>

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus: 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-moxtra-tutorial/create_aaduser_05.png) <br> 

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.

    c. Klicken Sie auf **Weiter**.

6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-moxtra-tutorial/create_aaduser_06.png) <br>
 
    a. In der **Vorname** Textfeld **Britta**.  

    b. In der **Nachname** geben, **Simon**.

    c. In der **Anzeigenamen** Textfeld **Britta Simon**.

    d. In der **Rolle** Liste **Benutzer**.
    e. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-moxtra-tutorial/create_aaduser_07.png) <br>
 
8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-moxtra-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie sich den Wert, der die **Neues Kennwort**.

    b. Klicken Sie auf **vollständige**.   

  
 
### Erstellen eines Moxtra-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Moxtra.

**Um einen Benutzer namens Britta Simon in Moxtra zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei der Moxtra-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der Symbolleiste auf der linken Seite auf **Admin Console > User Management**, und klicken Sie dann **Benutzer hinzufügen**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) <br>



1. Auf der **Add User** im Dialogfeld die folgenden Schritte ausführen:

    a. In der **Vorname** Textfeld **Britta**.

    b. In der **Nachname** Textfeld **Simon**.

    c. In der **E-Mail** Textfeld Geben Sie die e-Mail-Adresse des Britta im Azure-Portal.

    d. In der **Division** Textfeld **Dev**.

    e. In der **Abteilung** Textfeld **IT**.

    f. Wählen Sie **geschulten**.

    g. Klicken Sie auf **Hinzufügen**.





### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem Moxtra keinen Zugriff erteilen.
<br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon Moxtra zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.
<br><br>![Weisen Sie Benutzer][201] <br>

2. Wählen Sie in der Anwendungsliste **Moxtra**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_50.png) <br>

1. Klicken Sie im Menü am oberen Rand **Benutzer**.
<br><br>![Weisen Sie Benutzer][203] <br>

1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel „Moxtra“ klicken, sollten Sie automatisch bei Ihrer Moxtra-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_205.png








