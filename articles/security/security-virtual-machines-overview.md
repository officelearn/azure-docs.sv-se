---
title: Azure säkerhetsfunktioner som används med virtuella Azure-datorer | Microsoft Docs
description: Den här artikeln innehåller en översikt över grundläggande Azure säkerhetsfunktioner som kan användas med Azure Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 7d31a434d4ead6dd8f8a13a08d368389904b5b4a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364241"
---
# <a name="azure-virtual-machines-security-overview"></a>Översikt över säkerheten i Azure virtuella datorer
Du kan använda Azure virtuella datorer för att distribuera en mängd olika lösningar för datorer i en flexibel metod. Tjänsten stöder Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP och Azure BizTalk-tjänst. Så kan du distribuera alla arbetsbelastningar och alla språk på nästan alla operativsystem.

En virtuell dator i Azure ger dig virtualiseringsflexibilitet utan att du behöver köpa och underhålla den fysiska maskinvara som den virtuella datorn körs på. Du kan skapa och distribuera ditt program med försäkrar att dina data är skyddade och säker i mycket säkra datacenter.

Med Azure, kan du skapa utökad säkerhet, kompatibla lösningar som:

* Skydda dina virtuella datorer från virus och skadlig kod.
* Kryptera känsliga data.
* Säkra nätverkstrafik.
* Identifiera och identifiera hot.
* Uppfylla krav på efterlevnad.

Syftet med den här artikeln är att ge en översikt av grundläggande Azure säkerhetsfunktioner som kan användas med virtuella datorer. Länkar till artiklar ger information om varje funktion så kan du läsa mer.  

## <a name="antimalware"></a>Programvara mot skadlig kod
Med Azure, kan du använda program mot skadlig kod från säkerhet leverantörer, till exempel Microsoft, Symantec, Trend Micro och Kaspersky. Den här programvaran skyddar dina virtuella datorer från skadliga filer, annonsprogram och andra hot.

Microsoft Antimalware för Azure-molntjänster och virtuella datorer är en realtidsskydd funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara.  Microsoft Antimalware för Azure tillhandahåller konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera sig själva eller köras på Azure-system.

Microsoft Antimalware för Azure är en enskild agent lösning för program och klient-miljöer. Den har utformats för att köras i bakgrunden utan mänsklig inblandning. Du kan distribuera skydd utifrån behoven i din programbelastningar, med antingen grundläggande secure-som-standard eller avancerad anpassad konfiguration, inklusive övervakning av program mot skadlig kod.

När du distribuerar och aktivera Microsoft Antimalware för Azure finns följande grundläggande funktioner:

* **Realtidsskydd**: övervakar aktiviteten i Cloud Services och på virtuella datorer för att identifiera och blockera skadlig kod körning.
* **Schemalagd genomsökning**: regelbundet utför riktade genomsökning för att upptäcka skadlig kod, inklusive program som körs aktivt.
* **Korrigering av skadlig kod**: automatiskt vidtar åtgärder på upptäckt skadlig kod, till exempel ta bort eller sätta i karantän skadliga filer och rensa skadliga registerposter.
* **Signaturuppdateringar**: installerar automatiskt de senaste signaturerna (virusdefinitioner av) för att säkerställa att skyddet är uppdaterad på en förbestämd frekvens.
* **Uppdateringar för program mot skadlig kod motorn**: uppdateras automatiskt Microsoft Antimalware för Azure-motorn.
* **Uppdateringar för program mot skadlig kod plattform**: uppdateras automatiskt Microsoft Antimalware för Azure-plattformen.
* **Aktivt skydd**: rapporter telemetri metadata till Azure om identifierade hot och misstänkta resurser för att säkerställa snabba svar. Aktiverar realtid synkron signatur leverans via Microsoft Active Protection System (MAPS).
* **Exempel reporting**: ger och rapporterar prover till Microsoft Antimalware för Azure-tjänsten för att förbättra tjänsten och aktivera felsökning.
* **Undantag**: gör att program och administratörer kan konfigurera vissa filer, processer, och enheter för att utesluta dem från skyddet och skanning för prestanda och andra orsaker.
* **Program mot skadlig kod händelseinsamling**: registrerar tjänstens hälsa för program mot skadlig kod, misstänkta aktiviteter och åtgärder som vidtagits i händelseloggen för operativsystemet och samlar in dem i Azure storage-konto.

Mer information om program mot skadlig kod för att skydda dina virtuella datorer:

