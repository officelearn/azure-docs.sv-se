---
title: Återställa virtuella VMware-datorer med Azure Backup Server
description: Använd Azure Backup Server (MABS) för att återställa virtuella VMware-datorer som körs på en VMware-vCenter/ESXi-server.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: ab2fb4f8f79fa5a664f5cb0ba1bb537c1df658c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212367"
---
# <a name="restore-vmware-virtual-machines"></a>Återställa virtuella VMware-datorer

I den hÃ¤r artikeln beskrivs hur du anvÃ¤nder Microsoft Azure Backup Server (MABS) fÃ¥ã¥ fÃ¥r fÃ¥r vmware-återställningspunkter fÃ¤r kÃ¤r. En översikt över hur du använder MABS för att återställa data finns i [Återställa skyddade data](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server). I MABS Administrator Console finns det två sätt att hitta återställningsbara data - sök eller bläddra. När du återställer data kan du, eller kanske inte vill återställa data eller en virtuell dator till samma plats. Av denna anledning stöder MABS tre återställningsalternativ för VMware VM-säkerhetskopior:

* **Ursprunglig platsåterställning (OLR)** - Använd OLR för att återställa en skyddad virtuell dator till dess ursprungliga plats. Du kan bara återställa en virtuell dator till den ursprungliga platsen om inga diskar har lagts till eller tagits bort sedan säkerhetskopian inträffade. Om diskar har lagts till eller tagits bort måste du använda alternativ platsåterställning.

* **Alternativ platsåterställning (ALR)** - När den ursprungliga virtuella datorn saknas, eller om du inte vill störa den ursprungliga virtuella datorn, återställer du den virtuella datorn till en annan plats. Om du vill återställa en virtuell dator till en alternativ plats måste du ange platsen för en ESXi-värd, resurspool, mapp och lagringsdatalager och sökväg. För att skilja den återställda virtuella datorn från den ursprungliga virtuella datorn lägger MABS till "-Recovered" till namnet på den virtuella datorn.

* **Återställning av enskilda filplatser (ILR)** – Om den skyddade virtuella datorn är en virtuell windows server-dator kan enskilda filer/mappar inuti den virtuella datorn återställas med hjälp av MABS-ilr-kapacitet. Om du vill återställa enskilda filer läser du proceduren senare i den här artikeln.

## <a name="restore-a-recovery-point"></a>Återställa en återställningspunkt

1. Klicka på Återställningsvy i MABS-administratörskonsolen.

