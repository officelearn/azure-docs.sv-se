<properties 
    pageTitle="Aktivieren von Microsoft Passport for Work in Unternehmen | Microsoft Azure" 
    description="Bereitstellungsanweisungen zum Aktivieren von Microsoft Passport in Ihrer Organisation." 
    services="active-directory" 
    documentationCenter="" 
    authors="femila" 
    manager="stevenpo" 
    editor=""
    tags="azure-classic-portal"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="femila"/>

# Microsoft Passport for Work in Unternehmen aktivieren

Nach der Verknüpfung von in die Domäne eingebundenen Windows 10-Geräten mit Azure AD gehen Sie wie folgt vor, um Microsoft Passport for Work im Unternehmen zu aktivieren.

## Bereitstellen von System Center Configuration Manager, Version 1509 für Technical Preview
Für das Bereitstellen von Benutzerzertifikaten anhand von Microsoft Passport-Schüsseln sind folgende Elemente erforderlich:

- **System Center Configuration Manager-Version 1509 für technische Vorschau**. Weitere Informationen finden Sie unter [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update). und [System Center Configuration Manager-Teamblog](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **PKI-Infrastruktur**: Aktivieren von Microsoft Passport for Work mithilfe von Benutzerzertifikaten muss eine PKI-Infrastruktur vorhanden sein. Falls diese nicht vorhanden ist oder nicht für Benutzerzertifikate verwendet werden soll, kann ein Domänencontroller (DC) der neuen Version von Windows Server verwendet werden:
 - **Bereitstellen ein Domänencontrollers in der neuen Version von Windows Server**: auf einem völlig neuen Windows Server-Build 10551 oder höher (das ISO-Dateien stehen zum Download auf [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)) führen Sie die Schritte zum [Replikatdomänencontroller in einer vorhandenen Domäne installieren](https://technet.microsoft.com/en-us/library/jj574134.aspx) oder [eine neue Active Directory-Gesamtstruktur installieren, wenn eine neue Umgebung erstellen](https://technet.microsoft.com/en-us/library/jj574134.aspx).

## Konfigurieren von Microsoft Passport for Work über die Gruppenrichtlinie in Active Directory

 Mithilfe einer Active Directory-Gruppenrichtlinie lassen sich in die Domäne eingebundene Windows 10-Geräte so konfigurieren, dass bei der Windows-Anmeldung eines Benutzers Microsoft Passport-Anmeldeinformationen bereitgestellt werden.

1.  Öffnen Sie Server-Manager, und navigieren Sie zu **Tools** > **Group Policy Management**.
2.  Navigieren Sie von der Gruppenrichtlinienverwaltung zu dem Domänenknoten, der der Domäne entspricht, in der Sie Azure AD Join aktivieren möchten.
3.  Mit der rechten Maustaste **Group Policy Objects** und wählen Sie **Neu**. Geben Sie Ihrem Gruppenrichtlinienobjekt einen Namen, z. B. **aktivieren Microsoft Passport**. Klicken Sie auf **OK**.
4.  Mit der rechten Maustaste auf Ihr neues Gruppenrichtlinienobjekt, und wählen Sie dann **Bearbeiten**.
5.  Navigieren Sie zu **Computerkonfiguration** > **Richtlinien** > **Administrative Vorlagen** > **Windows-Komponenten** > **Passport for Work**.
6.  Mit der rechten Maustaste **Aktivieren Passport for Work** und wählen Sie dann **Bearbeiten**.
7.  Wählen Sie die **aktiviert** Optionsfeld, und klicken Sie dann auf **Übernehmen**. Klicken Sie auf **OK**.
8.  Sie können das Gruppenrichtlinienobjekt jetzt mit einem Speicherort Ihrer Wahl verknüpfen. Um diese Richtlinie für alle in die Domäne eingebundenen Windows 10-Geräte in Ihrer Organisation zu aktivieren, verknüpfen Sie die Gruppenrichtlinie mit der Domäne. Beispiel:
 - Eine bestimmte Organisationseinheit in AD, in der in die Domäne eingebundene Windows 10-Computer platziert werden
 - Eine bestimmte Sicherheitsgruppe mit in die Domäne eingebundenen Windows 10-Computern, die automatisch bei Azure AD registriert werden sollen

## Konfigurieren von Microsoft Passport for Work durch das Bereitstellen von PowerShell über Configuration Manager 

Führen Sie den folgenden PowerShell-Befehl aus:

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## Konfigurieren des Zertifikatprofils zum Registrieren des Registrierungszertifikats „Passport for Work“ im Konfigurationsmanager
Passport für Arbeit Anmeldung/Microsoft Hello zertifikatbasierte konfigurieren Certificate Profile (Ressourcen und Compliance > Compliance-Einstellungen -> Zugriff auf Unternehmensressourcen -> Zertifikatprofile) EKU Auswählen einer Vorlage, die Smartcard anmelden.

##Richten Sie eine geplante Aufgabe ein, die ausgelöst wird, wenn der Passport for Work-Container aktiviert wird und die Zertifikatauswertung anfordert.
Hierbei handelt es sich um eine kurzfristige Behelfslösung. Der Administrator muss dazu eine geplante Aufgabe erstellen, die auf das Erstellen eines Passport for Work-Containers lauscht und eine Zertifikatauswertung anfordert. Dies reduziert die Verzögerung bei der Einrichtung des Containers und der PIN, zusammen mit der Möglichkeit, bei der nächsten Anmeldung verwendet werden zu können.

**Um die geplante Aufgabe zu erstellen, verwenden Sie den folgenden Befehl oder mithilfe der Benutzeroberfläche**
    Schtasks / create/XML % 0\... \ < EnrollCertificate.xml >/TN <Task Name>

Der XML-Beispielcode lautet wie folgt: 

    <?xml version="1.0" encoding="UTF-16"?>
    <Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
       <RegistrationInfo>
         <Date>2015-09-04T17:48:45.9973761</Date>
          <Author><DomainName/UserName></Author>
        <URI>\Enroll Certificates</URI>
      </RegistrationInfo>
      <Triggers>
        <EventTrigger>
          <Enabled>true</Enabled>
          <Subscription>&lt;QueryList&gt;&lt;Query Id="0" Path="Microsoft-Windows-User Device Registration/Admin"&gt;&lt;Select Path="Microsoft-Windows-User Device Registration/Admin"&gt;*[System[Provider[@Name='Microsoft-Windows-User Device Registration'] and EventID=300]]&lt;/Select&gt;&lt;/Query&gt;&lt;/QueryList&gt;</Subscription>
        </EventTrigger>
      </Triggers>
      <Principals>
      </Principals>
      <Settings>
        <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
        <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
        <StopIfGoingOnBatteries>true</StopIfGoingOnBatteries>
        <AllowHardTerminate>true</AllowHardTerminate>
        <StartWhenAvailable>true</StartWhenAvailable>
        <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
        <IdleSettings>
          <StopOnIdleEnd>true</StopOnIdleEnd>
          <RestartOnIdle>false</RestartOnIdle>
        </IdleSettings>
        <AllowStartOnDemand>true</AllowStartOnDemand>
        <Enabled>true</Enabled>
        <Hidden>false</Hidden>
        <RunOnlyIfIdle>false</RunOnlyIfIdle>
        <WakeToRun>false</WakeToRun>
        <ExecutionTimeLimit>PT1H</ExecutionTimeLimit>
        <Priority>7</Priority>
        <RestartOnFailure>
          <Interval>PT1M</Interval>
          <Count>3</Count>
        </RestartOnFailure>
      </Settings>
      <Actions Context="Author">
        <Exec>
          <Command>wmic</Command>
          <Arguments>/namespace:\\root\ccm\dcm path SMS_DesiredConfiguration call TriggerEvaluation 1,0,"ScopeId_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy_db89c51e-1d1b-4a17-8e42-a5459b742ccd",8</Arguments>
        </Exec>
      </Actions>
    </Task>

Wobei die ScopeId_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy_db89c51e-1d1b-4a17-8e42-a5459b742ccd ist die ID für das Zertifikat, das im Schritt "Certificate Profile zum Registrieren von Passport für Arbeit Anmeldungszertifikats in Configuration Manager konfigurieren" erstellt und < 8 > ist die Version.

## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentifizieren von Identitäten ohne Kennwörter über Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Verbinden von in die Domäne eingebundenen Geräten mit Azure AD für Windows 10-Funktionen](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

