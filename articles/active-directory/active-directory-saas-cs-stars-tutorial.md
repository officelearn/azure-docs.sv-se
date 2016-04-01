<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit CS Stars | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und CS Stars konfigurieren."
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


# Tutorial: Azure Active Directory-Integration mit CS Stars

Dieses Lernprogramm wird gezeigt, wie CS Sterne mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Azure AD CS-Sterne bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer auf CS Stars Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei CS Stars anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit CS Stars konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein CS Stars-Abonnement, für das einmaliges Anmelden aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus drei Grundkomponenten:

1. Hinzufügen von CS Stars aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von CS Stars aus dem Katalog
Zum Konfigurieren der Integration von CS Stars in Azure AD müssen Sie CS Stars aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um CS Stars aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. 
<br><br>![Active Directory][1]<br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.
<br><br>![Clientanwendungen][2]<br>

4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.
<br><br>![Clientanwendungen][3]<br>

5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.
<br><br>![Clientanwendungen][4]<br>

6. Geben Sie in das Suchfeld **CS Sternen**.
<br><br>![Clientanwendungen][5]<br>

7. Wählen Sie im Ergebnisbereich **CS Sterne**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
<br><br>![Clientanwendungen][400]<br>



##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens „Britta Simon“ das einmalige Anmelden von Azure AD in CS Stars konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in CS Stars zu einem Benutzer in Azure AD ist. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in der CS-Sterne eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzernamen** in Azure AD als Wert für die **Benutzername** in CS Sterne.
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei CS Stars müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer CS Sterne](#creating-a-cs-stars-test-user)** – um eine Entsprechung Britta Simon in CS Sterne verfügen, der Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Ziel dieses Abschnitts ist Azure AD einmaliges Anmelden in Azure AD-Portal aktivieren und konfigurieren einmaliges Anmelden in der CS-Sterne-Anwendung.<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in CS Stars die folgenden Schritte aus:**

1. In Azure AD-Portal auf die **CS Sterne** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** Öffnen die **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.
<br><br>![Einmaliges Anmelden konfigurieren][6]<br>

2. Auf der **wie sollen sich Benutzer anmelden bei CS Sterne** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.
<br><br>![Azure AD Single Sign-On][7]<br>

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Konfigurieren von App-Einstellungen][8]<br>
 
     3.1 in der **-Anmelde-URL** Textfeld verwendete URL von den Benutzern zur Anmeldung bei der CS-Sterne-Anwendung (z. B.: *https://uat.csstars.com/enterprise/default.cmdx?ssoclient=C234UAT2*).

     > [AZURE.NOTE] Wenn Sie nicht wissen, was der richtige Wert ist, wenden Sie sich an Ihre Marsh ClearSight.

     3.2. Klicken Sie auf **Weiter**.
 
4. Auf die **CS Sterne einmaliges Anmelden konfigurieren** auf **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.
<br><br>![Was ist Azure AD Connect][9]<br>

5. Wenden Sie sich an Ihren Ansprechpartner von Marsh ClearSight, und übergeben Sie die Metadatendatei, um das einmalige Anmelden für CS Stars aktivieren zu lassen.


6. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**. 
<br><br>![Was ist Azure AD Connect][10]<br>

7. Auf der **Single Sign-On Bestätigung** auf **vollständig**.  
<br><br>![Was ist Azure AD Connect][11]<br>




### Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br>
Wählen Sie in der Liste Benutzer **Britta Simon**.
<br><br>![Erstellen von Azure AD-Benutzer][20]<br>

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**.
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)<br> 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Anzeigen der Liste der Benutzer im Menü am oberen Rand **Benutzer**.
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)<br> 
 
4. Zum Öffnen der **Add User** Klicken Sie im Dialogfeld in der Symbolleiste am unteren Bildschirmrand auf **Benutzer hinzufügen**. 
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)<br> 

5. Auf der **Erzählen Sie uns dieses Benutzers** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)<br> 
  1. Wählen Sie als "Benutzertyp" die Option "Neuer Benutzer in Ihrer Organisation" aus.
  2. Namen des Benutzers ein **Textfeld**, Typ **BrittaSimon**.
  3. Klicken Sie auf Weiter.

6.  Auf der **Benutzerprofil** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)<br> 
  1. In der **Vorname** Textfeld **Britta**.  
  2. In der **Nachname** Txtbox Typ **Simon**.
  3. In der **Anzeigenamen** Textfeld **Britta Simon**.
  4. In der **Rolle** Liste **Benutzer**.
  5. Klicken Sie auf **Weiter**.

7. Auf der **vorübergehendes Kennwort abrufen** Seite, klicken Sie auf **Erstellen**.
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)<br> 
 
8. Auf der **vorübergehendes Kennwort abrufen** Dialogfeld führen die folgenden Schritte aus:
<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)<br> 
  1. Notieren Sie sich den Wert, der die **Neues Kennwort**.
  2. Klicken Sie auf **vollständige**.   

  
 
### Erstellen eines CS Stars-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in CS Stars.

**Um einen Benutzer namens Britta Simon in CS Stars zu erstellen, führen Sie die folgenden Schritte aus:**

1. Wenden Sie sich an Ihren Ansprechpartner bei Marsh ClearSight.


### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Aktivieren von Britta Simon Azure einmaliges Anmelden zu verwenden, indem CS Sterne keinen Zugriff erteilen.
<br><br>![Benutzer zuweisen][200]<br>

**Um Britta Simon CS Stars zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.
<br><br>![Weisen Sie Benutzer][201]<br>
2. Wählen Sie in der Anwendungsliste **CS Sternen**.
<br><br>![Weisen Sie Benutzer][202]<br>
1. Klicken Sie im Menü am oberen Rand **Benutzer**.<br>
<br><br>![Weisen Sie Benutzer][203]<br>
1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][205]<br>



### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel „CS Stars“ klicken, sollten Sie automatisch in Ihrer CS Stars-Anwendung angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_01.png
[6]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_02.png
[7]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_03.png
[8]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_04.png
[9]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_05.png
[10]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_06.png
[11]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_07.png
[20]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_202.png
[203]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_csstars_403.png




