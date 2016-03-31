<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit Litmos | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Litmos konfigurieren."
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


# Tutorial: Azure Active Directory-Integration mit Litmos

Dieses Lernprogramm wird gezeigt, wie Litmos mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD Litmos bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer Zugriff auf Litmos hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Litmos anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit Litmos konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Litmos-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus drei Grundkomponenten:

1. Hinzufügen von Litmos aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von Litmos aus dem Katalog
Zum Konfigurieren der Integration von Litmos in Azure AD müssen Sie Litmos aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Litmos aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Portal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. <br><br>
![Active Directory][1]<br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.<br><br>
![Clientanwendungen][2]<br>
4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.<br><br>
![Clientanwendungen][3]<br>
5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.<br><br>
![Clientanwendungen][4]<br>
6. Geben Sie in das Suchfeld **Litmos**.<br><br>
![Clientanwendungen][5]<br>
7. Wählen Sie im Ergebnisbereich **Litmos**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
<br><br>![Clientanwendungen][500]<br>


##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll anhand eines Testbenutzers namens Britta Simon veranschaulicht werden, wie das einmalige Anmelden von Azure AD in Litmos konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Litmos als Gegenbenutzer zu einem Benutzer in Azure AD fungiert. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Litmos eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** in Litmos.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Litmos müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer Litmos](#creating-a-halogen-software-test-user)** – ein Gegenstück zur Britta Simon Litmos enthalten, die in Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Ziel dieses Abschnitts ist Azure AD einmaliges Anmelden in Azure AD-Portal aktivieren und konfigurieren Sie einmaliges Anmelden in Ihrer Anwendung Litmos.<br>
Im Rahmen dieses Verfahrens müssen Sie eine Base-64-codierte Zertifikatsdatei erstellen.  
Wenn Sie nicht mit diesem Verfahren vertraut sind, finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o).

Als Teil der Konfiguration, müssen Sie zum Anpassen der **SAML-Token-Attribute** für Ihre Anwendung Litmos.  
<br><br> ![Azure AD Single Sign-On][17] <br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Litmos die folgenden Schritte aus:**

1. In Azure AD-Portal auf der **Litmos** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.
<br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Auf der **wie sollen sich Benutzer anmelden Litmos** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.
<br><br> ![Azure AD Single Sign-On][7] <br>


1. Anmeldung bei der Website Ihres Unternehmens Litmos (z. B.: *https://azureapptest.litmos.com/account/Login*) als Administrator.
<br><br> ![Azure AD Single Sign-On][21] <br>


1. Klicken Sie in der Navigationsleiste auf der linken Seite auf **Konten**.
<br><br> ![Azure AD Single Sign-On][22] <br>


1. Klicken Sie auf die **Integrationen** Registerkarte.
<br><br> ![Azure AD Single Sign-On][23] <br>


1. Auf der **Integrationen** Registerkarte, einen Bildlauf nach unten zum **3rd Party Integrationen**, und klicken Sie dann auf **SAML 2.0** Registerkarte.
<br><br> ![Azure AD Single Sign-On][24] <br>

1. Kopieren Sie den Wert unter **wird der SAML-Endoiint für Litmos:**.
<br><br> ![Azure AD Single Sign-On][26] <br>


3. In Azure-Portal auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Azure AD Single Sign-On][8] <br>
 
    a. In der **Bezeichner** Textfeld die URL ein, von den Benutzern in Ihrer Anwendung Litmos anmelden (z. B.: *https://azureapptest.litmos.com/account/Login*).
     
    b. In der **-Antwort-URL** Textbox, fügen Sie den Wert, die Sie von der Anwendung Litmos im vorherigen Schritt kopiert haben.

    c. Klicken Sie auf **Weiter**.
 
4. Auf der **Litmos einmaliges Anmelden konfigurieren** führen die folgenden Schritte aus:
<br><br>![Azure AD Single Sign-On][2] <br>

    a. Klicken Sie auf "Zertifikat herunterladen", und speichern Sie das Zertifikat auf Ihrem Computer.


1. In der **Litmos** Anwendung, die folgenden Schritte ausführen:
<br><br>![Azure AD Single Sign-On][25] <br>

    a. Klicken Sie auf **SAML aktivieren**.

    b. Erstellen einer **Base-64-codierte** Datei aus dem heruntergeladenen Zertifikat.  

    >[AZURE.TIP] Weitere Informationen finden Sie unter [Gewusst wie: Konvertieren eines binären Zertifikats in eine Textdatei](http://youtu.be/PlgrzUZ-Y1o)

    c. Öffnen Sie das Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt in die Zwischenablage und fügen Sie ihn an die **SAML x. 509-Zertifikat** Textfeld.

    d. Klicken Sie auf **Speichern**.


6. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**. 
<br><br>![Azure AD Single Sign-On][10]<br>

7. Auf der **Single Sign-On Bestätigung** auf **vollständig**.  
  <br><br>![Azure AD Single Sign-On][11]


20. Klicken Sie im Menü am oberen Rand **Attribute** zum Öffnen der **SAML-Token-Attribute** Dialogfeld. 
<br><br>![Einmaliges Anmelden konfigurieren][12]<br>


24. Auf der **Benutzerattribut hinzufügen** im Dialogfeld die folgenden Schritte ausführen: 
<br><br>![Einmaliges Anmelden konfigurieren][14]<br>

  	| Attributname | Attributwert |
  	| ---            | ---             |
  	| E-Mail          | user.mail       |
  	| Vorname      | user.givenname  |
  	| Lastname       | user.surname    |

    Führen Sie für jede Datenzeile in der Tabelle oben die folgenden Schritte aus:
   
    a. Klicken Sie auf **Benutzerattribut hinzufügen**. <br><br>![Einmaliges Anmelden konfigurieren][15]<br>


    a. In the **Attribute Name** textbox, type the **Attribute Name** shown for that row.

    b. Select the **Attribute Value** shown for that row.

    c. Click **Complete** to close the **Add User Attribute** dialog.


25. Klicken Sie auf **Ändern**. 
<br><br>![Einmaliges Anmelden konfigurieren][16]<br>




### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br>
Wählen Sie in der Liste Benutzer **Britta Simon**.<br><br>![Erstellen von Azure AD-Benutzer][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Portal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png) <br> 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) <br>
 
4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) <br>

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus: 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png) <br> 

    a. Als **Benutzertyp**, Option **Neuer Benutzer in Ihrer Organisation**.

    b. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.

    c. Klicken Sie auf **Weiter**.