2. Använd bläddra i fönstret Bläddra eller filtrera för att hitta den virtuella dator som du vill återställa. När du har valt en virtuell dator eller mapp visar återställningspunkterna för fönstret tillgängliga återställningspunkter.

    ![Tillgängliga återställningspunkter](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. I fältet **Återställningspunkter** använder du kalendern och listrutan för att välja ett datum när en återställningspunkt togs. Kalenderdatum i fetstil har tillgängliga återställningspunkter.

4. Öppna **återställningsguiden**i verktygsfliksområdet. **Recover**

    ![Återställningsguide, Granska val av återställning](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Klicka på **Nästa** om du vill gå vidare till skärmen **Ange återställningsalternativ.**

6. Om du vill aktivera begränsning av nätverksbandbredd på skärmen **Ange återställningsalternativ** klickar du på **Ändra**. Om du vill lämna nätverksbegränsningen inaktiverad klickar du på **Nästa**. Det finns inga andra alternativ på den här guideskärmen för virtuella datorer med VMware. Om du väljer att ändra nätverksbandbreddsbegränsningen väljer du Aktivera begränsning av **nätverksbandbredd** i dialogrutan Begränsning för att aktivera den. När du har aktiverat **konfigurerar du inställningar** och **arbetsschema**.

7. På skärmen **Välj återställningstyp** väljer du om du vill återställa till den ursprungliga förekomsten eller till en ny plats och klickar på **Nästa**.

     * Om du väljer **Återställ till originalinstans**behöver du inte göra fler val i guiden. Data för den ursprungliga instansen används.

     * Om du väljer **Återställ som virtuell dator på en värd –** ange sedan information för **ESXi-värd, resurspool, mapp** och **sökväg**på skärmen **Ange mål** .

      ![Välj återställningstyp](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. Granska inställningarna på skärmen **Sammanfattning** och klicka på **Återställ** för att starta återställningsprocessen. Skärmen **Återställningsstatus** visar återställningsåtgärdens förlopp.

## <a name="restore-an-individual-file-from-a-vm"></a>Återställa en enskild fil från en virtuell dator

Du kan återställa enskilda filer från en skyddad vm-återställningspunkt. Den här funktionen är endast tillgänglig för virtuella windows server-datorer. Återställa enskilda filer liknar återställa hela den virtuella datorn, förutom att du bläddrar in i VMDK och hitta de filer du vill, innan du startar återställningsprocessen. Så här återställer du en enskild fil eller väljer filer från en virtuell Windows Server:To recover an individual file or select files from a Windows Server VM:

>[!NOTE]
>Återställa en enskild fil från en virtuell dator är endast tillgänglig för Windows VM och Disk Recovery Points.

1. Klicka på **Återställningsvy** i MABS-administratörskonsolen.

2. Använd **bläddra i** fönstret Bläddra eller filtrera för att hitta den virtuella dator som du vill återställa. När du har valt en virtuell dator eller mapp visar återställningspunkterna för fönstret tillgängliga återställningspunkter.

    ![Tillgängliga återställningspunkter](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. I fönstret **Återställningspunkter för:** använder du kalendern för att välja det datum som innehåller önskade återställningspunkter. Beroende på hur säkerhetskopieringsprincipen har konfigurerats kan datum ha mer än en återställningspunkt. När du har valt den dag då återställningspunkten togs kontrollerar du att du har valt rätt **återställningstid**. Om det valda datumet har flera återställningspunkter väljer du återställningspunkten genom att välja den på den nedrullningsbara menyn Återställningstid. När du har valt återställningspunkten visas listan över återställningsbara objekt i fönstret **Sökväg:** .

4. Om du vill hitta de filer som du vill återställa dubbelklickar du på objektet i kolumnen **Återställningsbara objekt** för att öppna det i fönstret **Sökväg.** Markera den fil, de filer eller mappar som du vill återställa. Om du vill markera flera objekt trycker du på **Ctrl** när du markerar varje objekt. Använd **sökfönstret Sök** i listan över filer eller mappar som visas i kolumnen **Återställbart objekt.** **Söklistan nedan** söker inte i undermappar. Om du vill söka igenom undermappar dubbelklickar du på mappen. Använd knappen **Upp** för att flytta från en underordnad mapp till den överordnade mappen. Du kan markera flera objekt (filer och mappar), men de måste finnas i samma överordnade mapp. Du kan inte återställa objekt från flera mappar i samma återställningsjobb.

    ![Granska val av återställning](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. När du har markerat objektet/objekten för återställning klickar du på **Återställ** i menyfliksområdet för administratörskonsolen för att öppna **återställningsguiden**. På återställningsguiden visar skärmen **Granskningsåterställningsval** de markerade objekt som ska återställas.

6. Om du vill aktivera begränsning av nätverksbandbredd på skärmen **Ange återställningsalternativ** klickar du på **Ändra**. Om du vill lämna nätverksbegränsningen inaktiverad klickar du på **Nästa**. Det finns inga andra alternativ på den här guideskärmen för virtuella datorer med VMware. Om du väljer att ändra nätverksbandbreddsbegränsningen väljer du Aktivera begränsning av **nätverksbandbredd** i dialogrutan Begränsning för att aktivera den. När du har aktiverat **konfigurerar du inställningar** och **arbetsschema**.
7. Klicka på **Nästa**på skärmen **Välj återställningstyp** . Du kan bara återställa filen eller mapparna till en nätverksmapp.
8. Klicka på **Bläddra** på skärmen **Ange mål** för att hitta en nätverksplats för dina filer eller mappar. MABS skapar en mapp där alla återställda objekt kopieras. Mappnamnet har prefixet MABS_day månads år. När du väljer en plats för de återställda filerna eller mappen tillhandahålls informationen för den platsen (målsökväg, målsökväg och tillgängligt utrymme).

    ![Ange plats för att återställa filer](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. På skärmen **Ange återställningsalternativ** väljer du vilken säkerhetsinställning som ska tillämpas. Du kan välja att ändra begränsningen av nätverksbandbreddsanvändningen, men begränsningen är inaktiverad som standard. **San-återställning** och **meddelande** är inte heller aktiverade.
10. Granska inställningarna på skärmen **Sammanfattning** och klicka på **Återställ** för att starta återställningsprocessen. Skärmen **Återställningsstatus** visar återställningsåtgärdens förlopp.

## <a name="next-steps"></a>Nästa steg

Om du vill felsöka problem när du använder Azure Backup Server läser du [felsökningsguiden för Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
