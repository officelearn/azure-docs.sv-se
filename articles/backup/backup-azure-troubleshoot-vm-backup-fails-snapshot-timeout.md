---
title: 'Felsöka Azure Backup-fel: Gäst Agent Status otillgänglig | Microsoft Docs'
description: Symptom, orsaker och lösningar Azure Backup-fel som rör agent, tillägg och -diskar.
services: backup
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
keywords: Azure-säkerhetskopiering; VM-agent. Nätverksanslutningen;
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: genli;markgal;sogup;
ms.openlocfilehash: de3fcc4abcc8558066d9e524011047d6a117f4e5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Felsöka Azure Backup-fel: problem med agenten eller tillägg

Den här artikeln innehåller felsökning som kan hjälpa dig att lösa Azure Backup fel relaterade till kommunikationen med VM-agent och tillägg.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>VM-agenten kunde inte kommunicera med Azure Backup

Felmeddelande: ”VM-agenten kunde inte kommunicera med Azure Backup”<br>
Felkod: ”UserErrorGuestAgentStatusUnavailable”

När du registrerar och schemalägga en virtuell dator för Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med den Virtuella datoragenten att ta en ögonblicksbild i tidpunkt. Något av följande villkor kan förhindra att ögonblicksbilden som utlöses. När en ögonblicksbild inte utlöses, misslyckas säkerhetskopieringen. Slutför följande felsökningssteg i angiven ordning och försök sedan utföra åtgärden:

