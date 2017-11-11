---
title: " Ta bort ett Recovery Services-valv i Azure | Microsoft Docs "
description: "Hur du tar bort ett Azure Backup och Recovery Services-valv. Ett säkerhetskopieringsvalv kan anropas i en Azure-molnet valvet eller Azure recovery-valvet. Felsökning av problem när du inte ta bort ett säkerhetskopieringsvalv i den klassiska portalen eller Azure-portalen."
services: service-name
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 5fa08157-2612-4020-bd90-f9e3c3bc1806
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/10/2017
ms.author: markgal;trinadhk
ms.openlocfilehash: b07b9e01a5a8d8a5189b130fb5a9baeef7a43f4f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="delete-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv
Azure Backup-tjänsten har två typer av valv – Backup-valvet och Recovery Services-valvet. Först kom Backup-valvet. Recovery Services-valvet tillkom senare som stöd för utökade Resource Manager-distributioner. På grund av utökade funktioner och information beroenden som måste vara lagrad i valvet, kan det vara förvirrande att ta bort en säkerhetskopiering eller Recovery Services-valvet. Den här artikeln förklarar hur du tar bort valv i den klassiska portalen och Azure portal.  

| **Distributionstyp** | **Portal** | **Valvnamnet** |
| --- | --- | --- |
| Klassisk |Klassisk |Säkerhetskopieringsvalvet |
| Resource Manager |Azure |Recovery Services-valv |

> [!NOTE]
> Backup-valvet kan inte skydda Resource Manager-distribuerade lösningar. Du kan dock använda en Recovery Services-valvet för att skydda classically distribuerade servrar och virtuella datorer.  
>

> [!IMPORTANT]
> Nu kan du uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Mer information finns i artikeln [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Uppgradera ett säkerhetskopieringsvalv till ett Recovery Services-valv). Microsoft rekommenderar att du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valv.<br/> Efter **30 November 2017**, kommer du inte kunna använda PowerShell för att skapa säkerhetskopieringsvalv. <br/> **Med 30 November 2017**:
>- Alla återstående säkerhetskopieringsvalv uppgraderas automatiskt till Recovery Services-valv.
>- Du kan inte längre komma åt dina säkerhetskopierade data i den klassiska portalen. Använd i stället Azure Portal till att få åtkomst till dina säkerhetskopierade data i Recovery Services-valv.
>

I den här artikeln använder vi termen, valvet, att referera till formuläret i säkerhetskopieringsvalvet eller Recovery Services-valvet. Vi använder formella namn, säkerhetskopieringsvalvet eller Recovery Services-valvet, när det är nödvändigt att skilja mellan valv.

## <a name="deleting-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv
Ta bort Recovery Services-valvet är autentiseringsprocessen - *angivna valvet inte innehåller några resurser*. Innan du kan ta bort Recovery Services-valvet, måste du ta bort eller ta bort alla resurser i valvet. Om du försöker ta bort ett valv som innehåller resurser får du ett felmeddelande som följande bild:

