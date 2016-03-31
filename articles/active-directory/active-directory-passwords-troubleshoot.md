<properties 
    pageTitle="Problembehandlung: Azure AD-Kennwortverwaltung | Microsoft Azure" 
    description="Allgemeine Problembehandlungsschritte für die Azure AD-Kennwortverwaltung, einschließlich Zurücksetzen, Ändern und Zurückschreiben von Kennwörtern und dem Registrieren von Informationen. Außerdem erfahren Sie, welche Informationen Sie zum Anfordern von Hilfe benötigen." 
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

# Problembehandlung für die Kennwortverwaltung
Wir helfen Ihnen, wenn Sie Probleme bei der Kennwortverwaltung haben. Die meisten Probleme, auf die Sie eventuell stoßen, können mithilfe weniger Problembehandlungsschritte gelöst werden:

* [**Erforderliche Informationen beim Anfordern von Hilfe**](#information-to-include-when-you-need-help)
* [**Probleme mit der Konfiguration der Kennwortverwaltung im Azure-Verwaltungsportal**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Probleme mit Berichten zur Kennwortverwaltung im Azure-Verwaltungsportal**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Probleme mit dem Zurücksetzen Portal zur Registrierung für**](#troubleshoot-the-password-reset-registration-portal)
* [**Probleme mit dem Kennwort Kennwortzurücksetzungsportal**](#troubleshoot-the-password-reset-portal)
* [**Probleme beim Rückschreiben von Kennwörtern**](#troubleshoot-password-writeback)
  - [Ereignisprotokoll-Fehlercodes für die Kennwortrückschreibung](#password-writeback-event-log-error-codes)
  - [Konnektivitätsprobleme bei der Kennwortrückschreibung](#troubleshoot-password-writeback-connectivity)

Wenn Sie die folgenden Schritte zur Problembehandlung ausprobiert haben und noch Probleme ausgeführt werden, Sie können eine Frage der [Azure AD-Foren](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) oder sich an den Support, und wir werden sehen Sie sich das Problem bald wie möglich.

## Erforderliche Informationen beim Anfordern von Hilfe

Wenn Ihr Problem mithilfe der folgenden Anleitungen nicht gelöst wird, können Sie sich an einen unserer Supportmitarbeiter wenden. Hierbei sollten Sie die folgenden Informationen bereithalten:

 - **Allgemeine Beschreibung des Fehlers** – welche Fehlermeldung wird dem Benutzer angezeigt?  Wenn keine Fehlermeldung angezeigt wird, beschreiben Sie das beobachtete unerwartete Verhalten so genau wie möglich.
 - **Seite** – welcher Seite haben Sie sich befunden, den Fehler (einschließlich URL)?
 - **Datum / Uhrzeit / Zeitzone** – was genaue Datum und Uhrzeit, die Sie haben den Fehler beobachtet (einschließlich Zeitzone)?
 - **Unterstützungscode** – was generiert, wenn der Benutzer gesehen, den Fehler haben (zu finden, den Fehler reproduzieren Unterstützungscode am unteren Bildschirmrand und dann senden dem Supportmitarbeiter die resultierende GUID). 
   - Wenn Sie sich auf einer Seite ohne Unterstützungscode befinden, drücken Sie F12, und suchen Sie nach SID und CID. Senden Sie beide Ergebnisse an den Supportmitarbeiter.

    ![][001]

 - **Benutzer-ID** – was die ID des Benutzers, der den Fehler (z. B. user@contoso.com) gesehen haben?
 - **Informationen über den Benutzer** – wurde der Verbund-Benutzer, der Kennworthash synchronisiert, nur Cloud?  Ist dem Benutzer eine AAD Premium- oder eine AAD Basic-Lizenz zugewiesen?
 - **Das Anwendungsereignisprotokoll** – Wenn Sie das Rückschreiben von Kennwörtern und der Fehler in Ihrer lokalen Infrastruktur ist zippen Sie eine Kopie des Anwendungsereignisprotokolls von Ihrem Azure AD Connect-Server und fügen Sie Ihre Anfrage zu senden.

Diese Informationen helfen uns dabei, Ihr Problem so schnell wie möglich zu beheben.


## Problembehandlung: Konfiguration der Kennwortverwaltung im Azure-Verwaltungsportal
Wenn beim Konfigurieren der Kennwortzurücksetzung ein Fehler auftritt, kann dieser möglicherweise durch das Ausführen der folgenden Problembehandlungsschritte beseitigt werden:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Auftretender Fehler</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Welcher Fehler wird dem Benutzer angezeigt?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Lösung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Ich sehe keine der <strong>Richtlinie zum Zurücksetzen des </strong>unter im Abschnitt der <strong>konfigurieren</strong> Registerkarte im Azure-Verwaltungsportal</p>
            </td>
            <td>
              <p>Die <strong>Richtlinie zum Zurücksetzen des </strong>Abschnitt ist nicht sichtbar, auf die <strong>konfigurieren</strong> Registerkarte im Azure-Verwaltungsportal.</p>
            </td>
            <td>
              <p>Dieser Fehler kann auftreten, wenn dem Administrator, der den Vorgang ausführt, keine AAD Premium- oder AAD Basic-Lizenz zugewiesen wurde. </p>
              <p>Um dieses Problem zu beheben, weisen Sie eine AAD Premium- oder AAD Basic-Lizenz auf dem betreffenden Administratorkonto zum Navigieren der <strong>Lizenzen</strong> Registerkarte, und versuchen Sie es erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Ich sehe keine der Konfigurationsoptionen unter der <strong>Richtlinie zum Zurücksetzen des</strong> -Abschnitt, der in der Dokumentation beschrieben werden.</p>
            </td>
            <td>
              <p>Die <strong>Richtlinie zum Zurücksetzen des </strong>Abschnitt wird angezeigt, jedoch ist das einzige Flag, das angezeigt wird der <strong>Benutzer zum Zurücksetzen des Kennworts aktiviert</strong> Flag.</p>
            </td>
            <td>
              <p>Der Rest der Benutzeroberfläche wird angezeigt, wenn Sie Umschalten der <strong>Benutzer zum Zurücksetzen des Kennworts aktiviert</strong> Kennzeichnung <strong>Ja.</strong></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Eine bestimmte Konfigurationsoption wird mir nicht angezeigt.</p>
            </td>
            <td>
              <p>Beispielsweise wird nicht angezeigt der <strong>Anzahl der Tage, bevor Benutzer ihre Kontaktdaten bestätigen müssen</strong> option, wenn ich einen Bildlauf der <strong>Richtlinie zum Zurücksetzen des</strong> Abschnitt (oder andere Beispiele für dasselbe Problem).</p>
            </td>
            <td>
              <p>Viele Elemente der Benutzeroberfläche werden erst angezeigt, wenn sie benötigt werden. Versuchen Sie, alle Optionen auf der Seite zu aktivieren, um sie anzuzeigen.</p>
              <p>Finden Sie unter <a href="../active-directory-passwords-customize#password-management-behavior">Anpassen der Richtlinie zum Zurücksetzen des</a> für Weitere Informationen über alle Steuerelemente, die Ihnen zur Verfügung stehen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Ich sehe keine der <strong>Kennwörter zurück zum lokalen</strong> Konfigurationsoption</p>
            </td>
            <td>
              <p>Die <strong>Kennwörter zurück zum lokalen</strong> Option ist nicht sichtbar unter den <strong>konfigurieren</strong> Registerkarte im Azure-Verwaltungsportal</p>
            </td>
            <td>
              <p>Diese Option wird nur angezeigt, wenn Sie Azure AD Connect heruntergeladen und die Kennwortrückschreibung konfiguriert haben. Nachdem Sie dies getan haben, wird die Option angezeigt, und Sie können das Zurückschreiben von Kennwörtern aus der Cloud aktivieren oder deaktivieren.</p>
              <p>Finden Sie unter <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">wie aktivieren bzw. Deaktivieren des Rückschreibens von Kennwörtern</a> für Weitere Informationen dazu.</p>
            </td>
          </tr>
        </tbody></table>

## Problembehandlung: Berichte zur Kennwortverwaltung im Azure-Verwaltungsportal
Wenn beim Konfigurieren der Berichte für die Kennwortverwaltung ein Fehler auftritt, kann dieser möglicherweise durch das Ausführen der folgenden Problembehandlungsschritte beseitigt werden:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Auftretender Fehler</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Welcher Fehler wird dem Benutzer angezeigt?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Lösung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Ich sehe keine Berichte zur Kennwortverwaltung.</p>
            </td>
            <td>
              <p>Die <strong>kennwortzurücksetzungsaktivität</strong> und <strong>Registrierungsaktivität für die kennwortzurücksetzung</strong> sind nicht sichtbar, klicken Sie unter der <strong>Aktivitätsprotokoll</strong> Berichte in der <strong>Berichte</strong> Registerkarte.</p>
            </td>
            <td>
              <p>Dieser Fehler kann auftreten, wenn dem Administrator, der den Vorgang ausführt, keine AAD Premium- oder AAD Basic-Lizenz zugewiesen wurde. </p>
              <p>Um dieses Problem zu beheben, weisen Sie eine AAD Premium- oder AAD Basic-Lizenz auf dem betreffenden Administratorkonto zum Navigieren der <strong>Lizenzen</strong> Registerkarte, und versuchen Sie es erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Benutzerregistrierungen werden mehrfach angezeigt.</p>
            </td>
            <td>
              <p>Wenn ein Benutzer eine alternative E-Mail-Adresse, ein Mobiltelefon und Sicherheitsfragen registriert, werden diese Optionen nicht als eine Zeile, sondern in separaten Zeilen angezeigt.</p>
            </td>
            <td>
              <p>Wenn sich ein Benutzer registriert, kann nicht davon ausgegangen werden, dass für alle Optionen auf der Registrierungsseite Daten konfiguriert werden. Deshalb werden alle Daten, die separat registriert werden, getrennt protokolliert.</p>
              <p>Wenn Sie diese Daten aggregieren möchten, können Sie den Bericht herunterladen und die Daten als Pivottabelle in Excel öffnen, um flexibler zu sein.</p>
            </td>
          </tr>
        </tbody></table>

## Problembehandlung: Registrierungsportal für die Kennwortzurücksetzung
Wenn beim Registrieren eines Benutzers für die Kennwortzurücksetzung ein Fehler auftritt, kann dieser möglicherweise durch das Ausführen der folgenden Problembehandlungsschritte beseitigt werden:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Auftretender Fehler</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Welcher Fehler wird dem Benutzer angezeigt?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Lösung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Das Verzeichnis ist nicht für die Kennwortzurücksetzung aktiviert.</p>
            </td>
            <td>
              <p>Ihr Administrator hat die Verwendung dieser Funktion nicht für Sie aktiviert.</p>
            </td>
            <td>
              <p>Wechseln der <strong>Benutzer zum Zurücksetzen des Kennworts aktiviert</strong> Kennzeichnung <strong>Ja</strong> und klicken Sie auf <strong>Speichern</strong> auf der Registerkarte Azure-Verwaltungsportal Directory Configuration. Dem Administrator, der diesen Vorgang ausführt, muss eine Azure AD Premium- oder Basic-Lizenz zugewiesen sein.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Dem Benutzer ist keine AAD Premium- oder AAD Basic-Lizenz zugewiesen.</p>
            </td>
            <td>
              <p>Ihr Administrator hat die Verwendung dieser Funktion nicht für Sie aktiviert.</p>
            </td>
            <td>
              <p>Weisen Sie eine Azure AD Premium oder Basic Azure AD-Lizenz, die gemäß den <strong>Lizenzen</strong> Registerkarte im Azure-Verwaltungsportal. Dem Administrator, der diesen Vorgang ausführt, muss eine Azure AD Premium- oder Basic-Lizenz zugewiesen sein.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Fehler beim Verarbeiten der Anforderung.</p>
            </td>
            <td>
              <p>Benutzern wird eine Fehlermeldung mit dem folgenden Text angezeigt:</p>
              <p>
                
              </p>
              <p>Error processing request </p>
              <p>When attempting to reset a password.</p>
            </td>
            <td>
              <p>This can be caused by many issues, but generally this error is caused by either a service outage or configuration issue that cannot be resolved. </p>
              <p>If you see this error and it is impacting your business, please contact support and we will assist you ASAP. See <a href="#information-to-include-when-you-need-help">Information to include when you need help</a> to see what you should provide to the support engineer to aid in a speedy resolution.</p>
            </td>
          </tr>
        </tbody></table>

## Problembehandlung: Kennwortzurücksetzungsportal
Wenn beim Zurücksetzen eines Kennworts für einen Benutzer ein Fehler auftritt, kann dieser möglicherweise durch das Ausführen der folgenden Problembehandlungsschritte beseitigt werden:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Auftretender Fehler</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Welcher Fehler wird dem Benutzer angezeigt?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Lösung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Das Verzeichnis ist nicht für die Kennwortzurücksetzung aktiviert.</p>
            </td>
            <td>
              <p>Ihr Konto ist nicht für die Kennwortzurücksetzung aktiviert.</p>
              <p>Ihr Administrator hat Ihr Konto nicht zur Verwendung mit diesem Dienst eingerichtet. </p>
              <p>
                
              </p>
              <p>If you'd like, we can contact an administrator in your organization to reset your password for you.</p>
            </td>
            <td>
              <p>Switch the <strong>Users Enabled for Password Reset</strong> flag to <strong>Yes</strong> and hit <strong>Save</strong> in the Azure Management Portal directory configuration tab. You must have an Azure AD Premium or Basic License assigned to the admin performing this operation.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>User does not have an AAD Premium or AAD Basic license assigned</p>
            </td>
            <td>
              <p>While we cannot reset non-admin account passwords automatically, we can contact your organization's admin to do it for you.</p>
            </td>
            <td>
              <p>Assign an Azure AD Premium or Azure AD Basic license to the user under the <strong>Licenses</strong> tab in the Azure Management Portal. You must have an Azure AD Premium or Basic License assigned to the admin performing this operation.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory is enabled for password reset, but user has missing or mal-formed authentication information</p>
            </td>
            <td>
              <p>Your account is not enabled for password reset</p>
              <p>We're sorry, but your administrator has not set up your account for use with this service. </p>
              <p>
                
              </p>
              <p>If you'd like, we can contact an administrator in your organization to reset your password for you.</p>
            </td>
            <td>
              <p>Ensure that user has properly formed contact data on file in the directory before proceeding. See <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">What data is used by password reset</a> for information on how to configure authentication information in the directory so that users do not see this error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory is enabled for password reset, but a user only has one piece of contact data on file when policy is set to require two verification steps</p>
            </td>
            <td>
              <p>Your account is not enabled for password reset</p>
              <p>We're sorry, but your administrator has not set up your account for use with this service. </p>
              <p>
                
              </p>
              <p>If you'd like, we can contact an administrator in your organization to reset your password for you.</p>
            </td>
            <td>
              <p>Ensure that user has at least two properly configured contact methods (e.g., both Mobile Phone and Office Phone) before proceeding. See <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">What data is used by password reset</a> for information on how to configure authentication information in the directory so that users do not see this error.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory is enabled for password reset, and user is properly configured, but user is unable to be contacted </p>
            </td>
            <td>
              <p>Oops!  We encountered an unexpected error while contacting you.</p>
            </td>
            <td>
              <p>This could be the result of a temporary service error or misconfigured contact data that we could not properly detect. If the user waits 10 seconds, a try again and “contact your administrator” link appears. Clicking try again will re-dispatch the call, whereas clicking “contact your administrator” will send a form email to user, password, or global admins (in that precedence order) requesting a password reset to be performed for that user account.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>User never receives the password reset SMS or phone call</p>
            </td>
            <td>
              <p>User clicks “text me” or “call me” and then never receives anything.</p>
            </td>
            <td>
              <p>This could be the result of a mal-formed phone number in the directory. Make sure the phone number is in the format “+ccc xxxyyyzzzzXeeee”. To learn more about formatting phone numbers for use with password reset see <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">What data is used by password reset</a>.</p>
              <p>If you require an extension to be routed to the user in question, note that password reset does not support extensions, even if you specify one in the directory (they are stripped before the call is dispatched). Try using a number without an extension, or integrating the extension into the phone number in your PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>User never receives password reset email</p>
            </td>
            <td>
              <p>User clicks “email me” and then never receives anything.</p>
            </td>
            <td>
              <p>The most common cause for this issue is that the message is rejected by a spam filter. Check your spam, junk, or deleted items folder for the email.</p>
              <p>Also ensure that you are checking the right email for the message…lots of people have very similar email addresses and end up checking the wrong inbox for the message. If neither of these options work, it’s also possible that the email address in the directory is malformed, check to make sure the email address is the right one and try again. To learn more about formatting email addresses for use with password reset see <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">What data is used by password reset</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>I have set a password reset policy, but when an admin account uses password reset, that policy is not applied</p>
            </td>
            <td>
              <p>Admin accounts resetting their passwords see the same options enabled for password reset, email and mobile phone, no matter what policy is set under the <strong>User Password Reset Policy</strong> section of the <strong>Configure</strong> tab.</p>
            </td>
            <td>
              <p>The options configured under the <strong>User Password Reset Policy</strong> section of the <strong>Configure</strong> tab only apply to end users in your organization.</p>
              <p>Microsoft manages and controls the Admin password reset policy in order to ensure the highest level of security</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>User prevented from attempting password reset too many times in a day</p>
            </td>
            <td>
              <p>User sees an error stating:</p>
              <p>
                
              </p>
              <p>Please use another option.</p>
              <p>You've tried to verify your account too many times in the last 1 hour(s). For security reasons, you'll have to wait 24 hour(s) before you can try again. </p>
              <p>If you'd like, we can contact an administrator in your organization to reset your password for you.</p>
            </td>
            <td>
              <p>We implement an automatic throttling mechanism to block users from attempting to reset their passwords too many times in a short period of time. This occurs when:</p>
              <ol class="ordered">
                <li>
                                        User attempts to validate a phone number 5 times in one hour.<br\><br\></li>
                <li>
                                        User attempts to use the security questions gate 5 times in one hour.<br\><br\></li>
                <li>
                                        User attempts to reset a password for the same user account 5 times in one hour.<br\><br\></li>
              </ol>
              <p>To fix this, instruct the user to wait 24 hours after the last attempt, and the user will then be able to reset his or her password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>User sees an error when validating his or her phone number</p>
            </td>
            <td>
              <p>When attempting to verify a phone to use as an authentication method, the user sees an error stating:</p>
              <p>
                
              </p>
              <p>Incorrect phone number specified.</p>
            </td>
            <td>
              <p>This error occurs when the phone number entered does not match the phone number on file.</p>
              <p>Make sure the user is entering the complete phone number, including area and country code, when attempting to use a phone-based method for password reset.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error processing request</p>
            </td>
            <td>
              <p>User sees an error that states:</p>
              <p>
                
              </p>
              <p>Error processing request </p>
              <p>When attempting to reset a password.</p>
            </td>
            <td>
              <p>This can be caused by many issues, but generally this error is caused by either a service outage or configuration issue that cannot be resolved. </p>
              <p>If you see this error and it is impacting your business, please contact support and we will assist you ASAP. See <a href="#information-to-include-when-you-need-help">Information to include when you need help</a> to see what you should provide to the support engineer to aid in a speedy resolution.</p>
            </td>
          </tr>
        </tbody></table>

## Problembehandlung: Kennwortrückschreibung
Wenn beim Aktivieren, Deaktivieren oder Verwenden der Kennwortrückschreibung ein Fehler auftritt, kann dieser möglicherweise durch das Ausführen der folgenden Problembehandlungsschritte beseitigt werden:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Auftretender Fehler</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Welcher Fehler wird dem Benutzer angezeigt?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Lösung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Allgemeine Fehler bei Integration und Start.</p>
            </td>
            <td>
              <p>Der Dienst zum Zurücksetzen von Kennwörtern wird lokal nicht gestartet, im Anwendungsereignisprotokoll für den Azure AD Connect-Computer wird Fehler 6800 protokolliert.</p>
              <p>
                
              </p>
              <p>After onboarding, federated or password hash synced users cannot reset their passwords.</p>
            </td>
            <td>
              <p>When Password Writeback is enabled, the sync engine will call the writeback library to perform the configuration (onboarding) by talking to the cloud onboarding service. Any errors encountered during onboarding or while starting the WCF endpoint for Password Writeback will result in errors in the Event log in your Azure AD Connect machine’s event log.</p>
              <p>During restart of ADSync service, if writeback was configured, the WCF endpoint will be started up. However, if the startup of the endpoint fails, we will simply log event 6800 and let the sync service startup. Presence of this event means that the Password Writeback endpoint was not started up. Event log details for this event (6800) along with event log entries generate by PasswordResetService component will indicate why the endpoint could not be started up. Review these event log errors and try to re-start the Azure AD Connect if Password Writeback still isn’t working. If the problem persists, try to disable and re-enable Password Writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error configuring writeback during Azure AD Connect installation.</p>
            </td>
            <td>
              <p>At the last step of the Azure AD Connect installation process, you see an error indicating that Password Writeback could not be configured.</p>
              <p>
                
              </p>
              <p>The Azure AD Connect Application event log contains error 32009 with text “Error getting auth token”.</p>
            </td>
            <td>
              <p>This error occurs in the following two cases:</p>
              <ul>
                <li class="unordered">
                                        You have specified an incorrect password for the global administrator account specified at the beginning of the Azure AD Connect installation process.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        You have attempted to use a federated user for the global administrator account specified at the beginning of the Azure AD Connect installation process.<br\><br\></li>
              </ul>
              <p>To fix this error, please ensure that you are not using a federated account for the global administrator you specified at the beginning of the Azure AD Connect installation process, and that the password specified is correct.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error configuring writeback during Azure AD Connect installation.</p>
            </td>
            <td>
              <p>The Azure AD Connect machine event log contains error 32002 thrown by the PasswordResetService.</p>
              <p>
                
              </p>
              <p>The error reads: “Error Connecting to ServiceBus, The token provider was unable to provide a security token…”</p>
              <p>
                
              </p>
            </td>
            <td>
              <p>The root cause of this error is that the password reset service running in your on-premises environment is not able to connect to the service bus endpoint in the cloud. This error is normally normally caused by a firewall rule blocking an outbound connection to a particular port or web address.</p>
              <p>
                
              </p>
              <p>Make sure your firewall allows outbound connections for the following:</p>
              <ul>
                <li class="unordered">
                                        All traffic over TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        Outbound connections to <br\><br\></li>
              </ul>
              <p>
                
              </p>
              <p>Once you have updated these rules, reboot the Azure AD Connect machine and Password Writeback should start working again.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Password Writeback endpoint on-prem not reachable</p>
            </td>
            <td>
              <p>After working for some time, federated or password hash synced users cannot reset their passwords.</p>
            </td>
            <td>
              <p>In some rare cases, the Password Writeback service may fail to re-start when Azure AD Connect has re-started. In these cases, first, check whether Password Writeback appears to be enabled on-prem. This can be done using the Azure AD Connect wizard or powershell (See HowTos section above).If the feature appears to be enabled, try enabling or disabling the feature again either through the UI or PowerShell. See “Step 2: Enable Password Writeback on your Directory Sync computer &amp; configure firewall rules” in <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">How to enable/disable Password Writeback</a> for more information on how to do this.</p>
              <p>
                
              </p>
              <p>If this doesn’t work, try completely uninstalling and re-installing Azure AD Connect.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Permissions errors</p>
            </td>
            <td>
              <p>Federated or password hash sync’d users who attempt to reset their passwords see an error after submitting the password indicating there was a service problem.</p>
              <p>
                
              </p>
              <p>In addition to this, during password reset operations, you may see an error regarding management agent was denied access in your on premises event logs.</p>
            </td>
            <td>
              <p>If you see these errors in your event log, confirm that the AD MA account (that was specified in the wizard at the time of configuration) has the necessary permissions for Password Writeback.</p>
              <p>
                
              </p>
              <p>NOTE that once this permission is given it can take up to 1 hour for the permissions to trickle down via sdprop background task on the DC. </p>
              <p>For password reset to work, the permission needs to be stamped on the security descriptor of the user object whose password is being reset. Until this permission shows up on the user object, password reset will continue to fail with access denied.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Error when configuring Password Writeback from the Azure AD Connect configuration wizard </p>
            </td>
            <td>
              <p>“Unable to Locate MA” error in Wizard while enabling/disabling Password Writeback</p>
            </td>
            <td>
              <p>There is a known bug in the released version of Azure AD Connect which manifests in the following situation:</p>
              <ol class="ordered">
                <li>
                                        You configure Azure AD Connect for tenant abc.com (Verified domain) using creds . This results in AAD connector with name “abc.com – AAD” being created.<br\><br\></li>
                <li>
                                        You then then change the AAD creds for the connector (using old sync UI) to  (note it’s the same tenant but different domain name). <br\><br\></li>
                <li>
                                        Now you try to enable/disable Password Writeback. The wizard will construct the name of the connector using the creds, as “abc.onmicrosoft.com – AAD” and pass to the Password Writeback cmdlet. This will fail because there is no connector created with this name.<br\><br\></li>
              </ol>
              <p>This has been fixed in our latest builds. If you have an older build, the one workaround is to use the powershell cmdlet to enable/disable the feature. See “Step 2: Enable Password Writeback on your Directory Sync computer &amp; configure firewall rules” in <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">How to enable/disable Password Writeback</a> for more information on how to do this.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Unable to reset password for users in special groups such as Domain Admins / Enterprise Admins etc.</p>
            </td>
            <td>
              <p>Federated or password hash sync’d users who are part of protected groups and attempt to reset their passwords see an error after submitting the password indicating there was a service problem.</p>
            </td>
            <td>
              <p>Privileged users in Active Directory are protected using AdminSDHolder. See <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> for more details. </p>
              <p>
                
              </p>
              <p>This means the security descriptors on these objects are periodically checked to match the one specified in AdminSDHolder and are reset if they are different. The additional permissions that are needed for Password Writeback therefore do not trickle to such users. This can result in Password Writeback not working for such users.As a result, we do not support managing passwords for users within these groups because it breaks the AD security model.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Reset operations fails with Object could not be found</p>
            </td>
            <td>
              <p>Federated or password hash sync’d users who attempt to reset their passwords see an error after submitting the password indicating there was a service problem.</p>
              <p>
                
              </p>
              <p>In addition to this, during password reset operations, you may see an error in your event logs from the Azure AD Connect service indicating an “Object could not be found” error.</p>
            </td>
            <td>
              <p>This error usually indicates that the sync engine is unable to find either the user object in the AAD connector space or the linked MV or AD connector space object. </p>
              <p>
                
              </p>
              <p>To troubleshoot this, make sure that the user is indeed synced from on-prem to AAD via the current instance of Azure AD Connect and inspect the state of the objects in the connector spaces and MV. Confirm that the AD CS object is connector to the MV object via the “Microsoft.InfromADUserAccountEnabled.xxx” rule.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Reset operations fails with Multiple matches found eror</p>
            </td>
            <td>
              <p>Federated or password hash sync’d users who attempt to reset their passwords see an error after submitting the password indicating there was a service problem.</p>
              <p>
                
              </p>
              <p>In addition to this, during password reset operations, you may see an error in your event logs from the Azure AD Connect service indicating a “Multiple maches found” error.</p>
            </td>
            <td>
              <p>This indicates that the sync engine detected that the MV object is connected to more than one AD CS objects via the “Microsoft.InfromADUserAccountEnabled.xxx”. This means that the user has an enabled account in more than one forest. </p>
              <p>
                
              </p>
              <p>Currently this scenario is not supported for Password Writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Password operations fail with a configuration error.</p>
            </td>
            <td>
              <p>Password operations fail with a configuration error. The application event log contains Azure AD Connect error 6329 with text: 0x8023061f (The operation failed because password synchronization is not enabled on this Management Agent.)</p>
            </td>
            <td>
              <p>This occurs if the Azure AD Connect configuration is changed to add&nbsp;a new AD forest (or to remove and re-add an existing forest) <strong>after</strong> the Password Writeback feature has already been enabled. Password operations for users in such newly added forests will fail. To fix the problem, disable and re-enable the Password Writeback feature after the forest configuration changes have been completed.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Writing back passwords that have been reset by users works properly, but writing back passwords changed by a user or reset for a user by an administrator fails.</p>
            </td>
            <td>
              <p>When attempting to reset a password on behalf of a user from the Azure Management Portal, you see a message stating: “The password reset service running in your on-premises environment does not support administrators resetting user passwords. Please upgrade to the latest version of Azure AD Connect to resolve this.”</p>
            </td>
            <td>
              <p>This occurs when the version of the synchronization engine does not support the particular Password Writeback operation that was used. Versions of Azure AD Connect later than 1.0.0419.0911 support all password management operations, including password reset writeback, password change writeback, and administrator-initiated password reset writeback from the Azure Management Portal.&nbsp; DirSync versions later than 1.0.6862 support password reset writeback only. To resolve this issue, we highly recommend that you install the latest version of Azure AD Connect or Azure Active Directory Connect (for more information, see <a href="../active-directory-aadconnect#download-azure-ad-connect">Directory Integration Tools</a>) to resolve this issue and to get the most out of Password Writeback in your organization.</p>
            </td>
          </tr>
        </tbody></table>


## Ereignisprotokoll-Fehlercodes für die Kennwortrückschreibung
Eine bewährte Methode bei der Problembehandlung für die Kennwortrückschreibung besteht darin, das Anwendungsereignisprotokoll auf Ihrem Azure AD Connect-Computer zu überprüfen. Dieses Ereignisprotokoll enthält Ereignisse aus zwei Quellen, die für die Kennwortrückschreibung von Interesse sind. Die Quelle "PasswordResetService" beschreibt Vorgänge und Probleme im Zusammenhang mit der Kennwortrückschreibung. Die Quelle "ADSync" beschreibt Vorgänge und Probleme im Zusammenhang mit dem Zurücksetzen von Kennwörtern in Ihrer AD-Umgebung.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Code</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Name / Meldung</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Datenquelle</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Beschreibung</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>BAIL: MMS(4924) 0x80230619 – "Eine Einschränkung verhindert, dass das Kennwort in das aktuell angegebene geändert wird."</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Dieses Ereignis tritt auf, wenn der Dienst für die Kennwortrückschreibung versucht, ein Kennwort für Ihr lokales Verzeichnis festzulegen, das die in der Domäne geltenden Anforderungen im Hinblick auf Alter, Verlauf, Komplexität oder Filterung für Kennwörter nicht erfüllt.</p>
              <ul>
                <li class="unordered">
                                        If you have a minimum password age, and have recently changed the password within that window of time, you will not be able to change the password again until it reaches the specified age in your domain. For testing purposes, minimum age should be set to 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password history requirements enabled, then you must select a password that has not been used in the last N times, where N is the password history setting. If you do select a password that has been used in the last N times, then you will see a failure in this case. For testing purposes, history should be set to 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password complexity requirements, all of them will be enforced when the user attempts to change or reset a password.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password filters enabled, and a user selects a password which does not meet the filtering criteria, then the reset or change operation will fail.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>HR 8023042</p>
            </td>
            <td>
              <p>Synchronisierungsmodul gibt folgenden Fehler wieder: hr = 80230402, Meldung = Versuch zum Abrufen eines Objekts ist fehlgeschlagen, weil doppelte Einträge mit dem gleichen Anker vorhanden sind</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Dieses Ereignis tritt auf, wenn die gleiche Benutzer-ID in mehreren Domänen aktiviert ist.  Beispielsweise tritt dieser Fehler auf, wenn Sie Konto-/Ressourcengesamtstrukturen synchronisieren, und währenddessen die gleiche Benutzer-ID verwendet wird und aktiviert ist.  </p>
              <p>Dieser Fehler tritt auf, wenn Sie ein nicht eindeutiges Anker-Attribut (wie z. B ein Alias oder einen UPN) verwenden und zwei Benutzer das gleiche Anker-Attribut teilen.</p>
              <p>Um dieses Problem zu beheben, stellen Sie sicher, dass kein Benutzer in Ihren Domänen doppelt vorhanden ist und Sie ein eindeutiges Ankerattribut für jeden Benutzer verwenden.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der lokale Dienst eine Anforderung zur Kennwortzurücksetzung für einen Verbundbenutzer oder einen Benutzer mit Kennworthashsynchronisierung ermittelt hat, die aus der Cloud stammt. Dieses Ereignis ist das erste Ereignis bei jedem Rückschreibevorgang für zurückgesetzte Kennwörter.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass ein Benutzer während einer Kennwortzurücksetzung ein neues Kennwort angefordert hat. Es wurde ermittelt, dass dieses Kennwort die Unternehmensanforderungen für Kennwörter erfüllt, und das Kennwort wurde erfolgreich in die lokale AD-Umgebung zurückgeschrieben.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass ein Benutzer ein Kennwort ausgewählt hat, und dass dieses Kennwort erfolgreich in der lokalen Umgebung empfangen wurde. Bei dem Versuch, das Kennwort in der lokalen AD-Umgebung festzulegen, ist jedoch ein Fehler aufgetreten. Dies kann aus verschiedenen Gründen passieren:</p>
              <ul>
                <li class="unordered">
                                        The user’s password does not meet the age, history, complexity, or filter requirements for the domain. Try a completely new password to resolve this.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The MA service account does not have the appropriate permissions to set the new password on the user account in question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The user’s account is in a protected group, such as domain or enterprise admins, which disallows password set operations.<br\><br\></li>
              </ul>
              <p>Finden Sie unter <a href="#troubleshoot-password-writeback">Problembehandlung: Kennwortrückschreibung</a> Informationen zu weiteren Situationen diesen Fehler verursachen können.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis tritt auf, wenn Sie die Kennwortrückschreibung mit Azure AD Connect aktivieren. Es zeigt, dass die Integration Ihrer Organisation in den Webdienst für die Kennwortrückschreibung gestartet wurde.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Integrationsvorgang erfolgreich war und die Funktion zur Kennwortrückschreibung einsatzbereit ist.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der lokale Dienst eine Anforderung zur Kennwortänderung für einen Verbundbenutzer oder einen Benutzer mit Kennworthashsynchronisierung ermittelt hat, die aus der Cloud stammt. Dieses Ereignis ist das erste Ereignis bei jedem Rückschreibevorgang für geänderte Kennwörter.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass ein Benutzer während einer Kennwortänderung ein neues Kennwort angefordert hat. Es wurde ermittelt, dass dieses Kennwort die Unternehmensanforderungen für Kennwörter erfüllt, und das Kennwort wurde erfolgreich in die lokale AD-Umgebung zurückgeschrieben.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass ein Benutzer ein Kennwort ausgewählt hat, und dass dieses Kennwort erfolgreich in der lokalen Umgebung empfangen wurde. Bei dem Versuch, das Kennwort in der lokalen AD-Umgebung festzulegen, ist jedoch ein Fehler aufgetreten. Dies kann aus verschiedenen Gründen passieren:</p>
              <ul>
                <li class="unordered">
                                        The user’s password does not meet the age, history, complexity, or filter requirements for the domain. Try a completely new password to resolve this.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The MA service account does not have the appropriate permissions to set the new password on the user account in question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The user’s account is in a protected group, such as domain or enterprise admins, which disallows password set operations.<br\><br\></li>
              </ul>
              <p>Finden Sie unter <a href="#troubleshoot-password-writeback">Problembehandlung: Kennwortrückschreibung</a> erfahren Sie, welche anderen Situationen diesen Fehler verursachen können.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Der lokale Dienst hat eine Anforderung zur Kennwortzurücksetzung für einen Verbundbenutzer oder einen Benutzer mit Kennworthashsynchronisierung ermittelt, die im Namen eines Benutzers vom Administrator initiiert wurde. Dieses Ereignis ist das erste Ereignis bei jedem Rückschreibevorgang für zurückgesetzte Kennwörter, der von einem Administrator initiiert wird.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Der Administrator hat während einer vom Administrator initiierten Kennwortzurücksetzung ein neues Kennwort angefordert. Es wurde ermittelt, dass dieses Kennwort die Unternehmensanforderungen für Kennwörter erfüllt, und das Kennwort wurde erfolgreich in die lokale AD-Umgebung zurückgeschrieben.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Der Administrator hat im Namen eines Benutzers ein Kennwort ausgewählt, und dieses Kennwort wurde erfolgreich in der lokalen Umgebung empfangen. Bei dem Versuch, das Kennwort in der lokalen AD-Umgebung festzulegen, ist jedoch ein Fehler aufgetreten. Dies kann aus verschiedenen Gründen passieren:</p>
              <ul>
                <li class="unordered">
                                        The user’s password does not meet the age, history, complexity, or filter requirements for the domain. Try a completely new password to resolve this.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The MA service account does not have the appropriate permissions to set the new password on the user account in question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        The user’s account is in a protected group, such as domain or enterprise admins, which disallows password set operations.<br\><br\></li>
              </ul>
              <p>Finden Sie unter <a href="#troubleshoot-password-writeback">Problembehandlung: Kennwortrückschreibung</a> Informationen zu weiteren Situationen diesen Fehler verursachen können.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis tritt auf, wenn Sie die Kennwortrückschreibung mit Azure AD Connect deaktivieren. Es zeigt, dass der Vorgang zum Entfernen Ihrer Organisation aus dem Webdienst für die Kennwortrückschreibung gestartet wurde.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Vorgang zum Entfernen erfolgreich war und die Funktion zur Kennwortrückschreibung erfolgreich deaktiviert wurde.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Vorgang zum Entfernen nicht erfolgreich war. Dies kann auf Berechtigungsfehler in Bezug auf das Cloudadministratorkonto oder das lokale Administratorkonto zurückzuführen sein, das bei der Konfiguration angegeben wurde. Oder der Fehler wurde ausgelöst, weil Sie versucht haben, ein Cloudkonto für einen globalen Verbundadministrator anzugeben, als Sie die Kennwortrückschreibung deaktiviert haben. Prüfen Sie zur Beseitigung des Fehlers Ihre Administratorberechtigungen, und stellen Sie sicher, dass Sie kein Verbundkonto zum Konfigurieren der Kennwortrückschreibung verwenden.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Dienst für die Kennwortrückschreibung erfolgreich gestartet wurde und bereit ist, Anforderungen zur Kennwortverwaltung aus der Cloud zu empfangen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Dienst für die Kennwortrückschreibung beendet wurde, und dass Anforderungen zur Kennwortverwaltung aus der Cloud nicht verarbeitet werden können.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass für den globalen Administrator, der während der Azure AD Connect-Einrichtung angegeben wurde, erfolgreich ein Autorisierungstoken abgerufen wurde, um den Vorgang zur Integration oder zum Entfernen zu starten.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Schlüssel zur Kennwortverschlüsselung, der zum Verschlüsseln von Kennwörtern aus der Cloud verwendet wird, die an Ihre lokale Umgebung gesendet werden, erfolgreich erstellt wurde.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist auf einen unbekannten Fehler während eines Vorgangs zur Kennwortverwaltung hin. Lesen Sie den Ausnahmetext im Ereignis, um weitere Informationen zu erhalten. Wenn Probleme vorliegen, deaktivieren Sie die Kennwortrückschreibung, und aktivieren Sie sie anschließend erneut. Wenn das Problem so nicht gelöst werden kann, senden Sie eine Kopie des Ereignisprotokolls zusammen mit der angegebenen Nachverfolgungs-ID an Ihren Supportmitarbeiter.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass bei der Verbindungsherstellung mit dem Clouddienst für die Kennwortzurücksetzung ein Fehler aufgetreten ist. Dies geschieht im Allgemeinen, wenn der lokale Dienst keine Verbindung mit dem Webdienst für die Kennwortzurücksetzung herstellen kann. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass bei der Verbindungsherstellung mit der Service Bus-Instanz Ihres Mandanten ein Fehler aufgetreten ist. Dies kann passieren, wenn Sie in Ihrer lokalen Umgebung ausgehende Verbindungen blockieren. Überprüfen Sie die Firewall, um sicherzustellen, dass Verbindungen über TCP 443 und mit <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, und versuchen Sie es erneut. Wenn weiterhin Probleme auftreten, deaktivieren Sie die Kennwortrückschreibung, und aktivieren Sie sie anschließend erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass die an die Webdienst-API übergebene Eingabe ungültig war. Wiederholen Sie den Vorgang.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass beim Entschlüsseln des aus der Cloud empfangenen Kennworts ein Fehler aufgetreten ist. Dieser Fehler kann verursacht werden, wenn der Entschlüsselungsschlüssel des Clouddiensts und der lokalen Umgebung nicht übereinstimmen. Um das Problem zu lösen, versuchen Sie die Kennwortrückschreibung in der lokalen Umgebung zu deaktivieren und erneut zu aktivieren.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Während der Integration wurden mandantenspezifische Informationen in einer Konfigurationsdatei in Ihrer lokalen Umgebung gespeichert. Dieses Ereignis zeigt an, dass beim Speichern dieser Datei ein Fehler aufgetreten ist, oder dass beim Starten des Diensts ein Fehler beim Lesen der Datei aufgetreten ist. Um das Problem zu lösen, versuchen Sie die Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren, um eine Neuerstellung dieser Konfigurationsdatei zu erzwingen. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>VERALTET – Dieses Ereignis ist in Azure AD Connect nicht vorhanden. Es wird ausschließlich in sehr frühen Builds von DirSync verwendet, die das Zurückschreiben unterstützen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Während der Integration werden Daten vom Clouddienst für die Kennwortzurücksetzung an den lokalen Dienst für die Kennwortzurücksetzung gesendet. Diese Daten werden dann in eine Datei im Arbeitsspeicher geschrieben, bevor sie an den Synchronisierungsdienst gesendet und sicher auf der Festplatte gespeichert werden. Dieses Ereignis weist auf ein Problem hin, das beim Schreiben oder Aktualisieren dieser Daten im Arbeitsspeicher aufgetreten ist. Um das Problem zu lösen, versuchen Sie die Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren, um eine Neuerstellung dieser Konfigurationsdaten zu erzwingen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass eine ungültige Antwort vom Webdienst für die Kennwortzurücksetzung empfangen wurde. Um dieses Problem zu lösen, versuchen Sie die Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass kein Autorisierungstoken für das globale Administratorkonto abgerufen werden konnte, das beim Azure AD Connect-Setup angegeben wurde. Dieser Fehler kann durch einen ungültigen Benutzernamen oder ein ungültiges Kennwort verursacht werden, der bzw. das für das globale Administratorkonto angegeben wurde. Eine weitere Fehlerursache kann sein, dass es sich um ein Konto für einen globalen Verbundadministrator handelt. Führen Sie zur Beseitigung dieses Fehlers die Konfiguration mit dem richtigen Benutzernamen und Kennwort erneut aus, und stellen Sie sicher, dass es sich bei dem Administratorkonto um ein verwaltetes Konto (nur Cloud oder Kennwortsynchronisierung) handelt.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass beim Generieren des Kennwortverschlüsselungsschlüssels oder beim Entschlüsseln eines vom Clouddienst empfangenen Kennworts ein Fehler aufgetreten ist. Bei Auftreten dieses Fehlers liegt häufig ein Problem mit der lokalen Umgebung vor. Prüfen Sie die Details im Ereignisprotokoll, um weitere Informationen zum aufgetretenen Problem und zu dessen Lösung zu erhalten. Sie können zur Lösung des Problems auch versuchen, die Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der lokale Dienst nicht ordnungsgemäß mit dem Webdienst für die Kennwortzurücksetzung kommunizieren konnte, um den Integrationsvorgang zu starten. Die Fehlerursache kann eine Firewallregel oder ein Problem beim Abrufen eines Authentifizierungstokens für Ihren Mandanten sein. Um dieses Problem zu beheben, stellen Sie sicher, dass Sie keine ausgehende Verbindungen über TCP 443 und TCP 9350-9354 oder mit blockieren <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, und dass das AAD-Administratorkonto verwenden integrierte ist nicht verbunden. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>VERALTET – Dieses Ereignis ist in Azure AD Connect nicht vorhanden. Es wird ausschließlich in sehr frühen Builds von DirSync verwendet, die das Zurückschreiben unterstützen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der lokale Dienst nicht ordnungsgemäß mit dem Webdienst für die Kennwortzurücksetzung kommunizieren konnte, um den Vorgang zum Entfernen zu starten. Die Fehlerursache kann eine Firewallregel oder ein Problem beim Abrufen eines Autorisierungstokens für Ihren Mandanten sein. Um dieses Problem zu beheben, stellen Sie sicher, dass Sie keine ausgehende Verbindungen über 443 oder zu blockieren <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, und dass das AAD-Administratorkonto, das Sie, um ein verbundkonto verwenden ist nicht. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass erneut versucht werden musste, eine Verbindung mit der Service Bus-Instanz Ihres Mandanten herzustellen. Unter normalen Umständen ist dies kein Problem, aber wenn dieses Ereignis häufiger angezeigt wird, prüfen Sie ggf. Ihre Netzwerkverbindung mit dem Service Bus. Dies gilt insbesondere, wenn Sie eine Verbindung mit hoher Latenz oder niedriger Bandbreite verwenden.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Um die Integrität des Diensts für die Kennwortrückschreibung zu überwachen, werden alle 5 Minuten Taktdaten an den Webdienst für die Kennwortzurücksetzung gesendet. Dieses Ereignis weist darauf hin, dass beim Senden dieser Integritätsinformationen zurück an den Cloudwebdienst ein Fehler aufgetreten ist. Diese Integritätsinformationen enthalten keine OII- oder PII-Daten. Es handelt sich lediglich um ein Taktsignal und grundlegende Dienststatistiken, um Dienststatusinformationen in der Cloud bereitzustellen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass ein unbekannter Fehler von AD zurückgegeben wurde. Prüfen Sie das Ereignisprotokoll auf dem Azure AD Connect-Server auf Ereignisse der Quelle "ADSync", um weitere Informationen zu diesem Fehler zu erhalten.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Benutzer, der ein Kennwort zurücksetzen oder ändern möchte, nicht im lokalen Verzeichnis gefunden wurde. Dieser Fehler kann auftreten, wenn der Benutzer lokal, aber nicht in der Cloud gelöscht wurde, oder wenn ein Problem mit der Synchronisierung vorliegt. Prüfen Sie Ihre Synchronisierungsprotokolle sowie die Details zu den letzten Synchronisierungsvorgängen, um weitere Informationen zu erhalten.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Wenn eine Anforderung zur Kennwortzurücksetzung oder -änderung aus der Cloud stammt, wird mithilfe des bei der Einrichtung von Azure AD Connect angegeben Cloudankers ermittelt, wie diese Anforderung mit einem Benutzer in Ihrer lokalen Umgebung verknüpft wird. Dieses Ereignis weist darauf hin, dass zwei Benutzer mit demselben Cloudankerattribut im lokalen Verzeichnis gefunden wurden. Prüfen Sie Ihre Synchronisierungsprotokolle sowie die Details zu den letzten Synchronisierungsvorgängen, um weitere Informationen zu erhalten.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass das Dienstkonto für den Verwaltungs-Agent über keine ausreichenden Berechtigungen verfügt, um für das betreffende Konto neues Kennwort festzulegen. Stellen Sie sicher, dass das MA-Konto in der Gesamtstruktur des Benutzers über die Berechtigungen zum Zurücksetzen und Ändern von Kennwörtern für alle Objekte in der Gesamtstruktur verfügt.  Führen Sie für Weitere Informationen zu diesem, finden Sie unter <a href="../active-directory-passwords-getting-started#step-4-set-up-the-appropriate-active-directory-permissions">Schritt 4: Einrichten der entsprechenden Active Directory-Berechtigungen</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass versucht wurde, ein Kennwort für ein Konto zurückzusetzen oder zu ändern, das lokal deaktiviert ist. Aktivieren Sie das Konto, und versuchen Sie es erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass versucht wurde, ein Kennwort für ein Konto zurückzusetzen oder zu ändern, das lokal gesperrt ist. Sperrungen können auftreten, wenn ein Benutzer innerhalb eines kurzen Zeitraums zu häufig versucht hat, ein Kennwort zu ändern oder zurückzusetzen. Entsperren Sie das Konto, und versuchen Sie es erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass der Benutzer beim Ausführen einer Kennwortänderung ein falsches aktuelles Kennwort angegeben hat. Geben Sie das richtige aktuelle Kennwort an, und versuchen Sie es erneut.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis tritt auf, wenn der Dienst für die Kennwortrückschreibung versucht, ein Kennwort für Ihr lokales Verzeichnis festzulegen, das die in der Domäne geltenden Anforderungen im Hinblick auf Alter, Verlauf, Komplexität oder Filterung für Kennwörter nicht erfüllt.</p>
              <ul>
                <li class="unordered">
                                        If you have a minimum password age, and have recently changed the password within that window of time, you will not be able to change the password again until it reaches the specified age in your domain. For testing purposes, minimum age should be set to 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password history requirements enabled, then you must select a password that has not been used in the last N times, where N is the password history setting. If you do select a password that has been used in the last N times, then you will see a failure in this case. For testing purposes, history should be set to 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password complexity requirements, all of them will be enforced when the user attempts to change or reset a password.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
                                        If you have password filters enabled, and a user selects a password which does not meet the filtering criteria, then the reset or change operation will fail.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Dieses Ereignis weist darauf hin, dass beim Zurückschreiben eines Kennworts in das lokale Verzeichnis aufgrund eines Konfigurationsproblems in Active Directory ein Fehler aufgetreten ist. Überprüfen Sie das Anwendungsereignisprotokoll für den Azure AD Connect-Computer auf Meldungen vom ADSync-Dienst, um weitere Informationen zur Fehlerursache zu erhalten. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>VERALTET – Dieses Ereignis ist in Azure AD Connect nicht vorhanden. Es wird ausschließlich in sehr frühen Builds von DirSync verwendet, die das Zurückschreiben unterstützen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>VERALTET – Dieses Ereignis ist in Azure AD Connect nicht vorhanden. Es wird ausschließlich in sehr frühen Builds von DirSync verwendet, die das Zurückschreiben unterstützen.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>VERALTET – Dieses Ereignis ist in Azure AD Connect nicht vorhanden. Es wird ausschließlich in sehr frühen Builds von DirSync verwendet, die das Zurückschreiben unterstützen.</p>
            </td>
          </tr>
        </tbody></table>
        
## Problembehandlung: Konnektivität bei der Kennwortrückschreibung

Wenn es bei Verwendung der Azure AD Connect-Komponente für die Kennwortrückschreibung zu Dienstunterbrechungen kommt, können Sie die folgenden Schritte für eine schnelle Problemlösung einsetzen:

 - [Neustarten des Azure AD Connect-Synchronisierungsdiensts](#restart-the-azure-AD-sync-service)
 - [Deaktivieren und erneutes Aktivieren der Funktion für die Kennwortrückschreibung](#disable-and-re-enable-the-password-writeback-feature)
 - [Installieren der aktuellen Azure AD Connect-Version](#install-the-latest-azure-ad-connect-release)
 - [Problembehandlung: Kennwortrückschreibung](#troubleshoot-password-writeback)

Im Allgemeinen wird empfohlen, diese Schritte in der oben genannten Reihenfolge auszuführen, um Ihren Dienst schnellstmöglich wiederherzustellen.

### Neustarten des Azure AD Connect-Synchronisierungsdiensts
Durch einen Neustart des Azure AD Connect-Synchronisierungsdiensts können Konnektivitätsprobleme oder andere vorübergehende Probleme mit dem Dienst beseitigt werden.

 1. Als Administrator, klicken Sie auf **Start** auf dem Server mit **Azure AD Connect**.
 2. Typ **"services.msc"** in das Suchfeld ein, und drücken Sie **EINGABETASTE**.
 3. Suchen Sie nach der **Microsoft Azure AD Connect** Eintrag.
 4. Mit der rechten Maustaste auf den Diensteintrag, klicken Sie auf **Neustart**, und warten Sie, bis der Vorgang abgeschlossen.

    ![][002]

Durch Ausführen dieser Schritte wird die Verbindung mit dem Clouddienst erneut hergestellt, und mögliche Unterbrechungen werden behoben.  Wenn das Problem durch einen Neustart des Synchronisierungsdiensts nicht gelöst werden kann, empfehlen wir, im nächsten Schritt die Funktion zur Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren.

### Deaktivieren und erneutes Aktivieren der Funktion für die Kennwortrückschreibung
Durch das Deaktivieren und anschließende erneute Aktivieren der Funktion für die Kennwortrückschreibung können Konnektivitätsprobleme behoben werden.

 1. Öffnen Sie als Administrator, der **Azure AD Connect-Konfigurations-Assistenten**.
 2. Auf der **mit Azure AD verbinden** Dialogfeld Geben Sie Ihre **Azure AD globaler Administrator-Anmeldeinformationen**
 3. Auf der **mit AD DS verbinden** Dialogfeld Geben Sie Ihre **Active Directory-Domänendienste-Admin-Anmeldeinformationen**.
 4. Auf der **eindeutige Identifizierung der Benutzer** Dialogfeld klicken Sie auf die **Weiter** Schaltfläche.
 5. Auf der **optionale Features** Dialogfeld, deaktivieren Sie die **Kennwort Zurückschreiben** Kontrollkästchen.

    ![][003]

 6. Klicken Sie auf **Weiter** den verbleibenden Assistentenseiten ohne Änderungen vorzunehmen, bis Sie zum Abrufen der **konfiguriert** Seite.
 7. Stellen Sie sicher, dass der Konfigurationsseite der **Kennwort Zurückschreiben Option als deaktiviert** und klicken Sie dann auf die grüne **konfigurieren** Schaltfläche, um Ihre Änderungen zu übernehmen.
 8. Auf der **beendet** Dialogfeld, deaktivieren Sie die **Jetzt synchronisieren** aus, und klicken Sie dann auf **Fertig stellen** um den Assistenten zu schließen.
 9. Öffnen Sie erneut die **Azure AD Connect-Konfigurations-Assistenten**.
 10.    **Wiederholen Sie die Schritte 2 bis 8**, außer dass Sie sicherstellen **Überprüfen Sie die Option Kennwort Zurückschreiben** auf die **optionale Features** Bildschirm, um den Dienst erneut zu aktivieren.

    ![][004]

Durch Ausführen dieser Schritte wird die Verbindung mit dem Clouddienst erneut hergestellt, und mögliche Unterbrechungen werden behoben. 

Wenn das Problem durch das Deaktivieren und erneute Aktivieren der Funktion für die Kennwortrückschreibung nicht gelöst werden kann, empfehlen wir, im nächsten Schritt die Funktion zur Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren.

### Installieren der aktuellen Azure AD Connect-Version
Durch das erneute Installieren des Azure AD Connect-Pakets werden Konfigurationsprobleme behoben, die sich möglicherweise auf die Fähigkeit zur Verbindungsherstellung mit den Clouddiensten oder auf die Verwaltung von Kennwörtern in Ihrer lokalen AD-Umgebung auswirken. 
Es wird empfohlen, diese Maßnahme erst zu ergreifen, wenn Sie ersten zwei Schritte bereits ausgeführt haben.

 1. Herunterladen der neuesten Version von Azure AD Connect [hier](active-directory-aadconnect.md#download-azure-ad-connect).
 2. Da Sie Azure AD Connect bereits installiert haben, müssen Sie lediglich ein direktes Upgrade Ihrer Azure AD Connect-Installation auf die aktuelle Version durchführen.
 3. Führen Sie das heruntergeladene Paket aus, und folgen Sie den Bildschirmanweisungen zum Aktualisieren Ihres Azure AD Connect-Computers.  Keine weiteren manuellen Schritte sind erforderlich, es sei denn, Sie die Synchronisierungsregeln angepasst haben, in diesem Fall sollten Sie **Sichern Sie diese, bevor Sie mit der Aktualisierung fortfahren und manuell erneut bereitgestellt werden, nachdem Sie fertig sind**.

Durch Ausführen dieser Schritte wird die Verbindung mit dem Clouddienst erneut hergestellt, und mögliche Unterbrechungen werden behoben. 

Wenn das Installieren der aktuellen Version von Azure AD Connect nicht zur Lösung des Problems führt, empfehlen wir, als letzten Schritt nach dem Installieren des aktuellen QFE für die Synchronisierung die Kennwortrückschreibung zu deaktivieren und erneut zu aktivieren. 

Wenn, die Ihr Problem nicht aufgelöst, und es wird empfohlen, einen Blick auf [Problembehandlung: Kennwortrückschreibung](#troubleshoot-password-writeback) und [Azure AD-Kennworts Management – häufig gestellte Fragen](active-directory-passwords-faq.md) um festzustellen, ob Ihr Problem möglicherweise dort beschrieben wird.


<br/>
<br/>
<br/>

## Links zur Dokumentation für die Kennwortzurücksetzung
Im Folgenden finden Sie Links zu allen Webseiten mit Informationen zur Kennwortzurücksetzung für Azure AD: 

* [**Ihr eigenes Kennwort zurücksetzen**](active-directory-passwords-update-your-own-password.md) -erfahren Sie mehr über zurücksetzen oder ändern Ihr eigenes Kennwort als Benutzer des Systems
* [**Funktionsweise**](active-directory-passwords-how-it-works.md) -erfahren Sie mehr über die sechs verschiedenen Komponenten der Dienst und die jeweilige
* [**Erste Schritte**](active-directory-passwords-getting-started.md) -erfahren Sie, wie Sie Benutzern das Zurücksetzen und Ändern ihrer Cloud- oder lokalen Kennwörter erlauben
* [**Anpassen von**](active-directory-passwords-customize.md) -erfahren Sie, wie das Aussehen und Verhalten des Diensts, um den Bedürfnissen Ihres Unternehmens anpassen.
* [**Bewährte Methoden**](active-directory-passwords-best-practices.md) -erfahren Sie, wie schnell bereitstellen und effektiv verwalten von Kennwörtern in Ihrer Organisation
* [**Einblicke**](active-directory-passwords-get-insights.md) -erfahren Sie mehr über unsere integrierten Berichtsfunktionen
* [**Häufig gestellte Fragen zu**](active-directory-passwords-faq.md) – Hier erhalten Sie Antworten auf häufig gestellte Fragen
* [**Erfahren Sie mehr**](active-directory-passwords-learn-more.md) – erhalten Sie tiefgehende technische Details zur Funktionsweise des Diensts



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"



