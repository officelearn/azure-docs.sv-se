<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit Flatter Files | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Flatter Files konfigurieren."
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
    ms.date="10/30/2015"
    ms.author="markusvi"/>


# Tutorial: Azure Active Directory-Integration mit Flatter Files

Dieses Lernprogramm wird gezeigt, wie flachere Dateien mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD flachere Dateien bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer auf Flatter Files Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Flatter Files anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit Flatter Files konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Flatter Files-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus drei Grundkomponenten:

1. Hinzufügen von Flatter Files aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von Flatter Files aus dem Katalog
Zum Konfigurieren der Integration von Flatter Files in Azure AD müssen Sie Flatter Files aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Flatter Files aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. <br><br>
![Active Directory][1]<br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.<br><br>
![Clientanwendungen][2]<br>
4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.<br><br>
![Clientanwendungen][3]<br>
5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.<br><br>
![Clientanwendungen][4]<br>
6. Geben Sie in das Suchfeld **flachere Dateien**.<br><br>
![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)<br>
7. Wählen Sie im Ergebnisbereich **flachere Dateien**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.<br><br>
![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)<br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens Britta Simon veranschaulicht werden, wie das einmalige Anmelden in Azure AD mit Flatter Files konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Flatter Files als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in flachere Dateien eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** flachere Dateien.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit Flatter Files müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer flachere Dateien](#creating-a-halogen-software-test-user)** – damit eine Entsprechung Britta Simon flachere Dateien, die in Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Ziel dieses Abschnitts ist Azure AD einmaliges Anmelden in Azure AD-Portal aktivieren und konfigurieren Sie einmaliges Anmelden in Ihrer Anwendung flachere Dateien. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

Zum Konfigurieren des einmaligen Anmeldens für Flatter Files benötigen Sie eine registrierte Domäne. Wenn Sie eine registrierte Domäne noch, Kontakt haben die flachere Dateien Supportteam über [support@flatterfiles.com](mailto:support@flatterfiles.com).  



**Führen Sie zum Konfigurieren des einmaligen Anmeldens in Azure AD mit Flatter Files die folgenden Schritte aus:**

1. In Azure AD-Portal auf der **flachere Dateien** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.
<br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Auf der **wie sollen sich Benutzer anmelden flachere Dateien** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.
<br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) <br>

3. Auf der **App-Einstellungen konfigurieren** Seite, klicken Sie auf **Weiter**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) <br>

    > [AZURE.NOTE] Flachere Dateien verwendet die gleiche SSO-Anmelde-URL für alle Kunden: [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
.
 
 
4. Auf der **einmaliges Anmelden konfigurieren, flachere Dateien** führen die folgenden Schritte aus:
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)  <br>

    a. Klicken Sie auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.


1. Melden Sie sich bei Ihrer Flatter Files-Anwendung als Administrator an.

2. Klicken Sie auf Dashboard. 
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  <br>



2. Klicken Sie auf **Einstellungen**, und führen Sie dann die folgenden Schritte aus, auf das **Unternehmen** Registerkarte: 
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  <br>

    a. Wählen Sie **SAML 2.0 für die Authentifizierung verwenden**.

    b. Klicken Sie auf **SAML konfigurieren**.



2. Auf der **SAML-Konfiguration** im Dialogfeld die folgenden Schritte ausführen: 
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  <br>

    a. Geben Sie in das Textfeld für die Domäne die registrierte Domäne ein.

    > [AZURE.NOTE] Wenn Sie eine registrierte Domäne noch, Kontakt haben die flachere Dateien Supportteam über [support@flatterfiles.com](mailto:support@flatterfiles.com).
    
    b. In Azure-Portal auf der konfigurieren einmaliges Anmelden bei Copt flachere Dateien im Dialogfeld die einmaliges Anmelden für Dienst-URL, und fügen Sie ihn in das Textfeld Identitätsanbieter-URL ein.

    c.  Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

    >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    d.  Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **FlatterFiles Identitätsanbieterzertifikat** Textfeld.

    e. Klicken Sie auf **Update**.

6. In Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**. 
<br><br>![Azure AD Single Sign-On][10]<br>

7. Auf der **Single Sign-On Bestätigung** auf **vollständig**.  
  <br><br>![Azure AD Single Sign-On][11]




### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br>
Wählen Sie in der Liste Benutzer **Britta Simon**.<br><br>![Erstellen von Azure AD-Benutzer][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) <br> 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) <br>
 
4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) <br>

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus: 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png) <br> 

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.

    c. Klicken Sie auf **Weiter**.

6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) <br>
 
    a. In der **Vorname** Textfeld **Britta**.  

    b. In der **Nachname** geben, **Simon**.

    c. In der **Anzeigenamen** Textfeld **Britta Simon**.

    d. In der **Rolle** Liste **Benutzer**.
    e. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) <br>
 
8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie sich den Wert, der die **Neues Kennwort**.

    b. Klicken Sie auf **vollständige**.   

  
 
### Erstellen eines Flatter Files-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Flatter Files.

**Um einen Benutzer namens Britta Simon in Flatter Files zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich auf Ihre **flachere Dateien** -Unternehmenswebsite als Administrator.

2. Klicken Sie im Navigationsbereich auf der linken Seite auf **Einstellungen**, und klicken Sie dann auf die Benutzer **Registerkarte**.
<br><br>![Cfreate eine flachere Dateien Benutzer](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)<br>

3. Klicken Sie auf **Benutzer hinzufügen**. 

4. Auf der **Add User** im Dialogfeld die folgenden Schritte ausführen:
<br><br>![Cfreate eine flachere Dateien Benutzer](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)<br>

    a. In der **Vorname** Textfeld **Britta**.

    b. In der **Nachname** Textfeld **Simon**. 

    c. In der **e-Mail-Adresse** Textfeld Geben Sie die e-Mail-Adresse des Britta im Azure-Portal.

    c. Klicken Sie auf **übermitteln**.   


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem flachere Dateien keinen Zugriff erteilen.
<br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon Flatter Files zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.
<br><br>![Weisen Sie Benutzer][201] <br>
2. Wählen Sie in der Anwendungsliste **flachere Dateien**.
<br><br>![Weisen Sie Benutzer](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) <br>
1. Klicken Sie im Menü am oberen Rand **Benutzer**.
<br><br>![Weisen Sie Benutzer][203] <br>
1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel „Flatter Files“ klicken, sollten Sie automatisch bei Ihrer Flatter Files-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png








