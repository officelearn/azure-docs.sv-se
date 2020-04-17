---
title: Hantera och övervaka SÄKERHETSKOPIERingar av MARS-agent
description: Lär dig hur du hanterar och övervakar SÄKERHETSKOPIERing av Microsoft Azure Recovery Services (MARS) Agent med hjälp av Azure Backup-tjänsten.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 404341c8324d9e127e8d8e6bc8083926c0d3106f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537363"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Hantera säkerhetskopiering av Microsoft Azure Recovery Services (MARS) Agent med hjälp av Azure Backup-tjänsten

I den här artikeln beskrivs hur du hanterar filer och mappar som säkerhetskopieras med Microsoft Azure Recovery Services Agent.

## <a name="modify-a-backup-policy"></a>Ändra en princip för säkerhetskopiering

När du ändrar principer för säkerhetskopiering kan du lägga till nya objekt, ta bort befintliga objekt från säkerhetskopian eller utesluta filer från att säkerhetskopieras med hjälp av Uteslutningsinställningar.

- **Lägg till objekt** använder det här alternativet endast för att lägga till nya objekt som ska säkerhetskopieras. Om du vill ta bort befintliga objekt använder du alternativet **Ta bort objekt** eller **Uteslutningsinställningar.**  
- **Ta bort objekt** använd det här alternativet för att ta bort objekt från att säkerhetskopieras.
  - Använd **Uteslutningsinställningar** för att ta bort alla objekt i en volym i stället för **Ta bort objekt**.
  - Om du rensar alla val i en volym behålls gamla säkerhetskopior av objekten, enligt kvarhållningsinställningarna vid tidpunkten för den senaste säkerhetskopieringen, utan utrymme för ändringar.
  - Om du vill markera dessa objekt igen, leder till en första fullständig säkerhetskopiering och nya principändringar tillämpas inte på gamla säkerhetskopior.
  - Om hela volymen inte är avstöjt behåller du tidigare säkerhetskopiering utan utrymme för att ändra bevarandeprincipen.
- **Uteslutningsinställningar** använder det här alternativet för att utesluta att vissa objekt säkerhetskopieras.

### <a name="add-new-items-to-existing-policy"></a>Lägga till nya objekt i befintlig princip

1. Klicka på **Schemalägg säkerhetskopiering**i **Åtgärder.**

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-configure-vault/schedule-first-backup.png)

2. På fliken **Välj principobjekt** och välj **Ändra säkerhetskopieringsschema för dina filer och mappar** och klicka på **Nästa**.

    ![Välj principobjekt](./media/backup-azure-manage-mars/select-policy-items.png)

