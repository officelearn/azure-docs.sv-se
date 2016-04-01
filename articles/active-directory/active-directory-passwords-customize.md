<properties 
    pageTitle="Anpassen: Azure AD-Kennwortverwaltung | Microsoft Azure" 
    description="Vorgehensweise zum Anpassen von Aussehen und Verhalten der Kennwortverwaltung und von Benachrichtigungen in Azure AD an Ihre Anforderungen." 
    services="active-directory" 
    documentationCenter="" 
    authors="asteen" 
    manager="kbrint" 
    editor="billmath"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="asteen"/>

# Anpassen der Kennwortverwaltung an die Anforderungen Ihrer Organisation
Es wird empfohlen, alle verfügbaren Konfigurationsoptionen für die Kennwortverwaltung zu untersuchen und auszuprobieren, um Ihren Benutzern das bestmögliche Ergebnis zu bieten. In der Tat können Sie dies sofort untersuchen, indem Sie auf der Konfigurationsregisterkarte Starten der **Active Directory-Erweiterung** in die [Azure-Verwaltungsportal](https://manage.windowsazure.com). Dieses Thema führt Sie durch alle anderen Password Management Anpassungen können Sie als Administrator über vornehmen **konfigurieren** Registerkarte Ihr Verzeichnis innerhalb der [Azure-Verwaltungsportal](https://manage.windowsazure.com), einschließlich:

| Thema |  |
| --------- | --------- |
| Wie aktiviere oder deaktiviere ich das Zurücksetzen von Kennwörtern? | [Einstellung: Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist](#users-enabled-for-password-reset) |
| Wie beschränke ich das Zurücksetzen von Kennwörtern auf eine bestimmte Gruppe von Benutzern? | [Beschränken der Kennwortzurücksetzung auf bestimmte Benutzer](#restrict-access-to-password-reset) |
| Wie ändere ich die unterstützten Authentifizierungsmethoden? | [Einstellung: Für Benutzer verfügbare Authentifizierungsmethoden](#authentication-methods-available-to-users) |
| Wie ändere ich die Anzahl erforderlicher Authentifizierungsmethoden? | [Einstellung: Anzahl erforderlicher Authentifizierungsmethoden](#number-of-authentication-methods-required) |
| Wie richte ich benutzerdefinierte Sicherheitsfragen ein? | [Einstellung: Benutzerdefinierte Sicherheitsfragen](#custom-security-questions) |
| Wie richte ich vorgefertigte lokalisierte Sicherheitsfragen ein? | [Einstellung: Informationsbasierte Sicherheitsfragen](#knowledge-based-security-questions) |
| Wie kann ich ändern, wie viele Sicherheitsfragen erforderlich sind? | [Einstellung: Anzahl der Sicherheitsfragen für die Registrierung oder das Zurücksetzen](#number-of-questions-required-to-register) |
| Wie kann ich meine Benutzer bei der Anmeldung zum Registrieren zwingen? | [Erzwungene registrierungsbasierte Einführung der Kennwortzurücksetzung](#require-users-to-register-when-signing-in) |
| Wie kann ich veranlassen, dass meine Benutzer ihre registrierten Authentifizierungsdaten in regelmäßigen Abständen erneut bestätigen müssen? | [Einstellung: Anzahl der Tage, bis die Benutzer ihre Authentifizierungsdaten erneut bestätigen müssen](#number-of-days-before-users-must-confirm-their-contact-data) |
| Wie kann ich anpassen, wie ein Benutzer mit dem Administrator in Kontakt tritt? | [Einstellung: Link „Wenden Sie sich Ihren Administrator“ anpassen](#customize-the-contact-your-administrator-link) |
| Wie kann ich Benutzern das Entsperren von AD-Konten ohne Zurücksetzen eines Kennworts ermöglichen? | [Einstellung: Benutzern ermöglichen, ihre AD-Konten ohne Zurücksetzen eines Kennworts zu entsperren](#allow-users-to-unlock-accounts-without-resetting-their-password) |
| Wie kann ich Benachrichtigungen zur Kennwortzurücksetzung für Benutzer aktivieren? | [Einstellung: Benutzer benachrichtigen, wenn ihre Kennwörter zurückgesetzt wurden](#notify-users-and-admins-when-their-own-password-has-been-reset) |
| Wie kann ich Benachrichtigungen zur Kennwortzurücksetzung für Administratoren aktivieren? | [Einstellung: Andere Administratoren benachrichtigen, wenn ein Administrator sein eigenes Kennwort zurückgesetzt hat](#notify-admins-when-other-admins-reset-their-own-passwords) |
| Wie kann ich das Look-and-Feel für das Zurücksetzen von Kennwörtern anpassen? | [Einstellung: Firmenname, Branding und Logo ](#password-managment-look-and-feel) |


## Aussehen und Verhalten der Kennwortverwaltung
In der folgenden Tabelle werden die Auswirkungen der einzelnen Steuerelemente auf die bereitgestellte Funktionalität für Benutzer beschrieben, die sich für die Kennwortzurücksetzung registrieren und ihre Kennwörter zurücksetzen.  Sie können diese Optionen unter Konfigurieren der **Verzeichniseigenschaften** Teil Ihres Verzeichnisses **konfigurieren** Registerkarte innerhalb der [Azure-Verwaltungsportal](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Richtliniensteuerung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Beschreibung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Wirkt sich auf?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="directory-name">
                  <p>Verzeichnisname</p>
                </div>
              </td>
              <td>
                <p>Legt fest, welcher Organisationsname den Benutzern oder Administratoren bei der E-Mail-Kommunikation zur Kennwortzurücksetzung angezeigt wird</p>
              </td>
              <td>
                <p>
                  <strong>"Wenden Sie sich an Ihren Administrator" e-Mails:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines the from address friendly name, e.g. “Microsoft on behalf of <strong>Wingtip Toys</strong>"<br><br></li>
                  <li class="unordered">
                                                Determines the subject name of the email, e.g. “<strong>Wingtip Toys</strong> Überprüfungscode für e-Mail-Konto "<br><br></li>
                </ul>
                <p>
                  <strong>E-Mails zur kennwortzurücksetzung:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines the from address friendly name, e.g. “Microsoft on behalf of <strong>Wingtip Toys</strong>"<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="sign-in-and-access-panel-page-appearance">
                  <p>Darstellung der Seiten " Anmelden" und " Zugriffsbereich"</p>
                </div>
              </td>
              <td>
                <p>Legt fest, ob Benutzern, die die Seite zur Kennwortzurücksetzung besuchen, das Microsoft-Logo oder Ihr eigenes benutzerdefiniertes Logo angezeigt wird.  Dieses Konfigurationselement fügt den Seiten "Zugriffsbereich" und "Anmelden" außerdem Ihr Branding hinzu.</p>
                <p>
                  
                </p>
                <p>You can learn more about the tenant branding and customization feature at <a href="https://technet.microsoft.com/library/dn532270.aspx">Add company branding to your Sign In and Access Panel pages</a>.</p>
              </td>
              <td>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines whether or not your logo is shown at the top of the password reset portal instead of the default Microsoft logo.<br><br></li>
                  <li class="unordered">
                    <strong>Note:</strong> you may not see your logo on the first page of the password reset portal if you come to the password reset page directly.  Once a user enters his or her userID and clicks next, your logo will appear.  You can force your logo to appear on page load by passing the whr parameter to the password reset page, like this: <a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a><br><br></li>
                </ul>
                <p>
                  <strong>”Contact your administrator” emails:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines whether or not your logo is shown at the bottom of the emails sent to admins when users choose to contact you by clicking the “contact your administrator” link on the password reset UI.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset emails:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines whether or not your logo is shown at the bottom of the emails sent to users when they reset their passwords.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Verhalten der Kennwortverwaltung
In der folgenden Tabelle werden die Auswirkungen der einzelnen Steuerelemente auf die bereitgestellte Funktionalität für Benutzer beschrieben, die sich für die Kennwortzurücksetzung registrieren und ihre Kennwörter zurücksetzen.  Sie können diese Optionen unter Konfigurieren der **Richtlinie zum Zurücksetzen des** Teil Ihres Verzeichnisses **konfigurieren** Registerkarte der [Azure-Verwaltungsportal](https://manage.windowsazure.com).

> [AZURE.NOTE] Das Administratorkonto, das Sie verwenden, benötigen eine AAD Premium-Lizenz zugewiesen sein, damit diese Steuerelemente finden Sie unter.<br><br>Diese richtliniensteuerelemente gelten nur für Endbenutzer, die ihre Kennwörter zurücksetzen, nicht für Administratoren.  **Administratoren besitzen eine Standardrichtlinie einer alternativen E-Mail-Adresse und/oder eines Mobiltelefons. Diese wird von Microsoft angegeben und kann nicht geändert werden.**

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Richtliniensteuerung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Beschreibung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Wirkt sich auf?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="users-enabled-for-password-reset">
                  <p>Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist</p>
                </div>
              </td>
              <td>
                <p>Legt fest, ob das Zurücksetzen von Kennwörtern für Benutzer in diesem Verzeichnis aktiviert ist. </p>
              </td>
              <td>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, no users can register their own challenge data.<br><br></li>
                  <li class="unordered">
                                                If set to yes, any end user in the directory can register challenge data by going to the registration portal at <a href="http://aka.ms/ssprsetup">http://AKA.ms/ssprsetup</a>.<br><br></li>
                  <li class="unordered">
                    <strong>Hinweis:</strong> Benutzer benötigen ein Azure AD Premium oder Basic-Lizenz zugewiesen werden, bevor sie für das Zurücksetzen von Kennwörtern registrieren können.<br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, users see a message saying the must contact their admin to reset their password.<br><br></li>
                  <li class="unordered">
                                                If set to yes, users are able to reset their passwords automatically by going to  <a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a>, oder klicken auf die <strong>kann nicht auf Ihr Konto zugreifen</strong> Link auf eine beliebige Organisations-ID-Anmeldeseite.<br><br></li>
                  <li class="unordered">
                    <strong>Hinweis:</strong> Benutzer benötigen ein Azure AD Premium oder Basic-Lizenz zugewiesen werden, bevor sie ihre Kennwörter zurücksetzen können.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="restrict-access-to-password-reset">
                  <p>Zugriff auf die Kennwortrücksetzung beschränken</p>
                </div>
              </td>
              <td>
                <p>Legt fest, ob nur eine bestimmte Gruppe von Benutzern das Zurücksetzen von Kennwörtern verwenden darf. (Nur sichtbar, wenn <strong>Benutzer, für das Zurücksetzen des Kennworts aktiviert</strong> Wert <strong>Ja</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Registrierungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, then all end users in your directory can register for password reset at <a href="http://aka.ms/ssprsetup">http://AKA.ms/ssprsetup</a><br><br></li>
                  <li class="unordered">
                                                If set to yes, then only end users specified in the <strong>Gruppe, die das Zurücksetzen von Kennwörtern ausführen kann</strong> Steuerelement kann registrieren, für die kennwortzurücksetzung an  <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a><br><br></li>
                </ul>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, then all end users in your directory can reset their passwords.<br><br></li>
                  <li class="unordered">
                                                If set to yes, then only end users specified in the <strong>Gruppe, die das Zurücksetzen von Kennwörtern ausführen kann</strong> Steuerelement kann ihre Kennwörter zurücksetzen.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="group-that-can-perform-password-reset">
                  <p>Gruppe, die eine Kennwortrücksetzung durchführen kann</p>
                </div>
              </td>
              <td>
                <p>Legt fest, welche Gruppe von Endbenutzern die Kennwortzurücksetzung durchführen kann. </p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>restrict access to password reset</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If no group is specified and you click <strong>Save</strong>, an empty group called <strong>SSPRSecurityGroupUsers</strong> will be created for you.<br><br></li>
                  <li class="unordered">
                                                If you’d like to specify your own group, you can provide your own display name.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If <strong>restrict access to password reset</strong> is set to <strong>yes</strong>, then only end users in this group will be able to register for password reset. <br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If <strong>restrict access to password reset</strong> is set to <strong>yes</strong>, then only end users in this group will be able to reset their passwords.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="authentication-methods-available-to-users">
                  <p>Authentication methods available to users</p>
                </div>
              </td>
              <td>
                <p>Determines which challenges a user is allowed to use to reset his or her password.</p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>users enabled for password reset</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                At least one option must be selected.<br><br></li>
                  <li class="unordered">
                                                We highly recommend enabling at least 2 options to give your users the most flexibility when resetting their passwords.<br><br></li>
                  <li class="unordered">
                                                If you are using security questions, we highly recommend you use them in conjunction with another authentication method, as security questions can be less secure than phone or email-based password reset methods.<br><br></li>
                </ul>
                <p>
                  <strong>Which directory fields are used?</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Office Phone corresponds to the <strong>Office Phone</strong> attribute on a user object in the directory.<br><br></li>
                  <li class="unordered">
                                                Mobile Phone corresponds to either the <strong>Authentication Mobile</strong> attribute (which is not publically visible) or the <strong>Mobile Phone</strong> attribute (which is publically visible) on a user object in the directory.  The service first checks <strong>Authentication Phone</strong> for data, and if there is none present, falls back to the <strong>Mobile Phone</strong> attribute.<br><br></li>
                  <li class="unordered">
                                                Alternate Email Address corresponds to either the <strong>Authentication Email</strong> attribute (which is not publically visible) or the <strong>Alternate Email</strong> attribute on a user object in the directory.  The service first checks <strong>Authentication Email</strong> for data, and if there is none present, falls back to the <strong>Alternate Email</strong> attribute.<br><br></li>
                  <li class="unordered">
                                                Security Questions are stored privately and securely on a user object in the directory and can only be answered by users during registration.  For security purposes, there is currently no way for an administrator to edit or see these answers.<br><br></li>
                  <li class="unordered">
                    <strong>Note: </strong>by default, only the cloud attributes Office Phone and Mobile Phone are synchronized to your cloud directory from your on-premises directory.  To learn more about which on-premises attributes are synced to the cloud, see <a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">Attributes synchronized to Azure AD.</a><br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Affects which authentication methods are displayed when users are registering.  If you do not enable a given authentication method, users will not be able to self-register for that authentication method.<br><br></li>
                  <li class="unordered">
                    <strong>Note: </strong>users are currently not able to register their own office phone numbers; that authentication method must be defined by their administrator.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines which authentication methods a user can use as challenges for a given verification step.  For example, if a user has data in both the <strong>Office Phone</strong> and <strong>Authentication Phone</strong> fields in Azure Active Directory, then he or she can use either of these authentication methods to recover his or her password.<br><br></li>
                  <li class="unordered">
                    <strong>Note: </strong>users will be able to reset their password if and only if they have data present in the authentication methods you have enabled as an administrator.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-authentication-methods-required">
                  <p>Number of authentication methods required</p>
                </div>
              </td>
              <td>
                <p>Determines the minimum number of the available authentication methods a user must go through to reset his or her password.</p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>users enabled for password reset</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Can be set to 1 or 2 authentication methods required.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines the minimum number of authentication methods a user must register before being able to finish the registration experience.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Affects number of verification steps a user must go through before being able to reset a password.  A verification step is defined to be a user using one piece of authentication information (such as a call to their office phone, or an email to their alternate email) to verify their account.<br><br></li>
                  <li class="unordered">
                    <strong>Note:</strong> If a user does not have the required amount of authentication information defined on his or her account in order to be successful resetting his or her password in accordance with the policy you’ve set, he or she will see an error page which will direct them to request an administrator to reset his or her password.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-register">
                  <p>Number of questions required to register</p>
                </div>
              </td>
              <td>
                <p>Determines the minimum number of questions a user must answer when registering for the security questions option.</p>
                <p>(Only visible if the <strong>Security Questions</strong> checkbox is enabled).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Can be set to 3 – 5 questions required to register.<br><br></li>
                  <li class="unordered">
                                                Number of questions required to register must be greater than or equal to the number of questions required to reset.<br><br></li>
                  <li class="unordered">
                                                We recommend you set the number of questions required to register to be higher than the number required to reset so users have more flexibility when resetting their passwords.  This is also a more secure configuration because we will randomly select questions for the user to answer from the pool of all of the questions they have registered.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines the minimum number of questions a user must answer before the user is considered fully registered for password reset.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-reset">
                  <p>Number of questions required to reset </p>
                </div>
              </td>
              <td>
                <p>Determines the minimum number of questions a user must answer when resetting a password.</p>
                <p>
                  
                </p>
                <p>(Only visible if the <strong>Security Questions</strong> checkbox is enabled).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Can be set to 3 – 5 questions required to reset.<br><br></li>
                  <li class="unordered">
                                                Number of questions required to reset must be less than or equal to the number of questions required to register.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines the minimum number of questions a user must answer before the user’s password can be reset.<br><br></li>
                  <li class="unordered">
                                                At the time of password reset, this number of questions will be selected at random from the user’s total list of registered questions.  For example, if the user has 5 questions registered, 3 of those 5 questions will be selected at random when the user comes to reset a password.  The user must then answer all of these questions correctly before the password can be reset.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="knowledge-based-security-questions">
                  <p>Knowledge based security questions</p>
                </div>
              </td>
              <td>
                <p>Defines the pre-canned security questions your users may choose from when registering for password reset and when resetting their passwords.</p>
                <p>
                  
                </p>
                <p>(Only visible if the <strong>Security Questions</strong> checkbox is enabled).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                All knowledge-based questions will be localized into the full set of O365 languages based off of the user's browser locale.<br><br></li>
                  <li class="unordered">
                                                Up to 20 total questions can be defined (the sum of your custom and knowledge-based questions).<br><br></li>
                 <li class="unordered">
                                                Min answer character limit is 3 characters.<br><br></li>
                  <li class="unordered">
                                                Max answer character limit is 40 characters.<br><br></li>
                  <li class="unordered">
                                                Users may not answer the same question twice.<br><br></li>
                  <li class="unordered">
                                                Users may not provide the same answer to two different questions twice.<br><br></li>
                  <li class="unordered">
                                                Any character set may be used to define answers (including Unicode characters).<br><br></li>
                  <li class="unordered">
                                                The number of questions defined must be greater than or equal to the number of questions required to register.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines which questions a user is able to provide answers for when registering for password reset.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines which questions a user is able to use to reset a password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-security-questions">
                  <p>Custom Security questions</p>
                </div>
              </td>
              <td>
                <p>Defines the security questions your users may choose from when registering for password reset and when resetting their passwords.</p>
                <p>
                  
                </p>
                <p>(Only visible if the <strong>Security Questions</strong> checkbox is enabled).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Up to 20 total questions can be defined (the sum of your custom and knowledge-based questions).<br><br></li>
                  <li class="unordered">
                                                Max question character limit is 200 characters.<br><br></li>
                  <li class="unordered">
                                                Min answer character limit is 3 characters.<br><br></li>
                  <li class="unordered">
                                                Max answer character limit is 40 characters.<br><br></li>
                  <li class="unordered">
                                                Users may not answer the same question twice.<br><br></li>
                  <li class="unordered">
                                                Users may not provide the same answer to two different questions twice.<br><br></li>
                  <li class="unordered">
                                                Any character set may be used to define questions and answers (including Unicode characters).<br><br></li>
                  <li class="unordered">
                                                The number of questions defined must be greater than or equal to the number of questions required to register.<br><br></li>
                  <li class="unordered">
                                                Defining different questions for different locales is not supported for custom questions.  All custom questions will be displayed in the language in which you enter them in the administrative UI, even if the user's browser locale is different.  If you need these questions to be localized, please use the "knowledge based" questions instead.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines which questions a user is able to provide answers for when registering for password reset.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Determines which questions a user is able to use to reset a password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="require-users-to-register-when-signing-in">
                  <p>Require users to register when signing in?</p>
                </div>
                <p>
                  
                </p>
              </td>
              <td>
                <p>Determines if a user is required to register contact data for password reset the next time he or she signs in.  
                </p>
                <p>This capability works on any sign-in page that uses a work or school account.  Such pages include all of Office 365, the Azure Management Portal, the Access Panel, and any federated or custom-developed applications that use Azure AD to sign in.
                </p>
                <p>
                  
                </p>
                <p>Enforced registration will only apply to users who are enabled for password reset, so if you have used the "restrict access to password reset" feature and scoped password reset to a specific group of users, then only users in that group will be required to register for password reset when signing in.</p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>users enabled for password reset</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If you disable this feature, you can also manually send users to <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> to register their contact data.  <br><br></li>
                  <li class="unordered">
                                                Users can also reach the registration portal by clicking the <strong>register for password reset</strong> link under the profile tab in the access panel.<br><br></li>
                  <li class="unordered">
                                                Registration via this method can be dismissed by clicking the cancel button or closing the window, but users will be nagged every time they sign in if they do not register.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                This setting does not affect the behavior of the registration portal itself, rather, it determines whether or not the registration portal is shown to users when they sign in to the access panel.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-days-before-users-must-confirm-their-contact-data">
                  <p>Number of days before users must confirm their contact data</p>
                </div>
              </td>
              <td>
                <p>When <strong>require users to register</strong> is turned on, this setting determines the period of time which can elapse before a user must re-confirm their data. </p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>require users to register when signing in to the access panel</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Note: </strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Values between 0-730 days are accepted, with 0 days meaning that users will never be asked to re-confirm their contact data.<br><br></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                This setting does not affect the behavior of the registration portal itself, rather, it determines whether or not the registration portal is shown to users when their contact data needs to be reconfirmed. <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="customize-the-contact-your-administrator-link">
                  <p>Customize the contact your administrator link?</p>
                </div>
              </td>
              <td>
                <p>Controls whether or not the contact your administrator link (shown to the left) that appears on the password reset portal when an error occurs or a user waits too long on an operation points to a custom URL or email address.</p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>users enabled for password reset</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Note: </strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If you enable this setting, you must choose a custom URL or email address by filling out the <strong>custom email address or url</strong> field immediately below this setting.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, users clicking the highlighted link will dispatch an email to the primary email address of all tenant administrators requesting that his or her password be reset.  This email is dispatched by following the logic below:<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
                                                        If there are password administrators, send the email to all password administrators, up to a maximum of 100 total recipients.<br><br></li>
                      <li class="unordered">
                                                        If there are no password administrators, send the email to all user administrators, up to a maximum of 100 total recipients.<br><br></li>
                      <li class="unordered">
                                                        If there are no user administrators, send the email to all global administrators, up to a maximum of 100 total recipients.<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
                                                If set to yes, this setting will customize the behavior of the highlighted link above to point to a custom URL or email address to which your users can navigate to get help with password reset.<br><br></li>
                  <li class="unordered">
                                                If you specify a URL, it will be opened in a new tab.<br><br></li>
                  <li class="unordered">
                                                If you specify an email address, we’ll create a mailto link to that email address.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-email-address-or-URL">
                  <p>Custom email address or URL</p>
                </div>
              </td>
              <td>
                <p>Controls the email address or URL to which the <strong>contact your administrator</strong> link points. </p>
                <p>
                  
                </p>
                <p>(Only visible if <strong>customize contact your administrator link</strong> is set to <strong>yes</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Must be a valid intranet or extranet URL or email address.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                Changes where the <strong>contact your administrator</strong> link points.<br><br></li>
                  <li class="unordered">
                                                If you provide an email address, the link will become a “mailto” link to that email address.<br><br></li>
                  <li class="unordered">
                                                If you provide a URL, the link will become a standard href pointing to that URL which will open in a new tab.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="write-back-passwords-to-on-premises-directory">
                  <p>Write back passwords to on-premises directory</p>
                </div>
              </td>
              <td>
                <p>Controls whether or not Password Writeback is enabled for this directory and, if writeback is on, indicates the status of the on-premises writeback service.</p>
                <p>
                  
                </p>
                <p>This is setting is useful if you want to temporarily disable the service without re-configuring Azure AD Connect.</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                This control only appears if you have installed Password Writeback by downloading the latest version of Azure AD Connect and enabling the <strong>Password Writeback</strong> option under the <strong>optional features</strong> selection screen.<br><br></li>
                  <li class="unordered">
                                                If you have enabled Password Writeback and feel there is a configuration issue with the service, you can come to this tab and look at the <strong>password write back service status</strong> label to see if something is wrong.<br><br></li>
                  <li class="unordered">
                                                Statuses that can be shown are:<br><br><ul><li class="unordered"><strong>Configured </strong>– everything is working as expected<br><br></li><li class="unordered"><strong>Not configured</strong> – you have writeback installed, but we can’t reach the service, check to make sure you are not blocking outbound connections to 443 and try re-installing the service if you still have problems.<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>Registration portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If writeback is deployed and configured and this switch is set to <strong>no</strong>, then writeback will be disabled, and federated and password hash sync’d users will not be able to register for password reset their passwords.<br><br></li>
                  <li class="unordered">
                                                If the switch is set to <strong>yes</strong>, then writeback will be enabled, and federated and password hash sync’d users will be able to reset their passwords.<br><br></li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If writeback is deployed and configured and this switch is set to <strong>no</strong>, then writeback will be disabled, and federated and password hash sync’d users will not be able to reset their passwords.<br><br></li>
                  <li class="unordered">
                                                If the switch is set to <strong>yes</strong>, then writeback will be enabled, and federated and password hash sync’d users will be able to reset their passwords.<br><br></li>
                </ul>
              </td>
            </tr>
             <tr>
              <td>
                <div id="allow-users-to-unlock-accounts-without-resetting-their-password">
                  <p>Allow users to unlock accounts without resetting their password</p>
                </div>
              </td>
              <td>
              
              <p>Designates whether or not users who visit the password reset portal should be given the option to unlock their on-premises Active Directory accounts without resetting their password. By default, Azure AD will always unlock accounts when performing a password reset, this setting allows you to separate those two operations.</p>
              
              <p>If set to “yes”, then users will be given the option to reset their password and unlock the account, or to unlock without resetting the password. </p>
              
              <p>If set to “no”, then users will only be able to perform a combined password reset and account unlock operation.</p>

              </td>
              <td>
                <p>
                  <strong>Note:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                In order to use this feature, you must install the August 2015 or later release of Azure AD Connect (v. 1.0.8667.0 or greater).<br><br><a href="http://www.microsoft.com/download/details.aspx?id=47594">Click here to download the latest version of Azure AD Connect.</a></li>
                        
                  <li class="unordered">
                    <strong>Note:</strong> In order to test this feature, you will need enable password writeback, and  use an account that is sourced from on-premises (like a federated or password synchronized user) and has a locked account.  Users who do not come from on premises and do not have a locked account will not see the option to unlock their accounts.</li>
                </ul>
                <p>
                  <strong>Password reset portal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                After enabling this option, when a user with an on-premises account that is locked arrives at the password reset portal, he or she will be given the option to unlock their account without resetting their password.<br><br>Note that if you are using password writeback, accounts are already automatically unlocked when the password is reset, and that this option simply decouples those operations.<br><br>This is an especially useful option to enable if you find that many of your helpdesk calls are generated by account unlock requests.</li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Benachrichtigungen zur Kennwortverwaltung
In der folgenden Tabelle werden die Auswirkungen der einzelnen Steuerelemente auf die bereitgestellte Funktionalität für Benutzer und Administratoren beschrieben, die Benachrichtigungen zur Kennwortzurücksetzung erhalten.  Sie können diese Optionen unter Konfigurieren der **Benachrichtigungen** Teil Ihres Verzeichnisses **konfigurieren** Registerkarte der [Azure-Verwaltungsportal](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Richtliniensteuerung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Beschreibung</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Wirkt sich auf?</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-admins-when-other-admins-reset-their-own-passwords">
                  <p>Administratoren benachrichtigen, wenn andere Administratoren ihre eigenen Kennwörter zurücksetzen</p>
                </div>
              </td>
              <td>
                <p>Legt fest, ob alle globale Administratoren über eine E-Mail an ihre primäre E-Mail-Adresse benachrichtigt werden, sobald ein anderer Administrator eines beliebigen Typs sein eigenes Kennwort zurücksetzt.</p>
              </td>
              <td>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, then no notifications will be sent.<br><br></li>
                  <li class="unordered">
                                                If set to yes, then all other global administrators will be notified when any single admin resets his or her own password.<br><br></li>
                  <li class="unordered">
                                                This notification is sent via an email to the primary email addresses of all other global admins in the organization.<br><br></li>
                </ul>
                <p>
                  <strong>Beispiel:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If this option was enabled when admin A resets his password, and there are 3 other admins in the tenant, B, C, and D, then admins B, C, and D would receive an email indicating admin A reset his password.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-users-and-admins-when-their-own-password-has-been-reset">
                  <p>Benutzer und Administratoren benachrichtigen, wenn ihr eigenes Kennwort zurückgesetzt wurde</p>
                </div>
              </td>
              <td>
                <p>Legt fest, ob Benutzer oder Administratoren, die ihre eigenen Kennwörter zurücksetzen, eine E-Mail-Benachrichtigung erhalten, dass ihr Kennwort zurückgesetzt wurde.</p>
              </td>
              <td>
                <p>
                  <strong>Kennwortzurücksetzungsportal:</strong>
                </p>
                <ul>
                  <li class="unordered">
                                                If set to no, then no notifications will be sent.<br><br></li>
                  <li class="unordered">
                                                If set to yes, then whenever a user or admin resets his own password, he or she will receive a notification indicating his or her password has been reset.<br><br></li>
                  <li class="unordered">
                                                This notification is sent via an email to the primary, and alternate (or authentication) email address of the user who reset his or her password.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/>
<br/>
<br/>

## Links zur Dokumentation für die Kennwortzurücksetzung
Im Folgenden finden Sie Links zu allen Webseiten mit Informationen zur Kennwortzurücksetzung für Azure AD: 

* [**Ihr eigenes Kennwort zurücksetzen**](active-directory-passwords-update-your-own-password.md) -erfahren Sie mehr über zurücksetzen oder ändern Ihr eigenes Kennwort als Benutzer des Systems
* [**Funktionsweise**](active-directory-passwords-how-it-works.md) -erfahren Sie mehr über die sechs verschiedenen Komponenten der Dienst und die jeweilige
* [**Erste Schritte**](active-directory-passwords-getting-started.md) -erfahren Sie, wie Sie Benutzern das Zurücksetzen und Ändern ihrer Cloud- oder lokalen Kennwörter erlauben
* [**Bewährte Methoden**](active-directory-passwords-best-practices.md) -erfahren Sie, wie schnell bereitstellen und effektiv verwalten von Kennwörtern in Ihrer Organisation
* [**Einblicke**](active-directory-passwords-get-insights.md) -erfahren Sie mehr über unsere integrierten Berichtsfunktionen
* [**Häufig gestellte Fragen zu**](active-directory-passwords-faq.md) – Hier erhalten Sie Antworten auf häufig gestellte Fragen
* [**Problembehandlung bei**](active-directory-passwords-troubleshoot.md) -erfahren Sie, wie Probleme mit dem Dienst schnell beheben
* [**Erfahren Sie mehr**](active-directory-passwords-learn-more.md) – erhalten Sie tiefgehende technische Details zur Funktionsweise des Diensts


[001]: ./media/active-directory-passwords-customize/001.jpg "Image_001.jpg"

