---
title: Översikt över stöd för flera klienter för VMware-VM-replikering till Azure (CSP) med Azure Site Recovery | Microsoft Docs
description: Innehåller en översikt över Azure Site Recovery-stöd för klient prenumerationer i en miljö med flera klientorganisationer via CSP-programmet.
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 2f1a158b6ff4ec603c77f834212cb92fa862b424
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919945"
---
# <a name="overview-of-multi-tenant-support-for-vmware-replication-to-azure-with-csp"></a>Översikt över stöd för flera klienter för VMware-replikering till Azure med CSP

[Azure Site Recovery](site-recovery-overview.md) har stöd för flera innehavare miljöer för klient-prenumerationer. Det stöder också flera innehavare för klient-prenumerationer som skapas och hanteras via Microsoft Cloud Solution Provider (CSP)-programmet.

Den här artikeln innehåller en översikt över implementering och hantering av flera innehavare VMware till Azure-replikering.

## <a name="multi-tenant-environments"></a>Miljöer för flera innehavare

Det finns tre större modeller för flera innehavare:

* **Delade som är värd för Services Provider (HSP)**: partnern äger den fysiska infrastrukturen och använder delade resurser (vCenter, Datacenter, fysiska lagringsutrymmet och så vidare) som värd för flera virtuella klientdatorer på samma infrastruktur. Partnern kan tillhandahålla katastrofåterställning management som en hanterad tjänst eller klienten kan äga haveriberedskap som en lösning för självbetjäning.

* **Dedikerad som är värd för tjänsteleverantör**: partnern äger den fysiska infrastrukturen, men använder dedikerade resurser (flera vCenters fysiska datalager och så vidare) som värd för varje klient virtuella datorer på en separat infrastruktur. Partnern kan tillhandahålla katastrofåterställning management som en hanterad tjänst eller klienten kan äga den som en lösning för självbetjäning.

* **Managed Services Provider (MSP)**: kunden äger den fysiska infrastrukturen som är värd för de virtuella datorerna och partnern som tillhandahåller katastrofåterställning aktivering och hantering.

## <a name="shared-hosting-services-provider-hsp"></a>Delade tjänster provider (HSP)

De två scenarierna är delmängder till det delade scenariot och de använder samma principer. Skillnaderna beskrivs i slutet av den delade vägledningen.

Grundläggande krav i ett scenario med flera innehavare är att klienter måste vara isolerade. En klient ska inte kunna se vad en annan klient har hosted. Det här kravet är inte lika viktigt eftersom den är i en självbetjäning miljö där det kan vara avgörande i en partnerhanterade miljö. Den här artikeln förutsätter att klientisolering krävs.

Arkitekturen visas i följande diagram.

