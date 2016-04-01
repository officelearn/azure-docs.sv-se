<properties
    pageTitle="Anmeldungen nach mehreren Fehlern"
    description="Ein Bericht, der die Benutzer ausweist, die sich nach mehreren aufeinander folgenden misslungenen Anmeldeversuchen erfolgreich angemeldet haben."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="gchander"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2015"
    ms.author="saah;kenhoff"/>

# Anmeldungen nach mehreren Fehlern
Dieser Bericht weist die Benutzer aus, die sich nach mehreren aufeinander folgenden misslungenen Anmeldeversuchen erfolgreich angemeldet haben. Mögliche Ursachen: <ul><li>Benutzer hat sein Kennwort vergessen.</li><li>Benutzer ist das Opfer eines erfolgreichen Kennworts erraten brute-force-Angriffs</li></ul><p>Ergebnisse aus diesem Bericht werden die Anzahl der aufeinander folgenden fehlgeschlagenen Versuche vor der erfolgreichen Anmeldung und einen Zeitstempel mit der ersten erfolgreichen Anmeldung enthalten.</p><p><b>Melden Sie Einstellungen</b>: Sie können die minimale Anzahl der aufeinanderfolgenden fehlerhaften Anmeldeversuche, die auftreten müssen, bevor es im Bericht angezeigt werden, kann konfigurieren. Beim Vornehmen von Änderungen an dieser Einstellung ist zu beachten, dass diese Änderungen nicht für vorhandene misslungene Anmeldungen gelten, die derzeit im vorhandenen Bericht enthalten sind. Sie gelten jedoch für alle künftigen Anmeldungen. Änderungen an diesem Bericht können nur von lizenzierten Administratoren vorgenommen werden.


![Anmeldungen nach mehreren Fehlern](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)


