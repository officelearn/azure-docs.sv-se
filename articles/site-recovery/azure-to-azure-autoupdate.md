---
title: Automatisk uppdatering av mobilitets tjänsten i Azure Site Recovery
description: Översikt över automatisk uppdatering av mobilitets tjänsten när du replikerar virtuella Azure-datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b57ce89979225015dc87bbfb17f9603897ef6d6b
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135844"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatisk uppdatering av mobilitets tjänsten i Azure till Azure-replikering

Azure Site Recovery använder en månatlig release-takt för att åtgärda eventuella problem och förbättra befintliga funktioner eller lägga till nya. För att hålla dig uppdaterad med tjänsten måste du planera för korrigerings distribution varje månad. För att undvika den kostnad som är kopplad till varje uppgradering kan du tillåta Site Recovery att hantera komponent uppdateringar.

Som vi nämnt i [haveri beredskap för Azure-till-Azure](azure-to-azure-architecture.md)installeras mobilitets tjänsten på alla virtuella Azure-datorer (VM) som har replikering aktiverat från en Azure-region till en annan. När du använder automatiska uppdateringar uppdaterar varje ny version mobilitets tjänst tillägget.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Så här fungerar automatiska uppdateringar

När du använder Site Recovery för att hantera uppdateringar distribuerar den en global Runbook (som används av Azure-tjänster) via ett Automation-konto som skapats i samma prenumeration som valvet. Varje valv använder ett Automation-konto. För varje virtuell dator i ett valv söker Runbook efter aktiva automatiska uppdateringar. Om det finns en nyare version av mobilitets tjänst tillägget installeras uppdateringen.

Standard-Runbook-schemat sker dagligen vid 12:00 i tids zonen för den replikerade virtuella datorns geografi. Du kan också ändra Runbook-schemat via Automation-kontot.

