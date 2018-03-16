---
title: "Azure SQL Database säkerhetskopieringar för upp till 10 år | Microsoft Docs"
description: "Lär dig hur Azure SQL Database stöder lagra säkerhetskopior för upp till 10 år."
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 12/22/2016
ms.author: sashan
ms.openlocfilehash: 2f31e89fce2746e57d6a670aef949d0d534af4c1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Azure SQL Database säkerhetskopieringar för upp till 10 år
Många program har regelverk, kompatibilitet eller andra företag syfte som kräver att du behåller databassäkerhetskopieringar utöver de 7-35 dagar som tillhandahålls av Azure SQL Database [automatiska säkerhetskopieringar](sql-database-automated-backups.md). Med hjälp av funktionen för långsiktig lagring av säkerhetskopior kan du lagra säkerhetskopiorna SQL-databasen i ett Azure Recovery Services-valv i upp till 10 år. Du kan lagra upp till 1 000 databaser per valvet. Du kan sedan välja någon säkerhetskopia i valvet för att återställa den som en ny databas.

> [!IMPORTANT]
> Långsiktig lagring av säkerhetskopior är för närvarande under förhandsgranskning och tillgänglig inom följande områden: östra, Australien, sydost, södra, centrala USA, östra Asien, östra USA, östra USA 2, centrala Indien, södra Indien, östra, västra Japan, norra centrala USA, Nordeuropa, södra centrala USA, Sydostasien, västra Europa, och västra USA.
>

> [!NOTE]
> Du kan aktivera upp till 200 databaser per valvet under en 24-timmarsperiod. Vi rekommenderar att du använder ett separat valv för varje server för att minimera effekten av den här gränsen. 
> 

## <a name="how-sql-database-long-term-backup-retention-works"></a>Så här fungerar SQL Database långsiktig lagring av säkerhetskopior.

Du kan associera en SQL-databasserver med långsiktig säkerhetskopiering kvarhållning med Azure Recovery Services-valvet. 

* Du måste skapa valvet i samma Azure-prenumerationen som skapats av SQLServer och i samma geografiska region och resursgruppen. 
* Sedan kan du konfigurera en bevarandeprincip för alla databaser. Principen gör veckovisa fullständiga databassäkerhetskopieringar som ska kopieras till Recovery Services-valvet och bevaras under den angivna bevarandeperioden (upp till 10 år). 
* Du kan sedan återställa databasen från någon av dessa säkerhetskopieringar till en ny databas i någon server i prenumerationen. Azure storage skapar en kopia av befintliga säkerhetskopior och kopian påverkar inte prestanda på den befintliga databasen.

> [!TIP]
> Instruktioner, se [konfigurera och återställning från Azure SQL Database långsiktig lagring av säkerhetskopior](sql-database-long-term-backup-retention-configure.md).

## <a name="enable-long-term-backup-retention"></a>Aktivera långsiktig lagring av säkerhetskopior.

Konfigurera långsiktig lagring av säkerhetskopior för en databas:

1. Skapa ett Azure Recovery Services-valv som din SQL database-server i samma region, prenumeration och resursgrupp. 
2. Registrera servern i valvet.
3. Skapa en princip för Azure Recovery Services-skydd.
4. Gäller protection-principen för de databaser som kräver långsiktig säkerhetskopiering kvarhållning.

Om du vill konfigurera, hantera och återställa en databas från långsiktig säkerhetskopiering kvarhållning av automatiska säkerhetskopieringar i ett Azure Recovery Services-valv, gör du något av följande:

