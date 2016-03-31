<properties
    pageTitle="Lernprogramm: Azure Active Directory-Integration in die Questetra BPM Suite | Microsoft Aure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und der Questetra BPM Suite konfigurieren."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="msStevenPo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/02/2015"
    ms.author="markusvi"/>


# Lernprogramm: Azure Active Directory-Integration in Questetra BPM Suite

Dieses Lernprogramm wird gezeigt, wie Questetra BPM-Suite mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD Questetra BPM-Suite bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer auf die Questetra BPM Suite Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch für die Questetra BPM Suite anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration in die Questetra BPM Suite konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein [Questetra BPM-Suite](https://senbon-imadegawa-988.questetra.net/) für einmaliges Anmelden aktiviert Abonnement


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus drei Grundkomponenten:

1. Hinzufügen der Questetra BPM Suite aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen der Questetra BPM Suite aus dem Katalog
Zum Konfigurieren der Integration der Questetra BPM Suite in Azure AD müssen Sie die Questetra BPM Suite der Liste mit den verwalteten SaaS-Apps aus dem Katalog hinzufügen.

**Führen Sie die folgenden Schritte aus, um die Questetra BPM Suite aus dem Katalog hinzuzufügen:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. <br><br>
![Active Directory][1]

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.<br><br>
![Clientanwendungen][2]
4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.<br><br>
![Clientanwendungen][3]
5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.<br><br>
![Clientanwendungen][4]
6. Geben Sie in das Suchfeld **Questetra BPM-Suite**.<br>
![Clientanwendungen][5]
7. Wählen Sie im Ergebnisbereich **Questetra BPM-Suite**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.<br>



##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens „Britta Simon“ das einmalige Anmelden von Azure AD in der Questetra BPM Suite konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in der Questetra BPM Suite zu einem Benutzer in Azure AD ist. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Questetra BPM-Suite eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** Questetra BPM-Suite.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei der Questetra BPM Suite müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer Questetra BPM-Suite](#creating-a-questetra-bpm-suite-test-user)** – um eine Entsprechung Britta Simon Questetra BPM-Suite haben, der Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Ziel dieses Abschnitts ist Azure AD einmaliges Anmelden in Azure AD-Portal aktivieren und konfigurieren Sie einmaliges Anmelden in Ihrer Anwendung Questetra BPM-Suite.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in der Questetra BPM Suite die folgenden Schritte aus:**

1. In Azure AD-Portal auf der **Questetra BPM-Suite** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.<br><br>
![Einmaliges Anmelden konfigurieren][8]

2. Auf der **wie sollen sich Benutzer anmelden Questetra BPM-Suite** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.<br><br>
![Azure AD Single Sign-On][9]


3. Melden Sie sich in einem Webbrowserfenster Ihre **Questetra BPM-Suite** -Unternehmenswebsite als Administrator.

4. Klicken Sie im Menü am oberen Rand **Systemeinstellungen**. <br><br> ![Azure AD Single Sign-On][10]

5. Zum Öffnen der **SingleSignOnSAML** auf **SSO (SAML)**. <br><br> ![Azure AD Single Sign-On][11]


6. In Azure-Portal auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus: <br><br>![Konfigurieren von App-Einstellungen][13]
 
    a. Sie **Questetra BPM-Suite** -Unternehmenswebsite im Abschnitt SP Informationen kopieren der **ACS-URL**, und fügen Sie ihn in die **Anmelde-URL** Textfeld.

    b. Sie **Questetra BPM-Suite** -Unternehmenswebsite im Abschnitt SP Informationen kopieren der **Entitäts-ID**, und fügen Sie ihn in die **Aussteller-URL** Textfeld.

    c. Sie **Questetra BPM-Suite** -Unternehmenswebsite im Abschnitt SP Informationen kopieren der **ACS-URL**, und fügen Sie ihn in der **-Antwort-URL** Textfeld.

    d. Klicken Sie auf **Weiter**.

 
7. Auf der **Questetra BPM-Suite einmaliges Anmelden konfigurieren** auf **Zertifikat herunterladen**, und speichern Sie die Datei lokal auf Ihrem Computer.<br><br>![Einmaliges Anmelden konfigurieren][14]


8. Sie **Questetra BPM-Suite** Unternehmens-Website, die folgenden Schritte ausführen: <br><br>![Einmaliges Anmelden konfigurieren][15]

    a. Wählen Sie **Enable Single Sign-On**.
     
    b. Kopieren Sie auf Azure-Portal die **Aussteller-URL** -Wert aus, und fügen Sie ihn in die **Entitäts-ID** Textfeld.

    c. Kopieren Sie auf Azure-Portal die **Single Sign-On-Dienst-URL** -Wert aus, und fügen Sie ihn in der **-in-Seiten-URL** Textfeld.

    d. Kopieren Sie auf Azure-Portal die **Dienst-URL für einmalige Abmeldung** -Wert aus, und fügen Sie ihn in die **URL der Abmeldeseite** Textfeld.

    e. In der **NameID Format** Textfeld **Urn: Oasis: Namen: Tc: SAML: 1.1 NameID-Format: EmailAddress**.


    f. Create a base-64 encoded file from your downloaded certificate. 

    >[AZURE.TIP] For more details, see [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o).

    g. Open your base-64 encoded certificate in notepad, copy the content of it into your clipboard, and then paste it into the **Validation certificate** textbox. 

    h. Click **Save**.


9. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**. <br><br>![Was ist Azure AD Connect][17]


10. Auf der **Single Sign-On Bestätigung** auf **vollständig**.  <br><br>![Was ist Azure AD Connect][18]




### Erstellen einen Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.
<br><br>![Azure AD-Testbenutzer erstellen][100] 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.
<br><br>![Azure AD-Testbenutzer erstellen][101] 

4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. 
<br><br>![Azure AD-Testbenutzer erstellen][102] 

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Azure AD-Testbenutzer erstellen][103]
 
    a. Als **Benutzertyp**, Option **Neuer Benutzer in Ihrer Organisation**.
  
    b. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.

    c. Klicken Sie auf Weiter.
