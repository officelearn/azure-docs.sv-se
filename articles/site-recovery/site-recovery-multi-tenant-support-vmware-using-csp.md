---
title: "Stöd för flera innehavare för VMware VM replikering till Azure (CSP-program) | Microsoft Docs"
description: "Beskriver hur du distribuerar Azure Site Recovery i en miljö med flera innehavare att samordna replikering, redundans och återställning av lokala virtuella VMware-datorer (VM) till Azure via CSP-programmet med hjälp av Azure portal"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: manayar
ms.openlocfilehash: 273efe0bdef421d753ea51e01060d48351cbe6fc
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Stöd för flera innehavare i Azure Site Recovery för att replikera virtuella VMware-datorer till Azure via CSP

Azure Site Recovery har stöd för miljöer med flera innehavare för klient-prenumerationer. Det stöder också flera innehavare för klient-prenumerationer som skapas och hanteras via programmet Microsoft Cloud Solution Providers (CSP). Den här artikeln beskrivs vägledning för att implementera och hantera flera innehavare VMware-Azure-scenarier. Mer information om att skapa och hantera innehavare prenumerationer finns [hanterar flera innehavare med CSP](site-recovery-manage-multi-tenancy-with-csp.md) .

Den här vägledningen ritar kraftigt från den befintliga dokumentationen för att replikera virtuella VMware-datorer till Azure. Mer information finns i [replikera VMware-datorer till Azure med Site Recovery](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Miljöer med flera innehavare
Det finns tre huvudsakliga modeller för flera innehavare:

* **Delade värd Services Provider (HSP)**: partnern som äger den fysiska infrastrukturen och använder delade resurser (vCenter, Datacenter, fysisk lagring och så vidare) som värd för flera innehavare virtuella datorer på samma infrastruktur. Partnern kan tillhandahålla katastrofåterställning hantering som en hanterad tjänst eller klienten kan äga katastrofåterställning som en lösning för självbetjäning.

* **Dedikerad värd tjänsteleverantör**: partnern som äger den fysiska infrastrukturen, men använder dedicerade resurser (flera Vcenter fysiska datastores och så vidare) som värd för varje klient virtuella datorer på en separat infrastruktur. Partnern kan tillhandahålla katastrofåterställning hantering som en hanterad tjänst eller klienten kan äga som en lösning för självbetjäning.

* **Hanterade Services Provider (MSP)**: kunden äger den fysiska infrastrukturen som är värd för de virtuella datorerna och partnern tillhandahåller katastrofåterställning aktivering och hantering.

## <a name="shared-hosting-multi-tenant-guidance"></a>Delade riktlinjer för flera innehavare
Det här avsnittet beskriver scenariot delade i detalj. De två scenarierna är delmängder av scenariot delade och de använder samma principer. Skillnaderna beskrivs i slutet av den delade vägledningen.

Grundläggande krav i ett scenario med flera innehavare är att isolera olika klienter. En klient ska inte kunna se vad en annan innehavare har värdet hosted. Det här kravet är inte lika viktigt i en självbetjäning miljö där det kan vara avgörande i en partner-hanterad miljö. Dessa riktlinjer förutsätter att klientisolering krävs.

Arkitekturen visas i följande diagram:

![Delad HSP med en vCenter](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**Delade scenario med en vCenter**

Som visas i föregående diagram har varje kund en separat hanteringsserver. Denna konfiguration begränsar klient har åtkomst till klient-specifika virtuella datorer och aktiverar klientisolering. Ett scenario med VMware replikeringen av den virtuella datorn använder konfigurationsservern för att hantera konton för att identifiera virtuella datorer och installera agenter. Samma principer som gäller för miljöer med flera innehavare med tillägget för att begränsa VM identifiering via vCenter-åtkomstkontroll.

Krav för isolering av data kräver att alla känsliga infrastruktur (till exempel autentiseringsuppgifter) förblir hemlig till innehavare. Därför rekommenderar vi att alla komponenter i hanteringsservern förblir under exklusiv kontroll av partnern. Management server-komponenter är:
* Konfigurationsservern (CS)
* Processervern (PS)
* Huvudmålservern (Huvudmålservern)

En skalbar PS är också under partnerns kontroll.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>Varje CS i ett scenario med flera innehavare använder två konton

- **konto för vCenter**: använda det här kontot för att identifiera klienten virtuella datorer. Den har vCenter åtkomstbehörigheter (som beskrivs i nästa avsnitt). För att undvika läckage av oavsiktlig åtkomst, rekommenderar vi att partner anger autentiseringsuppgifterna sig själva i konfigurationsverktyget.

- **Virtuella åtkomstkonto**: Använd det här kontot för att installera mobility-agenten på de virtuella klientdatorerna via en automatisk push. Det är vanligtvis ett domänkonto som en klient kan ge en partner eller en som partner kan hantera direkt. Om en klient inte vill dela information med partnern direkt, kan de tillåtet att ange autentiseringsuppgifter via tidsbegränsade åtkomst till CS eller partnerns hjälp att installera mobility agenter manuellt.

### <a name="requirements-for-a-vcenter-access-account"></a>Krav för ett konto för vCenter

Som nämns i föregående avsnitt, måste du konfigurera CS med ett konto som har en särskild roll som tilldelats. Rolltilldelning måste tillämpas på vCenter åtkomstkonto för varje vCenter-objekt och inte sprids till underordnade objekt. Den här konfigurationen garanterar klientisolering, eftersom åtkomst spridningen kan medföra oavsiktlig åtkomst till andra objekt.

![Sprid till underordnade objekt alternativ](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

Alternativet är att tilldela användarkontot och rollen på objektet datacenter och förmedla dem till underordnade objekt. Ge kontot ett *ingen åtkomst* rollen för alla objekt (till exempel andra klienter VM: ar) som ska vara otillgänglig för en viss klient. Den här konfigurationen är besvärlig och den exponerar oavsiktliga åtkomstkontroller, eftersom alla nya underordnade objekt beviljas automatiskt åtkomst som ärvs från överordnat. Därför rekommenderar vi att du använder det första tillvägagångssättet.

VCenter-kontoåtkomst procedur är följande:

1. Skapa en ny roll genom att klona den fördefinierade *skrivskyddad* roll, och ge det ett enkelt namn (till exempel Azure_Site_Recovery som visas i det här exemplet).

2. Tilldela följande behörigheter till den här rollen:

    * **DataStore**: allokera utrymme, bläddra datalagret, låg nivå filåtgärder, ta bort filen, uppdateringsfiler för virtuell dator

    * **Nätverket**: tilldela nätverk

    * **Resursen**: tilldela VM resurspool, migrera avstängda VM, migrera påslagen VM

    * **Uppgifter**: skapa uppdatera aktiviteten

    * **Virtuella**:
        * Configuration > alla
        * Interaktion > besvara frågan, enhetsanslutning, konfigurera CD-skivor, konfigurera diskettenheter media, Stäng av slå på Installera för VMware-verktyg
        * Inventering > från befintliga, skapa nya, registrera, avregistrera
        * Etablering > Tillåt virtuella hämtning, Tillåt virtuella filer överför
        * Hantering av ögonblicksbilder > Ta bort ögonblicksbilder

    ![Dialogrutan Redigera roll](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. Tilldela åtkomstnivåer till vCenter-kontot (används i innehavaren CS) för olika objekt på följande sätt:

>| Objekt | Roll | Kommentarer |
>| --- | --- | --- |
>| vCenter | Skrivskyddad | Krävs endast att tillåta vCenter åtkomst för att hantera olika objekt. Du kan ta bort den här behörigheten om kontot aldrig ska anges för en klient eller användas för alla hanteringsåtgärder på vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Värd- och -värdkluster | Azure_Site_Recovery | Nytt garanterar att åtkomst på objektnivå, så att endast tillgängliga värdar har klient virtuella datorer före redundans och efter återställning efter fel. |
>| DataStore-kluster med datalagret | Azure_Site_Recovery | Samma som föregående. |
>| Nätverk | Azure_Site_Recovery |  |
>| Hanteringsserver | Azure_Site_Recovery | Ger tillgång till alla komponenter (CS PS och Huvudmålservern) utanför CS-datorn. |
>| Klient virtuella datorer | Azure_Site_Recovery | Säkerställer att alla nya innehavaren virtuella datorer i en viss klient också få åtkomst eller inte är tillgängligt via Azure-portalen. |

VCenter-kontoåtkomst är slutförd. Det här steget uppfyller krav på lägsta behörighet att slutföra åtgärder för återställning efter fel. Du kan också använda dessa åtkomstbehörigheter med din befintliga principer. Ändra bara din befintliga behörigheter att inkludera rollbehörigheter från steg 2, detaljerad tidigare.

Att begränsa disaster recovery-åtgärder förrän redundansläge (som är utan funktioner för återställning efter fel), följer du föregående procedur, med ett undantag: i stället för att den *Azure_Site_Recovery* roll åtkomstkonto vCenter endast tilldela en *skrivskyddad* roll till det kontot. Den här behörighetsgruppen låter VM-replikering och redundans, vilket tillåter inte återställning efter fel. Allt annat i föregående procedur blir kvar som är. För att säkra isolering av innehavare och begränsa VM identifiering, varje tillstånd fortfarande tilldelas på objektnivån och inte sprids till underordnade objekt.

## <a name="other-multi-tenant-environments"></a>Andra miljöer med flera innehavare

I föregående avsnitt beskrivs hur du konfigurerar en miljö med flera innehavare för en delad värd-lösning. De andra två största lösningarna som är dedikerad värd och hanteras av tjänsten. I följande avsnitt beskrivs arkitekturen för dessa lösningar.

### <a name="dedicated-hosting-solution"></a>Dedikerad värd

I följande diagram visas den arkitektoniska skillnaden i en dedikerad värd-lösning är att varje klient infrastrukturen har ställts in för att klienten endast. Eftersom klienter är isolerade via separat Vcenter, värdtjänsten fortfarande måste följa stegen för CSP som värd för delade men behöver inte bry dig om klientisolering. CSP inställningar ändras inte.

![arkitektur för delade hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**Dedikerad värd scenario med flera Vcenter**

### <a name="managed-service-solution"></a>Hanterad tjänst-lösning

I följande diagram visas den arkitektoniska skillnaden i en hanterad tjänst-lösning är att varje klient infrastruktur är fysiskt avskild från andra klienter infrastruktur. Det här scenariot finns vanligtvis när klienten äger infrastrukturen och vill lösningsleverantören för att hantera katastrofåterställning. Igen, eftersom klienterna är fysiskt isolerade via olika nätverksinfrastrukturer, partner behöver gör CSP angavs som värd för delade men behöver inte bry dig om klientisolering. CSP etablering ändras inte.

![arkitektur för delade hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  
**Hanterad service scenario med flera Vcenter**

## <a name="next-steps"></a>Nästa steg
[Lär dig mer](site-recovery-role-based-linked-access-control.md) om rollbaserad åtkomstkontroll hantera distributioner av Azure Site Recovery.

[Hantera flera innehavare med CSP](site-recovery-manage-multi-tenancy-with-csp.md)