* [Microsoft program mot skadlig kod för Azure-molntjänster och virtuella datorer](azure-security-antimalware.md)
* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Hur du installerar och konfigurerar Trend Micro djup Security som en tjänst på en Windows VM](../virtual-machines/windows/classic/install-trend.md)
* [Hur du installerar och konfigurerar Symantec Endpoint Protection på en Windows VM](../virtual-machines/windows/classic/install-symantec.md)
* [Säkerhetslösningar i Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Maskinvarusäkerhetsmodul
Förbättra nyckelsäkerhet kan förbättra kryptering och autentisering skydd. Du kan förenkla hantering och säkerhet för dina kritiska hemligheter och nycklar genom att lagra dem i Azure Key Vault. 

Med Key Vault kan du lagra dina nycklar i HSM:er (Hardware Security Modules) som är certifierade enligt standarden FIPS 140-2 nivå 2. Din SQL Server krypteringsnycklarna för säkerhetskopiering eller [transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx) kan alla lagras i Nyckelvalvet med alla nycklar och hemligheter från ditt program. Behörigheter och åtkomst till dessa skyddade objekt hanteras via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Läs mer:

* [Vad är Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md)
* [Azure Key Vault-blogg](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Kryptering av virtuell dator
Azure Disk Encryption är en ny funktion för att kryptera din Windows- och Linux virtuella diskar. Azure Disk Encryption använder branschstandarden- [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows och [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux att tillhandahålla volymkryptering för Operativsystemet och datadiskar.

Lösningen är integrerad med Azure Key Vault som hjälper dig att styra och hantera disk krypteringsnycklar och hemligheter i nyckelvalvet-prenumeration. Det innebär att krypteras alla data i virtuella diskar i vila i Azure Storage.

Läs mer:

* [Azure Disk Encryption för Windows och Linux-IaaS-VM](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [Azure Disk Encryption för Linux och Windows-datorer](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [Kryptera en virtuell dator](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Säkerhetskopiering av virtuell dator
Azure-säkerhetskopiering är en skalbar lösning som hjälper dig att skydda dina programdata med noll kapitalinvesteringar och minimala driftskostnader. Programfel kan skada dina data och den mänskliga faktorn kan införa buggar i dina program. Dina virtuella datorer som kör Windows och Linux är skyddade med Azure Backup.

Läs mer:

* [Vad är Azure Backup?](../backup/backup-introduction-to-azure-backup.md)
* [Azure Backup Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Azure Backup-tjänsten och svar](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
En viktig del av din organisations BCDR-strategi är att räkna ut hur du kan skydda företagets arbetsbelastningar och appar som körs när du planerade och oplanerade avbrott inträffar. Azure Site Recovery hjälper samordna replikering, redundans och återställning av arbetsbelastningar och appar så att de är tillgängliga från en sekundär plats om den primära platsen kraschar.

Site Recovery:

* **Förenklar din BCDR-strategi**: Site Recovery gör det enkelt att hantera replikering, redundans och återställning av flera arbetsbelastningar och appar från en enda plats. Site Recovery samordnar replikering och redundans men inte komma åt dina programdata eller har inte någon information om den.
* **Tillhandahåller flexibel replikering**: genom att använda Site Recovery kan du replikera arbetsbelastningar som körs på Hyper-V virtuella datorer, virtuella VMware-datorer och Windows-/ Linux fysiska servrar.
* **Har stöd för redundans och återställning**: Site Recovery tillhandahåller redundanstestning så att den stöder haveriberedskap utan att påverka produktionsmiljöer. Du kan också köra planerade redundansväxlingar utan någon dataförlust för förväntade driftsavbrott, eller oplanerade redundansväxlingar med minimal dataförlust (beroende på replikeringsfrekvens) för oväntade haverier. Efter växling vid fel, kan du växla tillbaka till dina primära platser. Site Recovery tillhandahåller återställningsplaner som kan innehålla skript och Azure Automation Runbook-rutiner så att du kan anpassa redundans och återställning av program med flera nivåer.
* **Eliminerar sekundärt Datacenter**: du kan replikera till en sekundär lokal plats eller till Azure. Med Azure som mål för haveriberedskap slipper du kostnaderna och komplexiteten med att underhålla en sekundär plats. Replikerade data lagras i Azure Storage.
* **Kan integreras med befintlig BCDR-teknik**: Site Recovery samarbetar med andra program BCDR-programfunktioner. Du kan till exempel använda Site Recovery för att skydda SQL Server-serverdelen av företagets arbetsbelastningar. Detta inkluderar inbyggt stöd för SQL Server alltid aktiverad att hantera redundans för Tillgänglighetsgrupper.

Läs mer:

* [Vad är Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Hur fungerar Azure Site Recovery?](../site-recovery/site-recovery-components.md)
* [Vilka arbetsbelastningar skyddas av Azure Site Recovery?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtuellt nätverk
Virtuella datorer måste nätverksanslutningen. För att uppfylla detta krav, kräver Azure virtuella datorer måste vara ansluten till ett virtuellt Azure-nätverk. 

Azure-nätverk är en logisk konstruktion som bygger på fysisk Azure nätverksinfrastruktur. Varje logiska Azure-nätverk är isolerat från alla andra virtuella Azure-nätverk. Denna isolering kan försäkra nätverkstrafik i din distribution inte är tillgänglig för andra Microsoft Azure-kunder.

Läs mer:

* [Översikt över säkerheten i Azure-nätverk](security-network-overview.md)
* [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)
* [Nätverksfunktioner och partnerskap för företagsscenarier](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Hantering av säkerhetsprinciper och rapportering
Azure Security Center hjälper dig att förebygga, upptäcka och åtgärda hot. Security Center får du ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer. Det hjälper dig att identifiera hot som annars kanske skulle förbli oupptäckta och fungerar med ett vittomfattande ekosystem med säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka säkerheten för dina virtuella datorer genom att:

* Att tillhandahålla [säkerhetsrekommendationer](../security-center/security-center-recommendations.md) för virtuella datorer. Exempel rekommendationerna omfattar: tillämpa uppdateringar, konfigurera ACL: er slutpunkter, aktivera program mot skadlig kod, aktivera nätverkssäkerhetsgrupper och tillämpa diskkryptering.
* Övervaka status för virtuella datorer.

Läs mer:

* [Introduktion till Azure Security Center](../security-center/security-center-intro.md)
* [Vanliga och frågor svar om Azure Security Center](../security-center/security-center-faq.md)
* [Planera för Azure Security Center och åtgärder](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Efterlevnad
Virtuella Azure-datorer är certifierad för FISMA, FedRAMP, HIPAA, PCI DSS nivå 1 och andra program som nyckel kompatibilitet. Den här certifikatutfärdare blir det lättare för din egen Azure-program att uppfylla krav på efterlevnad och för ditt företag att hantera en mängd olika nationella och internationella krav.

Läs mer:

* [Microsoft Trust Center: kompatibilitet](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
* [Betrodda moln: Microsoft Azure-säkerhet, sekretess och efterlevnad](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