6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Azure AD-Testbenutzer erstellen][104] 
  
    a. In der **Vorname** Textfeld **Britta**. 
 
    b. In der **Nachname** geben, **Simon**.

    c. In der **Anzeigenamen** Textfeld **Britta Simon**.

    d. In der **Rolle** Liste **Benutzer**.

    e. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.
<br><br>![Azure AD-Testbenutzer erstellen][105]  

8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Azure AD-Testbenutzer erstellen][106]   
  1. Notieren Sie sich den Wert, der die **Neues Kennwort**.
  2. Klicken Sie auf **vollständige**.   
  
 
### Erstellen eines Questetra BPM Suite-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in der Questetra BPM Suite.

**Führen Sie die folgenden Schritte aus, um eine Benutzerin namens Britta Simon in der Questetra BPM Suite zu erstellen:**

1.  Melden Sie sich auf der Questetra BPM Suite-Unternehmenswebsite als Administrator an.
2.  Wechseln Sie zu **Systemeinstellungen > Benutzerliste > neuer Benutzer**. 
3.  Klicken Sie im Dialogfeld Neuer Benutzer führen Sie die folgenden Schritte aus: <br><br>![Testbenutzer erstellen][300] 

    a. In den **Namen** Textfeld geben Brittas Benutzernamen in Azure AD.

    b. In der **E-Mail** Textfeld geben Brittas Benutzernamen in Azure AD.

    c. In der **Kennwort** Geben Sie ein Kennwort ein.

4.  Klicken Sie auf **Hinzufügen neuer Benutzer**.



### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem Questetra BPM-Suite keinen Zugriff erteilen.
<br><br>![Was ist Azure AD Connect?][200]

**Führen Sie die folgenden Schritte aus, um Britta Simon der Questetra BPM Suite zuzuweisen:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.<br><br>![Was ist Azure AD Connect][201]
2. Wählen Sie in der Anwendungsliste **Questetra BPM-Suite**.
<br><br>![Was ist Azure AD Connect][205]
1. Klicken Sie im Menü am oberen Rand **Benutzer**.
<br><br>![Was ist Azure AD Connect][202]
1. Wählen Sie in der Liste Benutzer **Britta Simon**.
<br><br>![Was ist Azure AD Connect][203]
2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Was ist Azure AD Connect][204]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel für die Questetra BPM Suite klicken, sollten Sie automatisch an Ihrer Questetra BPM Suite-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png 

