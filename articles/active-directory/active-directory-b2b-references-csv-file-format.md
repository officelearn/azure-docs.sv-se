<properties
   pageTitle="CSV-Dateiformat bei der Vorschau der Azure Active Directory B2B-Zusammenarbeit | Microsoft Azure"
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
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# CSV-Dateiformat bei der Vorschau der Azure Active Directory (Azure AD) B2B-Zusammenarbeit

Die Vorschauversion der Azure AD B2B-Zusammenarbeit erfordert eine CSV-Datei mit Angaben zum Partnerbenutzer, die über das Azure AD-Portal hochgeladen werden müssen. Die CSV-Datei muss die nachfolgenden erforderlichen Bezeichnungen enthalten und kann bei Bedarf optionale Felder umfassen. Ändern Sie die CSV-Beispieldatei (siehe unten), ohne die Schreibweise der Bezeichnungen in der ersten Zeile zu ändern oder die Spalten neu anzuordnen.

>[AZURE.NOTE] Die erste Zeile der Bezeichnungen (z. B. E-Mail, DisplayName...) ist erforderlich für die CSV-Datei, die erfolgreich analysiert werden. Die Schreibweise muss mit den unten angegebenen Feldern übereinstimmen. Diese Bezeichnungen geben die Inhalte darunter an. Die Bezeichnungen der nicht benötigten optionalen Felder können aus der CSV-Datei entfernt werden. Die gesamte Spalte kann leer gelassen werden.

## Erforderliche Felder: <br/>
**E-Mail-Adresse:** e-Mail-Adresse der eingeladene Benutzer. <br/>
**DisplayName:** Anzeigename für eingeladene Benutzer (in der Regel vor- und Nachnamen).<br/>
**InviteContactUsUrl:** URL in der e-Mail-Einladung aufgenommen werden soll, für den Fall, dass der eingeladene Benutzer wenden Sie sich an Ihre Organisation möchte.<br/>

## Optionale Felder: <br/>
**InviteAppID:**  die ID der Anwendung für das branding der e-Mail-Adresse einladen und Annahme Seiten verwenden.<br/>
**InviteAppResources:** AppIDs auf unternehmensanwendungen zum Zuweisen von Benutzern. AppIDs sind in PowerShell abrufbar, indem Sie aufrufen `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/>
**InviteGroupResources:** ObjectIDs für Gruppen Benutzer hinzufügen. ObjectIDs sind in PowerShell abrufbar, indem Sie aufrufen `Get-MsolGroup | fl DisplayName, ObjectId`<br/>
**InviteReplyURL:** URL, einen eingeladenen Benutzer nach Annahme der Einladung zu leiten. Dies dürfte eine unternehmensspezifische-URL (z. B. [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Wenn dieses optionale Feld nicht angegeben ist, wird der eingeladene Benutzer zum App-Zugriffsbereich weitergeleitet, in dem er zu Ihren ausgewählten Unternehmens-Apps navigieren kann. Die URL der App Zugriff Bereich hat die Form  `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/>
**Sprache:** Sprache für die Einladung e-Mail und Rückzahlung Erfahrung mit der englischen Sprache als die Standardoption, wenn nicht anders angegeben. Die anderen 10 unterstützten Sprache Codes sind:<br/>
1. de: Deutsch<br/>
2. es: Spanisch<br/>
3. FR: Französisch<br/>
4. er: Italienisch<br/>
5. Ja: Japanisch<br/>
6. Ko: Koreanisch<br/>
7. pt-BR: Portugiesisch (Brasilien)<br/>
8. RU: Russisch<br/>
9. Zh-HANS: Chinesisch (vereinfacht)<br/>
10. Zh-HANT: Chinesisch (traditionell)<br/>

## Beispiel-CSV-Datei
Dies ist ein Beispiel für eine CSV-Datei, das Sie anpassen können.

>[AZURE.NOTE] Kopieren Sie und fügen ihn in Notepad ein, und speichern Sie es mit der Erweiterung ".csv". Bearbeiten Sie sie dann in Excel. Sie wird in eine Tabelle mit Bezeichnungen in der ersten Zeile gegliedert.

>[AZURE.NOTE] Fügen Sie weitere optionale Felder in Excel hinzu, indem Sie die Bezeichnung und zum Auffüllen der Spalte darunter.

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```

## Verwandte Artikel
Durchsuchen Sie unsere anderen Artikel zur Azure B2B-Zusammenarbeit

- [Was ist die Azure AD B2B-Zusammenarbeit?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [So funktioniert's](active-directory-b2b-how-it-works.md)
- [Ausführliche exemplarische Vorgehensweise](active-directory-b2b-detailed-walkthrough.md)
- [Tokenformat für externe Benutzer](active-directory-b2b-references-external-user-token-format.md)
- [Objektattributänderungen für externe Benutzer](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Aktuelle Einschränkungen der Vorschau](active-directory-b2b-current-preview-limitations.md)


