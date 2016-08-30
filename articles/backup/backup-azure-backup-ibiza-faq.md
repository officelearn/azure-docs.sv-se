<properties
   pageTitle="Vanliga frågor och svar om den offentliga förhandsversionen av tjänsten Azure Backup | Microsoft Azure"
   description="Den här versionen av Vanliga frågor och svar gäller den offentliga förhandsversionen av tjänsten Azure Backup. Svar på vanliga frågor om säkerhetskopieringsagenten, säkerhetskopiering och kvarhållning, återställning, säkerhet och andra vanliga frågor om Azure Backup-lösningen."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="backup solution; backup service"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="03/30/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# Vanliga frågor och svar om den offentliga förhandsversionen av tjänsten Azure Backup

> [AZURE.SELECTOR]
- [Vanliga frågor och svar om säkerhetskopiering i klassiskt läge](backup-azure-backup-faq.md)
- [Vanliga frågor och svar om säkerhetskopiering i ARM-läge](backup-azure-backup-ibiza-faq.md)

Den här artikeln innehåller information om den offentliga förhandsversionen av tjänsten Azure Backup. Artikeln uppdateras när nya vanliga frågor kommer in och kompletterar [Vanliga frågor och svar om Azure Backup](backup-azure-backup-faq). Vanliga frågor och svar om Azure Backup är en fullständig uppsättning frågor och svar om Azure Backup-tjänsten.  

Du kan ställa frågor om Azure Backup i Disqus-rutan i den här eller en relaterad artikel. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Vad ingår i den offentliga förhandsversionen?
Den offentliga förhandsversionen introducerar Recovery Services-valvet och ARM-stöd när du skyddar virtuella datorer i Azure. Recovery Services-valvet är nästa generations valv. Det är valvet som används av Azure Backup och Azure Site Recovery-tjänster (ASR). Tänk dig det som v.2-valvet.

## Recovery Services- och Backup-valv

**F1. Recovery Services-valven är alltså v.2, men stöds Backup-valv (v.1) fortfarande?** <br/>
S1. Ja, Backup-valv stöds fortfarande. Skapa Backup-valv på den klassiska portalen. Skapa Recovery Services-valv på Azure-portalen.

**F2. Kan jag migrera ett Backup-valv till ett Recovery Services-valv?** <br/>
S2. Tyvärr inte. För närvarande kan du inte migrera innehållet i ett Backup-valv till ett Recovery Services-valv. Vi arbetar för att lägga till den här funktionen, men den är inte tillgänglig som en del av den offentliga förhandsversionen.

**F3. Har Recovery Services-valv stöd för virtuella v.1- eller v.2-datorer?** <br/>
 S3. Recovery Services-valv stöder virtuella v.1- och v.2-datorer. Du kan säkerhetskopiera en virtuell dator som skapats på den klassiska portalen (vilket är v.1) eller en virtuell dator som skapats på Azure-portalen (vilket är v.2) till ett Recovery Services-valv.


## ARM-stöd för virtuella datorer i Azure

**F1. Finns det några begränsningar med ARM-stödet för virtuella datorer i Azure?** <br/>
S1. PowerShell-cmdlets för ARM är för närvarande inte tillgängliga. Du måste använda användargränssnittet på Azure-portalen när du lägger till resurser i en resursgrupp.



<!--HONumber=jun16_HO2-->


