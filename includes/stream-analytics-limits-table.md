<properties 
   pageTitle="Tabelle zu Beschränkungen für Stream Analytics"
   description="Beschreibung der Systembegrenzungen und empfohlenen Größen für Stream Analytics-Komponenten und -Verbindungen."
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="10/22/2015"
   ms.author="jeffstok" />

| Grenzwertbezeichner | Begrenzung       | Kommentare |
|----------------- | ------------|--------- |
| Maximale Anzahl der Streamingeinheiten pro Abonnement und Region | 50 | Eine Anforderung zur Erhöhung von streaming-Einheiten für Ihr Abonnement über 50 erfolgen kann, wenden Sie sich an [Microsoft-Support](https://support.microsoft.com/en-us). |
| Maximaler Durchsatz einer Streamingeinheit | 1 MB/s* | Der maximale Durchsatz pro Streamingeinheit hängt vom jeweiligen Szenario ab. Tatsächliche Durchsatz kann geringer sein und hängt von der Komplexität der Abfragen und Partitionierung. Weitere Informationen finden Sie der [Skalieren von Azure Stream Analytics-Aufträgen](../articles/stream-analytics/stream-analytics-scale-jobs.md) Artikel. |
| Beschränkung der Abfrage von SELECT-Anweisungen | 5 Ausgaben pro Abfrage | Diese Beschränkung wird in Zukunft möglicherweise erhöht. |



