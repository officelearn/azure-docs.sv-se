---
title: Säkerhet i Azure-funktioner som används med Azure virtual machines | Microsoft Docs
description: Den här artikeln innehåller en översikt över viktigaste Azure säkerhetsfunktioner som kan användas med Azure Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: terrylan
ms.openlocfilehash: 15ac70891f23d95709d1998bca1ce29ad735cb87
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109086"
---
# <a name="azure-virtual-machines-security-overview"></a>Säkerhetsöversikt för Azure virtuella datorer

Du kan använda Azure Virtual Machines för att distribuera en lång rad databehandlingslösningar på ett smidigt sätt. Tjänsten stöder Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP och Azure BizTalk Services. Så kan du distribuera alla arbetsbelastningar och valfritt språk på nästan vilket operativsystem.

En virtuell dator i Azure ger dig virtualiseringsflexibilitet utan att du behöver köpa och underhålla den fysiska maskinvara som den virtuella datorn körs på. Du kan skapa och distribuera program i trygg förvissning om att dina data är skyddade och säkra i mycket säkra datacenter.

Med Azure, kan du skapa säkrare lösningar som uppfyller kraven som:

* Skydda dina virtuella datorer från virus och skadlig kod.
* Kryptera känsliga data.
* Skydda nätverkstrafiken.
* Identifiera och Upptäck hot.
* Uppfyll efterlevnadskrav.

Målet med den här artikeln är att tillhandahålla en översikt över viktigaste Azure säkerhetsfunktioner som kan användas med virtuella datorer. Länkar till artiklar ge information om varje funktion så att du kan läsa mer.  

## <a name="antimalware"></a>Programvara mot skadlig kod

Med Azure kan använda du program mot skadlig kod från säkerhetsleverantörer som Microsoft, Symantec, Trend Micro och Kaspersky. Det här programmet kan du skydda dina virtuella datorer från skadliga filer, annonsprogram och andra hot.

Microsoft Antimalware för Azure Cloud Services och virtuella datorer är en realtidsskydd-funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara.  Microsoft Antimalware för Azure ger konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera sig själva eller köras på din Azure-system.

Microsoft Antimalware för Azure är en enskild agent-lösning för program och klient-miljöer. Det har utformats för att köras i bakgrunden utan mänsklig inblandning. Du kan distribuera skydd baserat på dina behov för din programbelastningar, med antingen grundläggande säker som standard eller avancerade anpassad konfiguration, bland annat övervakning av program mot skadlig kod.

När du distribuerar och aktivera Microsoft Antimalware för Azure finns följande huvudfunktioner:

* **Realtidsskydd**: Övervakar aktiviteten i Cloud Services och på virtuella datorer för att identifiera och blockera körning av skadlig kod.
* **Schemalagd genomsökning**: Utför regelbundet riktade genomsökning för att identifiera skadlig kod, däribland program som körs aktivt.
* **Korrigering av skadlig kod**: Vidtar automatiskt åtgärder på identifierad skadlig kod, till exempel ta bort eller placera det i karantän skadliga filer och rensar skadliga registerposter.
* **Signaturuppdateringar**: Installerar automatiskt de senaste signaturerna (virusdefinitioner av) för att säkerställa att skyddet är uppdaterad på en förbestämd frekvens.
* **Motoruppdateringar mot skadlig kod**: Uppdateras automatiskt Microsoft Antimalware för Azure-motorn.
* **Uppdateringar för program mot skadlig kod plattform**: Uppdateras automatiskt Microsoft Antimalware för Azure-plattformen.
* **Aktivt skydd**: Rapporterar telemetri metadata till Azure om identifierade hot och misstänkta resurser för att säkerställa snabba svar. Gör i realtid synkron signatur leverans via Microsoft Active Protection System (MAPS).
* **Exempel reporting**: Ger och rapporterar exempel till Microsoft Antimalware för Azure-tjänsten för att förfina tjänsten och aktivera felsökning.
* **Undantag**: Gör att program och administratörer för att konfigurera vissa filer, processer och enheter så de utesluts från skyddet och sökning efter prestanda och andra orsaker.
* **Händelseinsamling för program mot skadlig kod**: Registrerar tjänstehälsa för program mot skadlig kod, misstänkta aktiviteter och åtgärder som vidtagits i händelseloggen för operativsystemet och samlar in dem i Azure storage-kontot.

Mer information om program mot skadlig kod för att skydda dina virtuella datorer:

