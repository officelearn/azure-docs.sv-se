---
title: Säkerhets rekommendationer för Azure Marketplace-avbildningar | Microsoft Docs
description: Den här artikeln innehåller rekommendationer för avbildningar som ingår på marknads platsen
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: b574f7c4f30c3bce8bd7d0e234cb523c965772e1
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727588"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Säkerhets rekommendationer för Azure Marketplace-avbildningar

Vi rekommenderar att varje lösning uppfyller följande rekommendationer för säkerhets konfiguration. Detta hjälper till att upprätthålla en hög säkerhets nivå för partner lösnings avbildningar på Azure Marketplace.

Dessa rekommendationer kan också vara till hjälp för organisationer som inte har avbildningar på Azure Marketplace. Du kanske vill kontrol lera företagets Windows-och Linux-bildkonfigurationer mot de rikt linjer som finns i följande tabeller:

## <a name="open-source-based-images"></a>Öppna källbaserade avbildningar

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategori**                                                 | **Utcheckning**                                                                                                                                                                                                                                                                              |
| Säkerhet                                                     | Alla de senaste säkerhets korrigeringarna för Linux-distributionen är installerade.                                                                                                                                                                                                              |
| Säkerhet                                                     | Bransch rikt linjer för att skydda den virtuella dator avbildningen för den angivna Linux-distributionen har följts.                                                                                                                                                                                     |
| Säkerhet                                                     | Begränsa angrepps ytan genom att behålla minimalt utrymme med endast nödvändiga Windows Server-roller, funktioner, tjänster och nätverks portar.                                                                                                                                               |
| Säkerhet                                                     | Skanna käll koden och den resulterande VM-avbildningen för skadlig kod.                                                                                                                                                                                                                                   |
| Säkerhet                                                     | VHD-avbildningen innehåller bara nödvändiga låsta konton som inte har standard lösen ord som tillåter interaktiv inloggning. inga back dörrar.                                                                                                                                           |
| Säkerhet                                                     | Brand Väggs regler är inaktiverade, om inte programmet använder sig av funktionen, till exempel en brand Väggs enhet.                                                                                                                                                                             |
| Säkerhet                                                     | All känslig information har tagits bort från VHD-avbildningen, t. ex. testa SSH-nycklar, kända värdar-fil, loggfiler och onödiga certifikat.                                                                                                                                       |
| Säkerhet                                                     | Vi rekommenderar att LVM inte ska användas.                                                                                                                                                                                                                                            |
| Säkerhet                                                     | De senaste versionerna av de nödvändiga biblioteken ska ingå: </br> -OpenSSL v 1.0 eller senare </br> – Python 2,5 eller senare (python 2.6 + är starkt rekommenderat) </br> – Python pyasn1-paket om det inte redan har installerats </br> -d. OpenSSL v 1,0 eller senare                                                                |
| Säkerhet                                                     | Bash/shell-poster måste tas bort                                                                                                                                                                                                                                             |
| Nätverk                                                   | SSH-servern ska inkluderas som standard. Ange SSH Keep Alive till sshd-konfigurationen med följande alternativ: ClientAliveInterval 180                                                                                                                                                        |
| Nätverk                                                   | Avbildningen får inte innehålla någon anpassad nätverks konfiguration. Ta bort kommandot matcha. conf:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Distribution                                                   | Den senaste Azure Linux-agenten bör installeras </br> -Agenten ska installeras med hjälp av RPM-eller deb-paketet.  </br> -Du kan också använda processen för manuell installation, men installations paketen rekommenderas och föredras. </br> – Om du installerar agenten manuellt från GitHub-lagringsplatsen kopierar `waagent` du först filen till `/usr/sbin` och kör (som rot): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Agent konfigurations filen placeras på `/etc/waagent.conf`.    |
| Distribution                                                   | Garanterar att Azure-supporten kan ge våra partners till gång till serie konsolens utdata vid behov och tillhandahålla tillräckligt med tids gräns för disk montering för operativ system från moln lagring. Avbildningen måste ha lagt till följande parametrar till kernel-startlinjen:`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Distribution                                                   | Ingen växlings-partition på OS-disken. Du kan begära växling för att skapa en lokal resurs disk av Linux-agenten.         |
| Distribution                                                   | Vi rekommenderar att du skapar en enda rotnod för OS-disken.      |
| Distribution                                                   | endast 64-bitars operativ system.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server-baserade avbildningar

|||
|-------------| -------------------------|
| **Kategori**                                                     | **Utcheckning**                                                                                                                                                                |
| Säkerhet                                                         | Använd en säker OS-avbildning. Den virtuella hård disk som används för källan till alla avbildningar som baseras på Windows Server måste vara från Windows Server OS-avbildningarna som tillhandahålls via Microsoft Azure. |
| Säkerhet                                                         | Installera alla de senaste säkerhets uppdateringarna.                                                                                                                                     |
| Säkerhet                                                         | Program ska inte vara beroende av begränsade användar namn som administratör, rot och administratör.                                                                |
| Säkerhet                                                         | BitLocker-diskkryptering stöds inte på hård disken för operativ systemet. BitLocker kan användas på data diskar.                                                            |
| Säkerhet                                                         | Begränsa angrepps ytan genom att behålla minimalt utrymme med endast nödvändiga Windows Server roller, funktioner, tjänster och nätverks portar aktiverade.                         |
| Säkerhet                                                         | Skanna käll koden och den resulterande VM-avbildningen för skadlig kod.                                                                                                                     |
| Säkerhet                                                         | Ange Windows Server-avbildningar säkerhets uppdatering för automatisk uppdatering.                                                                                                                |
| Säkerhet                                                         | VHD-avbildningen innehåller bara nödvändiga låsta konton som inte har standard lösen ord som tillåter interaktiv inloggning. inga back dörrar.                             |
| Säkerhet                                                         | Brand Väggs regler är inaktiverade, om inte programmet använder sig av funktionen, till exempel en brand Väggs enhet.                                                               |
| Säkerhet                                                         | All känslig information har tagits bort från VHD-avbildningen. Till exempel är HOSTs-filer, loggfiler och onödiga certifikat borttagna.                                              |
| Distribution                                                       | endast 64-bitars operativ system.                            |
