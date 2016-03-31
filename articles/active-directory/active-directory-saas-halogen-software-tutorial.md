<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit Halogen Software"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Halogen Software konfigurieren."
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
    ms.date="10/01/2015"
    ms.author="markusvi"/>


# Tutorial: Azure Active Directory-Integration mit Halogen Software

Dieses Lernprogramm wird gezeigt, wie Halogenlampen mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD Halogenlampen Software bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer auf Halogen Software Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch für Halogen Software anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit Halogen Software konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement.
- Ein Halogen Software-Abonnement, für das die einmalige Anmeldung aktiviert ist.


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus zwei Hauptbausteinen:

1. Hinzufügen von Halogen Software aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von Halogen Software aus dem Katalog
Zum Konfigurieren der Integration von Halogen Software in Azure AD müssen Sie Halogen Software aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um Halogen Software aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. <br><br>
![Active Directory][1]

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.<br><br>
![Clientanwendungen][2]
4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.<br><br>
![Clientanwendungen][3]
5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.<br><br>
![Clientanwendungen][4]
6. Geben Sie in das Suchfeld **Halogenlampen Software**.<br>
![Clientanwendungen][5]
7. Wählen Sie im Ergebnisbereich **Halogenlampen Software**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.<br>



##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens "Britta Simon" das einmalige Anmelden von Azure AD in Halogen Software konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in Halogen Software zu einem Benutzer in Azure AD ist. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Halogenlampen Software eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** Halogenlampen Software.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Halogen Software müssen Sie die folgenden Bausteinen ausführen:

1. **[Konfigurieren von Azure AD einmaliges Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer Halogenlampen Software](#creating-a-halogen-software-test-user)** – um eine Entsprechung Britta Simon Halogenlampen Software verfügen, der Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren der einmaligen Anmeldung in Azure AD

Ziel dieses Abschnitts ist Azure AD einmaliges Anmelden in Azure AD-Portal aktivieren und konfigurieren Sie einmaliges Anmelden in Ihrer Anwendung Halogenlampen Software.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Halogen Software die folgenden Schritte aus:**

1. In Azure AD-Portal auf der **Halogenlampen Software** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.<br><br>
![Einmaliges Anmelden konfigurieren][8]

2. Auf der **wie sollen sich Benutzer anmelden Halogenlampen Software** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.<br><br>
![Azure AD Single Sign-On][9]

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus: <br><br>![Konfigurieren von App-Einstellungen][10]
 
     3.1 in der **-Anmelde-URL** Textfeld verwendete URL von den Benutzern zur Anmeldung bei Ihrem Halogenlampen Software-Anwendung, die mithilfe des folgenden Musters: *https://global.hgncloud.com/fabrikam/welcome.jsp*

     3.2. Klicken Sie auf **Weiter**.
 
4. Auf der **Konfigurieren Sie einmaliges Anmelden, um Halogenlampen Software** auf **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.<br><br>![Was ist Azure AD Connect][11]

5. In einem anderen Browserfenster Anmelden bei Ihrem **Halogenlampen Software** Anwendung als Administrator.
6. Klicken Sie auf die **Optionen** Registerkarte. <br><br>![Was ist Azure AD Connect][12]
7. Klicken Sie im linken Navigationsbereich auf **SAML-Konfiguration**. <br><br>![Was ist Azure AD Connect][13]
8. Auf der **SAML-Konfiguration** führen die folgenden Schritte aus: <br><br>![Was ist Azure AD Connect][14]

     8.1. Als **Eindeutiger Bezeichner**, Option **NameID**.

     8.2. Als **eindeutige Bezeichner Maps zu**, Option **Benutzername**.

     8.3. Um Ihre heruntergeladene Metadatendatei hochzuladen, klicken Sie auf **Durchsuchen** um die Datei auszuwählen und dann **Datei hochladen**.

     8.4. Um die Konfiguration zu testen, klicken Sie auf **Test ausführen**. > [AZURE.NOTE] Müssen Sie warten, bis die Meldung "*der SAML-Test ist abgeschlossen. Schließen Sie dieses Fenster*" erscheint. Schließen Sie dann das geöffnete Browserfenster. <br> Die **Enable SAML** Kontrollkästchen ist nur aktiviert, wenn der Test abgeschlossen wurde.

     8.5. Wählen Sie **SAML aktivieren**.
    
     8.6. Klicken Sie auf **Speichern**. 


9. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren für** Dialogfeld. <br><br>![Was ist Azure AD Connect][15]
10. Auf der **Single Sign-On Bestätigung** auf **vollständig**.  <br><br>![Was ist Azure AD Connect][16]




### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.
<br><br>![Was ist Azure AD Connect][100] 
2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.
3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.
<br><br>![Was ist Azure AD Connect][101] 
4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. 
<br><br>![Was ist Azure AD Connect][102] 
5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Was ist Azure AD Connect][103] 
  1. Als **Benutzertyp**, Option **Neuer Benutzer in Ihrer Organisation**.
  2. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.
  3. Klicken Sie auf Weiter.
6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Was ist Azure AD Connect][104] 
  1. In der **Vorname** Textfeld **Britta**.  
  2. In der **Nachname** Txtbox Typ **Simon**.
  3. In der **Anzeigenamen** Textfeld **Britta Simon**.
  4. In der **Rolle** Liste **Benutzer**.
  5. Klicken Sie auf **Weiter**.
7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.
<br><br>![Was ist Azure AD Connect][105]  
8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Was ist Azure AD Connect][106]   
  1. Notieren Sie sich den Wert, der die **Neues Kennwort**.
  2. Klicken Sie auf **vollständige**.   
  
 
### Erstellen eines Testbenutzers für Halogen Software

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Halogen Software.

**Um einen Benutzer namens Britta Simon in Halogen Software zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich auf Ihre **Halogenlampen Software** Anwendung als Administrator.
2. Klicken Sie auf die **hier** Registerkarte, und klicken Sie dann auf **Create User**.
<br><br>![Was ist Azure AD Connect][300]  
3. Auf der **neue Benutzer** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Was ist Azure AD Connect][301]
  1. In der **Vorname** Textfeld **Britta**. 
  2. In der **Nachname** Textfeld **Simon**.
  3. In der **Benutzername** Textfeld **Brita Simons Benutzername in Azure AD-Portal**.
  4. In der **Kennwort** Geben Sie ein Kennwort für Britta.
  5. Klicken Sie auf **Speichern**.


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem Halogenlampen Software keinen Zugriff erteilen.
<br><br>![Was ist Azure AD Connect?][200]

**Um Britta Simon Halogen Software zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.<br>
<br><br>![Was ist Azure AD Connect][201]
2. Wählen Sie in der Anwendungsliste **Halogenlampen Software**.
<br><br>![Was ist Azure AD Connect][202]
1. Klicken Sie im Menü am oberen Rand **Benutzer**.<br>
<br><br>![Was ist Azure AD Connect][203]
1. Wählen Sie in der Liste Benutzer **Britta Simon**.
<br><br>![Was ist Azure AD Connect][204]
2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Was ist Azure AD Connect][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie auf die Kachel Halogen Software im Zugriffsbereich klicken, sollten Sie automatisch in Ihrer Halogen Software-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

