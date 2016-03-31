<properties
    pageTitle="Anmeldungen aus mehreren geografischen Regionen"
    description="Ein Bericht, der auf Benutzer hinweist, bei denen zwei Anmeldungen aus verschiedenen Regionen zu stammen scheinen und die Zeit zwischen den Anmeldungen es dem jeweiligen Benutzer unmöglich macht, die Entfernung zwischen diesen Regionen reisend zurückgelegt zu haben."
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

# Anmeldungen aus mehreren geografischen Regionen
<p>Dieser Bericht enthält erfolgreiche Anmeldung, die von einem Benutzer, wenn zwei Anmeldungen, Ursprungsländer stammen aus verschiedenen Regionen und die Zeit zwischen den Anmeldungen zwischen diesen Regionen unternommen haben die Benutzer unmöglich macht. Mögliche Ursachen:</p><ul><li>Der Benutzer hat sein Kennwort für andere Benutzer freigegeben.</li><li>Benutzer verwendet einen Remotedesktop, um für die Anmeldung in einem Webbrowser zu starten.</li><li>Ein Hacker hat sich von einem anderen Land aus in dem Konto eines Benutzers angemeldet.</li><li>Der Benutzer verwendet eine VPN-Verbindung oder einen Proxy.</li><li>Der Benutzer ist über mehrere Geräte gleichzeitig angemeldet, z. B. über einen Desktop und ein Mobiltelefon, und die IP-Adresse des Mobiltelefons ist ungewöhnlich.</li></ul><p>Ergebnisse in diesem Bericht erfahren Sie, der erfolgreichen Anmeldung in den Ereignissen zusammen mit der Zeit zwischen den Anmeldungen, die vermutlichen Ursprungsländer der Anmeldung sowie die geschätzte Reisezeit zwischen diesen Regionen, in denen zu Zeit.</p><p>Die angezeigte Reisezeit ist lediglich eine Schätzung und unterscheidet sich möglicherweise von der tatsächlichen Reisezeit zwischen den Orten.</p>


![Anmeldungen aus mehreren geografischen Regionen](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)


