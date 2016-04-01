<properties
    pageTitle="Übersicht über das App-Modell v2.0 | Microsoft Azure"
    description="Eine Einführung in die Entwicklung von Apps mit Microsoft-Konto- und Azure Active Directory-Anmeldung."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/06/2015"
    ms.author="dastrock"/>

# App-Modell, Version 2.0, Vorschau: Anmelden von Benutzern am Microsoft-Konto und bei Azure AD in einer einzigen Anwendung

> [AZURE.NOTE]
    Diese Informationen gelten für App-Modell v2.0 (öffentliche Vorschauversion).  Anleitung zur Integration in die allgemein verfügbare Azure AD-service, finden Sie in der [Azure Active Directory-Entwicklerhandbuch](active-directory-developers-guide.md).

In der Vergangenheit musste ein App-Entwickler, der sowohl Unterstützung für Microsoft-Konten als auch für Azure Active Directory benötigte, die Integration für zwei separate Systeme bereitstellen. Mit dem App-Modell, Version 2.0 können Sie nun Benutzer mit beiden Kontotypen anmelden. Mit einer einzelnen Implementierung können Sie eine Zielgruppe erreichen, die Millionen von Benutzern sowohl mit privaten als auch geschäftlichen Konten umfasst.

Ihre apps können Sie auch verwenden eine [Satz von Office 365-REST-APIs](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) beide Typen von Konten verwenden.  Derzeit umfasst dies Outlook-APIs für E-Mail, Kontakte und Kalender.  Zusätzliche Dienste werden in naher Zukunft hinzugefügt werden.
<!-- TODO: customer reference article -->
<!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

Das App-Modell, Version 2.0 befindet sich in der Vorschau.  Während der Vorschauphase nehmen wir gerne Feedback entgegen und freuen uns, wenn Sie uns Ihre Erfahrung beim Testen des neuen App-Modells mitteilen.  Anhand dieses Feedbacks nehmen wir möglicherweise grundlegende Änderungen zur Verbesserung des Diensts vor.  Sie sollten eine Produktions-app mit dem v2. 0-app-Modell während dieses Zeitraums nicht freigeben.
<!-- TODO: Get approval on how it looks  -->

## Erste Schritte
Es gibt zwei Möglichkeiten, eine eigene App mit dem App-Modell, Version 2.0 zu erstellen und auszuführen.  Sie können auch senden Protokoll Nachrichten direkt mit [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) oder [Open ID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Alternativ können Sie unsere Bibliotheken verwenden, um die Arbeit für Sie erledigen – wählen Sie Ihre bevorzugte Plattform unten und erste Schritte.
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Neuerungen
Sie sollten diese Seite häufiger aufrufen, da Informationen über zukünftige Änderungen am App-Modell, Version 2.0 hier aufgeführt werden.  Außerdem veröffentlichen wir Tweets zu Updates unter @AzureAD.

- Erfahren Sie mehr über die [Typen von apps, die Sie mit app-Modell v2. 0 erstellen](active-directory-v2-flows.md).
- Für Entwickler, die mit Azure Active Directory vertraut sind, sollten Sie überprüfen die [Updates zu unseren Protokolle und die Unterschiede in der app-Modell von v2. 0](active-directory-v2-compare.md).
- Aktuelle [Vorschau Einschränkungen, Einschränkungen und Einschränkungen](active-directory-v2-limitations.md).

## Referenz
Die folgenden Links bieten ausführliche Informationen zur Plattform:

- Hier erhalten Sie Hilfe zur Verwendung von Stack Overflow der [Azure Active Directory](http://stackoverflow.com/questions/tagged/azure-active-directory) oder [adal](http://stackoverflow.com/questions/tagged/adal) Tags.
- Geben Sie uns Ihre Meinung in der Vorschau mit [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) – Wir möchten ihnen hören!  Verwenden Sie den Ausdruck "AppModelv2:" im Titel Ihres Beitrags, damit wir ihn einfacher finden können.
- [App-Modell, Version 2.0 –Protokollreferenz](active-directory-v2-protocols.md)
- [App-Modell, Version 2.0 –Tokenreferenz](active-directory-v2-tokens.md)
- [Office 365-REST-API-Referenz](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Bereiche und Consent im v2-Endpunkt](active-directory-v2-scopes.md)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
-->


