---
title: "Azure säkerhetsfunktioner som används med virtuella Azure-datorer | Microsoft Docs"
description: " En översikt över säkerheten i Azure huvudfunktioner som kan användas med virtuella Azure-datorer. Virtuella Azure-datorer ger dig virtualiseringsflexibilitet utan att köpa och underhålla den fysiska maskinvara som kör den virtuella datorn. "
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
ms.date: 05/04/2017
ms.author: terrylan
ms.openlocfilehash: f1fb7f876c7dc010c03f01a4f6698ddc18da1100
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="azure-virtual-machines-security-overview"></a>Översikt över säkerheten i Azure virtuella datorer
Med Azure Virtual Machines kan du effektivt distribuera många olika databehandlingslösningar. Med support för Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP och Azure BizTalk Services kan du distribuera alla arbetsbelastningar och alla språk på nästan alla operativsystem.

En virtuell dator i Azure ger dig virtualiseringsflexibilitet utan att du behöver köpa och underhålla den fysiska maskinvara som den virtuella datorn körs på.  Du kan skapa och distribuera ditt program med försäkrar att dina data är skyddade och säker i våra mycket säkert datacenter.

Med Azure, kan du skapa utökad säkerhet, kompatibla lösningar som:

* Skydda dina virtuella datorer mot virus och skadlig kod
* Kryptera känsliga data
* Skydda nätverkstrafik
* Identifiera och upptäck hot
* Uppfyll efterlevnadskrav

Syftet med den här artikeln är att ge en översikt av grundläggande Azure säkerhetsfunktioner som kan användas med virtuella datorer. Vi kan också innehålla länkar till artiklar som ger information om varje funktion så kan du läsa mer.  

Virtuell dator i Azure security grundfunktionerna som omfattas i den här artikeln:

* Programvara mot skadlig kod
* Maskinvarusäkerhetsmodul
* Kryptering av virtuell dator
* Säkerhetskopiering av virtuell dator
* Azure Site Recovery
* Virtuella nätverk
* Hantering av säkerhetsprinciper och rapportering
* Efterlevnad

## <a name="antimalware"></a>Programvara mot skadlig kod
Du kan använda program mot skadlig kod från säkerhet leverantörer, till exempel Microsoft, Symantec, Trend Micro och Kaspersky med Azure, för att skydda dina virtuella datorer från skadliga filer, annonsprogram och andra hot. Se avsnittet lär dig mer nedan om du vill söka efter artiklar i partner lösningar.

Microsoft Antimalware för Azure-molntjänster och virtuella datorer är en realtidsskydd funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara.  Microsoft Antimalware innehåller konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera sig själva eller köras på Azure-system.

Microsoft Antimalware är en enskild agent lösning för program och klient-miljöer är avsedd att köras i bakgrunden utan mänsklig inblandning. Du kan distribuera skydd utifrån behoven i din programbelastningar, med antingen grundläggande secure-som-standard eller avancerad anpassad konfiguration, inklusive övervakning av program mot skadlig kod.

När du distribuerar och aktivera Microsoft Antimalware finns följande grundläggande funktioner:

* Realtidsskydd - Övervakare aktivitet i Cloud Services och på virtuella datorer för att identifiera och blockera skadlig kod körning.
* Schemalagd genomsökning - utför regelbundet riktade genomsökning för att upptäcka skadlig kod, inklusive program som körs aktivt.
* Korrigering av skadlig kod - vidtar automatiskt åtgärder på upptäckt skadlig kod, till exempel ta bort eller sätta i karantän skadliga filer och rensa skadliga registerposter.
* Signaturuppdateringar - automatiskt installerar de senaste signaturerna (virusdefinitioner av) att garantera skydd är uppdaterad på en förbestämd frekvens.
* Motorn för program mot skadlig kod uppdaterar – automatiskt uppdaterar Microsoft Antimalware-motorn.
* Program mot skadlig kod plattform uppdaterar – automatiskt uppdaterar Microsoft Antimalware-plattformen.
* Aktivt skydd - rapporter till Azure telemetri metadata om identifierade hot och misstänkta resurser för att säkerställa snabba svar och möjliggör realtid synkron signatur leverans via Microsoft Active Protection System (MAPS).
* Exempel för reporting - innehåller och rapporter prover till tjänsten Microsoft Antimalware för att förbättra tjänsten och aktivera felsökning.
* Undantag – gör att program och administratörer kan konfigurera vissa filer, processer, och enheter för att utesluta dem från skyddet och skanning för prestanda och andra orsaker.
* Program mot skadlig kod händelseinsamling - registrerar tjänstens hälsa för program mot skadlig kod, misstänkta aktiviteter och åtgärder som vidtagits i händelseloggen för operativsystemet och samlar in dem i kundens Azure Storage-konto.