![Delad HSP med en vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Delade-värd med en vCenter-server**

I diagrammet har varje kund en separat hanteringsserver. Den här konfigurationen begränsar klient har åtkomst till klientspecifik virtuella datorer och aktiverar klientisolering. VMware-VM-replikering använder konfigurationsservern för att identifiera virtuella datorer och installera agenter. Samma principer gäller för flera innehavare miljöer, och Lägg till att begränsa identifieringen av virtuella datorer med hjälp av vCenter-åtkomstkontroll.

Kravet på isolering innebär att all infrastrukturinformation som känsliga (till exempel autentiseringsuppgifter) förblir hemlig till klienter. Därför rekommenderar vi att alla komponenter i management-servern är under din kontroll av partnern. Management server-komponenter är:

* Konfigurationsservern
* Processervern
* Huvudmålservern

En sak som skalats ut-server är också under partnerns kontroll.

## <a name="configuration-server-accounts"></a>Configuration server-konton

Varje konfigurationsservern i scenariot för flera innehavare använder två konton:

- **kontot för vCenter**: det här kontot används för att identifiera virtuella klientdatorer. Den har vCenter åtkomstbehörigheter som är tilldelade till den. För att undvika läckage av åtkomst, rekommenderar vi att partner anger autentiseringsuppgifterna sig själva i konfigurationsverktyget.

- **Kontot för virtuell dator**: det här kontot används för att installera tjänsten mobilitetsagenten på klient virtuella datorer, med en automatisk push-installation. Det är vanligtvis ett domänkonto som en klient kan ge en partner eller ett konto som partner kan hantera direkt. Om en klient inte vill dela information med partnern direkt måste ange de autentiseringsuppgifterna via tidsbegränsad åtkomst till konfigurationsservern. Med partners, de kan också installera Mobility service-agenten manuellt.

## <a name="vcenter-account-requirements"></a>vCenter-kontokrav

Konfigurera konfigurationsservern med ett konto som har en särskild roll som tilldelats till den.

- Rolltilldelningen måste tillämpas på det vCenter-kontot för varje vCenter-objekt och inte sprids till underordnade objekt. Den här konfigurationen garanterar klientisolering, eftersom åtkomst spridning kan resultera i oavsiktlig åtkomst till andra objekt.

    ![Sprid till underordnat objekt alternativet](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Annan metod är att tilldela användarkontot och rollen på datacenter-objekt och sprida dem till underordnade objekt. Ge kontot en **ingen åtkomst** rollen för alla objekt (till exempel virtuella datorer som tillhör andra klienter) som ska vara otillgänglig för en viss klient. Den här konfigurationen är besvärlig. Det visar oavsiktlig åtkomstkontroller, eftersom varje nytt underordnat objekt beviljas automatiskt åtkomst som ärvs från överordnat. Därför rekommenderar vi att du använder det första tillvägagångssättet.

### <a name="create-a-vcenter-account"></a>Skapa en vCenter-konto

1. Skapa en ny roll genom att klona den fördefinierade *skrivskyddad* roll, och ge den ett praktiskt namn (till exempel Azure_Site_Recovery som visas i det här exemplet).
2. Tilldela följande behörigheter till den här rollen:

    * **Datalager**: allokera utrymme, bläddra datalagret, filåtgärder, ta bort filen, uppdateringsfiler för virtuell dator
    * **Nätverk**: Network tilldela
    * **Resursen**: tilldela VM till resurspool, migrera avstängd VM, migrera påslagen VM
    * **Uppgifter**: Skapa uppgift, uppdatera uppgift
    * **VM - konfiguration**: alla
    - **VM - interaktion** > besvara fråga, enhetsanslutning, konfigurera CD-skiva, konfigurera diskettstation, stänga av, starta, installera VMware-verktyg
    - **VM - inventering** > Skapa från befintligt, skapa en ny, registrera, avregistrera
    - **VM - etablering** > Tillåt nedladdning till virtuell dator, Tillåt VM filer uppladdning
    - **VM - hantering av ögonblicksbilder** > Ta bort ögonblicksbilder

        ![Dialogrutan Redigera roll](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Tilldela åtkomstnivåer till vCenter-konto (används på konfigurationsservern för klient) för olika objekt på följande sätt:

>| Objekt | Roll | Kommentarer |
>| --- | --- | --- |
>| vCenter | Skrivskyddad | Krävs endast att tillåta vCenter åtkomst för att hantera olika objekt. Du kan ta bort den här behörigheten om kontot aldrig ska anges till en klient eller används för alla hanteringsåtgärder på vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Värd- och -kluster | Azure_Site_Recovery | Ser till att åtkomsten är på objektnivå, igen så att bara tillgängliga värdar har klient virtuella datorer före redundans och efter återställning efter fel. |
>| Kluster med datalager och datalager | Azure_Site_Recovery | Samma som föregående. |
>| Nätverk | Azure_Site_Recovery |  |
>| Hanteringsserver | Azure_Site_Recovery | Ger åtkomst till alla komponenter (CS, PS och MT) utanför den CS-datorn. |
>| Virtuella klientdatorer | Azure_Site_Recovery | Säkerställer att någon ny klient virtuella datorer för en viss klient också få åtkomst eller kan inte tillgängligt via Azure-portalen. |

VCenter-kontoåtkomst är slutförd. Det här steget uppfyller minsta möjliga behörigheter behovet att slutföra åtgärder för återställning efter fel. Du kan också använda dessa åtkomstbehörigheter med dina befintliga principer. Ändra just din befintliga behörigheter som anger att inkludera behörigheter från steg 2, detaljerad tidigare.

### <a name="failover-only"></a>Endast redundans
Att begränsa haveriberedskapsåtgärder fram till endast redundans (det vill säga utan återställning efter fel), använder föregående procedur, med följande undantag:

- I stället för att tilldela den *Azure_Site_Recovery* rollen åtkomstkonto vCenter endast tilldela en *skrivskyddad* rollen till det kontot. Denna behörighetsgrupp kan VM-replikering och redundans och återställning efter fel tillåts inte.
- Allt annat i föregående procedur förblir skick. För att se till att innehavare isolering och begränsa identifieringen av virtuella datorer, är varje behörighet fortfarande tilldelade på objektnivå endast och inte sprids till underordnade objekt.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Distribuera resurser till klientprenumeration

1. Skapa en resursgrupp på Azure-portalen och sedan distribuera ett Recovery Services-valv vanligt processer.
2. Ladda ned valvregistreringsnyckeln.
3. Registrera Konfigurationsservern för klienten med hjälp av valvregistreringsnyckeln.
4. Ange autentiseringsuppgifterna för de två åtkomstkontona, konto för åtkomst till vCenter-servern och konto för åtkomst till den virtuella datorn.

    ![Serverkonton med configuration Manager](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registrera servrar i valvet

1. Registrera konfigurationsservern med hjälp av vCenter-kontot som du skapade den vCenter-servern i Azure-portalen i valvet som du skapade tidigare.
2. Slut processen ”Förbered infrastruktur” för Site Recovery vanliga processer.
3. De virtuella datorerna är nu redo att replikeras. Kontrollera att endast klientens virtuella datorer visas i **replikera** > **Välj virtuella datorer**.

## <a name="dedicated-hosting-solution"></a>Dedikerad som värd

I följande diagram visas arkitektoniska skillnaden i en värdlösning med en dedikerad är att varje klient infrastrukturen har ställts in för den klienten endast.

![arkitektur för delade hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dedikerade värdtjänster scenario med flera vCenters**

## <a name="managed-service-solution"></a>Hanterad tjänst-lösning

I följande diagram visas arkitektoniska skillnaden i en hanterad tjänst-lösning är att varje klient infrastruktur är fysiskt åtskild från andra klienter infrastruktur. Det här scenariot finns vanligtvis när klienten äger infrastrukturen och vill ha en leverantör av lösningar att hantera katastrofåterställning.

![arkitektur för delade hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Hanterad service scenario med flera vCenters**

## <a name="next-steps"></a>Nästa steg
- [Läs mer](site-recovery-role-based-linked-access-control.md) om rollbaserad åtkomstkontroll i Site Recovery.
- Lär dig hur du [konfigurera haveriberedskap för virtuella VMware-datorer till Azure](vmware-azure-tutorial.md).
- Läs mer om [flera innehavare med CSP för virtuella VMWare-datorer](vmware-azure-multi-tenant-csp-disaster-recovery.md).
