---
title: VMware VM-haveriberedskap för flera innehavare med flera innehavare med Azure Site Recovery
description: Ger en översikt över Azure Site Recovery-stöd för VMWare-haveriberedskap till Azure i ett CSP-program (Multi-Tenant Environment).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 840049265d3b6e4d2fddd794646bfd5691aab9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083994"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Översikt över stöd för flera innehavare för VMware-haveriberedskap till Azure med CSP

[Azure Site Recovery](site-recovery-overview.md) stöder miljöer med flera innehavare för klientprenumerationer. Den stöder också flera innehavare för klientprenumerationer som skapas och hanteras via Programmet Microsoft Cloud Solution Provider (CSP).

Den här artikeln innehåller en översikt över implementering och hantering av VMware med flera innehavare till Azure-replikering.

## <a name="multi-tenant-environments"></a>Miljöer med flera innehavare

Det finns tre stora modeller för flera innehavare:

* **HSP (Shared Hosting Services Provider)**: Partnern äger den fysiska infrastrukturen och använder delade resurser (vCenter, datacenter, fysisk lagring och så vidare) för att vara värd för flera virtuella klientenheter på samma infrastruktur. Partnern kan tillhandahålla hantering av katastrofåterställning som en hanterad tjänst, eller så kan klienten äga haveriberedskap som en självbetjäningslösning.

* **Dedikerad värdtjänstleverantör:** Partnern äger den fysiska infrastrukturen, men använder dedikerade resurser (flera vCenters, fysiska datalager och så vidare) för att vara värd för varje klients virtuella datorer på en separat infrastruktur. Partnern kan tillhandahålla hantering av katastrofåterställning som en hanterad tjänst, eller så kan klienten äga den som en självbetjäningslösning.

* **Managed Services Provider (MSP)**: Kunden äger den fysiska infrastruktur som är värd för de virtuella datorerna och partnern tillhandahåller aktivering och hantering av katastrofåterställning.

## <a name="shared-hosting-services-provider-hsp"></a>Leverantör av delade värdtjänster (HSP)

De andra två scenarierna är delmängder av scenariot med delad värd och de använder samma principer. Skillnaderna beskrivs i slutet av vägledningen för delad värd.

Det grundläggande kravet i ett scenario med flera innehavare är att klienter måste isoleras. En klient ska inte kunna observera vad en annan klient har värd. I en partnerhanterad miljö är detta krav inte lika viktigt som det är i en självbetjäningsmiljö, där det kan vara kritiskt. Den här artikeln förutsätter att klientisolering krävs.

Arkitekturen visas i följande diagram.

