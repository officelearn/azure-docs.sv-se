---
title: Säkerhetsfunktioner som används med Azure Virtual Machines – Azure-säkerhet | Microsoft Docs
description: Den här artikeln innehåller en översikt över de centrala Azure-säkerhetsfunktioner som kan användas med Azure Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 186a0b0d4025f7d8a8888a9a0d2d0bd8832cf2d9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726493"
---
# <a name="azure-virtual-machines-security-overview"></a>Översikt över Azure Virtual Machines-säkerhet
Den här artikeln innehåller en översikt över de centrala Azure-säkerhetsfunktioner som kan användas med virtuella datorer.

Du kan använda Azure Virtual Machines för att distribuera en mängd olika data behandlings lösningar på ett flexibelt sätt. Tjänsten stöder Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP och Azure BizTalk Services. Så du kan distribuera alla arbets belastningar och språk på nästan alla operativ system.

En virtuell dator i Azure ger dig virtualiseringsflexibilitet utan att du behöver köpa och underhålla den fysiska maskinvara som den virtuella datorn körs på. Du kan bygga och distribuera dina program med garantier för att dina data är skyddade och säkra i mycket säkra data Center.

Med Azure kan du bygga säkerhets förbättrade och kompatibla lösningar som:

* Skydda dina virtuella datorer mot virus och skadlig kod.
* Kryptera dina känsliga data.
* Skydda nätverks trafik.
* Identifiera och identifiera hot.
* Uppfylla kraven för efterlevnad.  

## <a name="antimalware"></a>Program mot skadlig kod

Med Azure kan du använda program mot skadlig kod från säkerhets leverantörer som Microsoft, Symantec, Trend Micro och Kasper Sky. Den här program varan hjälper till att skydda dina virtuella datorer från skadliga filer, annons program och andra hot.

Microsoft Antimalware för Azure Cloud Services och Virtual Machines är en real tids skydds funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara.  Microsoft Antimalware för Azure tillhandahåller konfigurerbara aviseringar när kända skadlig eller oönskad program vara försöker installera sig själv eller köra på dina Azure-system.

Microsoft Antimalware för Azure är en lösning för en enskild agent för program-och klient miljöer. Den är utformad för att köras i bakgrunden utan mänsklig inblandning. Du kan distribuera skydd baserat på dina arbets belastningar för dina program, med antingen grundläggande säkerhet eller avancerad anpassad konfiguration, inklusive övervakning av program mot skadlig kod.

När du distribuerar och aktiverar Microsoft Antimalware för Azure är följande kärn funktioner tillgängliga:

* **Real tids skydd**: Övervakar aktivitet i Cloud Services och på Virtual Machines för att identifiera och blockera körning av skadlig kod.
* **Schemalagd genomsökning**: Utför regelbundet genomsökning för att identifiera skadlig kod, inklusive aktivt aktiva program.
* **Reparation av skadlig kod**: Vidtar automatiskt åtgärder vid identifierad skadlig kod, som att ta bort eller sätta skadliga filer och rensa skadliga register poster.
* **Uppdateringar av signatur**: Installerar automatiskt de senaste skydds signaturerna (virus definitioner) för att säkerställa att skyddet är uppdaterat på en fördefinierad frekvens.
* **Uppdateringar av motorn för program mot skadlig kod**: Uppdaterar automatiskt Microsoft Antimalware för Azure-motorn.
* **Plattforms uppdateringar för program mot skadlig kod**: Uppdaterar automatiskt Microsoft Antimalware för Azure-plattformen.
* **Aktivt skydd**: Rapporterar telemetri-metadata till Azure om identifierade hot och misstänkta resurser för att säkerställa snabba svar. Möjliggör överföring av synkron signatur i real tid via Microsoft Active Protection System (MAPS).
* **Exempel rapportering**: Innehåller och rapporterar exempel till Microsoft Antimalware för Azure-tjänsten för att förbättra tjänsten och aktivera fel sökning.
* **Undantag**: Gör det möjligt för program-och tjänst administratörer att konfigurera vissa filer, processer och enheter för att undanta dem från skydd och genomsökning av prestanda och andra orsaker.
* **Insamling av program mot skadlig kod**: Registrerar hälso tillstånd för program mot skadlig kod, misstänkta aktiviteter och reparations åtgärder som vidtagits i operativ systemets händelse logg och samlar in dem i ditt Azure Storage-konto.

Lär dig mer om program mot skadlig kod för att hjälpa till att skydda dina virtuella datorer:

