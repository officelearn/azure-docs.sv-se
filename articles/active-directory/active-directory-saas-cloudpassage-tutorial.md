<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit CloudPassage | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und CloudPassage konfigurieren."
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
    ms.author="markvi"/>


# Tutorial: Azure Active Directory-Integration mit CloudPassage

Dieses Lernprogramm wird gezeigt, wie CloudPassage mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD CloudPassage bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer auf CloudPassage Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei CloudPassage anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit CloudPassage konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement.
- Ein CloudPassage-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Umgebung für Azure AD haben, erhalten Sie eine kostenlose Testversion der einmonatigen Azure [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus zwei Hauptbausteinen:

1. Hinzufügen von CloudPassage aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von CloudPassage aus dem Katalog
Zum Konfigurieren der Integration von CloudPassage in Azure AD müssen Sie CloudPassage aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

### Um CloudPassage aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. <br><br>
![Active Directory][1]

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.<br><br>
![Clientanwendungen][2]
4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.<br><br>
![Clientanwendungen][3]
5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.<br><br>
![Clientanwendungen][4]
6. Geben Sie in das Suchfeld **CloudPassage**.<br><br>
![Clientanwendungen][5]
7. Wählen Sie im Ergebnisbereich **CloudPassage**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.<br><br>
![Clientanwendungen][6]



##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens "Britta Simon" das einmalige Anmelden von Azure AD in CloudPassage konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in CloudPassage zu einem Benutzer in Azure AD ist. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in CloudPassage eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** in CloudPassage.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei CloudPassage müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD einmaliges Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer CloudPassage](#creating-a-halogen-software-test-user)** – ein Gegenstück zur Britta Simon CloudPassage enthalten, die in Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Ziel dieses Abschnitts ist Azure AD einmaliges Anmelden in Azure AD-Portal aktivieren und konfigurieren Sie einmaliges Anmelden in Ihrer Anwendung CloudPassage.<br>
Die CloudPassage-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. 
Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:
<br><br> ![Einmaliges Anmelden konfigurieren][21]

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in CloudPassage die folgenden Schritte aus:**

1. In Azure AD-Portal auf der **CloudPassage** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.<br><br>
![Einmaliges Anmelden konfigurieren][7]

2. Auf der **wie sollen sich Benutzer anmelden CloudPassage** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.<br><br>
![Einmaliges Anmelden konfigurieren][8]

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus: <br><br>![Konfigurieren von App-Einstellungen][9]
 
     3.1. In der **-Anmelde-URL** Textfeld die URL ein, von Ihren Benutzern das Anmelden an Ihre app CloudPassage (z. B.: *https://portal.cloudpassage.com/saml/init/accountid*). 

     3.2. Geben Sie in das Textfeld Antwort-URL die URL ein (z. B.: *https://portal.cloudpassage.com/saml/consume/accountid*). <br> Sie können Ihre Wert für dieses Attribut abrufen, indem Sie auf **SSO-Installationsdokumentation** in die **Einstellungen für einmaliges Anmelden für** Abschnitt des Portals CloudPassage. <br><br>![Einmaliges Anmelden konfigurieren][10]

     3.3. Klicken Sie auf **Weiter**.



4. Auf der **Konfigurieren Sie einmaliges Anmelden, um CloudPassage** auf **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer. <br><br>![Einmaliges Anmelden konfigurieren][11]

5. Melden Sie sich in einem anderen Browserfenster bei der CloudPassage-Unternehmenswebsite als Administrator an.

6. Klicken Sie im Menü am oberen Rand **Einstellungen**, und klicken Sie dann auf **Site Administration**. <br><br> ![Einmaliges Anmelden konfigurieren][12]

7. Klicken Sie auf die **Authentifizierungseinstellungen** Registerkarte. <br><br> ![Einmaliges Anmelden konfigurieren][13]


8. In der **Single Sign-On Settings** führen die folgenden Schritte aus: <br><br> ![Einmaliges Anmelden konfigurieren][14]


     8.1. In the Azure portal, on the **Configure single sign-on at CloudPassage** dialog page, copy the **Issuer URL** value, and then paste it into the **SAML issuer URL** textbox.

     8.2. In the Azure portal, on the **Configure single sign-on at CloudPassage** dialog page, copy the **Service Provider (SP) initiated endpoint** value, and then paste it into the **SAML endpoint URL** textbox.

     8.3. In the Azure portal, on the **Configure single sign-on at CloudPassage** dialog page, copy the **Logout URL** value, and then paste it into the **Logout landing page** textbox.

     8.4. Create a **base-64** encoded file from your downloaded certificate. 
          
      >[AZURE.TIP] For more details, see [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o).

     8.5. Open your base-64 encoded certificate in notepad, copy the content of it into your clipboard, and then paste it into the **x 509 certificate** textbox.

     8.6. Click **Save**.


9. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**. <br><br> ![Einmaliges Anmelden konfigurieren][15]


10. Auf der **Single Sign-On Bestätigung** auf **vollständig**. <br><br> ![Einmaliges Anmelden konfigurieren][16]



11. In der Nu am oberen Rand, klicken Sie auf **Attribute** zum Öffnen der **SAML-Token-Attribute** Dialogfeld. <br><br> ![Einmaliges Anmelden konfigurieren][17]

12. So fügen die erforderlichen Attribute, für jede Zeile in der folgenden Tabelle werden führen Sie die folgenden Schritte aus: <br>

| Attributname | Attributwert |
| --- | --- |
| firstname | user.givenname |
| lastname | user.surname |
| email | user.mail |  

     12.1. Click **add user attribute**. <br><br> ![Configure Single Sign-On][18]

     12.2. In the **Attribute Name** textbox, type the attribute name shown for that row and as **Attribure Value**, select the attribute value shown for that row . <br><br> ![Configure Single Sign-On][19]
     
     12.2.3 Click **Complete**.


13. In den Tollbar unten, klicken Sie auf **Änderungen übernehmen**. <br><br> ![Einmaliges Anmelden konfigurieren][20]



### Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br><br>
Wählen Sie in der Liste Benutzer **Britta Simon**.<br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.<br>
![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.<br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 
 
4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. <br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus: <br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png) 
  1. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.
  2. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.
  3. Klicken Sie auf Weiter.

6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: <br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png) 
  1. In der **Vorname** Textfeld **Britta**.  
  2. In der **Nachname** geben, **Simon**.
  3. In der **Anzeigenamen** Textfeld **Britta Simon**.
  4. In der **Rolle** Liste **Benutzer**.
  5. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.<br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png) 
 
8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:<br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png) 
  1. Notieren Sie sich den Wert, der die **Neues Kennwort**.
  2. Klicken Sie auf **vollständige**.   


  
 
