---
title: "Migrera virtuella datorer i Azure mellan Azure-regioner med hjälp av Azure Site Recovery | Microsoft Docs"
description: "Använd Azure Site Recovery för att migrera Azure IaaS-VM från en Azure-region till en annan."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/07/2018
ms.author: raynew
ms.openlocfilehash: e7b925d2daed11ee4e070cda6bcbd4a3511d9c17
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="migrate-azure-vms-to-another-region"></a>Migrera virtuella Azure-datorer till en annan region

Förutom att använda den [Azure Site Recovery](site-recovery-overview.md) -tjänsten för att hantera och samordna haveriberedskap för lokala datorer och virtuella Azure-datorer för affärskontinuitet och haveriberedskap (BCDR) du kan också använda plats Återställning för att hantera migreringen av virtuella Azure-datorer till en sekundär region. För att migrera virtuella datorer i Azure måste du aktivera replikering för dem och dem över från den primära regionen till den sekundära regionen önskat.

Den här kursen visar hur du migrerar virtuella Azure-datorer till en annan region. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett Recovery services-valv
> * Aktivera replikering för en virtuell dator
> * Kör en redundansväxling för att migrera den virtuella datorn

Den här kursen förutsätter att du redan har en Azure-prenumeration. Om du inte, skapar en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

>[!NOTE]
>
> Site Recovery replikering för virtuella datorer i Azure är för närvarande under förhandsgranskning.



## <a name="prerequisites"></a>Förutsättningar

Den här kursen behöver du Azure virtuella datorer i en Azure-region som du vill migrera. Dessutom finns många inställningar som du bör kontrollera innan du börjar.


### <a name="verify-target-resources"></a>Kontrollera target-resurser

1. Kontrollera att din Azure-prenumeration kan du skapa virtuella datorer i målregionen som används för katastrofåterställning. Kontakta supporten om du vill aktivera kvoten som krävs.

2. Kontrollera att din prenumeration har tillräckligt med resurser för att stödja virtuella datorer med storlekar som matchar dina virtuella källdatorer. Site Recovery hämtar samma storlek eller den närmaste möjliga storleken för mål VM.


### <a name="verify-account-permissions"></a>Kontrollera behörigheter

Om du just har skapat din kostnadsfria Azure-konto är du administratör för din prenumeration. Om du inte är administratör för prenumerationen kan du arbeta med administratören att tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en ny virtuell dator, måste du ha:

1. Behörighet att skapa en virtuell dator i Azure-resurser. Virtual Machine-deltagare inbyggda rollen har de behörigheter som omfattar:
    - Behörighet att skapa en virtuell dator i den valda resursgruppen
    - Behörighet att skapa en virtuell dator i det valda virtuella nätverket
    - Behörighet att skriva till det valda lagringskontot

2. Du måste också behörighet att hantera Azure Site Recovery-åtgärder. Site Recovery-deltagare rollen har alla behörigheter som krävs för att hantera Site Recovery-åtgärder i Recovery Services-valvet.


### <a name="verify-vm-outbound-access"></a>Kontrollera VM utgående åtkomst

1. Kontrollera att du inte använder en autentiseringsproxy för att kontrollera nätverksanslutningen för virtuella datorer du vill migrera. 
2. För den här kursen antar vi att de virtuella datorerna som du vill migrera kan ansluta till internet och inte använder en en brandväggen proxy för utgående åtkomstkontroll. Om du kan kontrollera kraven [här](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

### <a name="verify-vm-certificates"></a>Verifiera certifikat för VM

Kontrollera att de senaste rotcertifikat finns på Azure virtuella datorer som du vill migrera. Om de senaste rotcertifikat inte kan inte den virtuella datorn registreras till Site Recovery på grund av säkerhetsbegränsningar.

- Installera de senaste uppdateringarna på den virtuella datorn för virtuella Windows-datorer, så att alla betrodda rotcertifikat som finns på datorn. I en frånkopplad miljö Följ standard Windows Update och certifikatet update processer för din organisation.
- För Linux virtuella datorer, följer du de riktlinjer som tillhandahålls av din Linux-distributören att hämta den senaste betrodda rotcertifikat och listan över återkallade certifikat på den virtuella datorn.



## <a name="create-a-vault"></a>Skapa ett valv

Skapa valvet i varje region, utom området källa.

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Nytt** > **Övervakning + hantering** > **Backup och Site Recovery**.
3. I **namn**, ange det egna namnet **ContosoVMVault**. Om du har mer än en prenumeration väljer du en.
4. Skapa en resursgrupp **ContosoRG**.
5. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. För att snabbt komma åt valvet från instrumentpanelen, klickar du på **fäst på instrumentpanelen** och klicka sedan på **skapa**.

   ![Nytt valv](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

Det nya valvet har lagts till i den **instrumentpanelen** under **alla resurser**, och på primära **Recovery Services-valv** sidan.






## <a name="select-the-source"></a>Välj källa

1. Klicka på Recovery Services-valv **ConsotoVMVault** > **+ replikera**.
2. I **källa**väljer **Azure - PREVIEW**.
3. I **datakällplats**, väljer du den Azure-region där din virtuella dator körs för närvarande.
4. Välj distributionsmodell hanteraren för filserverresurser. Välj sedan den **källresursgruppen**.
5. Spara inställningarna genom att klicka på **OK**.


## <a name="enable-replication-for-azure-vms"></a>Aktivera replikering för virtuella Azure-datorer

Site Recovery hämtar en lista över de virtuella datorerna som är associerade med prenumerationen och resursgruppen.


1. I Azure-portalen klickar du på **virtuella datorer**.
2. Välj den virtuella datorn som du vill migrera. Klicka sedan på **OK**.
3. I **inställningar**, klickar du på **Disaster recovery (förhandsgranskning)**.
4. I **konfigurera katastrofåterställning** > **målregionen** väljer du den mål-region som du replikerar.
5. Acceptera de andra standardinställningarna för den här kursen.
6. Klicka på **Aktivera replikering**. Detta startar ett jobb för att aktivera replikering för den virtuella datorn.

    ![Aktivera replikering](media/tutorial-migrate-azure-to-azure/settings.png)

>[!NOTE]
  >
  > Replikering av virtuella Azure-datorer med hanterade diskar stöds för närvarande inte. 

## <a name="run-a-failover"></a>Köra en redundansväxling

1. I **inställningar** > **replikerade objekt**, klicka på datorn och klicka sedan på **redundans**.
2. I **redundans**väljer **senaste**. Krypteringsinställningen inte är relevanta för det här scenariot.
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den Virtuella källdatorn innan växling vid fel. Redundans fortsätter även om avstängning misslyckas. Du kan följa förloppet för växling vid fel på den **jobb** sidan.
4. Kontrollera att den virtuella Azure-datorn visas i Azure som förväntat.
5. I **replikerade objekt**, högerklicka på den virtuella datorn > **slutföra migreringen**. Detta avslutar migreringsprocessen och stoppar replikering för den virtuella datorn.



## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen migrerat du en virtuell Azure-dator till en annan Azure-region. Du kan nu konfigurera katastrofåterställning för den migrerade virtuella datorn.

> [!div class="nextstepaction"]
> [Ställ in återställning efter migreringen](azure-to-azure-quickstart.md)

