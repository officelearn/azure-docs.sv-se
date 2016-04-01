<properties
   pageTitle="Tabelle zu Beschränkungen für Azure Backup"
   description="Beschreibung der Systembegrenzung für Azure Backup."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags
   ms.service="backup"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/05/2015"
   ms.author="trinadhk";"jimpark"; "aashishr" />


Für Azure Backup gelten die folgenden Beschränkungen.

| Bezeichnung | Standardlimit |
|---|---|
|Anzahl der Server/Computer, die für jeden Tresor registriert werden können|50 für Windows Server/Client-/SCDPM <br/> 200 für IaaS-VMs|
|Größe einer Datenquelle für im Azure-Tresorspeicher gespeicherten Daten|Max. 54.400 GB<sup>1</sup>|
|Anzahl der Sicherungstresore, die in einem Azure-Abonnement erstellt werden können|25|
|Häufigkeit der geplanten Sicherungen pro Tag|3 pro Tag für Windows Server und-Clients <br/> 2 pro Tag für SCDPM <br/> Einmal pro Tag für IaaS-VMs|
|Datenträger, die für die Sicherung an einen virtuellen Azure-Computer angeschlossen sind|16|

- <sup>1</sup>Die Beschränkung von  54.400 GB gilt nicht für IaaS-VM-Sicherungen.