### Erstellen eines CloudPassage-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in CloudPassage.

#### Um einen Benutzer namens Britta Simon in CloudPassage zu erstellen, führen Sie die folgenden Schritte aus:

1.  Anmelden bei Ihrem **CloudPassage** -Unternehmenswebsite als Administrator. 

2.  Klicken Sie in der Symbolleiste am oberen Rand auf **Einstellungen**, und klicken Sie dann auf **Site Administration**. <br>![Erstellen einen Testbenutzer CloudPassage][22] 

3.  Klicken Sie auf die **Benutzer** und klicken Sie dann auf **Add New User**. <br>![Erstellen einen Testbenutzer CloudPassage][23]
    
4.  In der **Add New User** führen die folgenden Schritte aus: <br>![Erstellen einen Testbenutzer CloudPassage][24]

     4.1. In der **Vorname** Textfeld Britta geben.

     4.2. In der **Nachname** Textfeld Simon geben.

     4.3. In der **Benutzername** Textfeld die **E-Mail** Textbox und **Geben Sie e-Mail-** Textfeld geben Brittas Benutzernamen in Azure AD.

     4.4. Als **Zugriffstyp**, Option **Halo Portal Zugriff aktivieren**.

     4.5. Klicken Sie auf **Hinzufügen**.










### Zuweisen des Azure AD-Testbenutzers

Ziel dieses Abschnitts ist Britta Simon Azure einmaliges Anmelden verwenden, indem CloudPassage gewährt keinen Zugriff zu aktivieren.
<br><br>![Benutzer zuweisen][30]

**Um Britta Simon CloudPassage zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.<br>
<br><br>![Weisen Sie Benutzer][26]
2. Wählen Sie in der Anwendungsliste **CloudPassage**.
<br><br>![Weisen Sie Benutzer][27]
1. Klicken Sie im Menü am oberen Rand **Benutzer**.<br>
<br><br>![Weisen Sie Benutzer][25]
1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][29]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel "CloudPassage" klicken, sollten Sie automatisch in Ihrer CloudPassage-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png























