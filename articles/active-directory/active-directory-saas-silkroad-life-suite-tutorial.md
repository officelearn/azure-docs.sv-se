<properties
    pageTitle="Tutorial: Azure Active Directory-Integration in SilkRoad Life Suite | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und der SilkRoad Life Suite konfigurieren."
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
    ms.date="10/23/2015"
    ms.author="markusvi"/>


# Tutorial: Azure Active Directory-Integration in die SilkRoad Life Suite

Dieses Lernprogramm wird gezeigt, wie SilkRoad Leben Suite mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD SilkRoad Leben Suite bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer auf die SilkRoad Life Suite zugreifen kann. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei der SilkRoad Life Suite anzumelden (einmaliges Anmelden).

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration in die SilkRoad Life Suite konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein SilkRoad Life Suite-Abonnement mit aktiviertem einmaligen Anmelden.


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus zwei Hauptbausteinen:

1. Hinzufügen der SilkRoad Life Suite aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen der SilkRoad Life Suite aus dem Katalog
Zum Konfigurieren der Integration der SilkRoad Life Suite in Azure AD müssen Sie die SilkRoad Life Suite aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um die SilkRoad Life Suite aus dem Katalog hinzuzufügen:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. <br><br>
![Active Directory][1]<br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.<br><br>
![Clientanwendungen][2]<br>
4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.<br><br>
![Clientanwendungen][3]<br>
5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.<br><br>
![Clientanwendungen][4]<br>
6. Geben Sie in das Suchfeld **SilkRoad Leben Suite**.<br><br>
![Clientanwendungen][5]<br>
7. Wählen Sie im Ergebnisbereich **SilkRoad Leben Suite**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
<br><br>![Clientanwendungen][50]<br>


##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens „Britta Simon“ das einmalige Anmelden von Azure AD in der SilkRoad Life Suite konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in der SilkRoad Life Suite einem Benutzer in Azure AD entspricht. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer SilkRoad Leben Suite eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** SilkRoad Leben Suite.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei der SilkRoad Life Suite müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer SilkRoad Leben Suite](#creating-a-silkroad-life-suite-test-user)** – um eine Entsprechung Britta Simon SilkRoad Leben Suite haben, der Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Ziel dieses Abschnitts ist Azure AD einmaliges Anmelden in Azure AD-Portal aktivieren und konfigurieren einmaliges Anmelden in der SilkRoad Leben Suite-Anwendung.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei der SilkRoad Life Suite die folgenden Schritte aus:**

5. Melden Sie sich bei Ihrer SilkRoad Life Suite-Unternehmenswebsite als Administrator an. 


    > [AZURE.NOTE] Um Zugriff auf die Anwendung SilkRoad Leben Suite Authentifizierung für das Konfigurieren des Verbunds mit Microsoft Azure AD zu erhalten, wenden Sie sich SilkRoad Support oder Ihrem Vertreter SilkRoad Dienste.


6. Wechseln Sie zu **Dienstanbieter**, und klicken Sie dann auf die Kachel **Verbund Details**. 
<br><br>![Azure AD Single Sign-On][10] <br>


1. Klicken Sie auf **Verbundmetadaten herunterladen**, und speichern Sie die Metadatendatei auf Ihrem Computer.
<br><br>![Azure AD Single Sign-On][11] <br>

3. In Azure AD-Portal auf der **SilkRoad Leben Suite** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.
<br><br> ![Einmaliges Anmelden konfigurieren][6] <br>

2. Auf der **wie sollen sich Benutzer anmelden SilkRoad Leben Suite** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.
<br><br> ![Azure AD Single Sign-On][7] <br>

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Azure AD Single Sign-On][8] <br>
 
    a. In der **-Anmelde-URL** Textfeld die URL ein, von den Benutzern zur Anmeldung bei Ihrem SilkRoad Leben Suite-Website (z. B.: *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).

    b. Öffnen Sie das heruntergeladene **Silkroad** -Metadatendatei.

    c. Suchen Sie die **AssertionConsumerService** -Tag ein, und kopieren Sie dann die **Speicherort** Attribut.         
    <br><br>![Azure AD Single Sign-On][21] <br>
   
    d. Fügen Sie den Wert in der **-Antwort-URL** Textfeld.
 
    e. Klicken Sie auf **Weiter**.
 
