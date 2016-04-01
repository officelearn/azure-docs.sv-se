<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit PerformanceCentre | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und PerformanceCentre konfigurieren."
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


# Tutorial: Azure Active Directory-Integration mit PerformanceCentre

Dieses Lernprogramm wird gezeigt, wie PerformanceCentre mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD PerformanceCentre bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer Zugriff auf PerformanceCentre hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei PerformanceCentre anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit PerformanceCentre konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein PerformanceCentre-Abonnement, für das die einmalige Anmeldung aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus drei Grundkomponenten:

1. Hinzufügen von PerformanceCentre aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von PerformanceCentre aus dem Katalog
Zum Konfigurieren der Integration von PerformanceCentre in Azure AD müssen Sie PerformanceCentre aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um PerformanceCentre aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. <br><br>
![Active Directory][1]<br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.<br><br>
![Clientanwendungen][2]<br>
4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.<br><br>
![Clientanwendungen][3]<br>
5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.<br><br>
![Clientanwendungen][4]<br>
6. Geben Sie in das Suchfeld **PerformanceCentre**.<br><br>
![Clientanwendungen][5]<br>
7. Wählen Sie im Ergebnisbereich **PerformanceCentre**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
<br><br>![Clientanwendungen][500]<br>


##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens Britta Simon veranschaulicht werden, wie das einmalige Anmelden in Azure AD in PerformanceCentre konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in PerformanceCentre als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in PerformanceCentre eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** in PerformanceCentre.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei PerformanceCentre müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer PerformanceCentre](#creating-a-halogen-software-test-user)** – ein Gegenstück zur Britta Simon PerformanceCentre enthalten, die in Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Ziel dieses Abschnitts ist Azure AD einmaliges Anmelden in Azure AD-Portal aktivieren und konfigurieren Sie einmaliges Anmelden in Ihrer Anwendung PerformanceCentre.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens in Azure AD mit PerformanceCentre die folgenden Schritte aus:**

1. In Azure AD-Portal auf der **PerformanceCentre** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.
<br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Auf der **wie sollen sich Benutzer anmelden PerformanceCentre** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.
<br><br> ![Azure AD Single Sign-On][7] <br>

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Azure AD Single Sign-On][8] <br>
 
     a. In der **-Anmelde-URL** Textfeld die URL ein, von Ihren Benutzern das Anmelden an Ihrem Standort PerformanceCentre (z. B.: *http://companyname.performancecentre.com/saml/SSO*).
 
     b. Klicken Sie auf **Weiter**.
 
4. Auf der **PerformanceCentre einmaliges Anmelden konfigurieren** führen die folgenden Schritte aus:
<br><br>![Azure AD Single Sign-On][9] <br>

    a. Klicken Sie auf **Metadaten**, und speichern Sie die Datei auf Ihrem Computer.



1. Anmelden bei Ihrem **PerformanceCentre** -Unternehmenswebsite als Administrator.

2. Klicken Sie auf der Registerkarte auf der linken Seite auf **konfigurieren**.
  <br><br>![Azure AD Single Sign-On][10]

2. Klicken Sie auf der Registerkarte auf der linken Seite auf **verschiedene**, und klicken Sie dann auf **Single Sign On**.
  <br><br>![Azure AD Single Sign-On][11]

2. Als **Protokoll**, Option **SAML**.
  <br><br>![Azure AD Single Sign-On][12]

2. Öffnen Sie die heruntergeladene Metadatendatei im Editor, kopieren Sie den Inhalt, fügen Sie ihn in die **Identitätsanbietermetadaten** Textfeld, und klicken Sie dann auf **Speichern**.
  <br><br>![Azure AD Single Sign-On][13]

2. Überprüfen Sie, ob die Werte für die **Entität Basis-URL** und **Entity ID-URL** richtig sind.
  <br><br>![Azure AD Single Sign-On][14]


6. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**. 
<br><br>![Azure AD Single Sign-On][15]<br>

7. Auf der **Single Sign-On Bestätigung** auf **vollständig**.  
  <br><br>![Azure AD Single Sign-On][16]




### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br>
Wählen Sie in der Liste Benutzer **Britta Simon**.<br><br>![Erstellen von Azure AD-Benutzer][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png) <br> 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) <br>
 
4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) <br>

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus: 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png) <br> 

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.

    c. Klicken Sie auf **Weiter**.

6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) <br>
 
    a. In der **Vorname** Textfeld **Britta**.  

    b. In der **Nachname** geben, **Simon**.

    c. In der **Anzeigenamen** Textfeld **Britta Simon**.

    d. In der **Rolle** Liste **Benutzer**.
    e. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) <br>
 
8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie sich den Wert, der die **Neues Kennwort**.

    b. Klicken Sie auf **vollständige**.   

  
 
### Erstellen eines PerformanceCentre-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in PerformanceCentre.

**Um einen Benutzer namens Britta Simon in PerformanceCentre zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer PerformanceCentre-Unternehmenswebsite als Administrator an.

2. Klicken Sie im Menü auf der linken Seite auf **Interrelate**, und klicken Sie dann auf **Teilnehmer erstellen**.
<br><br>![Benutzer erstellen][400]<br>

4. Auf der **kennen – erstellen Sie Teilnehmer** im Dialogfeld die folgenden Schritte ausführen:
<br><br>![Benutzer erstellen][401]<br>

    a. Geben Sie die erforderlichen Attribute für Britta Simon in die entsprechenden Textfelder ein.
    
    > [AZURE.IMPORTANT] Brittas Benutzer-Name-Attribut im PerformanceCentre muss identisch mit dem Benutzernamen in Azure AD.


    b. Select **Client Administrator** as **Choose Role**. 

    c. Click **Save**.   


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem PerformanceCentre keinen Zugriff erteilen.
<br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon PerformanceCentre zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.
<br><br>![Weisen Sie Benutzer][201] <br>
2. Wählen Sie in der Anwendungsliste **PerformanceCentre**.
<br><br>![Weisen Sie Benutzer][202] <br>
1. Klicken Sie im Menü am oberen Rand **Benutzer**.
<br><br>![Weisen Sie Benutzer][203] <br>
1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel „PerformanceCentre“ klicken, sollten Sie automatisch bei Ihrer PerformanceCentre-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png