Läs mer: Mer information om program mot skadlig kod för att skydda dina virtuella datorer finns:

* [Microsoft program mot skadlig kod för Azure-molntjänster och virtuella datorer](azure-security-antimalware.md)
* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Hur du installerar och konfigurerar Trend Micro djup Security som en tjänst på en Windows VM](../virtual-machines/windows/classic/install-trend.md)
* [Hur du installerar och konfigurerar Symantec Endpoint Protection på en Windows VM](../virtual-machines/windows/classic/install-symantec.md)
* [Säkerhetslösningar i Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Hardware security Module
Kryptering och autentisering skydd kan förbättras genom att förbättra nyckelsäkerhet. Du kan förenkla hantering och säkerhet för dina kritiska hemligheter och nycklar genom att lagra dem i Azure Key Vault. Med Key Vault kan du lagra dina nycklar i HSM:er (Hardware Security Modules) som är certifierade enligt standarden FIPS 140-2 nivå 2. Din SQL Server krypteringsnycklarna för säkerhetskopiering eller [transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx) kan alla lagras i Nyckelvalvet med alla nycklar och hemligheter från ditt program. Behörigheter och åtkomst till dessa skyddade objekt hanteras via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Läs mer:

* [Vad är Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md)
* [Azure Key Vault-blogg](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Kryptering av virtuell dator
Azure Disk Encryption är en ny funktion som gör att du krypterar din Windows- och Linux Azure virtuella diskar. Azure Disk Encryption använder branschstandarden [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows och [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux att tillhandahålla volymkryptering för Operativsystemet och datadiskar.

Lösningen är integrerad med Azure Key Vault som hjälper dig att styra och hantera disk krypteringsnycklar och hemligheter i prenumerationen nyckelvalv samtidigt som du säkerställer att krypteras alla data i virtuella diskar i vila i ditt Azure storage.

Läs mer:

* [Azure Disk Encryption för Windows och Linux-IaaS-VM](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [Azure Disk Encryption för Linux och Windows-datorer](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [Kryptera en virtuell dator](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Säkerhetskopiering av virtuell dator
Azure Backup är en skalbar lösning som skyddar dina programdata helt utan kapitalinvestering och med minimal driftskostnad. Programfel kan skada dina data och den mänskliga faktorn kan införa buggar i dina program. Dina virtuella datorer som kör Windows och Linux är skyddade med Azure Backup.

Läs mer:

* [Vad är Azure Backup?](../backup/backup-introduction-to-azure-backup.md)
* [Azure Backup Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Azure Backup Service – vanliga frågor och svar](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
En viktig del av din organisations BCDR-strategi är att komma fram till vad som krävs för att företagets arbetsbelastningar och appar ska fortsätta köra vid planerade och oplanerade driftavbrott. Azure Site Recovery hjälper samordna replikering, redundans och återställning av arbetsbelastningar och appar så att de är tillgängliga från en sekundär plats om den primära platsen kraschar.

Site Recovery:

* **Förenklar din BCDR-strategi** – Site Recovery gör det enkelt att hantera replikering, redundans och återställning av flera arbetsbelastningar och appar från en enda plats. Site Recovery samordnar replikering och redundans men kan inte komma åt dina programdata och har inte någon information om dem.
* **Tillhandahåller flexibel replikering** – med Site Recovery kan du replikera arbetsbelastningar som körs på Hyper-V virtuella datorer, virtuella VMware-datorer och Windows-/ Linux fysiska servrar.
* **Har stöd för redundans och återställning** – Site Recovery tillhandahåller redundanstestning så att den stöder haveriberedskap utan att påverka produktionsmiljöer. Du kan också köra planerade redundansväxlingar utan någon dataförlust för förväntade driftsavbrott, eller oplanerade redundansväxlingar med minimal dataförlust (beroende på replikeringsfrekvens) för oväntade haverier. Efter en redundans så kan du återställa till dina primära platser. Site Recovery tillhandahåller återställningsplaner som kan innehålla skript och Azure Automation Runbook-rutiner så att du kan anpassa redundans och återställning av program med flera nivåer.
* **Eliminerar sekundärt datacenter** – du kan replikera till en sekundär lokal plats eller till Azure. Med Azure som mål för haveriberedskap slipper du kostnaderna och komplexiteten med att underhålla en sekundär plats. Replikerade data lagras i Azure Storage.
* **Kan integreras med befintlig BCDR-teknik** – Site Recovery samarbetar med andra BCDR-programfunktioner. Du kan till exempel använda Site Recovery för att skydda SQL Server-serverdelen av företagets arbetsbelastningar. Detta inkluderar inbyggt stöd för SQL Server AlwaysOn för att hantera redundans för Tillgänglighetsgrupper.

Läs mer:

* [Vad är Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Hur fungerar Azure Site Recovery?](../site-recovery/site-recovery-components.md)
* [Vilka arbetsbelastningar skyddas av Azure Site Recovery?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtuella nätverk
Virtuella datorer måste nätverksanslutningen. För att uppfylla detta krav, kräver Azure virtuella datorer måste vara ansluten till ett Azure Virtual Network. Ett virtuellt Azure-nätverk är en logisk konstruktion som bygger på fysisk Azure nätverksinfrastruktur. Varje logiskt virtuella Azure-nätverk är isolerat från alla andra virtuella Azure-nätverk. Denna isolering kan försäkra nätverkstrafik i din distribution inte är tillgänglig för andra Microsoft Azure-kunder.

Läs mer:

* [Översikt över säkerheten i Azure-nätverk](security-network-overview.md)
* [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)
* [Nätverksfunktioner och partnerskap för företagsscenarier](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Hantering av säkerhetsprinciper och rapportering
Azure Security Center hjälper dig att förebygga, upptäcka och åtgärda hot och ger du ökad insyn i, och kontroll över säkerheten för dina Azure-resurser. Härifrån kan du övervaka och hantera principer för alla Azureprenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Azure Security Center hjälper dig att optimera och övervaka virtuella säkerhet med:

* Att tillhandahålla virtuella [säkerhetsrekommendationer](../security-center/security-center-recommendations.md) sådana som gäller systemuppdateringar, konfigurera ACL: er slutpunkter, aktivera program mot skadlig kod, aktivera nätverkssäkerhetsgrupper och gäller kryptering av.
* Övervaka status för dina virtuella datorer

Läs mer:

* [Introduktion till Azure Security Center](../security-center/security-center-intro.md)
* [Vanliga och frågor svar om Azure Security Center](../security-center/security-center-faq.md)
* [Planera för Azure Security Center och åtgärder](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Efterlevnad
Virtuella Azure-datorer är certifierad för FISMA, FedRAMP, HIPAA, PCI DSS nivå 1 och andra program som nyckel kompatibilitet. Den här certifikatutfärdare blir det lättare för din egen Azure-program att uppfylla krav på efterlevnad och för ditt företag att hantera en mängd olika nationella och internationella krav.

Läs mer:

* [Microsoft Trust Center: kompatibilitet](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
* [Betrodda moln: Microsoft Azure-säkerhet, sekretess och efterlevnad](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
