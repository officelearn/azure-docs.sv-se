---
title: Översikt över flera innehavare stöd för VMware VM-replikering till Azure (CSP) med hjälp av Azure Site Recovery | Microsoft Docs
description: En översikt över Azure Site Recovery-stöd för klienten prenumerationer i en miljö med flera innehavare, via CSP-programmet.
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 0168849c01add3f714b139c7984e3def74f40a5b
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071771"
---
# <a name="overview-of-multi-tenant-support-for-vmware-replication-to-azure-with-csp"></a>Översikt över flera innehavare stöd för VMware-replikering till Azure med CSP

[Azure Site Recovery](site-recovery-overview.md) har stöd för miljöer med flera innehavare för klient-prenumerationer. Det stöder också flera innehavare för klient-prenumerationer som skapas och hanteras via programmet Microsoft Cloud Solution Providers (CSP).

Den här artikeln innehåller en översikt över implementering och hantering av flera innehavare VMware till Azure-replikering.

## <a name="multi-tenant-environments"></a>Miljöer med flera innehavare

Det finns tre huvudsakliga modeller för flera innehavare:

* **Delade värd Services Provider (HSP)**: partnern som äger den fysiska infrastrukturen och använder delade resurser (vCenter, Datacenter, fysisk lagring och så vidare) som värd för flera innehavare virtuella datorer på samma infrastruktur. Partnern kan tillhandahålla katastrofåterställning hantering som en hanterad tjänst eller klienten kan äga katastrofåterställning som en lösning för självbetjäning.

* **Dedikerad värd tjänsteleverantör**: partnern som äger den fysiska infrastrukturen, men använder dedicerade resurser (flera Vcenter fysiska datastores och så vidare) som värd för varje klient virtuella datorer på en separat infrastruktur. Partnern kan tillhandahålla katastrofåterställning hantering som en hanterad tjänst eller klienten kan äga som en lösning för självbetjäning.

* **Hanterade Services Provider (MSP)**: kunden äger den fysiska infrastrukturen som är värd för de virtuella datorerna och partnern tillhandahåller katastrofåterställning aktivering och hantering.

## <a name="shared-hosting-services-provider-hsp"></a>Delade tjänsteleverantör (HSP)

De två scenarierna är delmängder av scenariot delade och de använder samma principer. Skillnaderna beskrivs i slutet av den delade vägledningen.

Grundläggande krav i ett scenario med flera innehavare är att klienter måste vara isolerad. En klient ska inte kunna se vad en annan innehavare har värdet hosted. Det här kravet är inte lika viktigt i en självbetjäning miljö där det kan vara avgörande i en partner-hanterad miljö. Den här artikeln förutsätter att klientisolering krävs.

Arkitekturen visas i följande diagram.

