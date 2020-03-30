---
title: Säkerhetsfunktioner som används med virtuella Azure-datorer
titleSuffix: Azure security
description: Den här artikeln innehåller en översikt över de grundläggande Azure-säkerhetsfunktioner som kan användas med Virtuella Azure-datorer.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: a1726e18ea8c1ba86d77d7b9ca3d50c444620361
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657171"
---
# <a name="azure-virtual-machines-security-overview"></a>Säkerhetsöversikt över virtuella azure-datorer
Den här artikeln innehåller en översikt över de grundläggande Azure-säkerhetsfunktioner som kan användas med virtuella datorer.

Du kan använda virtuella Azure-datorer för att distribuera ett brett utbud av datorlösningar på ett flexibelt sätt. Tjänsten stöder Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP och Azure BizTalk Services. Så du kan distribuera alla arbetsbelastningar och alla språk på nästan alla operativsystem.

En virtuell dator i Azure ger dig virtualiseringsflexibilitet utan att du behöver köpa och underhålla den fysiska maskinvara som den virtuella datorn körs på. Du kan skapa och distribuera dina program med försäkran om att dina data är skyddade och säkra i mycket säkra datacenter.

Med Azure kan du skapa säkerhetsförbättrade, kompatibla lösningar som:

* Skydda dina virtuella datorer från virus och skadlig kod.
* Kryptera dina känsliga data.
* Säker nätverkstrafik.
* Identifiera och upptäcka hot.
* Uppfylla efterlevnadskraven.  

## <a name="antimalware"></a>Antimalware

Med Azure kan du använda programvara mot skadlig kod från säkerhetsleverantörer som Microsoft, Symantec, Trend Micro och Kaspersky. Den här programvaran hjälper till att skydda dina virtuella datorer från skadliga filer, adware och andra hot.

Microsoft Antimalware för Azure Cloud Services och Virtuella datorer är en realtidsskyddsfunktion som hjälper till att identifiera och ta bort virus, spionprogram och annan skadlig programvara.  Microsoft Antimalware för Azure ger konfigurerbara aviseringar när kända skadliga eller oönskade program försöker installera sig själv eller köras på dina Azure-system.

Microsoft Antimalware för Azure är en lösning med en enda agent för program och klientmiljöer. Den är utformad för att köras i bakgrunden utan mänsklig inblandning. Du kan distribuera skydd baserat på behoven hos dina programarbetsbelastningar, med antingen grundläggande säker standard eller avancerad anpassad konfiguration, inklusive övervakning mot skadlig kod.

Läs mer om [Microsoft Antimalware för Azure](antimalware.md) och de grundläggande funktionerna som är tillgängliga.

Läs mer om programvara mot skadlig kod för att skydda dina virtuella datorer:

* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Så här installerar och konfigurerar du Trend Micro Deep Security som en tjänst på en Virtuell Windows-dator](/azure/virtual-machines/windows/classic/install-trend)
* [Installera och konfigurera Symantec-slutpunktsskydd på en virtuell dator med Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Säkerhetslösningar på Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

För ännu mer kraftfullt skydd, överväga att använda [Windows Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Med Windows Defender ATP får du:

* [Minskning av angreppsytan](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Nästa generations skydd](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Skydd och svar på slutpunkt](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatiserad utredning och reparation](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Säkerhetspoäng](/windows/security/threat-protection/microsoft-defender-atp/configuration-score)
* [Avancerad jakt](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Hantering och API:er](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsofts hotskydd](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Läs mer:

* [Komma igång med WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Översikt över WDATP-funktioner](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Säkerhetsmodul för maskinvara

Förbättrad nyckelsäkerhet kan förbättra krypterings- och autentiseringsskydd. Du kan förenkla hanteringen och säkerheten för dina kritiska hemligheter och nycklar genom att lagra dem i Azure Key Vault.

Key Vault ger möjlighet att lagra dina nycklar i maskinvarusäkerhetsmoduler (HSM) certifierade enligt FIPS 140-2 Nivå 2-standarder. Dina SQL Server-krypteringsnycklar för säkerhetskopiering eller [transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx) kan alla lagras i Key Vault med nycklar eller hemligheter från dina program. Behörigheter och åtkomst till dessa skyddade objekt hanteras via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Läs mer:

* [Vad är Azure Key Vault?](/azure/key-vault/key-vault-overview)
* [Blogg för Azure Key Vault](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Diskkryptering för virtuell dator

Azure Disk Encryption är en ny funktion för att kryptera dina Windows- och Linux-diskar för virtuella datorer. Azure Disk Encryption använder [bitlockerfunktionen i](https://technet.microsoft.com/library/cc732774.aspx) branschstandard i Windows och [dm-crypt-funktionen](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla volymkryptering för operativsystemet och datadiskarna.

Lösningen är integrerad med Azure Key Vault för att hjälpa dig att styra och hantera diskkrypteringsnycklarna och hemligheterna i din nyckelvalvsprenumeration. Det säkerställer att alla data i den virtuella datorn diskar krypteras i vila i Azure Storage.

Läs mer:

* [Virtuella Azure-diskkryptering för virtuella IaaS-datorer](/azure/security/azure-security-disk-encryption-overview)
* [Snabbstart: Kryptera en virtuell Windows IaaS-dator med Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Säkerhetskopiering av virtuella datorer

Azure Backup är en skalbar lösning som hjälper till att skydda dina programdata med noll kapitalinvesteringar och minimala driftskostnader. Programfel kan skada dina data och mänskliga fel kan införa buggar i dina program. Med Azure Backup är dina virtuella datorer som kör Windows och Linux skyddade.

Läs mer:

* [Vad är Azure Backup?](/azure/backup/backup-introduction-to-azure-backup)
* [Vanliga frågor och svar om Azure Backup-tjänst](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

En viktig del av organisationens BCDR-strategi är att ta reda på hur du håller företagets arbetsbelastningar och appar igång när planerade och oplanerade avbrott inträffar. Azure Site Recovery hjälper till att dirigera replikering, redundans och återställning av arbetsbelastningar och appar så att de är tillgängliga från en sekundär plats om din primära plats går ner.

Återställning av webbplatsen:

* **Förenklar din BCDR-strategi**: Site Recovery gör det enkelt att hantera replikering, redundans och återställning av flera affärsarbetsbelastningar och appar från en enda plats. Site Recovery dirigerar replikering och redundans men avlyssnar inte dina programdata eller har någon information om dem.
* **Ger flexibel replikering:** Genom att använda Site Recovery kan du replikera arbetsbelastningar som körs på virtuella Hyper-V-datorer, virtuella datorer med VMware och Windows/Linux fysiska servrar.
* **Stöder redundans och återställning:** Site Recovery tillhandahåller testundansväxlingar för att stödja haveriberedskapsövningar utan att påverka produktionsmiljöer. Du kan också köra planerade redundansväxlingar utan någon dataförlust för förväntade driftsavbrott, eller oplanerade redundansväxlingar med minimal dataförlust (beroende på replikeringsfrekvens) för oväntade haverier. Efter redundans kan du växla tillbaka till dina primära platser. Site Recovery tillhandahåller återställningsplaner som kan innehålla skript och Azure Automation Runbook-rutiner så att du kan anpassa redundans och återställning av program med flera nivåer.
* **Eliminerar sekundära datacenter:** Du kan replikera till en sekundär lokal plats eller till Azure. Om du använder Azure som mål för haveriberedskap elimineras kostnaden och komplexiteten för att underhålla en sekundär plats. Replikerade data lagras i Azure Storage.
* **Integrerar med befintliga BCDR-tekniker:** Site Recovery samarbetar med andra programs BCDR-funktioner. For example, you can use Site Recovery to help protect the SQL Server back end of corporate workloads. Detta inkluderar inbyggt stöd för SQL Server Always On för att hantera redundans av tillgänglighetsgrupper.

Läs mer:

* [Vad är Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Hur fungerar Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Vilka arbetsbelastningar skyddas av Azure Site Recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Virtuellt nätverk

Virtuella datorer behöver nätverksanslutning. För att stödja detta krav kräver Azure virtuella datorer som ska anslutas till ett virtuellt Azure-nätverk.

Ett virtuellt Azure-nätverk är en logisk konstruktion som bygger på den fysiska Azure-nätverksstrukturen. Varje logiskt Azure virtuellt nätverk är isolerat från alla andra virtuella Azure-nätverk. Den här isoleringen säkerställer att nätverkstrafik i dina distributioner inte är tillgänglig för andra Microsoft Azure-kunder.

Läs mer:

* [Översikt över Azure-nätverkssäkerhet](network-overview.md)
* [Översikt över virtuellt nätverk](/azure/virtual-network/virtual-networks-overview)
* [Nätverksfunktioner och partnerskap för företagsscenarier](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Hantering och rapportering av säkerhetspolicyer

Azure Security Center hjälper dig att förebygga, identifiera och reagera på hot. Security Center ger dig ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Det ger integrerad säkerhetsövervakning och principhantering över dina Azure-prenumerationer. Det hjälper till att upptäcka hot som annars skulle gå obemärkt förbi och fungerar med ett brett ekosystem av säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka säkerheten för dina virtuella datorer genom att:

* Tillhandahålla [säkerhetsrekommendationer](/azure/security-center/security-center-recommendations) för de virtuella datorerna. Exempel på rekommendationer är: tillämpa systemuppdateringar, konfigurera ACL-slutpunkter, aktivera skadlig kod, aktivera nätverkssäkerhetsgrupper och tillämpa diskkryptering.
* Övervaka tillståndet för dina virtuella datorer.

Läs mer:

* [Introduktion till Azure Security Center](/azure/security-center/security-center-intro)
* [Vanliga frågor och svar i Azure Security Center](/azure/security-center/security-center-faq)
* [Planering och åtgärder för Azure Security Center](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Efterlevnad

Virtuella Azure-datorer är certifierade för FISMA, FedRAMP, HIPAA, PCI DSS-nivå 1 och andra viktiga efterlevnadsprogram. Den här certifieringen gör det enklare för dina egna Azure-program att uppfylla efterlevnadskraven och för ditt företag att hantera ett brett spektrum av inhemska och internationella regelkrav.

Läs mer:

* [Microsoft Trust Center: Efterlevnad](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Trusted Cloud: Microsoft Azure-säkerhet, sekretess och efterlevnad](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Konfidentiell databehandling

Även om konfidentiell databehandling inte är tekniskt en del av säkerheten för virtuella datorer, hör ämnet för säkerhet på virtuella datorer till ämnet "beräkningssäkerhet" på högre nivå. Konfidentiell databehandling hör hemma inom kategorin "beräkningssäkerhet".

Konfidentiell databehandling säkerställer att när data är "i klartext", vilket krävs för https://en.wikipedia.org/wiki/Trusted_execution_environment effektiv bearbetning, är data skyddade i en betrodd körningsmiljö (TEE - även känd som en enklav), ett exempel som visas i figuren nedan.  

TEN säkerställer att det inte finns något sätt att visa data eller åtgärder inuti från utsidan, även med en felsökare. De säkerställer även att endast auktoriserad kod är tillåten att komma åt data. Om koden ändras eller manipuleras nekas åtgärderna och miljön inaktiveras. TEE tillämpar dessa skydd under hela utförandet av kod inom den.

Läs mer:

* [Introduktion till Azure-konfidentiell databehandling](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Azure konfidentiell databehandling](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Nästa steg

Läs mer om [metodtips](iaas.md) för säkerhet för virtuella datorer och operativsystem.
