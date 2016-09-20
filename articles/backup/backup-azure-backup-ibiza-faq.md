<properties
   pageTitle="Vanliga frågor och svar om Recovery Services-valv | Microsoft Azure"
   description="Den här versionen av Vanliga frågor och svar gäller den offentliga förhandsversionen av tjänsten Azure Backup. Svar på vanliga frågor om säkerhetskopieringsagenten, säkerhetskopiering och kvarhållning, återställning, säkerhet och andra vanliga frågor om Azure Backup-lösningen."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="lösning för säkerhetskopiering; säkerhetskopieringstjänst"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="08/21/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# Recovery Services-valv – vanliga frågor och svar

> [AZURE.SELECTOR]
- [Vanliga frågor och svar om säkerhetskopiering i klassiskt läge](backup-azure-backup-faq.md)
- [Vanliga frågor och svar om säkerhetskopiering i Resource Manager-läge](backup-azure-backup-ibiza-faq.md)

Den här artikeln innehåller information om Recovery Services-valv och är ett komplement till [Vanliga frågor och svar om Azure Backup](backup-azure-backup-faq). Vanliga frågor och svar om Azure Backup är en fullständig uppsättning frågor och svar om Azure Backup-tjänsten.  

Du kan ställa frågor om Azure Backup i Disqus-rutan i den här eller en relaterad artikel. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Stöds fortfarande Backup-valv (klassiskt läge) om Recovery Services-valven är Resource Manager-baserade? <br/>
Ja, Backup-valv stöds fortfarande. Skapa Backup-valv på den [klassiska portalen](https://manage.windowsazure.com). Skapa Recovery Services-valv på [Azure Portal](https://portal.azure.com). Vi rekommenderar dock att du skapar Recovery Services-valv eftersom alla framtida förbättringar endast blir tillgängliga i Recovery Services-valv. 

## Kan jag migrera ett Backup-valv till ett Recovery Services-valv? <br/>
Tyvärr inte. För närvarande kan du inte migrera innehållet i ett Backup-valv till ett Recovery Services-valv. Vi arbetar för att lägga till den här funktionen, men den är inte tillgänglig som en del av den offentliga förhandsversionen.

## Stöder Recovery Services-valv klassiska virtuella datorer eller Resource Manager-baserade virtuella datorer? <br/>
Recovery Services-valv stöder båda modellerna.  Du kan säkerhetskopiera en virtuell dator som skapats i den klassiska portalen (vilket är virtuella datorer i klassiskt läge) eller en virtuell dator som skapats i Azure Portal (som är Resource Manager-baserad) till ett Recovery Services-valv.

## Jag har säkerhetskopierat mina klassiska virtuella datorer i säkerhetskopieringsvalvet. Nu vill jag migrera mina virtuella datorer från klassiskt läge till Resource Manager-läge.  Hur kan jag säkerhetskopiera dem i Recovery Services-valv?
Säkerhetskopior av klassiska virtuella datorer i säkerhetskopieringsvalvet migreras inte automatiskt till Recovery Services-valvet när du migrerar de virtuella datorerna från klassiskt till Resource Manager-läge. Följ dessa steg för att migrera säkerhetskopior för virtuella datorer:

1. I säkerhetskopieringsvalvet går du till fliken **Skyddade objekt** och väljer den virtuella datorn. Klicka på [Stoppa skydd](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Lämna alternativet *Ta bort associerade säkerhetskopieringsdata* **avmarkerat**. 
2. Migrera den virtuella datorn från klassiskt läge till Resource Manager-läge. Kontrollera att lagring och nätverk för virtuell dator också migreras till Resource Manager-läge. 
3. Skapa ett Recovery Services-valv och konfigurera säkerhetskopiering på den migrerade virtuella datorn med åtgärden **Backup** på valvets instrumentpanel. Läs mer om hur du [aktiverar säkerhetskopiering i Recovery Services-valv](backup-azure-vms-first-look-arm.md).



<!--HONumber=sep16_HO1-->