* [Microsoft Antimalware för Azure Cloud Services och Virtual Machines](antimalware.md)
* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Så här installerar och konfigurerar du Trend Micro djupgående säkerhet som en tjänst på en virtuell Windows-dator](/azure/virtual-machines/windows/classic/install-trend)
* [Så här installerar och konfigurerar du Symantec Endpoint Protection på en virtuell Windows-dator](/azure/virtual-machines/windows/classic/install-symantec)
* [Säkerhetslösningar på Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Överväg att använda [Windows Defender Avancerat skydd](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)för ännu mer kraftfullt skydd. Med Windows Defender ATP får du:

* [Minskning av attack ytan](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Nästa generations skydd](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Endpoint Protection och-svar](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatiserad undersökning och reparation](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Säkra Poäng](/windows/security/threat-protection/windows-defender-atp/overview-secure-score-windows-defender-advanced-threat-protection)
* [Avancerad jakt](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Hantering och API: er](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Skydd mot Microsoft Threat](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Läs mer:

* [Kom igång med WDATP](/windows/security/threat-protection/windows-defender-atp/get-started)  
* [Översikt över WDATP-funktioner](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Modul för maskin varu säkerhet

Förbättrad nyckel säkerhet kan förbättra krypterings-och autentiserings skydd. Du kan förenkla hanteringen och säkerheten för viktiga hemligheter och nycklar genom att lagra dem i Azure Key Vault.

Med Key Vault kan du lagra dina nycklar i HSM:er (Hardware Security Modules) som är certifierade enligt standarden FIPS 140-2 nivå 2. Dina SQL Server krypterings nycklar för säkerhets kopiering eller [transparent data kryptering](https://msdn.microsoft.com/library/bb934049.aspx) kan lagras i Key Vault med alla nycklar och hemligheter från dina program. Behörigheter och åtkomst till dessa skyddade objekt hanteras via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Läs mer:

* [Vad är Azure Key Vault?](/azure/key-vault/key-vault-overview)
* [Azure Key Vault blogg](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Disk kryptering för virtuell dator

Azure Disk Encryption är en ny funktion för att kryptera dina Windows-och Linux-diskar för virtuella datorer. Azure Disk Encryption använder den branschstandardiserade [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) -funktionen i Windows och funktionen [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla volym kryptering för operativ systemet och data diskarna.

Lösningen är integrerad med Azure Key Vault som hjälper dig att styra och hantera disk krypterings nycklar och hemligheter i Key Vault-prenumerationen. Det säkerställer att alla data på de virtuella dator diskarna krypteras i vila i Azure Storage.

Läs mer:

* [Azure Disk Encryption för virtuella IaaS-datorer](/azure/security/azure-security-disk-encryption-overview)
* [Snabbstart: Kryptera en virtuell Windows IaaS-dator med Azure PowerShell](../azure-disk-encryption-linux-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Säkerhetskopiering av virtuell dator

Azure Backup är en skalbar lösning som hjälper dig att skydda dina program data utan kapital investering och minimala drifts kostnader. Programfel kan skada dina data och den mänskliga faktorn kan införa buggar i dina program. Med Azure Backup skyddas dina virtuella datorer som kör Windows och Linux.

Läs mer:

* [Vad är Azure Backup?](/azure/backup/backup-introduction-to-azure-backup)
* [Vanliga frågor och svar om Azure Backup-tjänsten](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

En viktig del av din organisations BCDR strategi är att ta reda på hur du håller företags arbets belastningar och appar som körs när planerade och oplanerade avbrott uppstår. Azure Site Recovery hjälper till att dirigera replikering, redundans och återställning av arbets belastningar och appar så att de är tillgängliga från en annan plats om din primära plats slutar fungera.

Site Recovery:

* **Fören klar din BCDR-strategi**: Site Recovery gör det enkelt att hantera replikering, redundans och återställning av flera arbets belastningar och appar från en enda plats. Site Recovery dirigerar replikering och redundans men fångar inte dina program data eller har ingen information om den.
* **Tillhandahåller flexibel replikering**: Med hjälp av Site Recovery kan du replikera arbets belastningar som körs på virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska Windows-och Linux-servrar.
* **Stöder redundans och återställning**: Site Recovery tillhandahåller redundanstest för att stödja haveri beredskap utan att påverka produktions miljöer. Du kan också köra planerade redundansväxlingar utan någon dataförlust för förväntade driftsavbrott, eller oplanerade redundansväxlingar med minimal dataförlust (beroende på replikeringsfrekvens) för oväntade haverier. Efter redundansväxlingen kan du växla tillbaka till dina primära platser. Site Recovery tillhandahåller återställningsplaner som kan innehålla skript och Azure Automation Runbook-rutiner så att du kan anpassa redundans och återställning av program med flera nivåer.
* **Eliminerar sekundära Data Center**: Du kan replikera till en sekundär lokal plats eller till Azure. Om du använder Azure som mål för haveri beredskap elimineras kostnaden och komplexiteten med att underhålla en sekundär plats. Replikerade data lagras i Azure Storage.
* **Integrerar med befintliga BCDR-tekniker**: Site Recovery partner med andra programs BCDR-funktioner. Du kan till exempel använda Site Recovery för att skydda SQL Server backend-delen av företags arbets belastningar. Detta inkluderar inbyggt stöd för SQL Server Always on för att hantera redundans för tillgänglighets grupper.

Läs mer:

* [Vad är Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Hur fungerar Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Vilka arbets belastningar skyddas av Azure Site Recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Virtuellt nätverk

Virtuella datorer behöver nätverks anslutning. För att stödja detta krav kräver Azure att virtuella datorer är anslutna till ett virtuellt Azure-nätverk.

Ett virtuellt Azure-nätverk är en logisk konstruktion som byggts ovanpå den fysiska Azures nätverks infrastruktur resurs. Varje logiskt virtuellt Azure-nätverk är isolerat från alla andra virtuella Azure-nätverk. Den här isoleringen hjälper till att säkerställa att nätverks trafiken i dina distributioner inte är tillgänglig för andra Microsoft Azure kunder.

Läs mer:

* [Översikt över Azure Network Security](network-overview.md)
* [Översikt över virtuella nätverk](/azure/virtual-network/virtual-networks-overview)
* [Nätverksfunktioner och partnerskap för företags scenarier](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Hantering och rapportering av säkerhets principer

Azure Security Center hjälper dig att förhindra, identifiera och svara på hot. Security Center ger ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Den ger integrerad säkerhetsövervakning och princip hantering i dina Azure-prenumerationer. Den hjälper till att upptäcka hot som annars kan gå vidare och fungerar med ett brett eko system med säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka säkerheten för dina virtuella datorer genom att:

* Tillhandahålla [säkerhets rekommendationer](/azure/security-center/security-center-recommendations) för de virtuella datorerna. Exempel på rekommendationer är: tillämpa system uppdateringar, konfigurera ACL-slutpunkter, aktivera program mot skadlig kod, aktivera nätverks säkerhets grupper och Använd disk kryptering.
* Övervaka statusen för dina virtuella datorer.

Läs mer:

* [Introduktion till Azure Security Center](/azure/security-center/security-center-intro)
* [Vanliga frågor och svar om Azure Security Center](/azure/security-center/security-center-faq)
* [Azure Security Center planering och åtgärder](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Efterlevnad

Azure Virtual Machines är certifierat för FISMA, FedRAMP, HIPAA, PCI DSS nivå 1 och andra viktiga compliance-program. Med den här certifieringen blir det enklare för dina egna Azure-program att uppfylla kraven på efterlevnad och för din verksamhet att hantera en mängd olika nationella och internationella myndighets krav.

Läs mer:

* [Microsoft säkerhets Center: Fastställ](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Betrott moln: Microsoft Azure säkerhet, sekretess och efterlevnad](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Konfidentiell dator användning

Även om konfidentiella data behandling inte är tekniskt en del av säkerheten för virtuella datorer, ingår avsnittet om säkerhet på den virtuella datorn på den högre nivån av "Compute"-säkerhet. Konfidentiell data behandling ingår i kategorin "Compute"-säkerhet.

Konfidentiell bearbetning garanterar att när data är "i klartext", vilket krävs för effektiv bearbetning, skyddas data i en betrodd körnings miljö https://en.wikipedia.org/wiki/Trusted_execution_environment (tee, även kallat en enklaven), ett exempel på som visas i bilden nedan .  

TEEs se till att det inte finns något sätt att visa data eller åtgärderna inuti utanför, även med en fel sökare. De säkerställer även att endast auktoriserad kod tillåts komma åt data. Om koden ändras eller manipuleras, nekas åtgärderna och miljön inaktive rad. TEE tillämpar dessa skydd i hela körningen av kod i den.

Läs mer:

* [Introduktion till Azures konfidentiella data behandling](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Azures konfidentiella data behandling](https://azure.microsoft.com/blog/azure-confidential-computing/)  
