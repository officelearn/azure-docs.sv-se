<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit TargetProcess | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und TargetProcess konfigurieren."
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


# Tutorial: Azure Active Directory-Integration mit TargetProcess

Dieses Lernprogramm wird gezeigt, wie TargetProcess mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD TargetProcess bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer Zugriff auf TargetProcess hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei TargetProcess anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit TargetProcess konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein TargetProcess-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus zwei Hauptbausteinen:

1. Hinzufügen von TargetProcess aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von TargetProcess aus dem Katalog
Zum Konfigurieren der Integration von TargetProcess in Azure AD müssen Sie TargetProcess aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um TargetProcess aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. <br><br>
![Active Directory][1]<br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.<br><br>
![Clientanwendungen][2]<br>
4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.<br><br>
![Clientanwendungen][3]<br>
5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.<br><br>
![Clientanwendungen][4]<br>
6. Geben Sie in das Suchfeld **TargetProcess**.<br><br>
![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_01.png)<br>
7. Wählen Sie im Ergebnisbereich **TargetProcess**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_10.png)<br>

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens Britta Simon veranschaulicht werden, wie das einmalige Anmelden von Azure AD in TargetProcess konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in TargetProcess als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in TargetProcess eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** in TargetProcess.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei TargetProcess müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer TargetProcess](#creating-a-targetprocess-test-user)** – ein Gegenstück zur Britta Simon TargetProcess enthalten, die in Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts besteht darin, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in Ihrer TargetProcess-Anwendung zu konfigurieren. Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen. Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

Zum Konfigurieren des einmaligen Anmeldens für TargetProcess benötigen Sie eine registrierte Domäne. Wenn Ihnen keine registrierte Domäne noch, wenden Sie sich an Ihre TargetProcess-Supportteam über [support@flatterfiles.com](mailto:support@flatterfiles.com).  



**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in TargetProcess die folgenden Schritte aus:**

1. In Azure AD-Portal auf der **TargetProcess** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.
<br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Auf der **wie sollen sich Benutzer anmelden TargetProcess** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.
<br><br> ![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_02.png) <br>

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus:.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_03.png) <br>


    a. In the **Sign On URL** textbox, type the URL used by your users to sign-on to your TargetProcess application (e.g.: *https://fabrikam.TargetProcess.com/*).

    b. Click **Next**.
 
 
4. Auf der **TargetProcess einmaliges Anmelden konfigurieren** führen die folgenden Schritte aus:
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_04.png) <br>

    a. Klicken Sie auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.


1. Melden Sie sich bei Ihrer TargetProcess-Anwendung als Administrator an.


1. Klicken Sie im Menü am oberen Rand **Setup**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_05.png) <br>

1. Klicken Sie auf **Einstellungen**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_06.png) <br>

1. Klicken Sie auf **Single Sign-on**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_07.png) <br>

1. Führen Sie im Einstellungsdialogfeld für einmaliges Anmelden für die folgenden Schritte aus:
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_08.png) <br>

    a. Klicken Sie auf **Enable Single Sign-on**.

    b. In Azure-Portal auf der **TargetProcess einmaliges Anmelden konfigurieren** Seite, und kopieren Sie die **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in die **Anmelde-URL** Textfeld.

    c. Öffnen Sie das heruntergeladene Zertifikat in Editor, kopieren Sie den Inhalt und fügen Sie ihn in das **Zertifikat** Textfeld.

    d. Klicken Sie auf **JIT-Bereitstellung aktivieren**.


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

  
 
### Erstellen eines TargetProcess-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in TargetProcess.
TargetProcess unterstützt die Just-in-Time-Bereitstellung. Haben Sie bereits in aktiviert [Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on).

Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung.




### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem TargetProcess keinen Zugriff erteilen.
<br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon TargetProcess zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.
<br><br>![Weisen Sie Benutzer][201] <br>

2. Wählen Sie in der Anwendungsliste **TargetProcess**.
<br><br>![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_09.png) <br>

1. Klicken Sie im Menü am oberen Rand **Benutzer**.
<br><br>![Weisen Sie Benutzer][203] <br>

1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel „TargetProcess“ klicken, sollten Sie automatisch bei Ihrer TargetProcess-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_205.png








