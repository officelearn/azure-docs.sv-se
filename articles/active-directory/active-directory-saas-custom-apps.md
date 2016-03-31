<properties 
    pageTitle="Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind | Microsoft Azure" 
    description="Erfahren Sie, wie Sie Apps per Self-Service und mithilfe von SAML und kennwortbasiertem SSO mit Azure Active Directory verbinden." 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="11/18/2015" 
    ms.author="asmalser" />

#Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind

Azure Active Directory-Anwendungskatalog enthält eine Liste der Programme, die bekanntermaßen eine Form von einmaliges Anmelden mit Azure Active Directory unterstützen wie unter [in diesem Artikel](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/). Wenn Sie (als IT-Spezialist oder Systemintegrator in Ihrer Organisation) die Anwendung gefunden haben, mit der Sie eine Verbindung herstellen möchten, können Sie mithilfe der Schritt-für-Schritt-Anleitungen im Azure-Verwaltungsportal das einmalige Anmelden aktivieren.

Kunden mit [Azure Active Directory Premium](https://msdn.microsoft.com/library/azure/dn532272.aspx) Lizenzen erworben werden diese zusätzlichen Funktionen, die aus aufgerufen werden, kann der **benutzerdefinierte** Kategorie von Azure AD-Anwendungskatalog:

* Self-Service-Verbindung für Anwendungen, die SAML 2.0-Identitätsanbieter unterstützen
* Self-Service-Verbindung für Webanwendungen, die über eine HTML-basierte Anmeldeseite mit kennwortbasierter einmaliger Anmeldung (Single Sign-On, SSO) verfügen
* Hinzufügen von Links zu jeder Anwendung in der [Office 365-Anwendungsstartprogramm](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) oder [Azure AD-Zugriffsbereich](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/#deploying-azure-ad-integrated-applications-to-users)
* Self-service-Verbindung, verwenden das SCIM-Protokoll für die benutzerbereitstellung ([hier beschriebenen](active-directory-scim-provisioning))

Diese Möglichkeiten beziehen sich nicht nur SaaS-Anwendungen, die Sie bereits nutzen, die aber noch nicht per Onboarding in den Azure AD-Anwendungskatalog aufgenommen wurden, sondern auch auf Drittanbieter-Webanwendungen, die Ihre Organisation auf von Ihnen kontrollierten Servern in der Cloud oder lokal bereitgestellt hat.

Hinweis: Application-Entwickler, die zum Testen der Kompatibilität zwischen ihrer Anwendung und diese Funktion können dazu eine [kostenlose Testversion von Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/), jedoch wird empfohlen, Sie erhalten eine [internen Nutzungsrechte Lizenz](https://mspartner.microsoft.com/en/us/pages/membership/internal-use-software.aspx).

##Hinzufügen einer nicht aufgeführten oder benutzerdefinierten Anwendung 

Konfigurieren eine Anwendung in Azure-Verwaltungsportal mit dem Azure Active Directory-Administratorkonto anmelden, und navigieren Sie zu der **Active Directory > [Verzeichnis] > Applications** Abschnitt **Hinzufügen**, und klicken Sie dann **eine Anwendung aus dem Katalog hinzufügen**. 

![][1]

In app-Katalog können Sie hinzufügen, eine benutzerdefinierte Anwendung mit der **benutzerdefinierte** Kategorie auf der linken Seite oder durch Auswählen der **eine eingesetzte Anwendung hinzufügen** verknüpfen, die in den Suchergebnissen angezeigt wird, wenn die gewünschte app nicht gefunden wurde. Nachdem Sie den Namen für Ihre Anwendung eingegeben haben, können Sie die Optionen für das einmalige Anmelden und das entsprechende Verhalten festlegen. 

**Tipp**: Verwenden Sie als bewährte Methode, die Suchen-Funktion um zu überprüfen, wenn die Anwendung bereits in der Galerie vorhanden ist. Wenn die Anwendung gefunden wird und in der Beschreibung das einmalige Anmelden erwähnt wird, wird die einmalige Verbundanmeldung für die Anwendung bereits unterstützt. 

![][2]

Das Hinzufügen einer benutzerdefinierten Anwendung gestaltet sich ähnlich benutzerfreundlich wie bei vorab integrierten Anwendungen. Wählen Sie zum Starten **Konfigurieren Sie einmaliges Anmelden**. Auf dem nächsten Bildschirm werden die folgenden drei Optionen zum Konfigurieren des einmaligen Anmeldens angeboten, die in den folgenden Abschnitten beschrieben werden.

![][3]


##Azure AD – einmaliges Anmelden

Wählen Sie diese Option aus, um die SAML-basierte Authentifizierung für die Anwendung zu konfigurieren. Hierfür ist es erforderlich, dass die Anwendung SAML 2.0 unterstützt, und Sie sollten sich vor dem Ausführen der nächsten Schritte informieren, wie die SAML-Funktionen der Anwendung genutzt werden können. Nach der Auswahl **Weiter**, werden Sie aufgefordert, die drei verschiedene URLs, die für die SAML-Endpunkte für die Anwendung eingeben. 

![][4]

Über die Quickinfosymbole im Dialogfeld erhalten Sie detaillierte Informationen, wofür die jeweiligen URLs bestimmt sind und wie sie verwendet werden. Nachdem diese eingegeben wurden, klicken Sie auf **Weiter** um zum nächsten Bildschirm zu gelangen. Auf diesem Bildschirm erhalten Sie Informationen darüber, was Sie auf Anwendungsseite konfigurieren müssen, damit diese ein SAML-Token von Azure AD akzeptiert. 

![][5]

Welche Werte erforderlich sind, variiert und hängt von der jeweiligen Anwendung ab. Prüfen Sie deshalb die SAML-Dokumentation der jeweiligen Anwendung. Die **Anmelden** und **Abmelden** Dienst-URL, die sowohl an den gleichen Endpunkt, der den SAML Anforderungsbehandlung-Endpunkt für die Instanz von Azure AD zu beheben. Die Aussteller-URL ist der Wert, der in dem für die Anwendung ausgestellten SAML-Token als „Issuer“ angezeigt wird. 

Nachdem die Anwendung konfiguriert wurde, klicken Sie auf **Weiter** Schaltfläche und dann die **vollständig** um das Dialogfeld zu schließen. 

##Zuweisen von Benutzern und Gruppen zu Ihrer SAML-Anwendung 

Nachdem Sie Ihre Anwendung zur Verwendung von Azure AD als SAML-Identitätsanbieter konfiguriert haben, ist sie fast zum Testen bereit. Als Sicherheitsmaßnahme stellt Azure AD nur dann ein Token aus, das den Benutzern die Anmeldung bei der Anwendung erlaubt, wenn ihnen zuvor mit Azure AD Zugriff gewährt wurde. Den Benutzern kann der Zugriff direkt oder über eine Gruppe gewährt werden, in der sie Mitglied sind. 

Um einen Benutzer oder eine Gruppe Ihrer Anwendung zuzuweisen, klicken Sie auf die **Zuweisen von Benutzern** Schaltfläche. Wählen Sie den Benutzer oder die Gruppe, die Sie zuweisen möchten, und wählen Sie dann die **Zuweisen** Schaltfläche. 

![][6]

Nach dem Zuweisen eines Benutzers wird es für Azure AD möglich, ein Token für den Benutzer auszustellen und im Zugriffsbereich dieses Benutzers eine Kachel für die Anwendung anzuzeigen. Wenn der Benutzer Office 365 verwendet, wird die Anwendungskachel auch im Office 365-App-Startfeld angezeigt. 

Sie können für die Anwendung mithilfe einer Kachel-Logo Hochladen der **Logo hochladen** auf die Schaltfläche der **konfigurieren** Registerkarte für die Anwendung. 

###Anpassen der Ansprüche, die in einem SAML-Token ausgegeben werden 

Wenn sich ein Benutzer bei der Anwendung authentifiziert, stellt Azure AD ein SAML-Token für die App aus, das Informationen über den Benutzer (oder „Ansprüche“) enthält, die ihn eindeutig identifizieren. Dazu gehören normalerweise der Benutzername, die E-Mail-Adresse, der Vorname und der Nachname des Benutzers. 

Sie können anzeigen oder bearbeiten Sie die Ansprüche gesendet im SAML-Token an die Anwendung unter der **Attribute** Registerkarte. 

![][7]

Es gibt zwei mögliche Gründe, warum Sie möglicherweise die Ansprüche im SAML-Token ausgestellt bearbeiten: 
• Die dienstanwendung wurde entwickelt, um erfordern einen anderen Satz von Anspruchs-URIs oder Werte von Ansprüchen 
•Your Anwendung wurde in einer Weise bereitgestellt, die den Anspruch "NameIdentifier" etwas anderes als den Benutzernamen (AKA User principal Name) in Azure Active Directory gespeichert werden muss. 

Informationen zum Hinzufügen und Bearbeiten von Ansprüchen für Szenarien, sehen Sie sich diese [Artikel zur Anpassung der Ansprüche](active-directory-saml-claims-customization.md). 

###Testen der SAML-Anwendung 

Nachdem die SAML-URLs und das Zertifikat in Azure AD und in der Anwendung konfiguriert, der Anwendung in Azure die Benutzer oder Gruppen zugewiesen und auch die Ansprüche überprüft und ggf. geändert wurden, kann sich der Benutzer bei der Anwendung anmelden. 

Um zu testen, einfach zu den Azure AD-Zugriffsbereich unter https://myapps.microsoft.com mit einem Benutzerkonto an, die Sie der Anwendung zugewiesen, und klicken Sie dann auf die Kachel für die Anwendung aus, um die einzelnen SSO-Prozess zu starten. Alternativ können Sie die Anmelde-URL für die Anwendung aufrufen und sich von dort aus anmelden. 

Tipps zum Debuggen, finden Sie in diesem [Artikel zum Debuggen SAML-basierten einmaliges Anmelden für Clientanwendungen](active-directory-saml-debugging.md) 

##Einmaliges Anmelden per Kennwort

Wählen Sie diese Option konfigurieren [kennwortbasierte einmalige](active-directory-appssoaccess-whatis.md) für eine Anwendung, die eine HTML-Seite hat. Das kennwortbasierte einmalige Anmelden (auch als „Password Vaulting“ oder „Kennworttresor“ bezeichnet) ermöglicht es Ihnen, den Benutzerzugriff und die Kennwörter für Webanwendungen zu verwalten, die keinen Identitätsverbund unterstützen. Es ist auch für Szenarien nützlich, in denen mehrere Benutzer ein Konto gemeinsam verwenden müssen, wie z. B. bei den App-Konten für die sozialen Medien Ihrer Organisation. 

Nach der Auswahl **Weiter**, werden Sie aufgefordert, die URL für die Anwendung von webbasierten Anmeldeseite einzugeben. Beachten Sie, dass es sich hierbei um die Seite handeln muss, welche die Eingabefelder für Benutzername und Kennwort enthält. Nach der Eingabe startet Azure AD einen Prozess, bei dem die Anmeldeseite im Hinblick auf eine Benutzernamenseingabe und eine Kennworteingabe analysiert wird. Wenn dieser Prozess nicht erfolgreich ist, wird Ihnen eine Anleitung für einen alternativen Prozess angezeigt, bei dem Sie eine Browsererweiterung installieren (erfordert Internet Explorer, Chrome oder Firefox) und damit die Felder manuell erfassen.

Sobald die Anmeldeseite wird erfasst, Benutzer und Gruppen zugewiesen werden können, und Anmeldeinformationen Richtlinien werden, wie reguläre festgelegt können [Kennwort-SSO-apps](active-directory-appssoaccess-whatis.md).

Hinweis: Sie können für die Anwendung mithilfe einer Kachel-Logo Hochladen der **Logo hochladen** Schaltfläche auf der **konfigurieren** Registerkarte für die Anwendung. 

##Vorhandenes einmaliges Anmelden

Wählen Sie diese Option, um im Azure AD-Zugriffsbereich oder im Office 365-Portal einen Link auf eine Anwendung hinzufügen. Sie können mit dieser Option Links auf benutzerdefinierte Web-Apps hinzufügen, die derzeit zur Authentifizierung nicht Azure AD verwenden, sondern Azure Active Directory-Verbunddienste (oder andere Verbunddienste). Oder Sie können Deep-Links auf spezifische SharePoint-Seiten oder andere Webseiten hinzufügen, die in den Zugriffsbereichen der betreffenden Benutzer angezeigt werden sollen. 

Nach der Auswahl **Weiter**, werden Sie aufgefordert, geben die URL der Anwendung zu verknüpfen. Nach Abschluss, Benutzer und Gruppen zugewiesen werden können die Anwendung, die bewirkt, dass die Anwendung angezeigt werden die [Office 365-Anwendungsstartprogramm](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) oder [Azure AD-Zugriffsbereich](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/#deploying-azure-ad-integrated-applications-to-users) für diese Benutzer.

Hinweis: Sie können für die Anwendung mithilfe einer Kachel-Logo Hochladen der **Logo hochladen** Schaltfläche auf der **konfigurieren** Registerkarte für die Anwendung. 

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png


