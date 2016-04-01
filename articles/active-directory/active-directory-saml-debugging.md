<properties 
    pageTitle="Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure Active Directory | Microsoft Azure" 
    description="Hier erfahren Sie, wie Sie das SAML-basierte einmalige Anmelden bei Anwendungen in Azure Active Directory debuggen. " 
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

#Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure Active Directory

Beim Debuggen einer SAML-basierten Anwendungsintegration ist es oft hilfreich, verwenden Sie ein Tool wie [Fiddler](http://www.telerik.com/fiddler) der SAML-Anforderung, der SAML-Antwort, und das tatsächliche SAML-Token, das an die Anwendung ausgegeben wird. Anhand des SAML-Tokens können Sie sicherstellen, dass alle erforderlichen Attribute, der Benutzername des SAML-Subjects und der URI des Ausstellers erwartungsgemäß übermittelt werden.

![][1]

Die Antwort von Azure AD, die das SAML-Token ist normalerweise eine, die tritt nach einer HTTP 302-Umleitung aus https://login.windows.net und wird gesendet an den konfigurierten **-Antwort-URL** der Anwendung. 
 
Sie können das SAML-Token anzeigen, durch Auswählen dieser Zeile und dann die **Inspektoren > WebForms** Registerkarte im rechten Bereich. Von dort aus mit der rechten Maustaste die **SAMLResponse** Wert, und wählen Sie **Senden an TextWizard**. Wählen Sie dann **aus Base64** aus der **Transformieren** Menü, um das Token zu decodieren und seinen Inhalt.
 
**Hinweis**: um den Inhalt der HTTP-Anforderung Fiddler möglicherweise aufgefordert, Entschlüsselung von HTTPS-Datenverkehr zu konfigurieren, die Sie tun müssen.

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png

