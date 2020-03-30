---
title: Säkerhetshärdning i AKS virtuella datorvärdar
description: Lär dig mer om säkerhetshärdningen i AKS VM-värdoperativsystemet
services: container-service
author: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d4105a9fba3c40c563198040afb811625727ead0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594388"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>Säkerhetshärdning i AKS virtuella datorvärdar 

Azure Kubernetes Service (AKS) är en säker tjänst som uppfyller SOC-, ISO-, PCI DSS- och HIPAA-standarder. Den här artikeln beskriver säkerhetshärdningen som tillämpas på AKS virtuella datorvärdar. Mer information om AKS-säkerhet finns i [Säkerhetsbegrepp för program och kluster i Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/concepts-security).

AKS-kluster distribueras på virtuella värddatorer, som kör ett säkerhetsoptimerat operativsystem. Den här värdoperativsystemet baseras för närvarande på en Ubuntu 16.04.LTS-avbildning med en uppsättning ytterligare säkerhetshärdningssteg (se Detaljer för säkerhetshärdning).   

Målet med säkerhetshärdade värd OS är att minska ytan av angrepp och tillåta distribution av behållare på ett säkert sätt. 

> [!Important]
> Säkerhetshärdade OS är inte CIS benchmarked. Även om det finns överlappningar med CIS-riktmärken är målet inte att vara CIS-kompatibelt. Målet för värd OS härdning är att konvergera på en säkerhetsnivå som överensstämmer med Microsofts egna interna värd säkerhetsstandarder. 

## <a name="security-hardening-features"></a>Funktioner för säkerhetshärdning 

* AKS tillhandahåller ett säkerhetsoptimerat värd-OS som standard. Det finns inget aktuellt alternativ för att välja ett alternativt operativsystem. 

* Azure tillämpar dagliga korrigeringar (inklusive säkerhetskorrigeringar) på AKS virtuella datorvärdar. Några av dessa patchar kommer att kräva en omstart, medan andra inte. Du ansvarar för att schemalägga OMSTART AV AKS VM-värdomstartar efter behov. Mer information om hur du automatiserar AKS-korrigering finns [i korrigering av AKS-noder](https://docs.microsoft.com/azure/aks/node-updates-kured).

Nedan följer en sammanfattning av bildhärdning arbete som genomförs i AKS-Engine för att producera den säkerhetsoptimerade värd OS. Arbetet har implementerats [i detta GitHub-projekt](https://github.com/Azure/aks-engine/projects/7).  

AKS-Engine marknadsför eller följer inte någon specifik säkerhetsstandard just nu, men CIS-gransknings-ID:n (Center for Internet Security) tillhandahålls i tillämpliga fall. 

## <a name="whats-configured"></a>Vad är konfigurerat?

| Cis  | Beskrivning av revisionen| 
|---|---|
| 1.1.1.1 |Se till att montering av cramfs filsystem är inaktiverad|
| 1.1.1.2 |Se till att montering av freevxfs filsystem är inaktiverad|
| 1.1.1.3 |Se till att monteringen av jffs2-filsystem är inaktiverad|
| 1.1.1.4 |Se till att monteringen av HFS-filsystem är inaktiverad|
| 1.1.1.5 |Se till att monteringen av HFS Plus-filsystem är inaktiverad|
|1.4.3 |Kontrollera autentisering som krävs för enanvändarläge |
|1.7.1.2 |Se till att den lokala inloggningsvarningsbannern är korrekt konfigurerad |
|1.7.1.3 |Se till att varningssignalen för fjärrinloggning är korrekt konfigurerad |
|1.7.1.5 |Se till att behörigheterna för /etc/issue är konfigurerade |
|1.7.1.6 |Se till att behörigheterna för /etc/issue.net är konfigurerade |
|2.1.5 |Kontrollera att --streaming-connection-indle-timeout inte är inställd på 0 |
|3.1.2 |Kontrollera att skicka paket omdirigering är inaktiverat |
|3.2.1 |Se till att källrutterna paket inte accepteras |
|3.2.2 |Se till att ICMP-omdirigeringar inte accepteras |
|3.2.3 |Se till att säkra ICMP-omdirigeringar inte accepteras |
|3.2.4 |Se till att misstänkta paket loggas |
|3.3.1 |Se till att IPv6-routerannonser inte accepteras |
|3.5.1 |Se till att DCCP är inaktiverat |
|3.5.2 |Se till att SCTP är inaktiverat |
|3.5.3 |Se till att RDS är inaktiverat |
|3.5.4 |Se till att TIPC är inaktiverat |
|4.2.1.2 |Se till att loggningen är konfigurerad |
|5.1.2 |Se till att behörigheterna för /etc/crontab är konfigurerade |
|5.2.4 |Se till att SSH X11-vidarebefordran är inaktiverad |
|5.2.5 |Se till att SSH MaxAuthTries är inställt på 4 eller mindre |
|5.2.8 |Se till att SSH-rotinloggning är inaktiverad |
|5.2.10 |Se till att SSH-tillståndAnvändarmiljö är inaktiverat |
|5.2.11 |Se till att endast godkända MAX-algoritmer används |
|5.2.12 |Kontrollera att timeoutintervall för SSH-inaktivitet är konfigurerat |
|5.2.13 |Se till att SSH LoginGraceTime är inställt på en minut eller mindre |
|5.2.15 |Se till att SSH-varningsbanderoller är konfigurerad |
|5.3.1 |Se till att kraven för att skapa lösenord är konfigurerade |
|5.4.1.1 |Se till att lösenordet upphör att gälla 90 dagar eller mindre |
|5.4.1.4 |Se till att inaktivt lösenordslås är 30 dagar eller mindre |
|5.4.4 |Kontrollera att standardanvändar umask är 027 eller mer restriktiv |
|5.6 |Se till att åtkomsten till kommandot su är begränsad|

## <a name="additional-notes"></a>Ytterligare information
 
* För att ytterligare minska angreppsytan har vissa onödiga drivrutiner för kärnmoduler inaktiverats i operativsystemet. 

* Säkerhetshärdade OPERATIVSYSTEM stöds INTE utanför AKS-plattformen. 

## <a name="next-steps"></a>Nästa steg  

Mer information om AKS-säkerhet finns i följande artiklar: 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[AKS-säkerhetsöverväganden](https://docs.microsoft.com/azure/aks/concepts-security)

[BÄSTA PRAXIS FÖR AKS](https://docs.microsoft.com/azure/aks/best-practices)
