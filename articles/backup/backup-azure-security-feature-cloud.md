---
title: Säkerhetsfunktioner som hjälper dig att skydda moln arbets belastningar
description: Lär dig hur du använder säkerhetsfunktioner i Azure Backup för att göra säkerhets kopieringar säkrare.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: b6ce2f9400ad46150fbd4ee86f126b137b5f7800
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278216"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Säkerhetsfunktioner som hjälper dig att skydda moln arbets belastningar som använder Azure Backup

Oron över säkerhetsproblem, som skadlig kod, utpressningstrojaner och intrång, ökar. Dessa säkerhetsproblem kan var kostsamma, både vad gäller pengar och data. För att skydda mot sådana angrepp tillhandahåller Azure Backup nu säkerhetsfunktioner som hjälper dig att skydda säkerhets kopierings data även efter borttagning. En sådan funktion är mjuk borttagning. Med mjuk borttagning, även om en skadlig aktör tar bort säkerhets kopian av en virtuell dator (eller säkerhets kopierings data tas bort av misstag), bevaras säkerhets kopierings data i ytterligare 14 dagar, vilket innebär att det inte går att återställa säkerhets kopierings objekt utan data förlust. Dessa ytterligare 14 dagars kvarhållning av säkerhets kopierings data i läget "mjuk borttagning" kostar ingen kostnad för kunden.

> [!NOTE]
> Mjuk borttagning skyddar bara borttagna säkerhets kopierings data. Om en virtuell dator tas bort utan säkerhets kopiering bevaras inte data av funktionen för mjuk borttagning. Alla resurser bör skyddas med Azure Backup för att garantera fullständig återhämtning.
>

## <a name="soft-delete"></a>Mjuk borttagning

### <a name="supported-regions"></a>Regioner som stöds

Mjuk borttagning stöds för närvarande i USA, västra centrala, Asien, östra, centrala Kanada, östra Frankrike, centrala Frankrike, centrala Korea, södra, Storbritannien, södra, Storbritannien, västra, östra Australien, södra Australien, norra Europa, västra USA, västra 2; USA, centrala USA, södra Asien, östra, norra centrala USA, södra centrala USA, Östra Japan, västra Japan, södra Indien, centrala Indien, västra Indien, östra USA 2, Schweiz, norra, Schweiz, västra och alla nationella regioner.

### <a name="soft-delete-for-vms"></a>Mjuk borttagning för virtuella datorer

