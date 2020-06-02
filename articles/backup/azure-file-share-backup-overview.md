---
title: Om säkerhets kopiering av Azure-filresurs
description: Lär dig hur du säkerhetskopierar Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 5d4129b3618810d80df7d5289a7a7417d74fd077
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259037"
---
# <a name="about-azure-file-share-backup"></a>Om säkerhets kopiering av Azure-filresurs

Säkerhets kopiering av Azure-filresurs är en inbyggd, molnbaserad säkerhets kopierings lösning som skyddar dina data i molnet och eliminerar ytterligare underhålls kostnader som ingår i lokala säkerhets kopierings lösningar. Azure Backups tjänsten integreras smidigt med Azure File Sync och gör att du kan centralisera dina fil resurs data och dina säkerhets kopior. Med den här enkla, tillförlitliga och säkra lösningen kan du konfigurera skydd för företags fil resurser i några enkla steg med en garanti att du kan återställa dina data i händelse av ett katastrof scenario.

## <a name="key-benefits-of-azure-file-share-backup"></a>Viktiga fördelar med Azure File Share-säkerhetskopiering

* Ingen infrastruktur: ingen distribution krävs för att konfigurera skydd för dina fil resurser.
* Anpassad kvarhållning: du kan konfigurera säkerhets kopior med dagliga/veckovis/månads Visa/årliga kvarhållning enligt dina krav.
* Inbyggda hanterings funktioner: du kan schemalägga säkerhets kopieringar och ange önskad kvarhållningsperiod utan ytterligare överbelastning av data rensning.
* Omedelbar återställning: Azure-filresurs säkerhets kopiering använder ögonblicks bilder av fil resurser, så du kan bara välja de filer som du vill återställa direkt.
* Avisering och rapportering: du kan konfigurera aviseringar för säkerhets kopierings-och återställnings problem och använda rapporterings lösningen som tillhandahålls av Azure Backup för att få insikter om säkerhets kopieringar över dina fil resurser.
* Skydd mot oavsiktlig borttagning av fil resurser: Azure Backup aktiverar funktionen för [mjuk borttagning](https://docs.microsoft.com/azure/storage/files/storage-files-prevent-file-share-deletion) på en lagrings konto nivå med en kvarhållningsperiod på 14 dagar. Även om en skadlig aktör tar bort fil resursen, bevaras fil resursens innehåll och återställnings punkter (ögonblicks bilder) för en konfigurerbar kvarhållningsperiod, vilket ger lyckad och fullständig återställning av käll innehåll och ögonblicks bilder utan data förlust.

## <a name="architecture"></a>Arkitektur

![Azure-fil resurs säkerhets kopierings arkitektur](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Så här fungerar säkerhets kopieringen

1. Det första steget i att konfigurera säkerhets kopiering för Azure-filresurser är att skapa ett Recovery Services-valv. Valvet ger dig en samlad vy över de säkerhets kopior som kon figurer ATS för olika arbets belastningar.

2. När du har skapat ett valv identifierar Azure Backup tjänsten de lagrings konton som kan registreras med valvet. Du kan välja det lagrings konto som är värd för de fil resurser som du vill skydda.

3. När du har valt lagrings kontot visar Azure Backup tjänsten den uppsättning av fil resurser som finns i lagrings kontot och lagrar deras namn i hanterings lager katalogen.

4. Sedan kan du konfigurera säkerhets kopierings principen (schema och kvarhållning) enligt dina krav och välja de fil resurser som ska säkerhets kopie ras. Azure Backups tjänsten registrerar scheman i kontroll planet för schemalagda säkerhets kopieringar.

5. Baserat på den angivna principen, utlöser Azure Backup Scheduler säkerhets kopieringar vid den schemalagda tiden. Som en del av detta jobb skapas fil resursens ögonblicks bild med hjälp av fil resurs-API: et. Endast ögonblicks bildens URL lagras i metadatalagret.

    >[!NOTE]
    >Fil resurs data överförs inte till säkerhets kopierings tjänsten eftersom säkerhets kopierings tjänsten skapar och hanterar ögonblicks bilder som ingår i ditt lagrings konto, och säkerhets kopieringarna överförs till valvet.

6. Du kan återställa innehållet i Azure-filresursen (enskilda filer eller hela resursen) från ögonblicks bilder som är tillgängliga på käll fil resursen. När åtgärden har Aktiver ATS hämtas ögonblicks bildens URL från metadatalagret och data visas och överförs från käll ögonblicks bilden till önskad mål fil resurs.

7. Övervaknings data för säkerhets kopierings-och återställnings jobbet skickas till Azure Backup övervaknings tjänsten. På så sätt kan du övervaka säkerhets kopiering av molnet för dina fil resurser på en enda instrument panel. Dessutom kan du också konfigurera aviseringar eller e-postaviseringar när säkerhets kopierings hälsan påverkas. E-postmeddelanden skickas via Azures e-posttjänst.

## <a name="backup-costs"></a>Kostnader för säkerhets kopiering

För närvarande debiteras du bara för ögonblicks bilder eftersom Azure File Share-säkerhetskopiering är en ögonblicks bilds lösning. Lagrings avgifter som skapas för ögonblicks bilder faktureras tillsammans med Azure Files användning enligt pris informationen som anges [här](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [säkerhetskopierar Azure-filresurser](backup-afs.md)
* Få svar på [frågor om att säkerhetskopiera Azure Files](backup-azure-files-faq.md)
