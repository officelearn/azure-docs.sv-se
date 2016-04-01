<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit Alcumus Info Exchange | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Alcumus Info Exchange konfigurieren."
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
    ms.author="markusvi"/>


# Tutorial: Azure Active Directory-Integration mit Alcumus Info Exchange

Dieses Lernprogramm wird gezeigt, wie Alcumus Info Exchange mit Azure Active Directory (Azure AD) integriert.<br>Integration von Azure AD Alcumus Info Exchange bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer auf Alcumus Info Exchange Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Alcumus Info Exchange anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit Alcumus Info Exchange konfigurieren zu können, benötigen Sie Folgendes:

- Ein [Azure AD](http://azure.microsoft.com/) Abonnement
- Ein [Alcumus Info Exchange](http://www.alcumusgroup.com/) für einmaliges Anmelden aktiviert Abonnement


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus drei Grundkomponenten:

1. Hinzufügen von Alcumus Info Exchange aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von Alcumus Info Exchange aus dem Katalog
Zum Konfigurieren der Integration von Alcumus Info Exchange in Azure AD müssen Sie Alcumus Info Exchange aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um Alcumus Info Exchange aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. 
<br><br>![Active Directory][1]<br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.
<br><br>![Clientanwendungen][2]<br>

4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.
<br><br>![Clientanwendungen][3]<br>

5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.
<br><br>![Clientanwendungen][4]<br>

6. Geben Sie in das Suchfeld **Alcumus Info Exchange**.
<br><br>![Clientanwendungen][5]<br>

7. Wählen Sie im Ergebnisbereich **Alcumus Info Exchange**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
<br><br>![Clientanwendungen][400]<br>



##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens "Britta Simon" das einmalige Anmelden von Azure AD in Alcumus Info Exchange konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in Alcumus Info Exchange zu einem Benutzer in Azure AD ist. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Alcumus Info Exchange hergestellt werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** in Alcumus Info Exchange.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Alcumus Info Exchange müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer Alcumus Info Exchange](#creating-a-alcumus-info-exchange-test-user)** – um eine Entsprechung Britta Simon Alcumus Info Exchange verfügen, der Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Ziel dieses Abschnitts ist Azure AD einmaliges Anmelden in Azure AD-Portal aktivieren und konfigurieren Sie einmaliges Anmelden in Ihrer Anwendung Alcumus Info Exchange.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Alcumus Info Exchange die folgenden Schritte aus:**

1. In Azure AD-Portal auf der **Alcumus Info Exchange** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.<br><br>
![Einmaliges Anmelden konfigurieren][6]

2. Auf der **wie sollen sich Benutzer anmelden Alcumus Info Exchange** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.<br><br>
![Azure AD Single Sign-On][7]

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Azure AD Single Sign-On][8]<br>
 
     3.1 in der **-Antwort-URL** Textfeld Geben Sie die Consumer-URL, die für Sie von Ihrem Alcumus Info Exchange Team unterstützen konnte.

     > [AZURE.NOTE] Wenn Sie nicht wissen, was der richtige Wert ist, wenden Sie sich an das Supportteam Alcumus Info Exchange über [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com).

     3.2. Klicken Sie auf **Weiter**.
 
4. Auf der **Alcumus Info Exchange einmaliges Anmelden konfigurieren** auf **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.<br><br>![Was ist Azure AD Connect][9]

5. Wenden Sie sich an das Supportteam Alcumus Info Exchange über [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com), geben sie die Metadatendatei, und sie informieren, dass SSO für Sie aktiviert werden soll.


6. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**. <br><br>![Was ist Azure AD Connect][10]

7. Auf der **Single Sign-On Bestätigung** auf **vollständig**.  <br><br>![Was ist Azure AD Connect][11]




### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br>
Wählen Sie in der Liste Benutzer **Britta Simon**.<br><br>![Erstellen von Azure AD-Benutzer][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.<br>
![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png) 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.<br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_03.png) 
 
4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. <br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_04.png) 

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus: <br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_05.png) 
  1. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.
  2. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.
  3. Klicken Sie auf Weiter.

6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: <br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_06.png) 
  1. In der **Vorname** Textfeld **Britta**.  
  2. In der **Nachname** Txtbox Typ **Simon**.
  3. In der **Anzeigenamen** Textfeld **Britta Simon**.
  4. In der **Rolle** Liste **Benutzer**.
  5. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.<br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png) 
 
8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:<br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png) 
  1. Notieren Sie sich den Wert, der die **Neues Kennwort**.
  2. Klicken Sie auf **vollständige**.   

  
 
### Erstellen eines Alcumus Info Exchange-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Alcumus Info Exchange.

**Um einen Benutzer namens Britta Simon in Alcumus Info Exchange zu erstellen, führen Sie die folgenden Schritte aus:**

1. Wenden Sie sich an das Supportteam Alcumus Info Exchange über [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com),


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem Alcumus Info Exchange keinen Zugriff erteilen.
<br><br>![Benutzer zuweisen][200]

**Um Britta Simon Alcumus Info Exchange zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.<br>
<br><br>![Weisen Sie Benutzer][201]
2. Wählen Sie in der Anwendungsliste **Alcumus Info Exchange**.
<br><br>![Weisen Sie Benutzer][202]
1. Klicken Sie im Menü am oberen Rand **Benutzer**.<br>
<br><br>![Weisen Sie Benutzer][203]
1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel "Alcumus Info Exchange" klicken, sollten Sie automatisch in Ihrer Alcumus Info Exchange-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png