![Delad HSP med ett vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Delad hosting med en vCenter-server**

I diagrammet har varje kund en separat hanteringsserver. Den här konfigurationen begränsar klientåtkomst till klientspecifika virtuella datorer och aktiverar klientisolering. VMware VM-replikering använder konfigurationsservern för att identifiera virtuella datorer och installera agenter. Samma principer gäller för miljöer med flera innehavare, med tillägg av att begränsa vm-identifiering med hjälp av vCenter-åtkomstkontroll.

Kravet på dataisolering innebär att all känslig infrastrukturinformation (t.ex. åtkomstautentiseringsuppgifter) förblir hemlig för klienter. Därför rekommenderar vi att alla komponenter i hanteringsservern förblir under partnerns exklusiva kontroll. Komponenterna för hanteringsserver är:

* Konfigurationsserver
* Bearbeta server
* Huvudmålservern

En separat utskalad processserver är också under partnerns kontroll.

## <a name="configuration-server-accounts"></a>Konfigurationsserverkonton

Varje konfigurationsserver i scenariot med flera innehavare använder två konton:

- **vCenter-åtkomstkonto:** Det här kontot används för att identifiera virtuella klient-datorer. Den har vCenter-åtkomstbehörigheter som tilldelats den. För att undvika åtkomstläckor rekommenderar vi att partner själva anger dessa autentiseringsuppgifter i konfigurationsverktyget.

- **Åtkomstkonto för virtuella**datorer : Det här kontot används för att installera mobilitetstjänstens agent på virtuella klientenheter, med en automatisk push. Det är vanligtvis ett domänkonto som en klient kan tillhandahålla en partner eller ett konto som partnern kan hantera direkt. Om en klient inte vill dela informationen med partnern direkt kan de ange autentiseringsuppgifterna genom begränsad åtkomst till konfigurationsservern. Eller, med partnerns hjälp, kan de installera Mobility-serviceagenten manuellt.

## <a name="vcenter-account-requirements"></a>krav på vCenter-konto

Konfigurera konfigurationsservern med ett konto som har en särskild roll tilldelad.

- Rolltilldelningen måste tillämpas på vCenter-åtkomstkontot för varje vCenter-objekt och inte spridas till de underordnade objekten. Den här konfigurationen säkerställer klientisolering, eftersom åtkomstspridning kan resultera i oavsiktlig åtkomst till andra objekt.

    ![Alternativet Sprid till underordnade objekt](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Den alternativa metoden är att tilldela användarkontot och rollen i datacenterobjektet och sprida dem till de underordnade objekten. Ge sedan kontot en **ingen åtkomstroll** för varje objekt (till exempel virtuella datorer som tillhör andra klienter) som ska vara otillgängliga för en viss klient. Den här konfigurationen är besvärlig. Den exponerar oavsiktliga åtkomstkontroller, eftersom varje nytt underordnat objekt också automatiskt beviljas åtkomst som ärvs från det överordnade objektet. Därför rekommenderar vi att du använder den första metoden.

### <a name="create-a-vcenter-account"></a>Skapa ett vCenter-konto

1. Skapa en ny roll genom att klona den fördefinierade *skrivskyddade* rollen och ge den ett bekvämt namn (till exempel Azure_Site_Recovery, som visas i det här exemplet).
2. Tilldela följande behörigheter till den här rollen:

   * **Datastore**: Allokera utrymme, Bläddra datalager, Låg nivå filåtgärder, Ta bort fil, Uppdatera virtuella datorfiler
   * **Nätverk**: Nätverkstilldelare
   * **Resurs:** Tilldela virtuell dator till resurspool, Migrera avstängd virtuell dator, Migrera på den virtuella datorn
   * **Uppgifter**: Skapa uppgift, Uppdatera uppgift
   * **VM - Konfiguration:** Alla
   * **VM - Interaktion** > Svarsfråga, Enhetsanslutning, Konfigurera CD-media, Konfigurera diskettmedia, Avstängning, Power on, VMware-verktyg installeras
   * **VM - Lager** > Skapa från befintliga, Skapa nya, Registrera, Avregistrera
   * **VM - Etablering** > Tillåt nedladdning av virtuella datorer, Tillåt överföring av filer till virtuella datorer
   * **VM - Hantering av ögonblicksbilder** > ta bort ögonblicksbilder

       ![Dialogrutan Redigera roll](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Tilldela åtkomstnivåer till vCenter-kontot (används i klientkonfigurationsservern) för olika objekt enligt följande:

>| Objekt | Roll | Anmärkningar |
>| --- | --- | --- |
>| vCenter | Skrivskyddad | Behövs bara för att tillåta vCenter-åtkomst för hantering av olika objekt. Du kan ta bort den här behörigheten om kontot aldrig kommer att tillhandahållas till en klient eller användas för någon hanteringsåtgärder på vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Värd- och värdkluster | Azure_Site_Recovery | Säkerställer att åtkomsten finns på objektnivå, så att endast tillgängliga värdar har virtuella klient-datorer före redundans och efter återställning efter återställning. |
>| Datalager- och datalagerkluster | Azure_Site_Recovery | Samma som föregående. |
>| Nätverk | Azure_Site_Recovery |  |
>| Hanteringsserver | Azure_Site_Recovery | Inkluderar åtkomst till alla komponenter (CS, PS och MT) utanför CS-maskinen. |
>| Virtuella klienter | Azure_Site_Recovery | Säkerställer att alla nya virtuella klient-datorer för en viss klient också får den här åtkomsten, eller så kan de inte identifieras via Azure-portalen. |

Åtkomsten till vCenter-kontot är nu klar. Det här steget uppfyller minimikravet för behörigheter för att slutföra återställningsåtgärder. Du kan också använda dessa åtkomstbehörigheter med dina befintliga principer. Ändra bara dina befintliga behörigheter som ska innehålla rollbehörigheter från steg 2, som tidigare beskrivits.

### <a name="failover-only"></a>Endast redundans
Om du vill begränsa katastrofåterställningsåtgärder fram till endast redundans (det vill än återställningsfunktioner utan återställning) använder du den föregående proceduren med följande undantag:

- I stället för att tilldela *Azure_Site_Recovery-rollen* till vCenter-åtkomstkontot tilldelar du bara en *skrivskyddad* roll till det kontot. Den här behörighetsgruppen tillåter VM-replikering och redundans, och den tillåter inte återställning efter fel.
- Allt annat i föregående process förblir som det är. För att säkerställa klientisolering och begränsa vm-identifiering tilldelas alla behörigheter fortfarande endast på objektnivå och sprids inte till underordnade objekt.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Distribuera resurser till klientprenumerationen

1. Skapa en resursgrupp på Azure-portalen och distribuera sedan ett Recovery Services-valv enligt den vanliga processen.
2. Ladda ned valvregistreringsnyckeln.
3. Registrera CS för klienten med hjälp av valvregistreringsnyckeln.
4. Ange autentiseringsuppgifterna för de två åtkomstkontona, kontot för att komma åt vCenter-servern och kontot för att komma åt den virtuella datorn.

    ![Konfigurationsserverkonton för hanteraren](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Registrera servrar i valvet

1. I Azure-portalen, i valvet som du skapade tidigare, registrerar du vCenter-servern till konfigurationsservern med hjälp av det vCenter-konto som du skapade.
2. Slutför processen "Förbered infrastruktur" för platsåterställning enligt den vanliga processen.
3. De virtuella datorerna är nu redo att replikeras. Kontrollera att endast klientens virtuella datorer visas i**virtuella datorer för** **replikera** > select .

## <a name="dedicated-hosting-solution"></a>Dedikerad värdlösning

Som visas i följande diagram är den arkitektoniska skillnaden i en dedikerad värdlösning att varje klients infrastruktur endast ställs in för den klienten.

![arkitektur-delad-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dedikerat värdscenario med flera vCenters**

## <a name="managed-service-solution"></a>Lösning för hanterade tjänster

Som visas i följande diagram är den arkitektoniska skillnaden i en hanterad tjänstlösning att varje klients infrastruktur också är fysiskt åtskild från andra klienters infrastruktur. Det här scenariot finns vanligtvis när klienten äger infrastrukturen och vill ha en lösningsprovider för att hantera haveriberedskap.

![arkitektur-delad-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Hanterad tjänstscenario med flera vCenters**

## <a name="next-steps"></a>Nästa steg
- [Läs mer](site-recovery-role-based-linked-access-control.md) om rollbaserad åtkomstkontroll i Site Recovery.
- Lär dig hur du [konfigurerar haveriberedskap för virtuella virtuella datorer med VMware till Azure](vmware-azure-tutorial.md).
- Läs mer om [flerargsinnehavare med CSP för virtuella VMWare-datorer](vmware-azure-multi-tenant-csp-disaster-recovery.md).