3. I **Fliken Ändra eller stoppa schemalägg säkerhetskopiering** väljer du Gör ändringar i **säkerhetskopieringsobjekt eller tidstider** och klickar på **Nästa**.

    ![Ändra eller schemalägga säkerhetskopiering](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Klicka på Lägg till **objekt** på fliken **Välj objekt** på fliken Markera objekt om du vill lägga till de objekt som du vill säkerhetskopiera.

    ![Ändra eller schemalägga säkerhetskopiera lägga till objekt](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. I fönstret **Välj objekt** väljer du flugor eller mappar som du vill lägga till och klickar på **OK**.

    ![Markera objekten](./media/backup-azure-manage-mars/select-item.png)

6. Slutför de efterföljande stegen och klicka på **Slutför** för att slutföra åtgärden.

### <a name="add-exclusion-rules-to-existing-policy"></a>Lägga till undantagsregler i befintlig princip

Du kan lägga till undantagsregler för att hoppa över filer och mappar som du inte vill ska säkerhetskopieras. Du kan göra detta när du definierar en ny princip eller ändrar en befintlig princip.

1. Klicka på **Schemalägg säkerhetskopiering**i åtgärdsfönstret . Gå till **Markera objekt till Säkerhetskopiering** och klicka på **Uteslutningsinställningar**.

    ![Markera objekten](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Klicka på Lägg till uteslutning i **uteslutningsinställningar.** **Add Exclusion**

    ![Markera objekten](./media/backup-azure-manage-mars/add-exclusion.png)

3. Bläddra bland de filer och mappar som du vill utesluta i **Välj objekt som ska uteslutas**och klicka på **OK**.

    ![Markera objekten](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Som standard är alla **undermappar** i de markerade mapparna undantagna. Du kan ändra detta genom att välja **Ja** eller **Nej**. Du kan redigera och ange vilka filtyper som ska uteslutas enligt nedan:

    ![Markera objekten](./media/backup-azure-manage-mars/subfolders-type.png)

5. Slutför de efterföljande stegen och klicka på **Slutför** för att slutföra åtgärden.

### <a name="remove-items-from-existing-policy"></a>Ta bort objekt från befintlig princip

1. Klicka på **Schemalägg säkerhetskopiering**i åtgärdsfönstret . Gå till **Välj objekt till Säkerhetskopiering**. Markera de filer och mappar som du vill ta bort från säkerhetskopieringsschemat i listan och klicka på **Ta bort objekt**.

    ![Markera objekten](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> Var försiktig när du tar bort en volym helt från principen.  Om du behöver lägga till den igen, kommer det att behandlas som en ny volym. Nästa schemalagda säkerhetskopiering utför en inledande säkerhetskopiering (fullständig säkerhetskopiering) i stället för inkrementell säkerhetskopiering. Om du tillfälligt behöver ta bort och lägga till objekt senare rekommenderas att du använder Inställningar för **undantag** i stället för **Ta bort objekt** för att säkerställa inkrementell säkerhetskopiering i stället för fullständig säkerhetskopiering.

2. Slutför de efterföljande stegen och klicka på **Slutför** för att slutföra åtgärden.

## <a name="stop-protecting-files-and-folder-backup"></a>Sluta skydda filer och mappsäkerhetskopiering

Det finns två sätt att sluta skydda säkerhetskopiering av filer och mappar:

- **Stoppa skyddet och behåll säkerhetskopierade data**.
  - Det här alternativet stoppar alla framtida säkerhetskopieringsjobb från skydd.
  - Azure Backup-tjänsten behåller alla befintliga återställningspunkter på obestämd tid. Återställningspunkter kontrolleras inte för utgångsdatum förrän skyddet har återupptagits.
  - Du kan återställa säkerhetskopierade data för oexpirerade återställningspunkter.
  - Om du bestämmer dig för att återuppta skyddet kan du använda alternativet *Aktivera schema för säkerhetskopiering.* Därefter skulle data lagras baserat på den nya bevarandeprincipen.
- **Stoppa skyddet och ta bort säkerhetskopierade data**.
  - Det här alternativet stoppar alla framtida säkerhetskopieringsjobb från att skydda dina data och ta bort alla återställningspunkter.
  - Du får ett e-postmeddelande om dataavisering för säkerhetskopiering med ett meddelande *Dina data för det här säkerhetskopieringsobjektet har tagits bort. Dessa data kommer att vara tillfälligt tillgängliga i 14 dagar, varefter de kommer att tas bort permanent* och rekommenderade åtgärder *Reprotect säkerhetskopieringsobjektet inom 14 dagar för att återställa dina data.*
  - Om du vill återuppta skyddet försöker du rotera igen inom 14 dagar från borttagningen.

### <a name="stop-protection-and-retain-backup-data"></a>Stoppa skyddet och behåll säkerhetskopierade data

1. Öppna HANTERINGSKONSOLEN FÖR MARS, gå till **åtgärdsfönstret**och **välj Schemalägg säkerhetskopiering**.

    ![Ändra eller stoppa en schemalagd säkerhetskopia.](./media/backup-azure-manage-mars/mars-actions.png)
1. På sidan **Välj principobjekt** väljer du **Ändra ett säkerhetskopieringsschema för dina filer och mappar** och klickar på **Nästa**.

    ![Ändra eller stoppa en schemalagd säkerhetskopia.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. På sidan **Ändra eller Stoppa en schemalagd säkerhetskopiering** väljer du Sluta använda det här **säkerhetskopieringsschemat, men behåll de lagrade säkerhetskopiorna tills ett schema aktiveras igen**. Välj sedan **Nästa**.

    ![Ändra eller stoppa en schemalagd säkerhetskopia.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Granska informationen i **Pausad schemalagd säkerhetskopiering** och klicka på **Slutför**.

    ![Ändra eller stoppa en schemalagd säkerhetskopia.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. I **Ändra säkerhetskopiering kontrollerar** du att återställningspausen för schema säkerhetskopiering är i framgångsstatus och klickar **nära** för att slutföra.

### <a name="stop-protection-and-delete-backup-data"></a>Stoppa skyddet och ta bort säkerhetskopierade data

1. Öppna HANTERINGSKONSOLEN FÖR MARS, gå till **åtgärdsfönstret** och välj **Schemalägg säkerhetskopiering**.
2. På sidan **Ändra eller Stoppa en schemalagd säkerhetskopiering** väljer du Sluta använda det här **säkerhetskopieringsschemat och tar bort alla lagrade säkerhetskopior**. Välj sedan **Nästa**.

    ![Ändra eller stoppa en schemalagd säkerhetskopia.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Välj **Slutför**på sidan **Stoppa en schemalagd säkerhetskopiering** .

    ![Stoppa en schemalagd säkerhetskopia.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Du uppmanas att ange en säkerhets-PIN -kod (personnummer), som du måste generera manuellt. Det gör du genom att logga in på Azure-portalen.
5. Gå till**Settings** > **Egenskaper**för inställningar för **återställningstjänsters valv** > .
6. Under **Säkerhets-PIN**väljer du **Generera**. Kopiera den här PIN-koden. PIN-koden är giltig i endast fem minuter.
7. Klistra in PIN-koden i hanteringskonsolen och välj sedan **OK**.

    ![Generera en säkerhets-PIN-kod.](./media/backup-azure-delete-vault/security-pin.png)

8. På sidan **Ändra för säkerhetskopiering** visas följande meddelande: *Borttagna säkerhetskopierade data sparas i 14 dagar. Efter den tiden tas säkerhetskopierade data bort permanent.*  

    ![Ta bort infrastrukturen för säkerhetskopiering.](./media/backup-azure-delete-vault/deleted-backup-data.png)

När du har tagit bort de lokala säkerhetskopieringsobjekten följer du nästa steg från portalen.

## <a name="re-enable-protection"></a>Återaktivera skydd

Om du stoppade skyddet medan du behöll data och bestämde dig för att återuppta skyddet kan du återaktivera säkerhetskopieringsschemat med hjälp av ändra säkerhetskopieringsprincipen.

1. På **Åtgärder** väljer du **Schemalägg säkerhetskopiering**.
1. Välj **Aktivera säkerhetskopieringsschema igen. Du kan också ändra säkerhetskopieringsobjekt eller tidar** och klicka på **Nästa**.<br>

    ![Ta bort infrastrukturen för säkerhetskopiering.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Klicka på **Nästa**i **Välj objekt som ska säkerhetskopieras**.

    ![Ta bort infrastrukturen för säkerhetskopiering.](./media/backup-azure-manage-mars/re-enable-next.png)
1. Ange schemat **för säkerhetskopiering**i Ange säkerhetskopieringsschema och klicka på **Nästa**.
1. Ange **kvarhållningslängd**i Välj bevarandeprincip och klicka på **Nästa**.
1. Slutligen på **bekräftelseskärmen** granskar du principinformationen och klickar på **Slutför**.

## <a name="re-generate-passphrase"></a>Återskapa lösenfras

En lösenfras används för att kryptera och dekryptera data samtidigt som du säkerhetskopierar eller återställer din lokala eller lokala dator med MARS-agenten till eller från Azure. Om du har tappat bort eller glömt lösenfrasen kan du återskapa lösenfrasen (förutsatt att datorn fortfarande är registrerad i Recovery Services Vault och säkerhetskopian är konfigurerad) genom att följa dessa steg:

- Gå till egenskaper för **Ändring** > av**åtgärder** > från MARS-agentkonsolen. Gå sedan till **fliken Kryptering**.<br>
- Välj **Kryssrutan Ändra lösenfras.**<br>
- Ange en ny lösenfras eller klicka på **Generera lösenfras**.
- Klicka på **Bläddra** om du vill spara den nya lösenfrasen.

    ![Generera lösenfras.](./media/backup-azure-manage-mars/passphrase.png)
- Klicka på **OK** om du vill tillämpa ändringar.  Om [säkerhetsfunktionen](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) är aktiverad på Azure-portalen för Recovery Services Vault uppmanas du att ange säkerhets-PIN-koden. Om du vill ta emot PIN-koden följer du stegen i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations).<br>
- Klistra in säkerhets-PIN-koden från portalen och klicka på **OK** för att tillämpa ändringarna.<br>

    ![Generera lösenfras.](./media/backup-azure-manage-mars/passphrase2.png)
- Kontrollera att lösenfrasen sparas på ett säkert sätt på en annan plats (förutom källdatorn), helst i Azure Key Vault. Håll reda på alla lösenfraser om du har flera datorer som säkerhetskopieras med MARS-agenterna.


## <a name="next-steps"></a>Nästa steg

- Information om scenarier och begränsningar som stöds finns i [supportmatrisen för MARS-agenten](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- Läs mer om [lagringsbeteende för säkerhetskopiering av principer för säkerhetskopiering på begäran](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