1. För att kunna ta bort säkerhetskopierade data för en virtuell dator måste säkerhets kopian stoppas. I Azure Portal går du till Recovery Services-valvet, högerklickar på säkerhets kopierings objekt och väljer **stoppa säkerhets kopiering**.

   ![Skärm bild av Azure Portal säkerhets kopierings objekt](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. I följande fönster får du ett alternativ för att ta bort eller behålla säkerhetskopierade data. Om du väljer **ta bort säkerhetskopierade data** och sedan **stoppar säkerhets kopieringen**tas inte den virtuella datorns säkerhets kopia bort permanent. I stället behålls säkerhets kopierings data i 14 dagar i läget Soft Deleted. Om du väljer **ta bort säkerhets kopierings data** skickas en e-postavisering till det konfigurerade e-postmeddelandet som informerar användaren om att 14 dagar fortfarande är av utökad kvarhållning för säkerhetskopierade data. Dessutom skickas en e-postavisering den 12: e dagen på att det finns två dagar kvar för att Resurrect borttagna data. Borttagningen skjuts upp fram till den femtonde dagen, när permanent borttagning kommer att ske och en slutgiltig e-postavisering skickas för att informera om permanent borttagning av data.

   ![Skärm bild av Azure Portal, stoppa säkerhets kopierings skärmen](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Under dessa 14 dagar visas den mjuka borttagna virtuella datorn i Recovery Services-valvet med en röd "mjuk borttagning"-ikon bredvid.

   ![Skärm bild av Azure Portal, VM i läget för mjuk borttagning](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Om det finns mjuka, borttagna säkerhets kopierings objekt i valvet kan valvet inte tas bort vid denna tidpunkt. Försök att ta bort valvet när säkerhets kopierings objekten har tagits bort permanent och att det inte finns något objekt i läget Soft Deleted kvar i valvet.

4. För att återställa den mjuk-borttagna virtuella datorn måste den först tas bort. Om du vill ångra borttagningen väljer du den mjuk-borttagna virtuella datorn och väljer sedan alternativet **ta bort**.

   ![Skärm bild av Azure Portal, ta bort virtuell dator](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Ett fönster visas där du får ett varnings meddelande om att ångra borttagning har valts och att alla återställnings punkter för den virtuella datorn tas bort och är tillgängliga för att utföra en återställnings åtgärd. Den virtuella datorn kommer att behållas i status "stoppa skydd med kvarhållning" med säkerhets kopior som pausats och säkerhets kopierings data kvarhålls för alltid med ingen säkerhets kopierings princip gällande.

   ![Skärm bild av Azure Portal, bekräfta borttagning av virtuell dator](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   I det här läget kan du också återställa den virtuella datorn genom att välja **Återställ virtuell dator** från den valda återställnings punkten.  

   ![Skärm bild av Azure Portal, Återställ VM-alternativ](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Skräp insamlaren kommer att köra och rensa återställnings punkter som har upphört att gälla först när användaren utför **säkerhets kopierings åtgärden återuppta** .

5. När du har slutfört borttagnings processen återgår statusen till "stoppa säkerhets kopiering med kvar data" och sedan kan du välja **återuppta säkerhets kopiering**. Åtgärden **återuppta säkerhets kopiering** kommer tillbaka till säkerhets kopierings objektet i det aktiva läget, associerat med en säkerhets kopierings princip som valts av användaren som definierar säkerhets kopierings-och bevarande scheman.

   ![Skärm bild av Azure Portal, återuppta säkerhets kopierings alternativ](./media/backup-azure-security-feature-cloud/resume-backup.png)

Det här Flow-diagrammet visar de olika stegen och tillstånden för ett säkerhets kopierings objekt när mjuk borttagning är aktiverat:

![Livs cykel för mjuk borttagning av säkerhets kopierings objekt](./media/backup-azure-security-feature-cloud/lifecycle.png)

Mer information finns i avsnittet [vanliga frågor och svar](backup-azure-security-feature-cloud.md#frequently-asked-questions) nedan.

## <a name="disabling-soft-delete"></a>Inaktiverar mjuk borttagning

Mjuk borttagning är aktiverat som standard på nyligen skapade valv för att skydda säkerhets kopierings data från oavsiktliga eller skadliga borttagningar.  Du bör inte inaktivera den här funktionen. Den enda omständigheterna där du bör inaktivera mjuk borttagning är om du planerar att flytta dina skyddade objekt till ett nytt valv. det går inte att vänta 14 dagar innan du tar bort och återskyddar (till exempel i en test miljö). Endast en säkerhets kopierings administratör kan inaktivera den här funktionen. Om du inaktiverar den här funktionen kommer all borttagning av skyddade objekt att leda till omedelbar borttagning, utan möjlighet att återställa. Säkerhets kopierings data i läget Soft Deleted tidigare inaktive ring av den här funktionen kommer att vara i läget Soft Deleted Om du vill ta bort dessa omedelbart permanent måste du ångra borttagningen och ta bort dem igen för att ta bort dem permanent.

Följ dessa steg om du vill inaktivera mjuk borttagning:

1. Gå till ditt valv i Azure Portal och gå sedan till **inställningar** -> **Egenskaper**.
2. I fönstret Egenskaper väljer du **säkerhets inställningar** -> **uppdatering**.  
3. I rutan säkerhets inställningar under **mjuk borttagning**väljer du **inaktivera**.


![Inaktivera mjuk borttagning](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Ta bort mjuka borttagna säkerhets kopierings objekt permanent

Säkerhets kopierings data i läget Soft Deleted tidigare inaktive ring av den här funktionen kommer att vara i läget Soft Deleted Om du vill ta bort dessa omedelbart tar du bort dem igen och tar bort dem permanent. 

Följ de här stegen:

1. Följ stegen för att [inaktivera mjuk borttagning](#disabling-soft-delete). 
2. Gå till ditt valv i Azure Portal, gå till **säkerhets kopierings objekt** och välj den mjuka BORTTAGNA virtuella datorn 

![Välj Mjuk borttagning av virtuell dator](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Välj alternativet **ångra borttagning**.

![Välj ångra borttagning](./media/backup-azure-security-feature-cloud/choose-undelete.png)


4. Ett fönster visas. Välj **ångra borttagning**.

![Välj ångra borttagning](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Välj **ta bort säkerhetskopierade data** om du vill ta bort säkerhetskopierade data permanent.

![Välj Ta bort säkerhets kopierings data](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Ange namnet på det säkerhetskopierade objektet för att bekräfta att du vill ta bort återställnings punkterna.

![Ange namnet på det säkerhetskopierade objektet](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Om du vill ta bort säkerhetskopierade data för objektet väljer du **ta bort**. Ett meddelande visas där du vet att säkerhets kopierings data har tagits bort.


## <a name="other-security-features"></a>Andra säkerhetsfunktioner

### <a name="storage-side-encryption"></a>Kryptering för lagring på serversidan

Azure Storage krypterar dina data automatiskt när de behålls i molnet. Kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES-kryptering, en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering liknar BitLocker-kryptering på Windows. Azure Backup krypterar automatiskt data innan de lagras. Azure Storage dekrypterar data innan de hämtas.  

I Azure skyddas data i överföring mellan Azure Storage och valvet av HTTPS. Dessa data finns kvar i Azure stamnät nätverket.

Mer information finns i [Azure Storage kryptering för data i vila](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

### <a name="vm-encryption"></a>VM-kryptering

Du kan säkerhetskopiera och återställa virtuella Windows-eller Linux Azure-datorer (VM: ar) med krypterade diskar med hjälp av tjänsten Azure Backup. Anvisningar finns i [säkerhetskopiera och återställa krypterade virtuella datorer med Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

### <a name="protection-of-azure-backup-recovery-points"></a>Skydd av Azure Backup återställnings punkter

Lagrings konton som används av Recovery Services-valven är isolerade och kan inte användas av användare i något skadligt syfte. Åtkomst tillåts endast via Azure Backup hanterings åtgärder, till exempel Restore. Dessa hanterings åtgärder styrs via rollbaserad Access Control (RBAC).

Mer information finns i [använda rollbaserad Access Control för att hantera Azure Backup återställnings punkter](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="soft-delete"></a>Mjuk borttagning

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Måste jag aktivera funktionen för mjuk borttagning i varje valv?

Nej, den skapas och aktive ras som standard för alla Recovery Services-valv.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Kan jag konfigurera det antal dagar för vilka mina data ska bevaras i läget Soft-Deleted när borttagningen har slutförts?

Nej, det har åtgärd ATS till 14 dagar efter ytterligare kvarhållning efter borttagnings åtgärden.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Behöver jag betala kostnaden för ytterligare 14-dagars kvarhållning?

Nej, den här 14-dagars ytterligare kvarhållning kommer kostnads fritt som en del av funktionen för mjuk borttagning.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Kan jag utföra en återställnings åtgärd när mina data är i läget för tyst Borttagning?

Nej, du måste ta bort den mjuka borttagna resursen för att kunna återställa. Åtgärden ta bort tar tillbaka resursen i **stopp skyddet med Behåll data tillstånd** där du kan återställa till en viss tidpunkt. Skräp insamlaren förblir pausad i det här läget.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Kommer mina ögonblicks bilder att följa samma livs cykel som mina återställnings punkter i valvet?

Ja.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hur kan jag aktivera schemalagda säkerhets kopieringar igen för en mjuk borttagen resurs?

Undelete följt av Resume-åtgärden skyddar resursen igen. Återuppta åtgärd associerar en säkerhets kopierings princip för att utlösa de schemalagda säkerhets kopieringarna med den valda kvarhållningsperioden. Dessutom körs skräp insamlaren så fort återställnings åtgärden har slutförts. Om du vill utföra en återställning från en återställnings punkt som har passerat förfallo datumet, rekommenderar vi att du gör det innan du utlöser återställnings åtgärden.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Kan jag ta bort mitt valv om det finns mjuka borttagna objekt i valvet?

Det går inte att ta bort Recovery Services valvet om det finns säkerhets kopierings objekt i läget Soft-Deleted i valvet. De mjuka borttagna objekten tas bort permanent 14 dagar efter borttagnings åtgärden. Om du inte kan vänta i 14 dagar [inaktiverar du mjuk borttagning](#disabling-soft-delete), tar bort de mjuka borttagna objekten och tar bort dem igen för att ta bort dem permanent. När du har kontrollerat att det inte finns några skyddade objekt och inga mjuka borttagna objekt kan valvet tas bort.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Kan jag ta bort data som är tidigare än de 14 dagarna mjuk borttagnings perioden efter borttagningen?

Nej. Du kan inte framtvinga borttagning av borttagna objekt, de tas bort automatiskt efter 14 dagar. Den här säkerhetsfunktionen är aktive rad för att skydda säkerhetskopierade data från oavsiktliga eller skadliga borttagningar.  Du bör vänta i 14 dagar innan du utför någon annan åtgärd på den virtuella datorn.  Objekt som inte kan tas bort debiteras inte.  Kontakta Microsoft-supporten om du behöver skydda de virtuella datorerna som är markerade för mjuk borttagning inom 14 dagar till ett nytt valv.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Kan mjuka borttagnings åtgärder utföras i PowerShell eller CLI?

Nej, stöd för PowerShell eller CLI är inte tillgängligt för tillfället.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Stöds mjuk borttagning för andra moln arbets belastningar, t. ex. SQL Server i virtuella Azure-datorer och SAP HANA i virtuella Azure-datorer?

Nej. För tillfället mjuk borttagning stöds bara för virtuella Azure-datorer.

## <a name="next-steps"></a>Nästa steg

- Läs om [säkerhets kontroller för Azure Backup](backup-security-controls.md).
