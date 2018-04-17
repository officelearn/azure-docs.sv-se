---
title: Säkerhetsmetoder för virtuell Azure-dator
description: Den här artikeln innehåller en mängd olika säkerhetsmetoder som ska användas i virtuella datorer i Azure.
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: ''
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: ddddf72d1ded0fb7952a641c287a4a5696e3aaef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="best-practices-for-azure-vm-security"></a>Metodtips för Virtuella Azure-säkerhet

I de flesta infrastruktur som en tjänst (IaaS)-scenarier [Azure virtuella datorer (VM)](https://docs.microsoft.com/azure/virtual-machines/) är huvudsakliga arbetsbelastningen för organisationer som använder cloud computing. Detta är särskilt tydligt i [hybridscenarion](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) där organisationer som vill migrera långsamt arbetsbelastningar till molnet. I sådana fall kan du följa den [allmänna säkerhetsaspekter för IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx), och gäller rekommenderade säkerhetsmetoder för dina virtuella datorer.

Den här artikeln beskrivs olika VM säkerhetsmetoder, var och en härledd från våra kunder och vår egen direkt upplevelser med virtuella datorer.

Bästa praxis är baserade på en konsensus meningar och de fungerar med den aktuella Azure-plattformen funktionerna och egenskapsuppsättningar. Eftersom åsikter och -tekniker kan förändras över tid, planerar vi att uppdatera den här artikeln regelbundet för att spegla ändringarna.

För varje rekommenderar förklaras artikeln:

* Vad det är bra.
* Varför är det en bra idé att aktivera den.
* Hur kan du lära dig att aktivera den.
* Vad som händer om du inte aktivera den.
* Möjliga alternativ till bästa praxis.

Artikeln går igenom de följande VM säkerhetsmetoderna:

* VM-autentisering och åtkomstkontroll
* Åtkomst till VM tillgänglighet och nätverk
* Skydda data i vila i virtuella datorer genom att tillämpa kryptering
* Hantera dina VM-uppdateringar
* Hantera dina VM säkerhetstillståndet
* Övervaka prestanda för VM

## <a name="vm-authentication-and-access-control"></a>VM-autentisering och åtkomstkontroll

Det första steget för att skydda den virtuella datorn är att se till att endast auktoriserade användare kan konfigurera nya virtuella datorer. Du kan använda [Azure principer](../azure-policy/azure-policy-introduction.md) skapa anpassade principer för att upprätta konventioner för resurser i din organisation, och använda dessa principer till resurser, exempelvis [resursgrupper](../azure-resource-manager/resource-group-overview.md).

Virtuella datorer som tillhör en resursgrupp naturligt ärver dess principer. Även om vi rekommenderar den här metoden för att hantera virtuella datorer, kan du också styra åtkomsten till enskilda VM-principer med hjälp av [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md).

När du aktiverar Resource Manager principer och RBAC för åtkomstkontroll för virtuell dator kan förbättra du övergripande VM-säkerhet. Vi rekommenderar att du sammanställa virtuella datorer med samma livscykel i samma resursgrupp. Med resursgrupper kan du distribuera, övervaka och dyker upp fakturering kostnaderna för dina resurser. Om du vill att användarna ska få åtkomst till och konfigurera virtuella datorer, Använd en [minsta privilegium metoden](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models). Och när du tilldelar behörigheter till användare som planerar att använda följande inbyggda Azure roller:

- [Virtual Machine-deltagare](../role-based-access-control/built-in-roles.md#virtual-machine-contributor): hantera virtuella datorer, men inte den virtuella nätverks- eller konto som de är anslutna.
- [Klassiska Virtual Machine-deltagare](../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): hantera virtuella datorer som skapats med hjälp av den klassiska distributionsmodellen, men inte den virtuella nätverks- eller konto som de virtuella datorerna är anslutna.
- [Säkerhetshanteraren](../role-based-access-control/built-in-roles.md#security-manager): hantera säkerhetskomponenter, säkerhetsprinciper och virtuella datorer.
- [DevTest Labs användaren](../role-based-access-control/built-in-roles.md#devtest-labs-user): kan visa allt och ansluta, starta, starta om och stänga av virtuella datorer.

Dela inte konton och lösenord för administratörer och inte återanvända lösenord i flera användarkonton eller tjänster, särskilt lösenord för sociala medier eller andra icke-administrativa aktiviteter. Helst bör du använda [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) mallar för att konfigurera dina virtuella datorer på ett säkert sätt. Genom att använda den här metoden kan du förbättra dina val för distribution och tillämpa säkerhetsinställningar under distributionen.

Organisationer som inte behöver använda data åtkomstkontroll genom att dra nytta av funktioner, till exempel RBAC kanske beviljar användarna fler behörigheter än vad som krävs. Olämpliga användaråtkomst till vissa data kan direkt påverka data.

## <a name="vm-availability-and-network-access"></a>Åtkomst till VM tillgänglighet och nätverk

Om den virtuella datorn kör kritiska program som behöver ha hög tillgänglighet, rekommenderar vi starkt att du använder flera virtuella datorer. För bättre tillgänglighet, skapar du minst två virtuella datorer i den [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md).

[Azure belastningsutjämnare](../load-balancer/load-balancer-overview.md) kräver också att Utjämning av nätverksbelastning virtuella datorer som tillhör samma tillgänglighetsuppsättning. Om dessa virtuella datorer måste kunna nås från Internet, måste du konfigurera en [Internetriktade belastningsutjämnare](../load-balancer/load-balancer-internet-overview.md).

När virtuella datorer är exponerad mot Internet, är det viktigt att du [styr flödet i nätverkstrafiken med nätverkssäkerhetsgrupper (NSG: er)](../virtual-network/virtual-networks-nsg.md). Eftersom NSG: er kan tillämpas på undernät, kan du minimera antalet NSG: er genom att gruppera dina resurser efter undernät och tillämpa NSG: er i undernät. Avsikten är att skapa ett lager för isolering av nätverk, vilket du kan göra genom att konfigurera den [nätverkssäkerhet](../best-practices-network-security.md) funktioner i Azure.

Du kan också använda just-in-time (JIT) VM-funktionen från Azure Security Center för att styra vem som har fjärråtkomst till en specifik virtuell dator, och hur lång tid.

Organisationer som inte framtvingar åtkomst till nätverket begränsningar för virtuella datorer mot Internet exponeras för säkerhetsrisker, till exempel en Remote Desktop Protocol (RDP) Brute Force-attacker.

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>Skydda data i vila i dina virtuella datorer genom att tillämpa kryptering

[Datakryptering i viloläge](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) är ett obligatoriskt steg mot data sekretess-, efterlevnads- och suveränitet. [Azure Disk Encryption](../security/azure-security-disk-encryption.md) kan IT-administratörer att kryptera Windows och Linux IaaS-VM-diskar. Diskkryptering kombinerar funktionen Windows BitLocker-standarden och Linux dm-crypt funktionen att ge volymkryptering för Operativsystemet och datadiskar.

Du kan använda Disk Encryption för att skydda dina data för att uppfylla organisationens säkerhets- och efterlevnadskrav. Företaget bör överväga att använda kryptering för att minska riskerna rör obehörig dataåtkomst. Vi rekommenderar också att du krypterar enheter innan du skriver känsliga data till dem.

Glöm inte att kryptera din VM datavolymer för att skydda dem i vila i Azure storage-konto. Skydda krypteringsnycklar och hemligheten med hjälp av [Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-whatis/).

Organisationer som inte behöver använda datakryptering exponeras mer att dataintegritet problem. Till exempel obehöriga eller icke-registrerade användare stjäla data i avslöjade konton eller få obehörig åtkomst till data som har kodats i ClearFormat. Förutom på sådana risker, för att uppfylla industrins föreskrifter måste företag bekräfta att de är utöva fordringar och använda rätt säkerhetsåtgärder för att förbättra sina datasäkerhet.

Mer information om diskkryptering finns [Azure Disk Encryption för Windows och Linux IaaS-VM](azure-security-disk-encryption.md).


## <a name="manage-your-vm-updates"></a>Hantera dina VM-uppdateringar

Eftersom virtuella Azure-datorer precis som alla lokala virtuella datorer är avsedd att vara användarhanterat, push Azure inte-Windows-uppdateringar till dem. Du är dock att lämna inställningen för automatisk uppdatering av Windows. Ett annat alternativ är att distribuera [Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) eller en annan lämplig uppdateringshantering produkt antingen på en annan virtuell dator eller lokalt. Behåll virtuella datorer aktuella både WSUS och Windows Update. Vi rekommenderar också att du använder en sökning produkt för att verifiera att alla IaaS-VM är uppdaterad.

Bilder som tillhandahålls av Azure uppdateras regelbundet för att inkludera den senaste runda av Windows-uppdateringar. Det finns dock ingen garanti för att bilderna är aktuella vid tidpunkten för distribution. En liten fördröjning (av fler än några veckor) följande offentliga versioner är möjligt. Söker efter och installera alla Windows-uppdateringar ska vara det första steget för varje distribution. Det här måttet är särskilt viktigt att tillämpa när du distribuerar avbildningar som kommer från dig eller dina egna bibliotek. Bilder som har angetts som en del av Azure Marketplace uppdateras automatiskt som standard.

Organisationer som inte tillämpa programuppdateringen principer exponeras mer hot som drar nytta av tidigare fast kända säkerhetsproblem. Förutom riskera sådana hot att följa industrins föreskrifter måste företag bekräfta att de är utöva fordringar och använder rätt säkerhetsåtgärder för att säkerställa säkerheten för arbetsbelastningen i molnet.

Det är viktigt att betona att programuppdateringen Metodtips för vanliga datacenter och Azure IaaS har många likheter. Därför rekommenderar vi att du utvärderar uppdatering i principer för att inkludera virtuella datorer i programvara.

## <a name="manage-your-vm-security-posture"></a>Hantera dina VM säkerhetstillståndet

Cyber hot är under utveckling och skydda dina virtuella datorer kräver en omfattande övervakning kapaciteten som kan snabbt identifiera hot, förhindra obehörig åtkomst till dina resurser, utlöser aviseringar och falsklarm. Säkerhetstillståndet för sådana arbetsbelastning omfattar alla säkerhetsaspekterna av den virtuella datorn från uppdateringshantering för säker åtkomst till nätverket.

Övervaka säkerhetstillståndet av din [Windows](../security-center/security-center-virtual-machine.md) och [virtuella Linux-datorer](../security-center/security-center-linux-virtual-machine.md), använda [Azure Security Center](../security-center/security-center-intro.md). Skydda dina virtuella datorer i Azure Security Center genom att dra nytta av följande funktioner:

* Tillämpa säkerhetsinställningar för OS med rekommenderade konfigurationsregler
* Identifiera och ladda ned system säkerhetsuppdateringar och viktiga uppdateringar som saknas
* Distribuera Endpoint program mot skadlig kod protection rekommendationer
* Validera diskkryptering
* Utvärdera och åtgärda problem
* Identifiera hot

Security Center kan övervaka aktivt för hot och potentiella hot som exponeras **säkerhetsaviseringar**. Korrelerade hot aggregeras i en enda vy som kallas **säkerhetsincident**.

Titta på följande videoklipp för att förstå hur Security Center kan hjälpa dig att identifiera potentiella hot i dina virtuella datorer finns i Azure:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player]

Organisationer som inte framtvingar ett starkt säkerhetstillståndet för sina virtuella datorer vara medveten om eventuella försök av obehöriga användare att kringgå upprättade säkerhetsåtgärder.

## <a name="monitor-vm-performance"></a>Övervaka prestanda för VM

Missbruk av resursen kan vara ett problem när VM processer använder mer resurser än de ska. Prestandaproblem med en virtuell dator kan leda till avbrott i tjänsten, som bryter mot säkerhetsprincipen för tillgänglighet. Därför är det viktigt att övervaka VM åtkomst inte bara reaktivt när ett problem uppstår, men även proaktivt mot baslinjeprestanda mätt under normal drift.

Genom att analysera [Azure diagnostikloggfiler](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), kan du övervaka dina VM-resurser och identifiera potentiella problem som kan påverka datorns prestanda och tillgänglighet. Azure Diagnostics tillägget innehåller övervakning och diagnostikfunktionerna för Windows-baserade virtuella datorer. Du kan aktivera dessa funktioner genom att inkludera tillägget som en del av den [Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md).

Du kan också använda [Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) insyn i din resurs hälsa.

Organisationer som inte övervakar prestanda för virtuell dator kan inte avgöra om vissa ändringar i prestandamönster normal eller onormal. Om den virtuella datorn förbrukar mer resurser än normalt kan dessa avvikelser tyda på potentiella från en extern resurs eller en komprometterad process som körs på den virtuella datorn.