> [!NOTE]
> Från och med samlad [uppdatering 35](site-recovery-whats-new.md#updates-march-2019)kan du välja ett befintligt Automation-konto som ska användas för uppdateringar. Innan Samlad uppdatering 35 har Site Recovery skapat Automation-kontot som standard. Du kan bara välja det här alternativet när du aktiverar replikering för en virtuell dator. Den är inte tillgänglig för en virtuell dator som redan har replikering aktiverat. Den inställning du väljer gäller för alla virtuella Azure-datorer som skyddas i samma valv.

Att aktivera automatiska uppdateringar kräver ingen omstart av dina virtuella Azure-datorer eller påverkar pågående replikering.

Jobb fakturering i Automation-kontot baseras på antalet jobb körnings minuter som används under en månad. Jobb körningen tar några sekunder till ungefär en minut varje dag och täcks som lediga enheter. Som standard ingår 500 minuter som kostnads fria enheter för ett Automation-konto, som du ser i följande tabell:

| Kostnads fria enheter som ingår (varje månad) | Pris |
|---|---|
| Jobb körning 500 minuter | ₹ 0.14/Minute

## <a name="enable-automatic-updates"></a>Aktivera automatiska uppdateringar

Det finns flera sätt som Site Recovery kan hantera tilläggs uppdateringar:

- [Hantera som en del av steget aktivera replikering](#manage-as-part-of-the-enable-replication-step)
- [Växla inställningarna för tilläggs uppdatering i valvet](#toggle-the-extension-update-settings-inside-the-vault)
- [Hantera uppdateringar manuellt](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Hantera som en del av steget aktivera replikering

När du aktiverar replikering för en virtuell dator som antingen startar [från vyn VM](azure-to-azure-quickstart.md) eller [från Recovery Services-valvet](azure-to-azure-how-to-enable-replication.md)kan du antingen låta Site Recovery hantera uppdateringar för Site Recovery tillägget eller hantera det manuellt.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Tilläggs inställningar":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Växla inställningarna för tilläggs uppdatering i valvet

1. Från Recovery Services-valvet går du till **Hantera**  >  **Site Recovery infrastruktur**.
1. Under **för**  >  **uppdaterings inställningarna**för Azure Virtual Machines-tillägg  >  **kan Site Recovery hantera**, väljer du **på**.

   Om du vill hantera tillägget manuellt väljer du **av**.

1. Välj **Spara**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Inställningar för tilläggs uppdatering":::

> [!IMPORTANT]
> När du väljer **tillåt Site Recovery att hantera**tillämpas inställningen på alla virtuella datorer i valvet.

> [!NOTE]
> Något av alternativen meddelar dig om Automation-kontot som används för att hantera uppdateringar. Om du använder den här funktionen i ett valv för första gången skapas ett nytt Automation-konto som standard. Alternativt kan du anpassa inställningen och välja ett befintligt Automation-konto. Alla efterföljande åtgärder för att aktivera replikering i samma valv kommer att använda det tidigare skapade Automation-kontot. För närvarande visar den nedrullningsbara menyn endast Automation-konton som finns i samma resurs grupp som valvet.

### <a name="manage-updates-manually"></a>Hantera uppdateringar manuellt

1. Om det finns nya uppdateringar för mobilitets tjänsten som är installerade på dina virtuella datorer visas följande meddelande: **ny Site Recovery uppdatering av replikeringskonfiguration är tillgänglig. Klicka för att installera.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Fönstret replikerade objekt":::

1. Välj meddelandet för att öppna sidan för val av virtuell dator.
1. Välj de virtuella datorer som du vill uppgradera och välj sedan **OK**. Uppdaterings mobilitets tjänsten kommer att starta för varje vald virtuell dator.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Lista över replikerade objekt för virtuella datorer":::

## <a name="common-issues-and-troubleshooting"></a>Vanliga problem och fel sökning

Om det är problem med automatiska uppdateringar visas ett fel meddelande under **konfigurations problem** på instrument panelen för valv.

Om du inte kan aktivera automatiska uppdateringar, se följande vanliga fel och rekommenderade åtgärder:

- **Fel**: du har inte behörighet att skapa ett Kör som-konto i Azure (tjänstens huvud namn) och bevilja deltagar rollen till tjänstens huvud namn.

  **Rekommenderad åtgärd**: kontrol lera att det inloggade kontot har tilldelats deltagare och försök igen. Mer information om hur du tilldelar behörigheter finns i avsnittet nödvändiga behörigheter i [How to: använda portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

  Välj **Reparera**om du vill åtgärda de flesta problem när du har aktiverat automatiska uppdateringar. Om reparations knappen inte är tillgänglig, se fel meddelandet som visas i fönstret inställningar för uppdaterings uppdatering.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Site Recovery tjänst reparations knapp i inställningar för tilläggs uppdatering":::

- **Fel**: Kör som-kontot har inte åtkomst behörighet till Recovery Services-resursen.

  **Rekommenderad åtgärd**: ta bort och återskapa sedan [Kör som-kontot](../automation/manage-runas-account.md). Eller kontrol lera att kör som-kontots Azure Active Directory program har åtkomst till Recovery Services-resursen.

- **Fel**: det gick inte att hitta kör som-kontot. Något av dessa har tagits bort eller inte skapats – Azure Active Directory program, tjänstens huvud namn, roll, Automation-certifikatets till gång, Automation-anslutning till gång – eller så är tumavtrycket inte identiskt mellan certifikat och anslutning.

  **Rekommenderad åtgärd**: ta bort och återskapa sedan [Kör som-kontot](../automation/manage-runas-account.md).

- **Fel**: Azure kör som-certifikatet som används av Automation-kontot upphör snart att gälla.

  Det självsignerade certifikatet som skapas för kör som-kontot går ut ett år från datumet då det skapades. Du kan förnya det när som helst innan det upphör att gälla. Om du har registrerat dig för e-postaviseringar får du också e-postmeddelanden när en åtgärd krävs från din sida. Det här felet visas två månader före utgångs datumet och ändras till ett kritiskt fel om certifikatet har upphört att gälla. När certifikatet har gått ut fungerar inte automatisk uppdatering förrän du förnyar samma.

  **Rekommenderad åtgärd**: Lös problemet genom att välja **Reparera** och **Förnya certifikat**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="förnya – cert":::

  > [!NOTE]
  > När du har förnyat certifikatet uppdaterar du sidan för att visa aktuell status.
