---
title: " Ta bort ett Recovery Services-valv i Azure | Microsoft Docs "
description: "Den här artikeln förklarar hur du tar bort Recovery Services-valvet. Artikeln innehåller åtgärder för felsökning när du försöker ta bort ett valv, men det går inte att."
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
ms.date: 12/20/2017
ms.author: markgal;trinadhk
ms.openlocfilehash: 4f4a92159b01b197984130c15195419e1b166fd3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="delete-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv
Den här artikeln förklarar hur du tar bort ett Recovery Services-valv i Azure-portalen. Om du har säkerhetskopieringsvalv, har de konverterats till Recovery Services-valv.   

Ta bort Recovery Services-valvet är autentiseringsprocessen - *angivna valvet inte innehåller några resurser*. Innan du kan ta bort Recovery Services-valvet, måste du ta bort eller ta bort alla resurser i valvet. Om du försöker ta bort ett valv som innehåller resurser får du ett felmeddelande som följande bild:

![Fel vid borttagning av valvet](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Tills du har avmarkerat resurser från valvet, klickar du på **försök** ger samma fel. Om du har fastnat på det här felmeddelandet klickar du på **Avbryt** och Använd följande steg för att ta bort resurserna i valvet.

## <a name="removing-items-from-a-vault-protecting-a-vm"></a>Ta bort objekt från ett valv som skyddar en VM
Om du redan har Recovery Services-valvet öppna går du vidare till nästa steg.

1. Öppna Azure portal och öppnar du vill ta bort valvet från instrumentpanelen.

   Om du inte har Recovery Services-valvet fäst på instrumentpanelen på navmenyn klickar du på **fler tjänster** och Skriv i listan över resurser, **återställningstjänster**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Recovery Services-valv**.

   ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Listan över Recovery Services-valv visas. Välj valvet som du vill ta bort från listan.

   ![Välj valvet från lista](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. I vyn valvet titta på den **Essentials** fönstret. Om du vill ta bort ett valv, får inte det finnas några skyddade objekt. Om den **säkerhetskopiering objekt** eller **säkerhetskopiera hanteringsservrar** Visa inte noll, måste du ta bort dessa objekt. Du kan inte ta bort valvet om den innehåller data.

    ![Titta på Essentials fönstret för skyddade objekt](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    Virtuella datorer och filer/mappar betraktas objekt för säkerhetskopiering och visas i den **säkerhetskopiering objekt** i fönstret Essentials. En DPM-server ingår i den **säkerhetskopiering hanteringsservern** i fönstret Essentials. **Replikerade objekt** hör till Azure Site Recovery-tjänsten.
3. Om du vill ta bort de skyddade objekten från valvet, hitta objekt i valvet. I valvet instrumentpanelen klickar du på **inställningar**, och klicka sedan på **Säkerhetskopiera objekt** att öppna menyn.

    ![Välj valvet från lista](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    Den **säkerhetskopiering objekt** menyn har separata listor, baserat på vilken typ av objekt: Azure virtuella datorer eller filmappar (se bilden). Standard objekttypen listan som visas är Azure virtuella datorer. Om du vill visa listan över mappar objekt i valvet, Välj **-mappar** från den nedrullningsbara menyn.
4. Innan du kan ta bort ett objekt från valvet skyddar en VM, måste du stoppa objektets säkerhetskopieringsjobbet och ta bort punkten återställningsdata. Följ dessa steg för varje objekt i valvet:

    a. På den **Säkerhetskopieringsobjekt** -menyn, högerklicka på objektet och på snabbmenyn Välj **stoppa säkerhetskopiering**.

    ![Stoppa jobbet](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    Stoppa säkerhetskopiering menyn öppnas.

    b. På den **stoppa säkerhetskopiering** menyn från den **väljer du ett alternativ** väljer du **ta bort säkerhetskopieringsdata** > skriver du namnet på objektet > och klicka på **stoppa säkerhetskopiering**.

    Skriv namnet på objektet om du vill kontrollera att du vill ta bort den. Den **stoppa säkerhetskopiering** knappen aktiveras när du har gjort objektet. Om du inte ser dialogrutan för att ange namnet på objektet säkerhetskopiering som du har valt den **behålla säkerhetskopierade Data** alternativet.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Du kan också kan du ange en orsak till varför du vill ta bort data och lägga till kommentarer. När du klickar på **stoppa säkerhetskopiering**, att ta bort jobbet ska slutföras innan du försöker ta bort valvet. Kontrollera den Azure-meddelanden för att verifiera att jobbet har slutförts, ![ta bort säkerhetskopieringsdata](./media/backup-azure-delete-vault/messages.png). <br/>
    När jobbet är klart, tjänsten skickar ett meddelande: säkerhetskopieringen har stoppats och den säkerhetskopiera informationen har tagits bort.

    c. När du tar bort ett objekt i listan på den **säkerhetskopiering objekt** -menyn klickar du på **uppdatera** kvarvarande objekt i valvet.

      ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/empty-items-list.png)

      När det finns inga objekt i listan, bläddra till den **Essentials** rutan i menyn Recovery Services-valvet. Det får inte finnas något **Säkerhetskopiera objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt** visas. Om objekt visas fortfarande i valvet, återgår till steg tre och välj ett annat objekt typen lista.  
5. När det finns inga fler objekt i verktygsfältet valvet, klickar du på **ta bort**.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-vault.png)
6. Kontrollera att du vill ta bort valvet, klicka på **Ja**.

    Valvet tas bort och portalen återgår till den **ny** service-menyn.

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>Vad händer om jag stoppats säkerhetskopieringen utan behålls data?
Om du har stoppat säkerhetskopieringsprocessen men oavsiktligt *behålls* data, måste du ta bort den säkerhetskopiera informationen innan du kan ta bort valvet. Ta bort säkerhetskopierade data:

1. På den **Säkerhetskopieringsobjekt** -menyn högerklickar du på objektet och klickar på snabbmenyn **ta bort säkerhetskopieringsdata**.

    ![Ta bort säkerhetskopierade data](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    Den **ta bort säkerhetskopierade Data** menyn öppnas.
2. På den **ta bort säkerhetskopierade Data** menyn, skriver du namnet på objektet och klicka på **ta bort**.

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

    Om du vill ta bort ett valv, måste du rensa eller ta bort valvet av skyddade data. Om det finns många återställningspunkter och data i skyddsgruppen, kan det ta flera minuter att ta bort data. Den **stoppa skydd** visar när jobbet har slutförts.

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

    ![Öppna produktionsservrar meny](./media/backup-azure-delete-vault/delete-production-server.png)

    Den **produktionsservrar** menyn öppnas och visar en lista över alla produktionsservrar i valvet.

    ![lista över produktionsservrar](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. På den **produktionsservrar** -menyn, högerklicka på servern och på **ta bort**.

    ![ta bort produktionsservern ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    Den **ta bort** menyn öppnas.

    ![ta bort produktionsservern ](./media/backup-azure-delete-vault/delete-blade.png)
3. På den **ta bort** menyn bekräfta servernamnet och klicka på **ta bort**. Du måste namnet på servern för att aktivera den **ta bort** knappen.

    När valvet har tagits bort, får du ett meddelande om valvet har tagits bort. När du tar bort alla servrar i valvet, bläddrar du till fönstret Essentials i valvet instrumentpanelen.
4. I instrumentpanelen för valvet, kontrollera att det finns inga **säkerhetskopiering objekt**, **säkerhetskopiera hanteringsservrar**, eller **replikerade objekt**. Klicka på verktygsfältet valvet **ta bort**.
5. Kontrollera att du vill ta bort valvet, klicka på **Ja**.

    Valvet tas bort och portalen återgår till den **ny** service-menyn.

## <a name="find-the-backup-management-servers-registered-to-the-vault"></a>Hitta säkerhetskopiering hanteringsservrar registrerad för valvet
Om du har flera servrar har registrerats ett valv, kan det vara svårt att komma ihåg dem. Se de servrar som registrerad för valvet och ta bort dem:

1. Öppna instrumentpanelen valvet.
2. I den **Essentials** rutan klickar du på **inställningar** att öppna menyn.

    ![Öppna inställningsmenyn](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. På den **inställningar** -menyn klickar du på **säkerhetskopiering infrastruktur**.
4. På den **säkerhetskopiering infrastruktur** -menyn klickar du på **Säkerhetskopieringshanteringsservrar**. Säkerhetskopieringshanteringsservrar menyn öppnas.

    ![lista över säkerhetskopiering hanteringsservrar](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Högerklicka på namnet på servern för att ta bort en server i listan och klicka sedan på **ta bort**.
    Den **ta bort** menyn öppnas.
6. På den **ta bort** menyn och ange namnet på servern. Du kan kopiera och klistra in den från listan över Säkerhetskopieringshanteringsservrar om det är ett långt namn. Klicka på **ta bort**.  
