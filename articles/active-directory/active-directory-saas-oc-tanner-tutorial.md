<properties
    pageTitle="Lernprogramm: Azure Active Directory-Integration mit O.C. Tanner – AppreciateHub | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und O.C. Tanner – AppreciateHub konfigurieren."
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
    ms.date="10/09/2015"
    ms.author="markusvi"/>


# Lernprogramm: Azure Active Directory-Integration mit O.C. Tanner – AppreciateHub

In diesem Lernprogramm wird die Integration von O.C. Tanner - AppreciateHub mit Azure Active Directory (Azure AD).<br>Integration von O.C. Tanner – AppreciateHub mit Azure AD bietet die folgenden Vorteile: 

- Sie können in Azure AD steuern, wer auf O.C. Tanner – AppreciateHub Zugriff hat. 
- Sie können für die Benutzer die automatische Anmeldung bei O.C. Tanner – AppreciateHub (einmaliges Anmelden) mit ihren Azure AD-Konten einrichten.
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Für das Konfigurieren der Azure AD-Integration mit O.C. Tanner – AppreciateHub benötigen Sie die folgenden Elemente:

- Ein Azure AD-Abonnement.
- Ein O.C. Tanner – AppreciateHub-Software-Abonnement, für das die einmalige Anmeldung aktiviert ist.


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus drei Grundkomponenten:

1. Hinzufügen von O.C. Tanner – AppreciateHub aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von O.C. Tanner – AppreciateHub aus dem Katalog
Für das Konfigurieren der Integration von O.C. Tanner – AppreciateHub in Azure AD müssen Sie O.C. Tanner – AppreciateHub aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**O.C. hinzufügen Tanner - AppreciateHub aus der Galerie, gehen Sie folgendermaßen vor:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. 
<br><br> ![Active Directory][1] <br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.
<br><br> ![Clientanwendungen][2] <br>

4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.
<br><br> ![Clientanwendungen][3] <br>

5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.
<br><br> ![Clientanwendungen][4] <br>

6. Geben Sie im Suchfeld den Suchbegriff **O.C. Tanner - AppreciateHub**.
<br><br> ![Clientanwendungen][5] <br>

7. Wählen Sie im Ergebnisbereich **O.C. Tanner - AppreciateHub**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
<br><br> ![Clientanwendungen][25] <br>




##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens "Britta Simon" das einmalige Anmelden mit Azure AD in O.C. Tanner – AppreciateHub konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in O.C. Tanner – AppreciateHub zu einem Benutzer in Azure AD ist. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in O.C. Tanner - muss AppreciateHub eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** in O.C. Tanner – AppreciateHub konfigurieren.
 
Für das Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit O.C. Tanner – AppreciateHub, sind folgende Bausteine erforderlich:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen eines O.C. Tanner – AppreciateHub-Testbenutzers](#creating-a-halogen-software-test-user)** – um eine Entsprechung von Britta Simon in O.C. Tanner – AppreciateHub zu haben, die mit ihrer Azure AD-Darstellung verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts ist, das einmalige Anmelden von Azure AD im Azure AD-Portal zu aktivieren und das einmalige Anmelden in der O.C. Tanner – AppreciateHub-Anwendung angemeldet werden.<br>

**So konfigurieren Sie Azure AD einmaliges Anmelden mit O.C. Tanner - AppreciateHub, gehen Sie folgendermaßen vor:**

1. Klicken Sie im Azure AD-Portal auf der Anwendungsintegrationsseite von **O.C. Tanner - AppreciateHub** anwendungsintegrationsseite, klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen die **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.<br><br>
![Einmaliges Anmelden konfigurieren][6]

2. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei O.C. Tanner - AppreciateHub** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.<br><br>
![Azure AD Single Sign-On][7]

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Konfigurieren von App-Einstellungen][8]
 
     a. Öffnen Sie die Metadatendatei, die über den folgenden Link: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).

     b. Suchen Sie die **Md:AssertionConsumerService** Knoten. 

     c. Kopieren Sie den Wert, der die **Speicherort** Attribut. 
        <br><br>![Konfigurieren von App-Einstellungen][12]
     
     d. In der **-Anmelde-URL** Textfeld unterhalb des Wertes, die Sie im vorherigen Schritt abgerufen haben.

     > [AZURE.NOTE] Wenn Sie Expiriencing Probleme, die die Antwort-URL aus der Metadatendatei abrufen können, wenden Sie sich an die O.C. Tanner - Supportteam über AppreciateHub [sso@octanner.com](mailto:sso@octanner.com).

     e. Klicken Sie auf **Weiter**.
 
4. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren um O.C. Tanner - AppreciateHub** auf **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.<br><br>![Was ist Azure AD Connect][9]

5. Kontaktieren Sie das O.C. Tanner – AppreciateHub-Supportteam, stellen Sie ihm die Metadatendatei bereit, und bitten Sie es, SSO für Sie zu aktivieren.


6. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**. <br><br>![Was ist Azure AD Connect][10]

7. Auf der **Single Sign-On Bestätigung** auf **vollständig**.  <br><br>![Was ist Azure AD Connect][11]




### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br>
Wählen Sie in der Liste Benutzer **Britta Simon**.<br><br>![Erstellen von Azure AD-Benutzer][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.<br>
![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.<br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png) 
 
4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. <br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png) 

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus: <br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png) 

    a. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.

    b. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.

    c. Klicken Sie auf **Weiter**.

6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: <br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
 
    a. In der **Vorname** Textfeld **Britta**.  

    b. In der **Nachname** geben, **Simon**.

    c. In der **Anzeigenamen** Textfeld **Britta Simon**.

    d. In der **Rolle** Liste **Benutzer**.
    e. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.<br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png) 
 
8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:<br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png) 
  
    a. Notieren Sie sich den Wert, der die **Neues Kennwort**.

    b. Klicken Sie auf **vollständige**.   

  
 
### Erstellen eines O.C. C. Tanner – AppreciateHub-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in O.C. Tanner – AppreciateHub konfigurieren.

**Erstellen Sie einen Benutzer mit dem Namen Britta Simon in O.C. Tanner - AppreciateHub, gehen Sie folgendermaßen vor:**

1. Bitten Sie das O.C. Tanner-Supportteam, einen Benutzer zu erstellen, dessen nameID-Attribut dem Benutzernamen von Britta Simon in Azure AD entspricht.


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist, Britta Simon für das einmalige Anmelden bei Azure zu aktivieren, indem sie Zugriff auf O.C. Tanner – AppreciateHub konfigurieren.
<br><br>![Benutzer zuweisen][200]

**Britta Simon O.C. zuweisen Tanner - AppreciateHub, gehen Sie folgendermaßen vor:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.<br>
<br><br>![Weisen Sie Benutzer][201]
2. Wählen Sie in der Anwendungsliste **O.C. Tanner - AppreciateHub**.
<br><br>![Weisen Sie Benutzer][202]
1. Klicken Sie im Menü am oberen Rand **Benutzer**.<br>
<br><br>![Weisen Sie Benutzer][203]
1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][205]



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel "O.C. Tanner – AppreciateHub" klicken, sollten Sie automatisch bei Ihrer O.C. Tanner – AppreciateHub-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png








