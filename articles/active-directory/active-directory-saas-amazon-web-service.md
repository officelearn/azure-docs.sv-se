<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit Amazon Web Service (AWS) | Microsoft Azure"
    description="Hier erfahren Sie, wie Sie Amazon Web Services (AWS) mit Azure Active Directory verwenden können, um einmaliges Anmelden, automatisierte Bereitstellung und vieles mehr zu ermöglichen."
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
    ms.date="09/30/2015"
    ms.author="markvi"/>


# Tutorial: Azure Active Directory-Integration mit Amazon Web Service (AWS)

Dieses Lernprogramm wird gezeigt, wie Amazon Web Service (AWS) mit Azure Active Directory (Azure AD) integrieren.<br>Integration von Amazon Web Service (AWS) in Azure AD bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer auf Amazon Web Service (AWS) Zugriff hat. 
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch für Amazon Web Service (AWS) anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – dem Azure Active Directory-Portal

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).

## Voraussetzungen 

Um die Azure AD-Integration mit Amazon Web Service (AWS) konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement.
- Ein Abonnement, bei dem einmaliges Anmelden für Amazon Web Service (AWS) aktiviert ist


> [AZURE.NOTE] Um die Schritte in diesem Lernprogramm zu testen, empfehlen nicht wir eine produktionsumgebung verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 

 
## Beschreibung des Szenarios
Ziel dieses Tutorials ist es, das einmalige Anmelden von Azure AD in einer Testumgebung zu testen. <br>
In diesem Lernprogramm beschriebene Szenario besteht aus drei Grundkomponenten:

1. Hinzufügen von Amazon Web Service (AWS) aus dem Katalog 
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von Amazon Web Service (AWS) aus dem Katalog
Zum Konfigurieren der Integration von Amazon Web Service (AWS) in Azure AD müssen Sie Amazon Web Service (AWS) aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

### Um Amazon Web Service (AWS) aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. 
   <br><br>![Active Directory][1]<br> 

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü. 
   <br><br>![Clientanwendungen][2]<br>

4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite. 
   <br><br>![Clientanwendungen][3]<br>

5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**. 
   <br><br>![Clientanwendungen][4]<br>

6. Geben Sie in das Suchfeld **Amazon Web Service (AWS)**.
   <br><br>![Clientanwendungen][5]<br>

7. Wählen Sie im Ergebnisbereich **Amazon Web Service (AWS)**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.
   <br><br>![Clientanwendungen][6]<br>