![Fel vid borttagning av valvet](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Tills du har avmarkerat resurser från valvet, klickar du på **försök** ger samma fel. Om du har fastnat på det här felmeddelandet klickar du på **Avbryt** och Använd följande steg för att ta bort resurserna i valvet.

### <a name="removing-the-items-from-a-vault-protecting-a-vm"></a>Ta bort objekt från ett valv som skyddar en VM
Om du redan har Recovery Services-valvet öppna går du vidare till nästa steg.

1. Öppna Azure portal och öppnar du vill ta bort valvet från instrumentpanelen.

   Om du inte har Recovery Services-valvet fäst på instrumentpanelen på navmenyn klickar du på **fler tjänster** och Skriv i listan över resurser, **återställningstjänster**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Recovery Services-valv**.

   ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Listan över Recovery Services-valv visas. Välj valvet som du vill ta bort från listan.

   ![Välj valvet från lista](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. I vyn valvet titta på den **Essentials** fönstret. Om du vill ta bort ett valv, får inte det finnas några skyddade objekt. Om du ser ett annat värde än noll under **säkerhetskopiering objekt** eller **säkerhetskopiera hanteringsservrar**, måste du ta bort dessa objekt innan du kan ta bort valvet.

    ![Titta på Essentials fönstret för skyddade objekt](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    Virtuella datorer och filer/mappar betraktas objekt för säkerhetskopiering och visas i den **säkerhetskopiering objekt** i fönstret Essentials. En DPM-server ingår i den **säkerhetskopiering hanteringsservern** i fönstret Essentials. **Replikerade objekt** hör till Azure Site Recovery-tjänsten.
3. Om du vill ta bort de skyddade objekten från valvet, hitta objekt i valvet. I valvet instrumentpanelen klickar du på **inställningar**, och klicka sedan på **Säkerhetskopiera objekt** att öppna det bladet.

    ![Välj valvet från lista](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    Den **säkerhetskopiering objekt** bladet har separata listor, baserat på vilken typ av objekt: Azure virtuella datorer eller filmappar (se bilden). Standard objekttypen listan som visas är Azure virtuella datorer. Om du vill visa listan över mappar objekt i valvet, Välj **-mappar** från den nedrullningsbara menyn.
4. Innan du kan ta bort ett objekt från valvet skyddar en VM, måste du stoppa objektets säkerhetskopieringsjobbet och ta bort punkten återställningsdata. Följ dessa steg för varje objekt i valvet:

    a. På den **Säkerhetskopieringsobjekt** bladet, högerklicka på objektet och på snabbmenyn Välj **stoppa säkerhetskopiering**.

    ![Stoppa jobbet](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    Stoppa säkerhetskopiering blad öppnas.

    b. På den **stoppa säkerhetskopiering** bladet från den **väljer du ett alternativ** väljer du **ta bort säkerhetskopieringsdata** > skriver du namnet på objektet > och klicka på **stoppa säkerhetskopiering**.

    Skriv namnet på objektet om du vill kontrollera att du vill ta bort den. Den **stoppa säkerhetskopiering** knappen aktiveras när du har gjort objektet. Om du inte ser dialogrutan för att ange namnet på objektet säkerhetskopiering som du har valt den **behålla säkerhetskopierade Data** alternativet.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Du kan också kan du ange en orsak till varför du vill ta bort data och lägga till kommentarer. När du klickar på **stoppa säkerhetskopiering**, att ta bort jobbet ska slutföras innan du försöker ta bort valvet. Kontrollera den Azure-meddelanden för att verifiera att jobbet har slutförts, ![ta bort säkerhetskopieringsdata](./media/backup-azure-delete-vault/messages.png). <br/>
    När jobbet har slutförts får du ett meddelande om säkerhetskopieringen har stoppats och säkerhetskopierade data för att objektet har tagits bort.

    c. När du tar bort ett objekt i listan på den **säkerhetskopiering objekt** -menyn klickar du på **uppdatera** kvarvarande objekt i valvet.

      ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/empty-items-list.png)

      När det finns inga objekt i listan, bläddra till den **Essentials** rutan i bladet Backup-valvet. Det får inte finnas något **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas. Om objekt visas fortfarande i valvet, återgår till steg tre och välj ett annat objekt typen lista.  
5. När det finns inga fler objekt i verktygsfältet valvet, klickar du på **ta bort**.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-vault.png)
6. Kontrollera att du vill ta bort valvet, klicka på **Ja**.

    Valvet tas bort och portalen återgår till den **ny** service-menyn.

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>Vad händer om jag stoppats säkerhetskopieringen utan behålls data?
Om du har stoppat säkerhetskopieringsprocessen men oavsiktligt *behålls* data, måste du ta bort den säkerhetskopiera informationen innan du kan ta bort valvet. Ta bort säkerhetskopierade data:

1. På den **Säkerhetskopieringsobjekt** bladet, högerklicka på objektet och klickar på snabbmenyn **ta bort säkerhetskopieringsdata**.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Den **ta bort säkerhetskopierade Data** blad öppnas.
2. På den **ta bort säkerhetskopierade Data** bladet skriver du namnet på objektet och klickar på **ta bort**.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-retained-vault.png)

    När du har tagit bort data tillbaka till steg 4c och fortsätta med processen.

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>Ta bort ett valv som används för att skydda en DPM-server
Innan du kan ta bort ett valv som används för att skydda DPM-servern måste du rensa några återställningspunkter som har skapats och sedan avregistrera servern från valvet.

Ta bort data som är associerade med en skyddsgrupp:

1. I DPM-administratörskonsolen klickar du på **skydd** > Välj en skyddsgrupp > Välj Skyddsgruppsmedlemmen > och klicka på verktygsfliken **ta bort**.

  Välj Skyddsgruppsmedlem att aktivera den **ta bort** knapp i menyfliksområdet. I det här exemplet medlemmen är **dummyvm9**. Om du vill välja flera medlemmar i skyddsgruppen, håll ned Ctrl-tangenten medan du klickar på medlemmar.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    Den **stoppa skydd** öppnas.
2. I den **stoppa skydd** markerar **ta bort skyddade data**, och klicka på **stoppa skydd**.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    Om du vill ta bort ett valv, måste du rensa eller ta bort valvet av skyddade data. Beroende på antalet återställningspunkter och data i skyddsgruppen, kan det ta var som helst från några sekunder till flera minuter att ta bort data. Den **stoppa skydd** dialogrutan visar status när jobbet har slutförts.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. Fortsätt för alla medlemmar i alla skyddsgrupper.

    Ta bort alla skyddade data och skydd grupper.
4. Växla till Azure-portalen när du tar bort alla medlemmar från skyddsgruppen. Öppna instrumentpanelen valvet och kontrollera att det finns inga **säkerhetskopiering objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt**. Klicka på verktygsfältet valvet **ta bort**.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-vault.png)

    Om det finns säkerhetskopieringshanteringsservrar registrerad för valvet, kan du ta bort valvet även om det finns inga data i valvet. Om du har tagit bort säkerhetskopieringshanteringsservrar som är kopplad till valvet, men det finns servrar som anges i den **Essentials** fönstret finns [hitta säkerhetskopieringshanteringsservrar registrerad för valvet](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault).
5. Kontrollera att du vill ta bort valvet, klicka på **Ja**.

    Valvet tas bort och portalen återgår till den **ny** service-menyn.

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>Ta bort ett valv som används för att skydda en produktionsserver
Innan du kan ta bort ett valv som används för att skydda en produktionsserver, måste du ta bort eller avregistrera servern från valvet.

Ta bort produktionsservern som är kopplad till valvet:

1. Öppna valvet instrumentpanelen i Azure-portalen och på **inställningar** > **säkerhetskopiering infrastruktur** > **produktionsservrar**.

    ![Öppna bladet produktionsservrar](./media/backup-azure-delete-vault/delete-production-server.png)

    Den **produktionsservrar** blad öppnas och visar en lista över alla produktionsservrar i valvet.

    ![lista över produktionsservrar](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. På den **produktionsservrar** bladet, högerklicka på servern och på **ta bort**.

    ![ta bort produktionsservern ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    Den **ta bort** blad öppnas.

    ![ta bort produktionsservern ](./media/backup-azure-delete-vault/delete-blade.png)
3. På den **ta bort** bladet bekräftar servernamnet och klickar på **ta bort**. Du måste namnet på servern för att aktivera den **ta bort** knappen.

    När valvet har tagits bort, får du ett meddelande om valvet har tagits bort. När du tar bort alla servrar i valvet, bläddrar du till fönstret Essentials i valvet instrumentpanelen.
4. I instrumentpanelen för valvet, kontrollera att det finns inga **säkerhetskopiering objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt**. Klicka på verktygsfältet valvet **ta bort**.
5. Kontrollera att du vill ta bort valvet, klicka på **Ja**.

    Valvet tas bort och portalen återgår till den **ny** service-menyn.

## <a name="delete-a-backup-vault-in-classic-portal"></a>Ta bort ett säkerhetskopieringsvalv i klassiska portalen
Följande instruktioner används för att ta bort ett säkerhetskopieringsvalv i den klassiska portalen. Innan du kan ta bort säkerhetskopieringsvalvet, du måste ta bort återställningspunkter, säkerhetskopieras objekt och ta bort de registrerade servrarna. De registrerade servrarna är Windows Server, arbetsstation eller virtuella datorer som har registrerats i valvet.

1. Öppna den [klassisk portal](https://manage.windowsazure.com).

2. Välj valvet som du vill ta bort från listan över säkerhetskopieringsvalv.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/classic-portal-delete-vault-open-vault.png)

    Valvet instrumentpanelen öppnas. Titta på antalet Windows-servrar och/eller Azure virtuella datorer som är kopplad till valvet. Också titta på det totala lagringsutrymmet som används i Azure. Stoppa alla säkerhetskopieringsjobb och ta bort alla data innan du tar bort valvet.

3. Klicka på den **skyddade objekt** fliken och klicka sedan på **stoppa skydd**

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/classic-portal-delete-vault-stop-protect.png)

    Den **upphöra med skyddet av ditt valv** visas.
4. I den **upphöra med skyddet av ditt valv** dialogrutan Kontrollera **ta bort associerade säkerhetskopieringsdata** och klicka på ![markering](./media/backup-azure-delete-vault/checkmark.png). <br/>
    Du kan du kan också välja en orsak till att stoppa skydd och ange en kommentar.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/classic-portal-delete-vault-verify-stop-protect.png)

    När du tar bort objekt i valvet, är valvet tom.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/classic-portal-delete-vault-post-delete-data.png)
