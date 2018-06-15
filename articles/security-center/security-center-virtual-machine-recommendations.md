---
title: Skydda dina virtuella datorer i Azure Security Center | Microsoft Docs
description: Det här dokumentet behandlar rekommendationerna i Azure Security Center som hjälper dig skydda dina virtuella datorer och uppfyller säkerhetsprinciper.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 54375f6f98b4989a7af8bcde649d967f77c6c862
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
ms.locfileid: "27623506"
---
# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Skydda dina virtuella datorer i Azure Security Center
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsrisker, skapar rekommendationer som hjälper dig att konfigurera nödvändiga kontroller.  Rekommendationer gäller för Azure resurstyper: virtuella datorer (VM), nätverk, SQL och program.

Den här artikeln tar rekommendationer som gäller för virtuella datorer.  VM rekommendationer center runt datainsamling, tillämpa systemuppdateringar, etablering skadlig kod, kryptera dina Virtuella diskar och mycket mer.  Använd tabellen nedan som referens för att förstå tillgängliga rekommendationer för Virtuella datorer och vad varje göra om du använder den.

## <a name="available-vm-recommendations"></a>Tillgängliga rekommendationer för Virtuella datorer
| Rekommendation | Beskrivning |
| --- | --- |
| [Aktivera insamling av data för prenumerationer](security-center-enable-data-collection.md) |Rekommenderar att du aktiverar datainsamling i säkerhetsprincipen för var och en av dina prenumerationer och alla virtuella datorer (VM) i dina prenumerationer. |
| [Aktivera kryptering för Azure Storage-konto](security-center-enable-encryption-for-storage-account.md) | Rekommenderar att du aktiverar Azure Storage Service-kryptering för data i vila. Kryptering för lagring-tjänsten (SSE) fungerar genom att kryptera data när de skrivs till Azure-lagring och dekrypterar före hämtning. SSE är endast tillgänglig för Azure Blob-tjänsten och kan användas för blockblobbar, sidblobbar, och tilläggsblobar. Läs mer i [Lagringstjänstens kryptering av vilande data](../storage/common/storage-service-encryption.md).</br>SSE stöds endast i hanteraren för filserverresurser storage-konton. Klassiska lagringskonton stöds inte för närvarande. Klassiskt och Resource Manager distributionsmodellerna finns [Azure distributionsmodeller](../azure-classic-rm.md). |
| [Åtgärda säkerhetskonfigurationer](security-center-remediate-os-vulnerabilities.md) |Rekommenderar att du justerar OS-konfigurationer med de rekommenderade konfigurationen säkerhetsreglerna, tillåter t.ex. inte att lösenord ska sparas. |
| [Tillämpa systemuppdateringar](security-center-apply-system-updates.md) |Rekommenderar att du distribuerar systemsäkerhet och viktiga uppdateringar för virtuella datorer som saknas. |
| [Tillämpa Just-In-Time nätverk åtkomstkontroll](security-center-just-in-time.md) | Rekommenderar att du installerar just-in-time VM-åtkomst. Den bara i tiden funktionen är i förhandsvisning och finns på standardnivån av Security Center. Se [priser](security-center-pricing.md) att lära dig mer om Security Center prisnivåer. |
| [Starta om datorn efter uppdateringarna](security-center-apply-system-updates.md#reboot-after-system-updates) |Rekommenderar att du startar om en virtuell dator för att slutföra processen med att tillämpa uppdateringar. |
| [Installera slutpunktsskydd](security-center-install-endpoint-protection.md) |Rekommenderar att du etablerar program mot skadlig kod för virtuella datorer (endast virtuella Windows-datorer). |
| [Aktivera VM-Agent](security-center-enable-vm-agent.md) |Du kan se vilka virtuella datorer som kräver VM-agenten. VM-agenten måste installeras på virtuella datorer för att etablera korrigeringsgenomsökning, baslinjegenomsökning och program mot skadlig kod. VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Artikeln [VM Agent and Extensions – Part 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) (VM-agenter och tillägg – del 2) innehåller information om hur VM-agenten ska installeras. |
| [Tillämpa diskkryptering](security-center-apply-disk-encryption.md) |Rekommenderar att krypterar dina VM-diskar med Azure Disk Encryption (virtuella Windows- och Linux-datorer). Kryptering rekommenderas både för OS- och datavolymer på den virtuella datorn. |
| [Uppdatera OS-versionen](security-center-update-os-version.md) |Rekommenderar att du uppdaterar versionen av operativsystemet (OS) för din molntjänst till den senaste versionen för OS-familjen.  Mer information om molntjänster finns i [översikt över molntjänster](../cloud-services/cloud-services-choose-me.md). |
| [Sårbarhetsbedömning inte installerad](security-center-vulnerability-assessment-recommendations.md) |Rekommenderar att du installerar en lösning för sårbarhetsbedömning på den virtuella datorn. |
| [Åtgärda sårbarheter](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Gör att du kan visa system- och säkerhetssårbarheter som identifieras av sårbarhetsbedömningen som är installerad på den virtuella datorn. |

## <a name="see-also"></a>Se också
Mer information om rekommendationer som gäller för andra typer av Azure finns i:

* [Skydda dina program i Azure Security Center](security-center-application-recommendations.md)
* [Skydda ditt nätverk i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din SQL Azure-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