* [Microsoft Antimalware för Azure-molntjänster och virtuella datorer](azure-security-antimalware.md)
* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Hur du installerar och konfigurerar du Trend Micro Deep Security som en tjänst på en virtuell Windows-dator](../virtual-machines/windows/classic/install-trend.md)
* [Hur du installerar och konfigurerar du Symantec Endpoint Protection på en virtuell Windows-dator](../virtual-machines/windows/classic/install-symantec.md)
* [Säkerhetslösningar i Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Överväg att använda för ännu mer kraftfullt skydd [Windows Defender Avancerat skydd](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Med Windows Defender ATP får du:

* [Minska attackytan](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Nästa generations skydd](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Endpoint protection och svar](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatiserad undersökning och åtgärder](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [säker poäng](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-secure-score-windows-defender-advanced-threat-protection)
* [Avancerade jakt](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Hanterings- och API: er](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Läs mer: 

* [Kom igång med WDATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/get-started)  
* [Översikt över WDATP funktioner](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Modul för maskinvarusäkerhet

Förbättra nyckelsäkerhet kan förbättra skydd för kryptering och autentisering. Du kan förenkla hanteringen av och säkerheten för viktiga hemligheter och nycklar genom att lagra dem i Azure Key Vault. 

Med Key Vault kan du lagra dina nycklar i HSM:er (Hardware Security Modules) som är certifierade enligt standarden FIPS 140-2 nivå 2. Din SQL Server krypteringsnycklar för säkerhetskopiering eller [transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx) kan alla lagras i Key Vault med andra nycklar eller hemligheter från dina program. Behörigheter och åtkomst till de skyddade objekten hanteras via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Läs mer:

* [Vad är Azure Key Vault?](../key-vault/key-vault-overview.md)
* [Azure Key Vault-bloggen](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>VM-diskkryptering

Azure Disk Encryption är en ny funktion för att kryptera din Windows- och Linux virtuella diskar. Azure Disk Encryption används vanliga [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows och [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux för att kryptera volymer för Operativsystemet och datadiskarna.

Lösningen är integrerad med Azure Key Vault för att styra och hantera diskkrypteringsnycklarna och hemligheter i key vault-prenumeration. Det innebär att alla data på diskar i virtuella datorer krypteras i vila i Azure Storage.

Läs mer:

* [Azure Disk Encryption för virtuella IaaS-datorer](../security/azure-security-disk-encryption-overview.md)
* [Snabbstart: Kryptera en virtuell dator IaaS Windows med Azure PowerShell](../security/quick-encrypt-vm-powershell.md)

## <a name="virtual-machine-backup"></a>Säkerhetskopiering av virtuell dator

Azure Backup är en skalbar lösning som hjälper dig att skydda dina programdata helt utan kapitalinvestering och minimal driftskostnad. Programfel kan skada dina data och den mänskliga faktorn kan införa buggar i dina program. Dina virtuella datorer som kör Windows och Linux är skyddade med Azure Backup.

Läs mer:

* [Vad är Azure Backup?](../backup/backup-introduction-to-azure-backup.md)
* [Azure Backup service FAQ](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

En viktig del av din organisations BCDR-strategi är att räkna ut hur att företagets arbetsbelastningar och appar som körs när planerade och oplanerade driftstopp inträffar. Azure Site Recovery hjälper till att dirigera replikering, redundans och återställning av arbetsbelastningar och appar så att de är tillgängliga från en sekundär plats om din primära plats kraschar.

Site Recovery:

* **Förenklar din BCDR-strategi**: Site Recovery gör det enkelt att hantera replikering, redundans och återställning av flera arbetsbelastningar och appar från en enda plats. Site Recovery dirigerar replikering och redundans men inte komma åt dina programdata eller har ingen information om den.
* **Tillhandahåller flexibel replikering**: Genom att använda Site Recovery kan replikera du arbetsbelastningar som körs på Hyper-V-datorer, virtuella VMware-datorer och fysiska Windows/Linux-servrar.
* **Har stöd för redundans och återställning**: Site Recovery tillhandahåller redundanstestning för att stödja tester av haveriberedskap utan att påverka produktionsmiljöer. Du kan också köra planerade redundansväxlingar utan någon dataförlust för förväntade driftsavbrott, eller oplanerade redundansväxlingar med minimal dataförlust (beroende på replikeringsfrekvens) för oväntade haverier. Efter en redundansväxling, kan du växla tillbaka till dina primära platser. Site Recovery tillhandahåller återställningsplaner som kan innehålla skript och Azure Automation Runbook-rutiner så att du kan anpassa redundans och återställning av program med flera nivåer.
* **Eliminerar sekundära Datacenter**: Du kan replikera till en sekundär lokal plats eller till Azure. Använda Azure som mål för haveriberedskap slipper du kostnaderna och komplexiteten med att underhålla en sekundär plats. Replikerade data lagras i Azure Storage.
* **Integrerar med befintlig BCDR-teknik**: Site Recovery fungerar med andra program BCDR-funktioner. Du kan exempelvis använda Site Recovery för att skydda serverdelen för SQL Server för företagets arbetsbelastningar. Detta omfattar inbyggt stöd för SQL Server Always On att hantera redundans för Tillgänglighetsgrupper.

Läs mer:

* [Vad är Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Hur fungerar Azure Site Recovery?](../site-recovery/site-recovery-components.md)
* [Vilka arbetsbelastningar är skyddade med Azure Site Recovery?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtuellt nätverk

Virtuella datorer behöver nätverksanslutning. För att stödja detta krav, kräver Azure virtuella datorer som ska anslutas till en Azure-nätverk. 

Azure-nätverk är en logisk konstruktion som bygger på den fysiska Azure nätverksresurserna. Varje logisk Azure-nätverk är isolerat från alla andra Azure-nätverk. Denna isolering hjälper att se till att nätverkstrafik i dina distributioner inte är tillgänglig för andra Microsoft Azure-kunder.

Läs mer:

* [Översikt över Azure network security](security-network-overview.md)
* [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)
* [Nätverksfunktioner och partnerskap för enterprise-scenarier](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Hantering av säkerhetsprinciper och rapportering

Azure Security Center hjälper dig att förhindra, upptäcka och svara på hot. Security Center ger dig ökad insyn i och kontroll över säkerheten hos dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för alla Azure-prenumerationer. Det hjälper dig att identifiera hot som kan annars oupptäckta och fungerar med ett vittomfattande ekosystem med säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka säkerheten för dina virtuella datorer genom att:

* Att tillhandahålla [säkerhetsrekommendationer](../security-center/security-center-recommendations.md) för de virtuella datorerna. Exempel rekommendationer ges bland annat: tillämpa systemuppdateringar, konfigurera ACL: er slutpunkter, aktivera program mot skadlig kod, aktivera nätverkssäkerhetsgrupper och tillämpa diskkryptering.
* Övervaka tillståndet för dina virtuella datorer.

Läs mer:

* [Introduktion till Azure Security Center](../security-center/security-center-intro.md)
* [Vanliga och frågor svar om Azure Security Center](../security-center/security-center-faq.md)
* [Azure Security Center planerings- och](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Efterlevnad

Azure Virtual Machines är certifierat för FISMA, FedRAMP, HIPAA, PCI DSS Level 1 och andra viktiga efterlevnadsprogram. Denna certifiering gör det enklare för dina egna Azure-program att uppfylla efterlevnadskrav och för din verksamhet att hantera en mängd olika nationella och internationella bestämmelser.

Läs mer:

* [Microsoft Trust Center: efterlevnad](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Tillförlitliga molnet: Microsoft Azure-säkerhet, sekretess och efterlevnad](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Konfidentiellt databehandling

Medan konfidentiell databehandling inte tillhör tekniskt säkerhet för virtuella datorer, för avsnittet om säkerhet för virtuella datorer som tillhör ämnet på högre nivå av ”beräkning” säkerhet. Konfidentiellt databehandling tillhör inom kategorin ”beräkning” säkerhet. 

Konfidentiellt databehandling säkerställer att när data är ”i klartext”, vilket krävs för effektiv bearbetning, data skyddas i en betrodd körningsmiljö https://en.wikipedia.org/wiki/Trusted_execution_environment (TEE – även känt som en enklaven), i bilden nedan visas ett exempel där .  

T-stänger se till att det finns inget sätt att visa data eller åtgärder i utifrån, även med en felsökare. De säkerställer även att endast auktoriserade kod har behörighet att komma åt data. Om koden ändras eller manipulerats, åtgärderna som nekas och miljön inaktiveras. TEE framtvingar dessa skydd under körning av kod i den. 

Läs mer:

* [Introduktion till Azure konfidentiell databehandling](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Azure konfidentiell databehandling](https://azure.microsoft.com/blog/azure-confidential-computing/)  

