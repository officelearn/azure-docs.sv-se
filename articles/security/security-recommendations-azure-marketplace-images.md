---
title: Säkerhetsrekommendationer för Azure Marketplace-bilder | Microsoft Docs
description: Den här artikeln innehåller rekommendationer för bilder som finns på marknaden plats
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: barclayn
ms.openlocfilehash: 4ae36f87c29975c82bb99f713893a9dc78a249e6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2017
ms.locfileid: "23931137"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Säkerhetsrekommendationer för Azure Marketplace-bilder

Vi rekommenderar att varje lösning uppfyller följande rekommendationer för konfiguration. Detta kommer att upprätthålla en hög säkerhetsnivå för partner lösning avbildningar i Azure Marketplace.

De här rekommendationerna kan också vara användbara för organisationer som inte har avbildningar i Azure marketplace. Du kanske vill kontrollera konfigurationer för ditt företags-Windows- och Linux bild mot de riktlinjer som finns i tabellerna nedan.

## <a name="open-source-based-images"></a>Öppna käll-baserade bilder

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategori**                                                 | **Kontrollera**                                                                                                                                                                                                                                                                              |
| Säkerhet                                                     | Alla de senaste säkerhetskorrigeringarna för Linux-distribution är installerade.                                                                                                                                                                                                              |
| Säkerhet                                                     | Riktlinjer för branschstandarder för att skydda VM-avbildning för den specifika Linux-distributionen har följts.                                                                                                                                                                                     |
| Säkerhet                                                     | Begränsa risken för angrepp genom att hålla minimala storleken med endast nödvändiga Windows-serverroller, funktioner, tjänster och nätverk portar.                                                                                                                                               |
| Säkerhet                                                     | Skanna källkod och resulterande VM-avbildning för skadlig kod.                                                                                                                                                                                                                                   |
| Säkerhet                                                     | VHD-avbildningen innehåller endast nödvändiga låsta konton som inte har standardlösenordet som skulle låta interaktiv inloggning; Inga bakdörrar.                                                                                                                                           |
| Säkerhet                                                     | Brandväggsregler inaktiveras om programmet funktionellt bygger på dem, till exempel en brandväggsinstallation.                                                                                                                                                                             |
| Säkerhet                                                     | All känslig information har tagits bort från VHD-avbildningen, till exempel testa SSH-nycklar, kända hosts-filen, loggfiler och onödiga certifikat.                                                                                                                                       |
| Säkerhet                                                     | Du rekommenderas att LVM inte användas.                                                                                                                                                                                                                                            |
| Säkerhet                                                     | Senaste versionerna av bibliotek som krävs ska tas med: </br> -OpenSSL v1.0 eller större </br> -Python 2.5 eller senare (Python 2.6 + rekommenderas) </br> -Python pyasn1 paket om du inte redan är installerat </br> -d.OpenSSL v 1.0 eller senare                                                                |
| Säkerhet                                                     | Bash-gränssnittet historikposter måste tas bort                                                                                                                                                                                                                                             |
| Nätverk                                                   | SSH-server bör inkluderas som standard. Ange SSH keep alive sshd config med följande alternativ: ClientAliveInterval 180                                                                                                                                                        |
| Nätverk                                                   | Avbildningen får inte innehålla några anpassade nätverkskonfigurationen. Ta bort resolv.conf:`rm /etc/resolv.conf`                                                                                                                                                                                |
| Distribution                                                   | Senaste Azure Linux-agenten ska installeras </br> -Agenten ska installeras med hjälp av RPM eller Deb-paketet.  </br> -Du kan också använda manuell installationen, men installer-paket rekommenderas och önskade. </br> -Om du installerar agenten manuellt från github-lagringsplatsen, först kopiera den `waagent` filen till `/usr/sbin` och kör (som rot): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Konfigurationsfilen för agenten ska placeras vid `/etc/waagent.conf`.    |
| Distribution                                                   | Garanterar att Azure-supporten kan ge våra samarbetspartners med seriella konsolens utdata vid behov och tillhandahålla tillräckliga timeout för montering av OS-disken från molnet. Bilden måste ha lagt till följande parametrar för Kernel Start raden:`console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Distribution                                                   | Ingen växlingen partition på OS-disk. Växlingen kan begäras för att skapa på den lokala resurs disken av Linux-agenten.         |
| Distribution                                                   | Det rekommenderas att en enda rot-partition skapas för OS-disk.      |
| Distribution                                                   | 64-bitars endast operativsystem.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server-baserade avbildningar

|||
|-------------| -------------------------|
| **Kategori**                                                     | **Kontrollera**                                                                                                                                                                |
| Säkerhet                                                         | Använda en säker grundläggande operativsystemsavbildning. Den virtuella Hårddisken som används för källan till en bild som baseras på Windows Server måste vara från Windows Server-OS-avbildningar som tillhandahålls via Microsoft Azure. |
| Säkerhet                                                         | Installera alla senaste säkerhetsuppdateringarna.                                                                                                                                     |
| Säkerhet                                                         | Program ska inte ha ett beroende på begränsade användarnamn som administratör, roten och administratör.                                                                |
| Säkerhet                                                         | BitLocker-diskkryptering stöds inte på hårddisken för operativsystemet. BitLocker kan användas på datadiskar.                                                            |
| Säkerhet                                                         | Begränsa risken för angrepp genom att hålla minimala storleken med endast nödvändiga Windows-serverroller, funktioner, tjänster och nätverk portar som är aktiverad.                         |
| Säkerhet                                                         | Skanna källkod och resulterande VM-avbildning för skadlig kod.                                                                                                                     |
| Säkerhet                                                         | Ange Windows Server-avbildningar säkerhetsuppdatering för automatisk uppdatering.                                                                                                                |
| Säkerhet                                                         | VHD-avbildningen innehåller endast nödvändiga låsta konton som inte har standardlösenordet som skulle låta interaktiv inloggning; Inga bakdörrar.                             |
| Säkerhet                                                         | Brandväggsregler inaktiveras om programmet funktionellt bygger på dem, till exempel en brandväggsinstallation.                                                               |
| Säkerhet                                                         | All känslig information har tagits bort från VHD-avbildningen. Till exempel bör HOSTS-filen, loggfiler och onödiga certifikat tas bort.                                              |
| Distribution                                                       | 64-bitars endast operativsystem.                            |