**Orsak 1: [den virtuella datorn inte har tillgång till internet](#the-vm-has-no-internet-access)**  
**Orsak 2: [agenten är installerad på den virtuella datorn, men det är inte svarar (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Orsak 3: [agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 4: [går inte att hämta status för ögonblicksbild eller en ögonblicksbild kan inte utföras](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**Orsak 5: [tillägget säkerhetskopiering misslyckas med att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>Ögonblicksbild åtgärden misslyckas eftersom den virtuella datorn inte är ansluten till nätverket

Felmeddelande: ”ögonblicksbild misslyckades på grund av ingen nätverksanslutning på den virtuella datorn”<br>
Felkod: ”ExtensionSnapshotFailedNoNetwork”

När du registrerar och schemalägga en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden som utlöses. Om ögonblicksbilden inte utlöses, kan det uppstå en säkerhetskopieringen har misslyckats. Slutför följande felsökningssteg i angiven ordning och försök sedan utföra åtgärden:    
**Orsak 1: [den virtuella datorn inte har tillgång till internet](#the-vm-has-no-internet-access)**  
**Orsak 2: [går inte att hämta status för ögonblicksbild eller en ögonblicksbild kan inte utföras](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 3: [tillägget säkerhetskopiering misslyckas med att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>VMSnapshot misslyckas tillägget

Felmeddelande: ”VMSnapshot tillägget misslyckades”<br>
Felkod: ”ExtentionOperationFailed”

När du registrerar och schemalägga en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden som utlöses. Om ögonblicksbilden inte utlöses, kan det uppstå en säkerhetskopieringen har misslyckats. Slutför följande felsökningssteg i angiven ordning och försök sedan utföra åtgärden:  
**Orsak 1: [går inte att hämta status för ögonblicksbild eller en ögonblicksbild kan inte utföras](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 2: [tillägget säkerhetskopiering misslyckas med att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 3: [agenten är installerad på den virtuella datorn, men det är inte svarar (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 4: [agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>Det går inte att säkerhetskopiera eftersom VM-agenten inte svarar

Felmeddelande: ”Det går inte att utföra åtgärden eftersom den Virtuella Datoragenten inte svarar” <br>
Felkod: ”GuestAgentSnapshotTaskStatusError”

När du registrerar och schemalägga en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden som utlöses. Om ögonblicksbilden inte utlöses, kan det uppstå en säkerhetskopieringen har misslyckats. Slutför följande felsökningssteg i angiven ordning och försök sedan utföra åtgärden:  
**Orsak 1: [agenten är installerad på den virtuella datorn, men det är inte svarar (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 2: [agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 3: [den virtuella datorn inte har tillgång till internet](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>Säkerhetskopiering misslyckas med ett internt fel

Felmeddelande: ”Det gick inte att säkerhetskopiera med ett internt fel - försök igen om några minuter” <br>
Felkod: ”BackUpOperationFailed” / ”BackUpOperationFailedV2”

När du registrerar och schemalägga en virtuell dator för Azure Backup-tjänsten startar Säkerhetskopiering jobbet genom att kommunicera med VM-tillägg att ta en ögonblicksbild i tidpunkt för säkerhetskopiering. Något av följande villkor kan förhindra att ögonblicksbilden som utlöses. Om ögonblicksbilden inte utlöses, kan det uppstå en säkerhetskopieringen har misslyckats. Slutför följande felsökningssteg i angiven ordning och försök sedan utföra åtgärden:  
**Orsak 1: [den virtuella datorn inte har tillgång till internet](#the-vm-has-no-internet-access)**  
**Orsak 2: [agenten installeras i den virtuella datorn, men inte svarar (för virtuella Windows-datorer)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Orsak 3: [agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Orsak 4: [går inte att hämta status för ögonblicksbild eller en ögonblicksbild kan inte utföras](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**Orsak 5: [tillägget säkerhetskopiering misslyckas med att uppdatera eller läsa in](#the-backup-extension-fails-to-update-or-load)**  
**Orsak 6: [Backup-tjänsten har inte behörighet att ta bort gamla återställningspunkter på grund av ett lås för resurs-grupp](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="the-vm-has-no-internet-access"></a>Den virtuella datorn har inte tillgång till internet
Per distributionskrav har inte den virtuella datorn Internetåtkomst. Eller så kanske den har begränsningar som förhindrar åtkomst till Azure-infrastrukturen.

Tillägget säkerhetskopiering kräver anslutning till Azure offentliga IP-adresser ska fungera. Tillägget skickar kommandon till en Azure storage-slutpunkt (http-URL) för hantering av ögonblicksbilder av den virtuella datorn. Om filnamnstillägget inte har åtkomst till internet, misslyckas säkerhetskopieringen förr eller senare.

####  <a name="solution"></a>Lösning
För att lösa problemet, försök med något av följande metoder:

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>Tillåt åtkomst till Azure storage som motsvarar regionen

Du kan använda [tjänsten taggar](../virtual-network/security-overview.md#service-tags) att tillåta anslutningar till lagring av specifik region. Kontrollera att den regel som tillåter åtkomst till lagringskontot har högre prioritet än regeln som blockerar Internetåtkomst. 

![Nätverkssäkerhetsgruppen med lagring taggar för en region](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

För att förstå de steg för steg om hur du konfigurerar tjänsten taggar kan du titta på [den här videon](https://youtu.be/1EjLQtbKm1M).

> [!WARNING]
> Storage service-taggar finns i förhandsgranskningen. De är endast tillgängliga i vissa regioner. En lista över regioner finns [tjänsten taggar för lagring](../virtual-network/security-overview.md#service-tags).

##### <a name="create-a-path-for-http-traffic"></a>Skapa en sökväg för HTTP-trafik

1. Om du har nätverksbegränsningar på plats (t.ex, en nätverkssäkerhetsgrupp) kan du distribuera en HTTP-proxyserver för att dirigera trafiken.
2. Om du vill tillåta åtkomst till internet från HTTP-proxyserver, att lägga till regler till nätverkssäkerhetsgruppen, om du har en.

Information om hur du ställer in en HTTP-proxy för VM-säkerhetskopieringar finns [förbereda din miljö för att säkerhetskopiera virtuella datorer i Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity).

Om du använder Azure hanterade diskar kan behöva ett inledande ytterligare porten (port 8443) på brandvägg.

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agenten är installerad på den virtuella datorn, men det är inte svarar (för virtuella Windows-datorer)

#### <a name="solution"></a>Lösning
Den Virtuella datoragenten kan vara skadad eller tjänsten kan ha stoppats. Installera om den Virtuella datoragenten hjälper dig att få den senaste versionen. Det kan också starta om kommunikation med tjänsten.

1. Avgör om Gästagenten för Windows-tjänsten körs i VM-tjänster (services.msc). Försök att starta om tjänsten Windows Gästagenten och starta säkerhetskopieringen.    
2. Om tjänsten Windows Gästagenten inte visas i tjänster på Kontrollpanelen, gå till **program och funktioner** avgör om Windows Gästagent-tjänsten är installerad.
4. Om Windows-Gästagenten visas i **program och funktioner**, avinstallera Windows-Gästagenten.
5. Hämta och installera den [senaste versionen av agenten MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörsrättigheter för att slutföra installationen.
6. Kontrollera att Gästagenten för Windows-tjänster som visas i tjänster.
7. Köra en säkerhetskopiering på begäran: 
    * I portalen, Välj **säkerhetskopiering nu**.

Kontrollera också att [Microsoft .NET 4.5 har installerats](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) i den virtuella datorn. .NET 4.5 krävs för VM-agenten kan kommunicera med tjänsten.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agenten som är installerad på den virtuella datorn är inaktuellt (för virtuella Linux-datorer)

#### <a name="solution"></a>Lösning
De flesta agent-relaterade tillägget-relaterade fel eller för Linux virtuella datorer orsakas av problem som påverkar en inaktuell VM-agent. Följ dessa allmänna riktlinjer för att felsöka problemet:

1. Följ instruktionerna för [uppdatering Linux VM-agenten](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Vi *rekommenderar* att du uppdaterar agenten endast via en lagringsplats för distribution. Vi rekommenderar inte hämta agent-koden direkt från GitHub och uppdatera den. Om den senaste agenten för din distribution inte är tillgänglig, kontakta distribution stöd för instruktioner om hur du installerar den. Om du vill söka efter den senaste agenten, gå till den [Windows Azure Linux-agenten](https://github.com/Azure/WALinuxAgent/releases) sidan på GitHub-lagringsplatsen.

2. Kontrollera att Azure-agenten körs på den virtuella datorn genom att köra följande kommando: `ps -e`

 Om processen inte körs startar du om den med hjälp av följande kommandon:

 * För Ubuntu: `service walinuxagent start`
 * För andra distributioner: `service waagent start`

3. [Konfigurera automatisk omstart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Kör en ny test-säkerhetskopia. Om felet kvarstår kan du samla in följande loggar från den virtuella datorn:

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * / var/logga/azure / *

Om vi behöver utförlig loggning för waagent gör du följande:

1. Leta upp följande rad i filen /etc/waagent.conf: **aktivera utförlig loggning (y | n)**
2. Ändra den **Logs.Verbose** värde från *n* till *y*.
3. Spara ändringarna och starta sedan om waagent genom att slutföra stegen som beskrivs tidigare i det här avsnittet.

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Går inte att hämta status för ögonblicksbild eller en ögonblicksbild kan inte utföras
VM-säkerhetskopiering är beroende av en ögonblicksbild kommandot utfärdas till det underliggande storage-kontot. Säkerhetskopieringen kan misslyckas eftersom den inte har åtkomst till lagringskontot eller eftersom körningen av uppgiften ögonblicksbild är försenad.

#### <a name="solution"></a>Lösning
Ögonblicksbilden misslyckas kan leda till att följande villkor:

| Orsak | Lösning |
| --- | --- |
| VM-statusen rapporteras felaktigt eftersom den virtuella datorn stängs av i Remote Desktop Protocol (RDP). | Om du stänger av den virtuella datorn i RDP Kontrollera portal för att avgöra om VM-statusen är korrekt. Om det inte är korrekt, stänger du den virtuella datorn i portalen med hjälp av den **avstängning** alternativet på VM-instrumentpanelen. |
| Den virtuella datorn kan inte hämta värden eller fabric-adress från DHCP. | DHCP måste vara aktiverat på gästen för IaaS VM-säkerhetskopiering för att fungera. Om den virtuella datorn inte kan hämta värden eller fabric-adress från DHCP-svar 245, kan inte den hämta eller köra alla tillägg. Om du behöver en statisk privat IP-adress kan du konfigurera det via plattformen. DHCP-alternativet inuti den virtuella datorn ska aktiveras vänster. Mer information finns i [ange en statisk IP för interna privata](../virtual-network/virtual-networks-reserved-private-ip.md). |

### <a name="the-backup-extension-fails-to-update-or-load"></a>Tillägget säkerhetskopiering misslyckas med att uppdatera eller läsa in
Om tilläggen inte kan ladda misslyckas säkerhetskopiering eftersom en ögonblicksbild inte kan utföras.

#### <a name="solution"></a>Lösning

Avinstallera tillägget om du vill tvinga tillägget VMSnapshot att läsa in. Nästa säkerhetskopiering försöket laddar tillägget.

Så här avinstallerar tillägget:

1. I den [Azure-portalen](https://portal.azure.com/)går du till den virtuella datorn som upplever säkerhetskopieringen har misslyckats.
2. Välj **inställningar**.
3. Välj **tillägg**.
4. Välj **Vmsnapshot tillägget**.
5. Välj **avinstallera**.

För Linux VM, om tillägget VMSnapshot inte visas i Azure-portalen [uppdatera Azure Linux-agenten](../virtual-machines/linux/update-agent.md), och sedan köra säkerhetskopieringen. 

Gör så här gör att tillägg installeras under nästa säkerhetskopiering.

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>Backup-tjänsten har inte behörighet att ta bort gamla återställningspunkter på grund av ett lås för resurs-grupp
Det här problemet är specifikt för hanterade virtuella datorer där användaren låser resursgruppen. I det här fallet säkerhetskopieringstjänsten kan inte ta bort äldre återställningspunkter. Eftersom det finns en gräns på 18 återställningspunkter, starta nya säkerhetskopior misslyckas.

#### <a name="solution"></a>Lösning

Utför följande steg för att ta bort samlingen återställning plats för att lösa problemet: <br>
 
1. Ta bort låset i resursgruppen där den virtuella datorn finns. 
2. Installera ARMClient med Chocolatey: <br>
   https://github.com/projectkudu/ARMClient
3. Logga in på ARMClient: <br>
    `.\armclient.exe login`
4. Hämta samlingen återställning punkt som motsvarar den virtuella datorn: <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    Exempel: `.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. Ta bort samlingen återställning punkt: <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. Under nästa schemalagda säkerhetskopiering skapar automatiskt en återställning punkt samling och nya återställningspunkter.

 
Problemet kan återkomma om du låser resursgruppen igen. 