* Med hjälp av Azure portal: Klicka på **långsiktig lagring av säkerhetskopior**, väljer du en databas och klicka sedan på **konfigurera**. 

   ![Välj en databas för långsiktig lagring av säkerhetskopior.](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

* Med hjälp av PowerShell: Gå till [konfigurera och återställning från Azure SQL Database långsiktig lagring av säkerhetskopior](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-a-database-thats-stored-with-the-long-term-backup-retention-feature"></a>Återställa en databas som lagras med funktionen för långsiktig lagring av säkerhetskopior.

För att återställa från en säkerhetskopia för långsiktig lagring av säkerhetskopior.:

1. Lista över valvet där säkerhetskopian lagras.
2. Lista över behållare som är mappad till din logiska server.
3. Lista över datakälla i valvet som är mappad till din databas.
4. Lista över de återställningspunkter som är tillgängliga för återställning.
5. Återställ databasen från återställningspunkten till servern i din prenumeration.

Om du vill konfigurera, hantera och återställa en databas från långsiktig säkerhetskopiering kvarhållning av automatiska säkerhetskopieringar i ett Azure Recovery Services-valv, gör du något av följande:

* Med hjälp av Azure portal: Gå till [hantera långsiktig lagring av säkerhetskopior med Azure-portalen](sql-database-long-term-backup-retention-configure.md). 

* Med hjälp av PowerShell: Gå till [hantera långsiktig lagring av säkerhetskopior med hjälp av PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="get-pricing-for-long-term-backup-retention"></a>Hämta priser för långsiktig lagring av säkerhetskopior.

Långsiktig säkerhetskopiering kvarhållning av en SQL-databas debiteras enligt den [Azure-säkerhetskopiering tjänster priser priser](https://azure.microsoft.com/pricing/details/backup/).

När SQL-databasservern är registrerad för valvet, debiteras du för det totala lagringsutrymmet som används av de veckovisa säkerhetskopiorna som lagras i valvet.

## <a name="view-available-backups-that-are-stored-in-long-term-backup-retention"></a>Visa tillgängliga säkerhetskopior som lagras i långsiktig lagring av säkerhetskopior.

Om du vill konfigurera, hantera och återställa en databas från långsiktig säkerhetskopiering kvarhållning av automatiska säkerhetskopieringar i ett Azure Recovery Services-valv med hjälp av Azure portal, gör du något av följande:

* Med hjälp av Azure portal: Gå till [hantera långsiktig lagring av säkerhetskopior med Azure-portalen](sql-database-long-term-backup-retention-configure.md). 

* Med hjälp av PowerShell: Gå till [hantera långsiktig lagring av säkerhetskopior med hjälp av PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="disable-long-term-retention"></a>Inaktivera långsiktig kvarhållning

Tjänsten recovery hanterar automatiskt rensning av säkerhetskopior baserat på den angivna bevarandeprincipen. 

Ta bort bevarandeprincip för den här databasen om du vill sluta skicka säkerhetskopieringar för en viss databas till valvet.
  
```
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
```

> [!NOTE]
> Säkerhetskopieringar som redan finns i valvet påverkas inte. De tas bort automatiskt av återställningstjänsten när deras kvarhållningsperiod upphör att gälla.

## <a name="long-term-backup-retention-faq"></a>Långsiktig lagring av säkerhetskopior vanliga frågor och svar

**Kan jag manuellt ta bort specifika säkerhetskopior i valvet?**

För närvarande inte. Valvet rensas automatiskt säkerhetskopior när kvarhållningsperioden har gått ut.

**Kan jag registrera Min server för att lagra säkerhetskopior till flera valv?**

Nej, du kan för närvarande säkerhetskopieringar till endast en valvet i taget.

**Kan jag ha ett valv och server för olika prenumerationer?**

Nej, för närvarande på valvet och servern måste vara i samma prenumeration och resursgrupp.

**Kan jag använda ett valv som skapats i en region som skiljer sig från Min server region?**

Nej, valvet och servern måste vara i samma region för att minimera kopiera tid och undvika avgifterna för trafiken.

**Hur många databaser kan lagra i ett valv?**

Vi stöder för närvarande upp till 1 000 databaser per valvet. 

**Hur många valv kan skapa per prenumeration?**

Du kan skapa upp till 25 valv per prenumeration.

**Hur många databaser kan konfigurera per dag per valvet?**

Du kan ställa in 200 databaser per dag per valvet.

**Fungerar långsiktig lagring av säkerhetskopior med elastiska pooler?**

Ja. Alla databaser i poolen kan konfigureras med bevarandeprincipen.

**Kan jag välja den tid då säkerhetskopian skapades?**

Nej, SQL-databas kontrollerar schemat för säkerhetskopiering för att minimera påverkan på prestandan för dina databaser.

**Jag har transparent datakryptering är aktiverat för databasen. Kan jag använda den med valvet?** 

Ja, transparent datakryptering stöds. Du kan återställa databasen från valvet, även om den ursprungliga databasen finns inte längre.

**Vad händer med säkerhetskopieringar i valvet om min prenumeration har inaktiverats?** 

Om din prenumeration har inaktiverats behålla det befintliga databaser och säkerhetskopieringar. Nya säkerhetskopior kopieras inte till valvet. När du återaktivera prenumerationen återupptas tjänsten kopiera säkerhetskopiering till valvet. Ditt valv blir tillgänglig för återställningsåtgärder med hjälp av säkerhetskopior som har kopierats det innan prenumerationen pausades. 

**Kan jag få åtkomst till SQL-databasfilerna säkerhetskopiering så att jag kan hämta eller återställa dem till SQLServer?**

Nej, inte för närvarande.

**Är det möjligt att ha flera scheman (varje dag, vecka, månad, varje år) i en SQL-bevarandeprincip.**

Ingen, flera scheman är endast tillgänglig för säkerhetskopiering för virtuella datorer.

**Vad händer om vi konfigurerar långsiktig lagring av säkerhetskopior för en databas som är en databasdagar för sekundär aktiv geo-replikering?**

Eftersom vi inte göra säkerhetskopior på repliker, finns det inget alternativ för långsiktig lagring av säkerhetskopior på sekundära databaser. Det är dock viktigt för användare att ställa in långsiktig lagring av säkerhetskopior på en databasdagar för sekundär aktiv geo-replikering av följande skäl:
* När en växling sker och databasen blir en primär databas, tar vi en fullständig säkerhetskopiering, som har överförts till valvet.
* Det kostar ingenting extra till kunden för att ställa in långsiktig lagring av säkerhetskopior på en sekundär databas.

## <a name="next-steps"></a>Nästa steg
Eftersom databassäkerhetskopieringar skydda data från oavsiktliga skador eller tas bort, är de en viktig del av alla affärskontinuitet och haveriberedskap. Mer information om de andra SQL-databas kontinuitet för företag-lösningarna, se [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).