![Delad HSP med en vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Delade-värd med en vCenter-server**

Varje kund har en separat management-server i diagrammet. Denna konfiguration begränsar klient har åtkomst till klient-specifika virtuella datorer och aktiverar klientisolering. VMware VM-replikering använder konfigurationsservern för att identifiera virtuella datorer och installera agenter. Samma principer som gäller för miljöer med flera innehavare med tillägget för att begränsa VM identifiering med hjälp av vCenter-åtkomstkontroll.

Kravet på isolering innebär att alla känsliga infrastruktur (till exempel autentiseringsuppgifter) förblir hemlig till innehavare. Därför rekommenderar vi att alla komponenter i hanteringsservern förblir under exklusiv kontroll av partnern. Management server-komponenter är:

* Konfigurationsservern
* Processervern
* Huvudmålservern

En sak som skalats ut server är också under partnerns kontroll.

## <a name="configuration-server-accounts"></a>Configuration server-konton

Varje konfigurationsservern i ett scenario med flera innehavare använder två konton:

- **konto för vCenter**: det här kontot används för att identifiera klienten virtuella datorer. Den har vCenter åtkomstbehörigheter till den. För att undvika läckage av åtkomst, rekommenderar vi att partner anger autentiseringsuppgifterna sig själva i konfigurationsverktyget.

- **Virtuella åtkomstkonto**: det här kontot används för att installera mobilitetstjänstagenten innehavaren virtuella datorer med en automatisk push. Det är vanligtvis ett domänkonto som en klient kan ge en partner eller ett konto som partner kan hantera direkt. Om en klient inte vill dela information med partnern direkt, kan de ange autentiseringsuppgifter via tidsbegränsade åtkomst till konfigurationsservern. Eller partnerns hjälp kan installeras på mobilitetstjänstagenten manuellt.

## <a name="vcenter-account-requirements"></a>vCenter-kontokrav

Konfigurera konfigurationsservern med ett konto som har en särskild roll som tilldelats.

- Rolltilldelning måste tillämpas på vCenter åtkomstkonto för varje vCenter-objekt och inte sprids till underordnade objekt. Den här konfigurationen garanterar klientisolering, eftersom åtkomst spridningen kan medföra oavsiktlig åtkomst till andra objekt.

    ![Sprid till underordnade objekt alternativ](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Den alternativa metoden är att tilldela användarkontot och rollen på objektet datacenter och förmedla dem till underordnade objekt. Ge kontot ett **ingen åtkomst** rollen för alla objekt (till exempel virtuella datorer som tillhör andra klienter) som ska vara otillgänglig för en viss klient. Den här konfigurationen är besvärlig. Den visar oavsiktliga åtkomstkontroller, eftersom alla nya underordnade objekt beviljas automatiskt åtkomst som ärvs från överordnat. Därför rekommenderar vi att du använder det första tillvägagångssättet.

### <a name="create-a-vcenter-account"></a>Skapa en vCenter-konto

1. Skapa en ny roll genom att klona den fördefinierade *skrivskyddad* roll, och ge det ett enkelt namn (till exempel Azure_Site_Recovery som visas i det här exemplet).
2. Tilldela följande behörigheter till den här rollen:

    * **DataStore**: allokera utrymme, bläddra datalagret, låg nivå filåtgärder, ta bort filen, uppdateringsfiler för virtuell dator
    * **Nätverket**: tilldela nätverk
    * **Resursen**: tilldela VM resurspool, migrera avstängda VM, migrera påslagen VM
    * **Uppgifter**: skapa uppdatera aktiviteten
    * **VM - konfiguration**: alla
    - **VM - interaktion** > besvara frågan, enhetsanslutning, konfigurera CD-skivor, konfigurera diskettenheter media, Stäng av slå på Installera för VMware-verktyg
    - **VM - lagret** > från befintliga, skapa nya, registrera, avregistrera
    - **VM - etablering** > Tillåt virtuella hämtning, Tillåt virtuella filer överför
    - **VM - ögonblicksbild management** > Ta bort ögonblicksbilder

        ![Dialogrutan Redigera roll](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Tilldela åtkomstnivåer till vCenter-konto (används i konfigurationsservern klient) för olika objekt på följande sätt:

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

### <a name="failover-only"></a>Endast redundans
Att begränsa katastrofåterställningsåtgärder fram till endast redundans (det vill säga utan funktioner för återställning efter fel), använder föregående procedur, med följande undantag:

- I stället för att den *Azure_Site_Recovery* roll åtkomstkonto vCenter endast tilldela en *skrivskyddad* roll till det kontot. Den här behörighetsgruppen låter VM-replikering och redundans, vilket tillåter inte återställning efter fel.
- Allt annat i föregående procedur blir kvar som är. För att säkra isolering av innehavare och begränsa VM identifiering, är var behörigheten fortfarande tilldelade på objektnivån och inte sprids till underordnade objekt.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Distribuera resurser till klientprenumeration

1. Skapa en resursgrupp i Azure-portalen och sedan distribuera Recovery Services-valvet vanliga processer.
2. Ladda ned valvregistreringsnyckeln.
3. Registrera CS för klienten med hjälp av valvregistreringsnyckeln.
4. Ange autentiseringsuppgifterna för de två kontona, konto för åtkomst till vCenter-servern och konto för åtkomst till den virtuella datorn.

    ![Serverkonton med configuration Manager](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registrera servrar i valvet

1. Registrera vCenter-servern till konfiguration, med hjälp av vCenter-kontot som du skapade i Azure-portalen i valvet som du skapade tidigare.
2. Avsluta ”Förbered infrastruktur”-processen för Site Recovery vanliga processer.
3. De virtuella datorerna är nu redo att replikeras. Kontrollera att endast klientorganisationens virtuella datorer visas i **replikera** > **Välj virtuella datorer**.

## <a name="dedicated-hosting-solution"></a>Dedikerad värd

I följande diagram visas den arkitektoniska skillnaden i en dedikerad värd-lösning är att varje klient infrastrukturen har ställts in för att klienten endast.

![arkitektur för delade hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dedikerad värd scenario med flera Vcenter**

## <a name="managed-service-solution"></a>Hanterad tjänst-lösning

I följande diagram visas den arkitektoniska skillnaden i en hanterad tjänst-lösning är att varje klient infrastruktur är fysiskt avskild från andra klienter infrastruktur. Det här scenariot finns vanligtvis när klienten äger infrastrukturen och vill lösningsleverantören för att hantera katastrofåterställning.

![arkitektur för delade hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Hanterad service scenario med flera Vcenter**

## <a name="next-steps"></a>Nästa steg
- [Lär dig mer](site-recovery-role-based-linked-access-control.md) om rollbaserad åtkomstkontroll i Site Recovery.
- Lär dig hur du [konfigurera återställning av virtuella VMware-datorer till Azure](vmware-azure-tutorial.md).
- Lär dig mer om [multitenans med CSP: N för virtuella VMWare-datorer](vmware-azure-multi-tenant-csp-disaster-recovery.md).
