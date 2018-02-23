---
title: "Övervakaren Resource Manager distribuerade virtuella datorsäkerhetskopieringar | Microsoft Docs"
description: "Övervaka händelser och aviseringar från säkerhetskopiering för Resource Manager distribuerade virtuella datorer. Skicka e-post baserat på aviseringar."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: fed32015-2db2-44f8-b204-d89f6fd1bea2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: markgal;trinadhk;giridham;
ms.openlocfilehash: 1e9f6d44965e8a6cd9529ef860f0fb57fd8e572d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Övervaka varningar vid säkerhetskopiering av virtuella Azure-datorer
Aviseringar är svar från tjänsten för att en händelse tröskelvärdet har uppnåtts eller överskridits. Att veta när problem start kan vara viktigt att som företag kostnaderna hålls nere. Aviseringar, vanligtvis sker inte enligt ett schema och därför är det bra att känna så snart som möjligt efter aviseringar. När en säkerhetskopiering eller återställning av jobbet misslyckas, till exempel visas en varning inom fem minuter till felet. I instrumentpanelen för valvet visas panelen Backup aviseringar kritiskt och varningsnivå händelser. Du kan visa alla händelser i inställningarna för säkerhetskopiering aviseringar. Men vad gör du om en varning visas när du arbetar på ett separat problem? Om du inte vet när aviseringen händer, kan det bero på en mindre besvär eller det kan äventyra data. Kontrollera att rätt personer är medvetna om en avisering - när det uppstår genom att konfigurera tjänsten för att skicka aviseringar via e-post. Mer information om hur du konfigurerar e-postaviseringar finns [konfigurera meddelanden](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Hur hittar jag information om aviseringar?
Om du vill visa information om den händelse som utlöste en avisering, måste du öppna bladet säkerhetskopiering aviseringar. Det finns två sätt att öppna bladet säkerhetskopiering aviseringar: antingen från säkerhetskopia aviseringar panelen i valvet instrumentpanelen eller från bladet aviseringar och händelser.

Öppna bladet säkerhetskopiering aviseringar från panelen Backup aviseringar:

* På den **säkerhetskopiering aviseringar** panelen på valvet instrumentpanelen klickar du på **kritisk** eller **varning** operativa händelser för den allvarlighetsgraden.

    ![Aviseringar om säkerhetskopiering panelen](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Öppna bladet säkerhetskopiering aviseringar från bladet aviseringar och händelser:

1. Från instrumentpanelen valvet klickar du på **alla inställningar**. ![Alla inställningar för](./media/backup-azure-monitor-vms/all-settings-button.png)
2. På den **inställningar** bladet, klickar du på **aviseringar och händelser**. ![Aviseringar och händelser](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. På den **aviseringar och händelser** bladet, klickar du på **säkerhetskopiering aviseringar**. ![Säkerhetskopiera aviseringar knappen](./media/backup-azure-monitor-vms/backup-alerts.png)

    Den **säkerhetskopiering aviseringar** blad öppnas och visar de filtrerade varningarna.

    ![Aviseringar om säkerhetskopiering panelen](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Om du vill visa detaljerad information om en viss avisering i listan över händelser, klickar du på aviseringen för att öppna dess **information** bladet.

    ![Händelseinformation](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Om du vill anpassa de attribut som visas i listan finns [visa ytterligare händelse attribut](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Konfigurera meddelanden
 Du kan konfigurera tjänsten för att skicka e-postmeddelanden för aviseringar som uppstod under den senaste timmen eller när vissa typer av händelser inträffar.

Att ställa in e-postmeddelanden för aviseringar

1. Klicka på menyn säkerhetskopiering aviseringar **konfigurera meddelanden**

    ![Säkerhetskopiera aviseringar-menyn](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Konfigurera meddelanden blad öppnas.

    ![Konfigurera meddelanden bladet](./media/backup-azure-monitor-vms/configure-notifications.png)
2. Klicka på bladet konfigurera aviseringar för e-postmeddelanden **på**.

    Mottagarna och allvarlighetsgrad dialogrutor har en stjärna bredvid dem, eftersom denna information krävs. Ange minst en e-postadress och Välj minst en allvarlighetsgrad.
3. I den **mottagarna (e-post)** dialogrutan Skriv e-postadresserna för som ta emot meddelanden. Använd formatet: username@domainname.com. Avgränsa flera e-postadresser med semikolon (;).
4. I den **Avisera** området, Välj **Per avisering** att skicka meddelande när den angivna signalen eller **timvis sammanfattad** att skicka en sammanfattning för den senaste timmen.
5. I den **allvarlighetsgrad** dialogrutan Välj en eller flera nivåer som du vill ska utlösa e-postmeddelande.
6. Klicka på **Spara**.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Vilka aviseringstyper är tillgängliga för Azure IaaS-VM säkerhetskopiering?
   | Aviseringsnivå | Aviseringar skickas |
   | --- | --- |
   | Kritiska | om säkerhetskopieringen har misslyckats, Återställningsfel |
   | Varning | för säkerhetskopieringsjobb har slutförts med varningar (t.ex: vissa skrivare kunde inte skapa en ögonblicksbild) |
   | Information | finns för närvarande inga informationsaviseringar för Virtuella Azure-säkerhetskopiering |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Finns det situationer där det inte skickas någon e-post, även om konfigurationen anger att avisering ska skickas?
Det finns situationer där en avisering inte skickas, trots att meddelanden har konfigurerats korrekt. I följande situationer e-postmeddelande skickas inte meddelanden att undvika avisering brus:

* Om meddelanden har konfigurerats för varje timme sammanfattad och en avisering aktiveras som lösts inom en timme.
* Jobbet har avbrutits.
* Ett säkerhetskopieringsjobb utlöses och misslyckas och en annan säkerhetskopiering pågår.
* En schemalagd säkerhetskopiering för en Resource Manager-aktiverad virtuell startar, men den virtuella datorn finns inte längre.

## <a name="customize-your-view-of-events"></a>Anpassa visningen av händelser
Den **granskningsloggar** inställningen levereras med en fördefinierad uppsättning filter och kolumner visar operativa händelseinformation. Du kan anpassa vyn så att om den **händelser** blad öppnas, den visar den information du vill.

1. I den [valvet instrumentpanelen](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), bläddra till och klickar på **granskningsloggar** att öppna den **händelser** bladet.

    ![Granskningsloggar](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    Den **händelser** blad öppnas Funktionshändelser filtreras för aktuella valvet.

    ![Granskningsfiltret för loggar](./media/backup-azure-monitor-vms/audit-logs-filter.png)

    Bladet visar en lista över kritiska, fel, varning och informationshändelser som uppstod under den senaste veckan. Tidsintervallet är standardvärdet i den **Filter**. Den **händelser** bladet visar också ett stapeldiagram spårning när de inträffade. Om du inte vill se stapeldiagram i den **händelser** -menyn klickar du på **Dölj diagram** att växla ut diagrammet. Standardvyn för händelser visar information om åtgärden, nivå, Status, resurser och tid. Information om exponera ytterligare händelse attribut finns i avsnittet [expanderande händelseinformation](backup-azure-monitor-vms.md#view-additional-event-attributes).
2. Mer information om en arbetsloggen i den **åtgärden** kolumnen, klickar du på ett arbetsloggen för att öppna dess bladet. Bladet innehåller detaljerad information om händelser. Händelser grupperas efter deras Korrelations-ID och en lista över de händelser som inträffade i tidsintervallet.

    ![Åtgärdsinformation](./media/backup-azure-monitor-vms/audit-logs-details-window.png)
3. Om du vill visa detaljerad information om en viss händelse i listan över händelser, klickar du på händelsen för att öppna dess **information** bladet.

    ![Händelseinformation](./media/backup-azure-monitor-vms/audit-logs-details-window-deep.png)

    Händelsenivå informationen är detaljerad informationen hämtar. Om du hellre vill visa det här mycket information om varje händelse och vill lägga till det här mycket information till den **händelser** bladet finns i avsnittet [expanderande händelseinformation](backup-azure-monitor-vms.md#view-additional-event-attributes).

## <a name="customize-the-event-filter"></a>Anpassa händelsefilter
Använd den **Filter** justera eller Välj den information som visas i ett visst blad. Så här filtrerar händelseinformationen:

1. I den [valvet instrumentpanelen](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard), bläddra till och klickar på **granskningsloggar** att öppna den **händelser** bladet.

    ![Granskningsloggar](./media/backup-azure-monitor-vms/audit-logs-1606-1.png)

    Den **händelser** blad öppnas Funktionshändelser filtreras för aktuella valvet.

    ![Granskningsfiltret för loggar](./media/backup-azure-monitor-vms/audit-logs-filter.png)
2. På den **händelser** -menyn klickar du på **Filter** att öppna det bladet.

    ![Öppna bladet](./media/backup-azure-monitor-vms/audit-logs-filter-button.png)
3. På den **Filter** bladet justera de **nivå**, **tidsintervallet**, och **anroparen** filter. Andra filter är inte tillgängliga eftersom de har ställts in för att ange den aktuella informationen för Recovery Services-valvet.

    ![Granska informationen i loggarna frågan](./media/backup-azure-monitor-vms/filter-blade.png)

    Du kan ange den **nivå** av händelse: kritisk, fel, varning eller information. Du kan välja valfri kombination av händelsenivåerna, men du måste ha minst ett valt nivån. Aktivera nivån eller inaktivera. Den **tidsintervallet** filter kan du ange hur lång tid för att samla in händelser. Om du använder en anpassad tidsrymd, anger du start- och sluttider.
4. När du är redo att fråga efter operations loggarna använder du filtret, klickar du på **uppdatering**. Resultatet visas i den **händelser** bladet.

    ![Åtgärdsinformation](./media/backup-azure-monitor-vms/edited-list-of-events.png)

### <a name="view-additional-event-attributes"></a>Visa ytterligare händelse attribut
Med hjälp av den **kolumner** knappen, kan du aktivera ytterligare händelse attribut som ska visas i listan på den **händelser** bladet. Listan över händelser visar information för åtgärden, nivå, Status, resurser och tid. Aktivera ytterligare attribut:

1. På den **händelser** bladet, klickar du på **kolumner**.

    ![Öppna kolumner](./media/backup-azure-monitor-vms/audi-logs-column-button.png)

    Den **Välj kolumner** blad öppnas.

    ![Bladet för kolumner](./media/backup-azure-monitor-vms/columns-blade.png)
2. Klicka på kryssrutan för att välja attributet. Attributet kryssrutan kopplar på och av.
3. Klicka på **återställa** att återställa listan med attribut i den **händelser** bladet. När du lägger till eller ta bort attribut i listan, använda **återställa** att visa den nya listan över händelse attribut.
4. Klicka på **uppdatera** att uppdatera data i attribut. Följande tabell innehåller information om varje attribut.

| Kolumnnamn | Beskrivning |
| --- | --- |
| Åtgärd |Namnet på åtgärden |
| Nivå |Nivån av åtgärden värdena kan vara: information, varning, fel eller kritiskt |
| Status |Beskrivande tillstånd för åtgärden |
| Resurs |URL-adress som identifierar resursen. kallas även resurs-ID |
| Tid |Tid, mätt från den aktuella tiden när händelsen inträffade |
| Anropare |Vem eller vad kallas eller utlöste händelsen; kan vara systemet eller en användare |
| Tidsstämpel |Den tid när händelsen har utlösts |
| Resursgrupp |Associerade resursgruppen. |
| Resurstyp |Den interna resurstyp som används av Resource Manager |
| Prenumerations-ID:t |Associerade prenumerations-ID |
| Kategori |Kategori av händelsen |
| Korrelations-id |Vanliga ID för relaterade händelser |

## <a name="use-powershell-to-customize-alerts"></a>Använd PowerShell för att anpassa aviseringar
Du kan hämta anpassade aviseringar för jobben i portalen. Definiera PowerShell-baserade Varningsregler operativa loggar händelser för att få dessa jobb. Använd *PowerShell version 1.3.0 eller senare*.

Om du vill definiera ett anpassat meddelande för att varna för Säkerhetskopieringsfel, använder du ett kommando som följande skript:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.RecoveryServices/recoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/Microsoft.RecoveryServices/vaults/trinadhVault -Actions $actionEmail
```

**ResourceId** : du kan hämta ResourceId från granskningsloggarna. Resurs-ID är en URL som anges i kolumnen resurs åtgärden loggar.

**OperationName** : OperationName är i formatet ”Microsoft.RecoveryServices/recoveryServicesVault/*EventName*” där *EventName* kan vara:<br/>

* Registrera dig <br/>
* Avregistrera <br/>
* ConfigureProtection <br/>
* Backup <br/>
* Återställ <br/>
* StopProtection <br/>
* DeleteBackupData <br/>
* CreateProtectionPolicy <br/>
* DeleteProtectionPolicy <br/>
* UpdateProtectionPolicy <br/>

**Status för** : värden som stöds är igång, lyckades eller misslyckades.

**ResourceGroup** : Detta är den resursgrupp som resursen tillhör. Du kan lägga till kolumnen resursgruppen genererade loggar. Resursgruppen är en av de tillgängliga typerna av händelseinformation.

**Namnet** : namnet på regeln.

**CustomEmail** : Ange anpassade e-postadressen som du vill skicka en avisering

**SendToServiceOwners** : det här alternativet skickar varningsmeddelanden till alla administratörer och medadministratörer i prenumerationen. Den kan användas i **ny AzureRmAlertRuleEmail** cmdlet

### <a name="limitations-on-alerts"></a>Begränsningar för aviseringar
Händelsebaserat aviseringar regleras följande begränsningar:

1. Aviseringar aktiveras på alla virtuella datorer i Recovery Services-valvet. Du kan anpassa varningen för en delmängd av virtuella datorer i en Recovery Services-valvet.
2. Den här funktionen är i förhandsgranskningen. [Läs mer](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. Aviseringar skickas från ”alerts-noreply@mail.windowsazure.com”. För närvarande kan du ändra avsändarens e-post.

## <a name="next-steps"></a>Nästa steg
Händelseloggar aktivera bra post före och gransknings-och stöd för säkerhetskopieringsåtgärder. Följande åtgärder loggas:

* Registrera dig
* Avregistrera
* Konfigurera skydd
* Säkerhetskopiering (både schemalagd samt säkerhetskopiering på begäran)
* Återställ
* Stoppa skydd
* Ta bort säkerhetskopierade data
* Lägg till princip
* Ta bort princip
* Uppdatera principen
* Avbryt jobb

En bred beskrivning av händelser och åtgärder och granskningsloggar i Azure-tjänster finns i artikeln [visa händelser och granskningsloggar](../monitoring-and-diagnostics/insights-debugging-with-events.md).

Information om hur du skapar en virtuell dator från en återställningspunkt igen kolla [återställa virtuella Azure-datorer](backup-azure-arm-restore-vms.md). Om du behöver information om hur du skyddar virtuella datorer, se [förhandstitt: Säkerhetskopiera virtuella datorer till ett Recovery Services-valv](backup-azure-vms-first-look-arm.md). Lär dig mer om hanteringsaktiviteter för VM-säkerhetskopieringar i artikeln [hantera Azure-säkerhetskopiering för virtuella datorer](backup-azure-manage-vms.md).
