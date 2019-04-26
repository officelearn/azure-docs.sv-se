---
title: Säkerhetsrekommendationer för Azure Marketplace-avbildningar | Microsoft Docs
description: Den här artikeln innehåller rekommendationer för bilder som finns på marknaden plats
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: fa521b81c95f7c0556b082e5487848ef4d7ecaf7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444120"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Säkerhetsrekommendationer för Azure Marketplace-avbildningar

Vi rekommenderar att varje lösning uppfyller följande konfiguration säkerhetsrekommendationerna. Detta upprätthåller en hög säkerhetsnivå för partner solution-avbildningar i Azure Marketplace.

De här rekommendationerna kan också vara användbart för organisationer som inte har bilder på Azure marketplace. Du kanske vill kontrollera konfigurationer för ditt företags-Windows- och Linux-avbildning mot de riktlinjer som finns i följande tabeller:

## <a name="open-source-based-images"></a>Öppna källbaserad bilder

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategori**                                                 | **Kontrollera**                                                                                                                                                                                                                                                                              |
| Säkerhet                                                     | Alla senaste säkerhetsuppdateringar för Linux-distribution är installerade.                                                                                                                                                                                                              |
| Säkerhet                                                     | Riktlinjer för branschstandarder för att skydda VM-avbildning för den specifika Linux-distributionen har följts.                                                                                                                                                                                     |
| Säkerhet                                                     | Minska risken för angrepp genom att hålla minimal fotavtryck med endast nödvändiga Windows Server-roller, funktioner, tjänster och nätverk portar.                                                                                                                                               |
| Säkerhet                                                     | Skanna källkoden och resulterande VM-avbildning för skadlig kod.                                                                                                                                                                                                                                   |
| Säkerhet                                                     | VHD-avbildningen innehåller endast nödvändiga låsta konton som inte har standardlösenordet som skulle låta interaktiv inloggning; Inga bakdörrar.                                                                                                                                           |
| Säkerhet                                                     | Brandväggsregler är inaktiverade, såvida inte programmet funktionellt bygger på dem, till exempel en brandväggsinstallation.                                                                                                                                                                             |
| Säkerhet                                                     | All känslig information har tagits bort från VHD-avbildning, till exempel testa SSH-nycklar, kända hosts-filen, loggfiler och onödiga certifikat.                                                                                                                                       |
| Säkerhet                                                     | Du rekommenderas att LVM inte bör användas.                                                                                                                                                                                                                                            |
| Säkerhet                                                     | Senaste versionerna av bibliotek som krävs ska finnas med: </br> -OpenSSL version 1.0 eller senare </br> -Python 2.5 eller högre (Python 2.6 + rekommenderas) </br> -Python pyasn1-paketet om du inte redan är installerat </br> -d.OpenSSL v 1.0 eller senare                                                                |
| Säkerhet                                                     | Bash-gränssnittet historikposter måste tas bort                                                                                                                                                                                                                                             |
| Nätverk                                                   | SSH-server ska tas med som standard. Ange SSH keep alive i sshd-konfigurationen med följande alternativ: ClientAliveInterval 180                                                                                                                                                        |
| Nätverk                                                   | Bilden får inte innehålla någon anpassad nätverkskonfiguration. Ta bort resolv.conf: `rm /etc/resolv.conf`                                                                                                                                                                                |
| Distribution                                                   | Senaste Azure Linux-agenten ska installeras </br> -Agenten ska installeras med hjälp av RPM- eller Deb-paketet.  </br> -Du kan också använda manuella installationen, men installer-paket är rekommenderade och rekommenderas. </br> – Om du installerar agenten manuellt från GitHub-lagringsplatsen, först kopiera den `waagent` filen till `/usr/sbin` och kör (som rot): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Agentens konfigurationsfil är placerad på `/etc/waagent.conf`.    |
| Distribution                                                   | Garanterar att Azure-supporten kan tillhandahålla våra partner med utdata vid behov och ange tillräckliga tidsgränser för montering av OS-diskar från molnlagring från seriell konsol. Bilden måste ha lagt till följande parametrar till i Kernel Boot Line: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Distribution                                                   | Ingen swap-partition på OS-disken. Växling kan begäras för att skapa på den lokala resursdisk av Linux-agenten.         |
| Distribution                                                   | Du rekommenderas att skapas en enskild rotpartition för OS-disken.      |
| Distribution                                                   | 64-bitars endast operativsystem.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server-baserade avbildningar

|||
|-------------| -------------------------|
| **Kategori**                                                     | **Kontrollera**                                                                                                                                                                |
| Säkerhet                                                         | Använd en säker grundläggande OS-avbildning. Den virtuella Hårddisken används för källan till en bild som baseras på Windows Server måste vara från Operativsystemet Windows Server-avbildningarna som tillhandahålls via Microsoft Azure. |
| Säkerhet                                                         | Installera alla senaste säkerhetsuppdateringarna.                                                                                                                                     |
| Säkerhet                                                         | Program bör inte ha ett beroende på begränsade användarnamn som administratör, rot och administratör.                                                                |
| Säkerhet                                                         | BitLocker-diskkryptering stöds inte på hårddisken för operativsystemet. BitLocker kan användas på datadiskar.                                                            |
| Säkerhet                                                         | Minska risken för angrepp genom att hålla minimal fotavtryck med endast nödvändiga Windows Server-roller, funktioner, tjänster och nätverk portar som är aktiverad.                         |
| Säkerhet                                                         | Skanna källkoden och resulterande VM-avbildning för skadlig kod.                                                                                                                     |
| Säkerhet                                                         | Ange Windows Server-avbildningar säkerhetsuppdatering för automatisk uppdatering.                                                                                                                |
| Säkerhet                                                         | VHD-avbildningen innehåller endast nödvändiga låsta konton som inte har standardlösenordet som skulle låta interaktiv inloggning; Inga bakdörrar.                             |
| Säkerhet                                                         | Brandväggsregler är inaktiverade, såvida inte programmet funktionellt bygger på dem, till exempel en brandväggsinstallation.                                                               |
| Säkerhet                                                         | All känslig information har tagits bort från VHD-avbildningen. Till exempel bör VÄRDAR filen, loggfiler och onödiga certifikat tas bort.                                              |
| Distribution                                                       | 64-bitars endast operativsystem.                            |
