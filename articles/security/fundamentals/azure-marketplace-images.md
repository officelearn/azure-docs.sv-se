---
title: Säkerhetsrekommendationer för Azure Marketplace-avbildningar | Microsoft-dokument
description: Den här artikeln innehåller rekommendationer för bilder som ingår på marknaden
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 3925e39824d1702ff43a6b981ac997ddab658b96
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548660"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Säkerhetsrekommendationer för Azure Marketplace-avbildningar

Avbildningen måste uppfylla dessa rekommendationer för säkerhetskonfiguration. Detta bidrar till att upprätthålla en hög säkerhetsnivå för partnerlösningsavbildningar på Azure Marketplace.

Kör alltid en säkerhetsriskidentifiering på avbildningen innan du skickar in den. Om du upptäcker ett säkerhetsproblem i din egen publicerade bild måste du informera dina kunder i tid om både sårbarheten och hur du korrigerar det.

## <a name="open-source-based-images"></a>Öppna källkodsbaserade bilder

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategori**                                                 | **Kontrollera**                                                                                                                                                                                                                                                                              |
| Säkerhet                                                     | Installera alla de senaste säkerhetskorrigeringarna för Linux-distributionen.                                                                                                                                                                                                              |
| Säkerhet                                                     | Följ branschriktlinjerna för att skydda vm-avbildningen för den specifika Linux-distributionen.                                                                                                                                                                                     |
| Säkerhet                                                     | Begränsa angreppsytan genom att hålla minimalt fotavtryck med endast nödvändiga Windows Server-roller, funktioner, tjänster och nätverksportar.                                                                                                                                               |
| Säkerhet                                                     | Sök igenom källkod och resulterande VM-avbildning för skadlig kod.                                                                                                                                                                                                                                   |
| Säkerhet                                                     | VHD-avbildningen innehåller endast nödvändiga låsta konton som inte har standardlösenord som tillåter interaktiv inloggning. inga bakdörrar.                                                                                                                                           |
| Säkerhet                                                     | Inaktivera brandväggsregler om inte programmet är funktionellt beroende av dem, till exempel en brandväggsinstallation.                                                                                                                                                                             |
| Säkerhet                                                     | Ta bort all känslig information från VHD-avbildningen, till exempel testnycklar för SSH, kända värdar, loggfiler och onödiga certifikat.                                                                                                                                       |
| Säkerhet                                                     | Undvik att använda LVM.                                                                                                                                                                                                                                            |
| Säkerhet                                                     | Inkludera de senaste versionerna av nödvändiga bibliotek: </br> - OpenSSL v1.0 eller mer </br> - Python 2.5 eller högre (Python 2.6+ rekommenderas starkt) </br> - Python pyasn1-paket om det inte redan är installerat </br> - d.OpenSSL mot 1.0 eller mer                                                                |
| Säkerhet                                                     | Rensa bash/shell-historikposter.                                                                                                                                                                                                                                             |
| Nätverk                                                   | Inkludera SSH-servern som standard. Ställ SSH hålla vid liv för att sshd config med följande alternativ: ClientAliveInterval 180.                                                                                                                                                        |
| Nätverk                                                   | Ta bort en anpassad nätverkskonfiguration från avbildningen. Ta bort resolv.conf: `rm /etc/resolv.conf`.                                                                                                                                                                                |
| Distribution                                                   | Installera den senaste Azure Linux-agenten.</br> - Installera med RPM- eller Deb-paketet.  </br> - Du kan också använda den manuella installationsprocessen, men installationspaketen rekommenderas och föredras. </br> - Om du installerar agenten manuellt från GitHub-databasen kopierar du först `waagent` filen till `/usr/sbin` och kör (som root): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Agentkonfigurationsfilen placeras `/etc/waagent.conf`på . |
| Distribution                                                   | Se till att Azure Support kan ge våra partner seriella konsolutdata när det behövs och ge tillräcklig timeout för OS-diskmontering från molnlagring. Lägg till följande parametrar i avbildningen Kernel Boot Line: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`. |
| Distribution                                                   | Ingen växlingspartition på OS-disken. Swap kan begäras för att skapas på den lokala resursdisken av Linux-agenten.         |
| Distribution                                                   | Skapa en enda rotpartition för OS-disken.      |
| Distribution                                                   | Endast 64-bitars operativsystem.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server-baserade bilder

|||
|-------------| -------------------------|
| **Kategori**                                                     | **Kontrollera**                                                                                                                                                                |
| Säkerhet                                                         | Använd en säker OS-basavbildning. Den virtuella hårddisk som används för källan till en avbildning baserad på Windows Server måste komma från Windows Server OS-avbildningar som tillhandahålls via Microsoft Azure. |
| Säkerhet                                                         | Installera de senaste säkerhetsuppdateringarna.                                                                                                                                     |
| Säkerhet                                                         | Program bör inte vara beroende av begränsade användarnamn som administratör, rot eller administratör.                                                                |
| Säkerhet                                                         | Aktivera BitLocker-diskkryptering för både operativsystem och datahårddiskar.                                                             |
| Säkerhet                                                         | Begränsa angreppsytan genom att hålla minimalt fotavtryck med endast nödvändiga Windows Server-roller, funktioner, tjänster och nätverksportar aktiverade.                         |
| Säkerhet                                                         | Sök igenom källkod och resulterande VM-avbildning för skadlig kod.                                                                                                                     |
| Säkerhet                                                         | Ställ in säkerhetsuppdatering för Windows Server-avbildningar för automatisk uppdatering.                                                                                                                |
| Säkerhet                                                         | VHD-avbildningen innehåller endast nödvändiga låsta konton som inte har standardlösenord som tillåter interaktiv inloggning. inga bakdörrar.                             |
| Säkerhet                                                         | Inaktivera brandväggsregler om inte programmet är funktionellt beroende av dem, till exempel en brandväggsinstallation.                                                               |
| Säkerhet                                                         | Ta bort all känslig information från VHD-avbildningen, inklusive HOSTS-filer, loggfiler och onödiga certifikat.                                              |
| Distribution                                                       | Endast 64-bitars operativsystem.                            |

Även om din organisation inte har avbildningar på Azure-marknadsplatsen bör du överväga att kontrollera dina Windows- och Linux-avbildningskonfigurationer mot dessa rekommendationer.

## <a name="contacting-customers"></a>Kontakta kunder

Så här identifierar du kunder och deras e-post via kontakt:

1.  Välj **Insikter**på den vänstra skenan i Cloud Partner Portal .
2.  På fliken **Order och Användning** använder du fälten **Startdatum** och **Slutdatum** för att fråga användningen inom det datumintervall som krävs. Detta visar vilka Azure-prenumerationer som användes för erbjudandet dagligen. Exportera dessa data. 
3.  På samma sätt frågar och exporterar du kundbasen på fliken **Kund.**
4.  Matcha prenumerations-ID från steg 2 till prenumerations-ID från steg 3 för att hitta nödvändig kundinformation.
