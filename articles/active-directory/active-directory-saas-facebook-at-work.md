<properties
    pageTitle="Tutorial: Azure Active Directory-Integration mit Facebook at Work | Microsoft Azure"
    description="Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Facebook at Work konfigurieren."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="asmalser"/>


# Tutorial: Azure Active Directory-Integration mit Facebook at Work

Dieses Lernprogramm wird gezeigt, wie Facebook, bei der Arbeit mit Azure Active Directory (Azure AD) integrieren.<br>Integrieren Facebook bei der Arbeit mit Azure AD bietet Ihnen folgende Vorteile: 

- Sie können in Azure AD steuern, wer Zugriff auf Facebook at Work hat. 
- Sie können automatisch Konten für Benutzer bereitstellen, denen Zugriff auf Facebook at Work erteilt wurde.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Facebook at Work anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten. 

Wenn Sie weitere Informationen zur Integration von SaaS-app in Azure AD wissen möchten, finden Sie unter [Was Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory ist](active-directory-appssoaccess-whatis.md).


## Voraussetzungen 

Um die Azure AD-Integration mit CS Stars konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Facebook at Work mit aktivierter Funktion für einmaliges Anmelden

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie eine Testversion Azure AD-Umgebung haben, erhalten Sie eine einmonatigen Testversion [hier](https://azure.microsoft.com/pricing/free-trial/). 


## Hinzufügen von Facebook at Work aus dem Katalog
Zum Konfigurieren der Integration von Facebook at Work in Azure AD müssen Sie Facebook at Work aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Facebook at Work aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. In der **Azure-Verwaltungsportal**, klicken Sie im linken Navigationsbereich auf **Active Directory**. 
<br><br>![Active Directory][1]<br>

2. Aus der **Verzeichnis** wählen Sie das Verzeichnis für die Verzeichnisintegration aktiviert werden sollen.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der Verzeichnisansicht **Applikationen** im oberen Menü.
<br><br>![Clientanwendungen][2]<br>

4. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite.
<br><br>![Clientanwendungen][3]<br>

5. Auf der **Was möchten Sie tun** Dialogfeld klicken Sie auf **eine Anwendung aus dem Katalog hinzufügen**.
<br><br>![Clientanwendungen][4]<br>

6. Geben Sie in das Suchfeld **Facebook am Arbeitsplatz**.

7. Wählen Sie im Ergebnisbereich **Facebook am Arbeitsplatz**, und klicken Sie dann auf **abgeschlossen** um die Anwendung hinzuzufügen.


### Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt wird erläutert, wie Sie es Benutzern mithilfe einer Verbundanmeldung auf Basis des SAML-Protokolls ermöglichen, sich mit ihrem Konto in Azure Active Directory bei Facebook at Work zu authentifizieren.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens in Azure AD mit Facebook at Work die folgenden Schritte aus:**

1.  Nach dem Hinzufügen von Facebook bei der Arbeit in Azure-Verwaltungsportal, klicken Sie auf **Konfigurieren Sie einmaliges Anmelden**.

2.  In der **App-URL konfigurieren** Bildschirm, geben Sie die URL ein, in dem Benutzer in Ihrem Facebook zur Arbeit der Anwendung anmelden. Dies ist Ihre Facebook an Arbeit Mandanten-URL 
(Beispiel: https://example.facebook.com/). Sobald Sie fertig sind, klicken Sie auf **Weiter**.

3.  Melden Sie sich in einem anderen Webbrowserfenster bei der Facebook at Work-Unternehmenswebsite als Administrator an.

4. Befolgen Sie die Anweisungen zum Konfigurieren von Facebook bei der Arbeit mit Azure AD als Identitätsanbieter unter folgender URL: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.  Nach Abschluss der Browser Windows Azure-Verwaltungsportal angezeigt, klicken Sie auf das Kontrollkästchen, um zu überprüfen, ob Sie die Prozedur ausgeführt haben, geben Sie dann klicken Sie auf **Weiter** und **vollständig**.


## Automatische Bereitstellung von Benutzern in Facebook at Work

Azure AD unterstützt die Möglichkeit, die Kontodetails zugewiesener Benutzer mit Facebook at Work zu synchronisieren. Dank dieser automatischen Synchronisierung kann Facebook at Work die erforderlichen Daten für die Autorisierung von Benutzern für den Zugriff abzurufen, bevor sie sich zum ersten Mal anmelden. Sie hebt zudem die Bereitstellung von Benutzern in Facebook at Work auf, wenn der Zugriff in Azure AD widerrufen wurde.

Automatische Bereitstellung kann eingerichtet werden durch Klicken auf **Bereitstellung konfigurieren** im Fenster Azure Management Portal.

Weitere Informationen zum Konfigurieren der automatischen Bereitstellung finden Sie unter [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## Zuweisen von Benutzern zu Facebook at Work

Damit bereitgestellten AAD-Benutzern Facebook at Work im Zugriffsbereich angezeigt wird, muss ihnen im Azure-Verwaltungsportal der Zugriff zugewiesen werden.

**So weisen Sie Facebook at Work Benutzer zu:**

1.  Klicken Sie auf der Startseite für Facebook, bei der Arbeit in Azure-Verwaltungsportal auf **Konten zuweisen**.

2.  In der **anzeigen** Menü wählen, ob Sie Facebook am Arbeitsplatz eines Benutzers oder einer Gruppe zuweisen, und klicken Sie auf das Häkchen möchten.

3.  Wählen Sie in der angezeigten Liste die Benutzer oder Gruppen aus, denen Sie Facebook at Work zuweisen möchten.

4.  Klicken Sie in der Fußzeile der Seite auf die **Zuweisen** Schaltfläche.


## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png






