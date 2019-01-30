---
title: Automatisk uppdatering av Mobilitetstjänsten i Azure till Azure-katastrofåterställning | Microsoft Docs
description: Innehåller en översikt över automatisk uppdatering av Mobilitetstjänsten, vid replikering av virtuella Azure-datorer med Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: f31fccd2bf6d0daae03b025b53a41a0fad4ce2ef
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210139"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatisk uppdatering av Mobilitetstjänsten i replikering från Azure till Azure

Azure Site Recovery har en månatlig frisläppningstakt där förbättringar av befintliga funktioner eller nya paket har lagts till och kända problemen eventuella har lösts. Detta skulle innebära att för att fortsätta vara uppdaterad med tjänsten, du måste planera för distribution av dessa korrigeringar per månad. Användare kan i stället välja att tillåta Site Recovery för att hantera uppdateringar av komponenter för att undvika over-huvud som är associerade med uppgraderingen. Enligt beskrivningen i den [arkitektur referens](azure-to-azure-architecture.md) för haveriberedskap med Azure-datorer, Mobilitetstjänsten installeras på alla virtuella Azure-datorer som replikering är aktiverat vid replikering av virtuella datorer från en Azure region till en annan. När du har aktiverat Automatiska uppdateringar hämtar mobilitetstjänsttillägget uppdateras med varje ny version. Det här dokumentet beskriver följande:

- Hur fungerar automatisk uppdatering?
- Aktivera automatiska uppdateringar
- Vanliga problem och felsökning
 
## <a name="how-does-automatic-update-work"></a>Hur fungerar automatisk uppdatering

När du tillåter Site Recovery för att hantera uppdateringar distribueras en global runbook (som används av Azure-tjänster) via ett automation-konto som skapats i samma prenumeration som valvet. En automation-kontot används för ett specifikt valv. Runbook kontrollerar för varje virtuell dator i ett valv som uppdateras automatiskt är aktiverade och initierar en uppgradering av tillägget Mobilitetstjänsten om det finns en nyare version. Standardschemat för runbook återkommer varje dag kl. 12:00 AM enligt tidszonen för den replikerade virtuella geo. Schemat för runbook kan också ändras via automation-kontot av användaren, om det behövs. 

> [!NOTE]
> Aktivera automatiska uppdateringar kräver inte en omstart av virtuella datorer i Azure och påverkar inte pågående replikering.

> [!NOTE]
> Faktureringen för jobb som används av automation-konto är baserat på antalet jobbkörningstid minuter som används i månaden och som standard 500 minuter är inkluderade som kostnadsfria enheter för ett automation-konto. Körningen av de dagliga mängder jobb från en **några sekunder till ungefär en minut** och kommer att **som beskrivs i den kostnadsfria krediten**.

KOSTNADSFRIA enheter som ingår (PER månad) ** pris jobbet köras tid 500 minuter ₹0.14 per minut

## <a name="enable-automatic-updates"></a>Aktivera automatiska uppdateringar

Du kan välja att tillåta Site Recovery för att hantera uppdateringar på följande sätt:-

- [Aktivera replikering steget](#as-part-of-the-enable-replication-step)
- [Visa/Dölj tillägget uppdatera inställningarna i valvet](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>Som en del av Aktivera replikering steg:

När du aktiverar replikering för en virtuell dator antingen startar [från vyn virtuell dator](azure-to-azure-quickstart.md), eller [från recovery services-valvet](azure-to-azure-how-to-enable-replication.md), får du ett alternativ för att välja att antingen tillåta Site Recovery till hantera uppdateringar för Site Recovery-tillägget eller för att hantera samma manuellt.

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Visa/Dölj tillägget uppdatera inställningarna i valvet

1. I valvet, gå till **hantera**-> **Site Recovery-infrastruktur**
2. Under **för Azure-datorer**-> **tillägget uppdateringsinställningar**, klickar på växlingsknappen för att välja om du vill tillåta *ASR att hantera uppdateringar* eller *hantera manuellt*. Klicka på **Spara**.

![vault-toggle-auto-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> När du väljer *Tillåt ASR hanterar*, inställningen tillämpas på alla virtuella datorer i motsvarande valv.


> [!Note] 
> Båda alternativen meddelar dig om automation-kontot som används för att hantera uppdateringarna. Om du aktiverar den här funktionen för första gången i ett valv, skapas ett nytt automation-konto. Alla efterföljande aktivera replikeringar i samma valv kommer att använda det tidigare skapade.

### <a name="manage-manually"></a>Hantera manuellt

1. Om det finns nya uppdateringar av mobilitetstjänsten installeras på virtuella datorer i Azure kan se du ett meddelande som läser ”ny Site recovery replikering agentuppdatering är tillgänglig. Klicka för att installera ”.

     ![Replikerade objekt fönster](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
3. Välj meddelandet för att öppna sidan för val av virtuell dator.
4. Välj de virtuella datorer som du vill uppgradera mobilitetstjänsten på och välj **OK**.

     ![Replikerade objekt VM-lista](./media/vmware-azure-install-mobility-service/update-okpng.png)

Uppdateringen av Mobilitetstjänsten jobbet startar för var och en av de valda virtuella datorerna.


## <a name="common-issues--troubleshooting"></a>Vanliga problem och felsökning

Om det finns ett problem med automatiska uppdateringar, kommer du att meddelas av samma under Configuration ärenden i instrumentpanelen för valvet. 

Om du har försökt att aktivera Automatiska uppdateringar och den misslyckades, se nedan för att felsöka.

**Fel**: Du har inte behörighet att skapa ett Kör som-konto i Azure (tjänsthuvudkonto) eller att bevilja tjänstens huvudnamn en deltagarroll. 
- Rekommenderad åtgärd: Se till att det inloggade kontot har tilldelats ”Bidragsgivar” och försök igen. Referera till [detta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) dokumentet för ytterligare information om hur du tilldelar rätt behörigheter.
 
När automatiska uppdateringar är aktiverade, de flesta av problem som kan vara lagats av tjänsten Site Recovery och kräver att du klickar på den ”**reparera**-knappen.

![reparera-knappen](./media/azure-to-azure-autoupdate/repair.png)

Om knappen reparera inte är tillgängligt finns i felmeddelandet visas under rutan inställningar för tillägget.

 - **Fel**: Kör som-kontot har inte behörighet att få åtkomst till recovery services-resursen.

    **Rekommenderad åtgärd**: Ta bort och sedan [återskapa kör som-kontot](https://docs.microsoft.com/azure/automation/automation-create-runas-account) eller se till att Automation kör som kontots Azure Active Directory-program har åtkomst till recovery services-resursen.

- **Fel**: Kör som-kontot hittades inte. En av dessa har tagits bort eller inte skapats – Azure Active Directory-program, tjänstens huvudnamn, roll, Automation-certifikattillgång, Automation-anslutningstillgång, eller tumavtrycket är inte identiskt för certifikat och anslutning. 

    **Rekommenderad åtgärd**: Ta bort och [återskapa kör som-kontot](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