6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) <br>
 
    a. In der **Vorname** Textfeld **Britta**.  

    b. In der **Nachname** geben, **Simon**.

    c. In der **Anzeigenamen** Textfeld **Britta Simon**.

    d. In der **Rolle** Liste **Benutzer**.
    e. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) <br>
 
8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie sich den Wert, der die **Neues Kennwort**.

    b. Klicken Sie auf **vollständige**.   

  
 
### Erstellen eines Litmos-Testbenutzers

Ziel dieses Abschnitts ist zum Erstellen eines Benutzers Britta Simon in Litmos aufgerufen.<br>
Die Litmos-Anwendung unterstützt die Just-in-Time-Bereitstellung. Das bedeutet, dass ggf. automatisch ein Benutzerkonto erstellt wird, wenn über den Zugriffsbereich auf die Anwendung zugegriffen wird.

**Um einen Benutzer namens Britta Simon in Litmos zu erstellen, führen Sie die folgenden Schritte aus:**


1. Anmeldung bei der Website Ihres Unternehmens Litmos (z. B.: *https://azureapptest.litmos.com/account/Login*) als Administrator.
<br><br> ![Azure AD Single Sign-On][21] <br>


1. Klicken Sie in der Navigationsleiste auf der linken Seite auf **Konten**.
<br><br> ![Azure AD Single Sign-On][22] <br>


1. Klicken Sie auf die **Integrationen** Registerkarte.
<br><br> ![Azure AD Single Sign-On][23] <br>


1. Auf der **Integrationen** Registerkarte, einen Bildlauf nach unten zum **3rd Party Integrationen**, und klicken Sie dann auf **SAML 2.0** Registerkarte.
<br><br> ![Azure AD Single Sign-On][24] <br>

1. Wählen Sie **Autogenerate Benutzer:**.
<br><br> ![Azure AD Single Sign-On][27] <br>


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem Litmos keinen Zugriff erteilen.
<br><br>![Benutzer zuweisen][200] <br>

**Um Britta Simon Litmos zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.
<br><br>![Weisen Sie Benutzer][201] <br>
2. Wählen Sie in der Anwendungsliste **Litmos**.
<br><br>![Weisen Sie Benutzer][202] <br>
1. Klicken Sie im Menü am oberen Rand **Benutzer**.
<br><br>![Weisen Sie Benutzer][203] <br>
1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel „Litmos“ klicken, sollten Sie automatisch bei Ihrer Litmos-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png