5. I listan över flikar, klickar du på **registrerade objekt**. Den **typen** nedrullningsbara menyn kan du välja vilken typ av server som är registrerad för valvet. Typen kan vara Windows Server eller virtuell dator i Azure. Välj den virtuella dator som är registrerad för valvet som i exemplet nedan och klickar på **avregistrera**.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/classic-portal-unregister.png)

  Om du vill ta bort registreringen för en Windows-Server från den **typen** nedrullningsbara menyn och väljer **Windows Server**, klickar du på ![markering](./media/backup-azure-delete-vault/checkmark.png) att uppdatera skärmen och sedan Klicka på **ta bort**. <br/>

  ![Välj Windows Server](./media/backup-azure-delete-vault/select-windows-server.png)

6. I listan över flikar, klickar du på **instrumentpanelen** att öppna fliken. Kontrollera att det finns inga registrerade servrar eller Azure virtuella datorer som skyddas i molnet. Kontrollera också att det finns inga data i lagringen. Klicka på **ta bort** att ta bort valvet.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/classic-portal-list-of-tabs-dashboard.png)

    Ta bort Backup-valvet bekräftelseskärm öppnas. Välj ett alternativ varför du tar bort valvet och klicka på ![markering](./media/backup-azure-delete-vault/checkmark.png). <br/>

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/classic-portal-delete-vault-confirmation-1.png)

    Valvet tas bort och du kommer tillbaka till instrumentpanelen för den klassiska portalen.

### <a name="find-the-backup-management-servers-registered-to-the-vault"></a>Hitta säkerhetskopiering hanteringsservrar registrerad för valvet
Om du har flera servrar har registrerats ett valv, kan det vara svårt att komma ihåg dem. Se de servrar som registrerad för valvet och ta bort dem:

1. Öppna instrumentpanelen valvet.
2. I den **Essentials** rutan klickar du på **inställningar** att öppna det bladet.

    ![Öppna inställningsbladet](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. På den **inställningsbladet**, klickar du på **säkerhetskopiering infrastruktur**.
4. På den **säkerhetskopiering infrastruktur** bladet, klickar du på **Säkerhetskopieringshanteringsservrar**. Säkerhetskopieringshanteringsservrar blad öppnas.

    ![lista över säkerhetskopiering hanteringsservrar](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Högerklicka på namnet på servern för att ta bort en server i listan och klicka sedan på **ta bort**.
    Den **ta bort** blad öppnas.
6. På den **ta bort** bladet anger du namnet på servern. Du kan kopiera och klistra in den från listan över Säkerhetskopieringshanteringsservrar om det är ett långt namn. Klicka på **ta bort**.  