##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt soll veranschaulicht werden, wie basierend auf einem Testbenutzer namens "Britta Simon" das einmalige Anmelden von Azure AD in Amazon Web Service (AWS) konfiguriert und getestet werden kann.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, wer der entsprechende Gegenbenutzer in Amazon Web Service (AWS) zu einem Benutzer in Azure AD ist. Das heißt, muss eine linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Amazon Web Service (AWS) eingerichtet werden.<br>
Diese linkbeziehung wird durch Zuweisen des Werts der hergestellt der **Benutzername** in Azure AD als Wert für die **Benutzername** in Amazon Web Service (AWS).
 
Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Amazon Web Service (AWS) müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren von Azure AD einmaliges Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : für Ihre Benutzer dieses Feature verwenden können.
2. **[Erstellen ein Azure AD-Testbenutzer](#creating-an-azure-ad-test-user)** – Azure AD einmaliges Anmelden mit Britta Simon testen.
4. **[Erstellen einen Testbenutzer Amazon Web Service (AWS)](#creating-a-halogen-software-test-user)** – um eine Entsprechung Britta Simon in Amazon Web Service (AWS) verfügen, der Azure AD-Darstellung Ihrer verknüpft ist.
5. **[Zuweisen von Test-Azure AD-Benutzer](#assigning-the-azure-ad-test-user)** - Britta Simon Azure AD einmaliges Anmelden verwenden aktivieren.
5. **[Testen Sie einmaliges Anmelden für](#testing-single-sign-on)** – zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren der einmaligen Anmeldung in Azure AD

Ziel dieses Abschnitts ist Azure AD einmaliges Anmelden in Azure AD-Portal aktivieren und konfigurieren Sie einmaliges Anmelden in Ihrer Anwendung Amazon Web Service (AWS).<br>
Die Amazon Web Service (AWS)-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format, das Sie benutzerdefinierte attributzuordnungen hinzufügen muss Ihre **Saml-token-Attribute** Konfiguration. 
Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:


<br><br>![Einmaliges Anmelden konfigurieren][27]<br>

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Amazon Web Service (AWS) die folgenden Schritte aus:**

1. In Azure AD-Portal auf der **Amazon Web Service (AWS)** AHA klicken Sie auf **einmaliges Anmelden konfigurieren** zum Öffnen der **Konfigurieren Sie einmaliges Anmelden**  Dialogfeld.
<br><br>![Einmaliges Anmelden konfigurieren][7]<br>

2. Auf der **wie sollen sich Benutzer anmelden, Amazon Web Service (AWS)** Seite **Azure AD Single Sign-On**, und klicken Sie dann auf **Weiter**.
<br><br>![Einmaliges Anmelden konfigurieren][8]<br>

3. Auf der **App-Einstellungen konfigurieren** Dialogfeld auf Weiter. 
<br><br>![Konfigurieren von App-Einstellungen][9]<br>
 
4. Auf der **Konfigurieren Sie einmaliges Anmelden bei Amazon Web Service (AWS)** auf **Metadaten**, und speichern Sie die Metadatendatei lokal auf Ihrem Computer.
<br><br>![Einmaliges Anmelden konfigurieren][10]<br>

5. Melden Sie sich in einem anderen Browserfenster bei der Unternehmenswebsite von Amazon Web Service (AWS) als Administrator an.

6. Klicken Sie auf **Konsole Home**.
<br><br>![Einmaliges Anmelden konfigurieren][11]<br>

7. Klicken Sie auf **Identity and Access Management**. 
<br><br>![Einmaliges Anmelden konfigurieren][12]<br>

8. Klicken Sie auf **Identitätsanbieter**, und klicken Sie dann auf **Create Provider**. 
<br><br>![Einmaliges Anmelden konfigurieren][13]<br>

9. Auf der **Anbieter konfigurieren** Dialogfeld führen die folgenden Schritte aus: 
<br><br>![Einmaliges Anmelden konfigurieren][14]<br>

     a. Als **Anbietertyp**, Option **SAML**.

     b. In der **Anbietername** Textfeld Geben Sie einen Anbieternamen (z. B.: *WAAD*).

     c. Um Ihre heruntergeladene Metadatendatei hochzuladen, klicken Sie auf **Choose File**.

     d. Klicken Sie auf **Nächster Schritt**.


10. Auf der **Anbieterinformationen überprüfen** Seite, klicken Sie auf **Erstellen**. 
<br><br>![Einmaliges Anmelden konfigurieren][15]<br>

11. Klicken Sie auf **Rollen**, und klicken Sie dann auf **neue Rolle erstellen**. 
<br><br>![Einmaliges Anmelden konfigurieren][16]<br>

12. Auf der **Rollennamen festgelegt** im Dialogfeld die folgenden Schritte ausführen: 
<br><br>![Einmaliges Anmelden konfigurieren][17]<br>

     a. In der **Rollenname** Textfeld Geben Sie einen Rollennamen (z. B.: *TestUser*).

     b. Klicken Sie auf **Nächster Schritt**.

13. Auf der **Rollentyp auswählen** im Dialogfeld die folgenden Schritte ausführen: 
<br><br>![Einmaliges Anmelden konfigurieren][18]<br>

     a. Wählen Sie **Rolle für den Zugriff von Identity Provider**.

     b. In der **Grant Web Single Sign-On (WebSSO) Zugriff auf SAML-Anbieter** auf **wählen**.


14. Auf der **vertrauen herstellen** im Dialogfeld die folgenden Schritte ausführen:  
<br><br>![Einmaliges Anmelden konfigurieren][19]<br>
     
     a. Wählen Sie als SAML-Anbieter, den SAML-Anbieter Previousley erstellt haben (z. B.: *WAAD*) 

     b. Klicken Sie auf **Nächster Schritt**.


15. Auf der **Überprüfen Rolle vertrauen** Dialogfeld klicken Sie auf **Nächstes**. 
<br><br>![Einmaliges Anmelden konfigurieren][32]<br>


16. Auf der **Richtlinie Anfügen** Dialogfeld klicken Sie auf **Nächstes**.  
<br><br>![Einmaliges Anmelden konfigurieren][33]<br>


17. Auf der **Überprüfung** im Dialogfeld die folgenden Schritte ausführen:   
<br><br>![Einmaliges Anmelden konfigurieren][34]<br>

     a. Kopieren der **Rolle ARN** Wert.

     b. Kopieren der **Vertrauenswürdige Einheiten** ARN-Wert.

     c. Klicken Sie auf **Rolle erstellen**. 

18. Auf Azure AD-Portal, wählen Sie die Konfiguration von SSO Bestätigung, und klicken Sie dann auf **Weiter**.
<br><br>![Was ist Azure AD Connect][20]<br>

19. Auf der **Single Sign-On Bestätigung** auf **abgeschlossen** Schließen die **einmaliges Anmelden konfigurieren** Dialogfeld.
<br><br>![Was ist Azure AD Connect][22]<br>


20. Klicken Sie im Menü am oberen Rand **Attribute** zum Öffnen der **SAML-Token-Attribute** Dialogfeld. 
<br><br>![Einmaliges Anmelden konfigurieren][21]<br>

21. Klicken Sie auf **Benutzerattribut hinzufügen**. 
<br><br>![Einmaliges Anmelden konfigurieren][23]<br>

22. Führen Sie die folgenden Schritte aus, klicken Sie im Dialogfeld Benutzer-Attribut hinzufügen. 
<br><br>![Einmaliges Anmelden konfigurieren][24]<br> 

     a. In der **Attributnamen** Textfeld **https://aws.amazon.com/SAML/Attributes/Role**.

     b. In der **Attributwert** Textfeld **[Rolle ARN-Value] [Wert für vertrauenswürdige Entität ARN]**.

     >[AZURE.TIP] Dies sind die Werte, die Sie bei der Erstellung Ihrer Rolle im Dialogfeld Überprüfung kopiert haben. 

     c. Klicken Sie auf **abgeschlossen** Schließen die **Benutzerattribut hinzufügen** Dialogfeld.

23. Klicken Sie auf **Benutzerattribut hinzufügen**. 
<br><br>![Einmaliges Anmelden konfigurieren][23]<br>


24. Führen Sie die folgenden Schritte aus, klicken Sie im Dialogfeld Benutzer-Attribut hinzufügen. 
<br><br>![Einmaliges Anmelden konfigurieren][25]<br>


     a. In the **Attribute Name** textbox, type **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. In the **Attribute Value** textbox, type **userprincipalname**.

     c. Click **Complete** to close the **Add User Attribute** dialog.


25. Klicken Sie auf **Ändern**. 
<br><br>![Einmaliges Anmelden konfigurieren][26]<br>





### Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.<br>
Wählen Sie in der Liste Benutzer **Britta Simon**.<br><br>![Ein Azure AD-Testbenutzer erstellen](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)<br>

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
  
 
### Erstellen eines Testbenutzers für Amazon Web Service (AWS)

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Amazon Web Service (AWS).

### Um einen Benutzer namens Britta Simon in Amazon Web Service (AWS) zu erstellen, führen Sie die folgenden Schritte aus:

1. Melden Sie sich bei Ihrem **Amazon Web Service (AWS)** -Unternehmenswebsite als Administrator.

2. Klicken Sie auf die **Konsole Home** Symbol. 
<br><br>![Einmaliges Anmelden konfigurieren][11]<br>

3. Klicken Sie auf Identität und Verwaltung des Zugriffs. 
<br><br>![Einmaliges Anmelden konfigurieren][28]<br>

4. Klicken Sie im Dashboard auf Benutzer, und klicken Sie dann auf neue Benutzer erstellen können. 
<br><br>![Einmaliges Anmelden konfigurieren][29]<br>

5. Führen Sie im Dialogfeld Create User die folgenden Schritte aus: 
<br><br>![Einmaliges Anmelden konfigurieren][30]<br>

     a. In der **Geben Sie Benutzernamen** Textfelder ein, geben Sie Brita Simons Benutzernamen (Userprincipalname) in Azure AD.

     b. Klicken Sie auf **Erstellen**.




### Zuweisen des Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist es, Britta Simon  für das einmalige Anmelden bei Azure zu aktivieren, indem sie Zugriff auf Amazon Web Service (AWS) erhält.

![Benutzer zuweisen][31]

**Um Britta Simon CloudPassage zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie auf der Azure-Portal zum Öffnen der Anwendungsansicht in der Verzeichnisansicht auf **Anwendungen** im oberen Menü.
<br><br>![Weisen Sie Benutzer][26]<br>

2. Wählen Sie in der Anwendungsliste **Amazon Web Service (AWS)**.
<br><br>![Weisen Sie Benutzer][27]<br>

1. Klicken Sie im Menü am oberen Rand **Benutzer**.
<br><br>![Weisen Sie Benutzer][25]<br>

1. Wählen Sie in der Liste Benutzer **Britta Simon**.

2. Klicken Sie unten auf der Symbolleiste auf **Zuweisen**.
<br><br>![Weisen Sie Benutzer][29]<br>

### Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist Ihre Azure AD-Konfiguration von SSO über den Zugriffsbereich zu testen.<br>
Wenn Sie im Zugriffsbereich auf die Kachel "Amazon Web Service (AWS)" klicken, sollten Sie automatisch in Ihrer Anwendung Amazon Web Service (AWS) angemeldet werden.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png

























