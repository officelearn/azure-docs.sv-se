<properties
   pageTitle="Tokenformat für externe Benutzer bei der Vorschau der Azure Active Directory B2B-Zusammenarbeit | Microsoft Azure"
   description="Azure Active Directory B2B ermöglicht Geschäftspartnern den gezielten Zugriff auf Ihre Unternehmensanwendungen und unterstützt so Ihre unternehmensübergreifenden Beziehungen."
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Externe Benutzer token-Format für die Vorschau der Azure Active Directory (Azure AD) B2B-Zusammenarbeit
Die Ansprüche für einen standardmäßigen Azure AD Token Beschreibung finden Sie in der [unterstützte Token und Anspruchstypen](active-directory-token-and-claims.md) Artikel auf azure.microsoft.com.

Die Ansprüche, die für einen authentifizierten B2B Zusammenarbeit externe Benutzer unterschiedlich sind, lauten wie folgt:<br/>
- **OID:** die Objekt-ID aus dem Mandanten Ressource<br/>
- **TID**: Mandanten-ID aus dem Mandanten Ressource<br/>
- **Aussteller**: Dies ist die Ressource Mandanten<br/>
- **IDP**: Dies ist die Startseite Mandanten des Benutzers<br/>
- **AltSecId**: Dies ist die alternative Sicherheits-ID, die für Sie nicht transparent ist.<br/>

## Verwandte Artikel
Durchsuchen Sie unsere anderen Artikel zur Azure B2B-Zusammenarbeit:

- [Was ist die Azure AD B2B-Zusammenarbeit?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [So funktioniert's](active-directory-b2b-how-it-works.md)
- [Ausführliche exemplarische Vorgehensweise](active-directory-b2b-detailed-walkthrough.md)
- [Referenz zum CSV-Dateiformat](active-directory-b2b-references-csv-file-format.md)
- [Objektattributänderungen für externe Benutzer](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Aktuelle Einschränkungen der Vorschau](active-directory-b2b-current-preview-limitations.md)