4. Auf der **SilkRoad Leben Suite einmaliges Anmelden konfigurieren** führen die folgenden Schritte aus:
<br><br>![Azure AD Single Sign-On][9] <br>

    a. Klicken Sie auf "Zertifikat herunterladen", und speichern Sie das Zertifikat auf Ihrem Computer.

    b. Klicken Sie auf **Weiter**.




1. In der **SilkRoad** Anwendung, klicken Sie auf **Authentifizierungsquellen**.
<br><br>![Azure AD Single Sign-On][12] <br>



1. Klicken Sie auf **Hinzufügen Authentifizierungsquelle**. 
<br><br>![Azure AD Single Sign-On][13] <br>



1. In der **Authentifizierungsquelle hinzufügen** führen die folgenden Schritte aus: 
<br><br>![Azure AD Single Sign-On][14] <br>

    a. Unter **Option 2 - Metadatendatei**, klicken Sie auf **Durchsuchen** um die heruntergeladene Metadatendatei hochzuladen.

    b. Klicken Sie auf **Erstellen Identitätsanbieter mithilfe von Dateidaten**.



1. In der **Authentifizierungsquellen** auf **Bearbeiten**. 
<br><br>![Azure AD Single Sign-On][15] <br>


1. Auf der **Authentifizierungsquelle bearbeiten** im Dialogfeld die folgenden Schritte ausführen: 
<br><br>![Azure AD Single Sign-On][16] <br>

    a. Als **aktiviert**, Option **Ja**.

    b. In der **IdP Beschreibung** Textbox, geben Sie eine Beschreibung für die Konfiguration (z. B.: *Azure AD SSO*).

    c. In der **IdP-Namen** Geben einen Namen, die für Ihre Konfiguration spezifisch sind (z. B.: *Azure SP*).

    d. Klicken Sie auf **Speichern**.


6. Deaktivieren Sie alle anderen authentifizierungsquellen. 
<br><br>![Azure AD – einmaliges Anmelden][17]<br>

7. In Azure AD-Portal auf der **Single Sign-On Bestätigung** auf **Weiter**.  
  <br><br>![Azure AD Single Sign-On][18]

1. Auf der **Single Sign-On Bestätigung** auf **vollständig**.
  <br><br>![Azure AD Single Sign-On][19]


### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br>
Wählen Sie in der Liste Benutzer **Britta Simon**.<br><br>![Erstellen von Azure AD-Benutzer][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png) <br> 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) <br>
 
4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) <br>

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus: 
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png) <br> 

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.

    c. Klicken Sie auf **Weiter**.

6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png) <br>
 
    a. In der **Vorname** Textfeld **Britta**.  

    b. In der **Nachname** geben, **Simon**.

    c. In der **Anzeigenamen** Textfeld **Britta Simon**.

    d. In der **Rolle** Liste **Benutzer**.
    e. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.
<br><br> ![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) <br>
 
8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png) <br>
  
    a. Notieren Sie sich den Wert, der die **Neues Kennwort**.

    b. Klicken Sie auf **vollständige**.   

  
 
### Erstellen eines SilkRoad Life Suite-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in der SilkRoad Life Suite. Brittas müssen ein SSO-ID (auch als bezeichnet ein *AuthParam*), entspricht der Britta **Emailaddress** in Azure AD.

**Führen Sie die folgenden Schritte aus, um eine Benutzerin namens Britta Simon in der SilkRoad Life Suite zu erstellen:**

1. Bitten Sie Ihre SilkRoad Leben Suite-Support-Team zum Erstellen eines Benutzers, das als **SSO-ID** -Attribut den gleichen Wert wie die **Emailaddress** von Britta Simon in Azure AD.



### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem SilkRoad Leben Suite keinen Zugriff erteilen.
<br><br>![Benutzer zuweisen][200] <br>

**Führen Sie die folgenden Schritte aus, um Britta Simon der SilkRoad Life Suite zuzuweisen:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.
<br><br>![Weisen Sie Benutzer][201] <br>
2. Wählen Sie in der Anwendungsliste **SilkRoad Leben Suite**.
<br><br>![Weisen Sie Benutzer][202] <br>
1. Klicken Sie im Menü am oberen Rand **Benutzer**.
<br><br>![Weisen Sie Benutzer][203] <br>
1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel für die SilkRoad Life Suite klicken, sollten Sie automatisch an Ihrer SilkRoad Life Suite-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png







