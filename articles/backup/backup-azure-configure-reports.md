---
title: Konfigurera rapporter för Azure Backup
description: Konfigurera Power BI-rapporter för Azure Backup med Recovery Services-valvet.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 493a8881975e6b7568a7823bfc86fc97b4389378
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418287"
---
# <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter
Den här artikeln visar stegen för att följa för att konfigurera rapporter för Azure Backup med Recovery Services-valvet. Den visar också hur du kommer åt rapporter med hjälp av Power BI. När du har slutfört de här stegen kan du gå direkt till Power BI för att visa, anpassa och skapa rapporter.

> [!IMPORTANT]
> Från den 1 November 2018, kan vissa kunder problem uppstå vid inläsning av data i Azure Backup-App i Power BI, säger ”extra tecken påträffades i slutet av JSON-indata. Undantaget orsakades av gränssnittet IDataReader ”.
Detta är p.g.a. en ändring i formatet där data läses in i lagringskontot.
Hämta den senaste appen (version 1.8) för att undvika det här problemet.
>
>

## <a name="supported-scenarios"></a>Scenarier som stöds
- Azure Backup-rapporter har stöd för säkerhetskopiering av Azure virtuella datorer eller fil- och säkerhetskopiering till molnet med hjälp av Azure Recovery Services-agenten.
- Rapporter för Azure SQL Database, Azure-filresurser, Data Protection Manager och Azure Backup server stöds inte just nu.
- Du kan visa rapporter över valv och prenumerationer, om samma lagringskonto har konfigurerats för varje valv. Det valda lagringskontot måste vara i samma region som Recovery Services-valvet.
- Frekvensen för schemalagd uppdatering för rapporterna är 24 timmar i Power BI. Du kan också utföra en ad hoc-uppdatering av rapporter i Power BI. I det här fallet används den senaste informationen i kundens lagringskonto för att återge rapporter.

## <a name="prerequisites"></a>Förutsättningar
- Skapa en [Azure storage-konto](../storage/common/storage-quickstart-create-account.md) konfigureras för rapporter. Det här lagringskontot används för att lagra rapporter-relaterade data.
- [Skapa en Power BI-konto](https://powerbi.microsoft.com/landing/signin/) för att visa, anpassa och skapa egna rapporter med hjälp av Power BI-portalen.
- Registrera resursprovidern **Microsoft.insights**, om den inte redan har registrerats. Använd prenumerationerna för lagringskontot och Recovery Services-valv så att rapportering av data kan flöda till lagringskontot. Om du vill göra det här steget, gå till Azure portal, Välj **prenumeration** > **resursprovidrar**, och markera den här leverantören att registrera den.

## <a name="configure-storage-account-for-reports"></a>Konfigurera lagringskonto för rapporter
Följ dessa steg om du vill konfigurera lagringskontot för Recovery Services-valvet med hjälp av Azure portal. Det här är en engångskonfiguration. När lagringskontot har konfigurerats kan gå du direkt till Power BI för att visa Innehållspaketet och använda rapporter.
1. Om du redan har ett Recovery Services-valv som är öppna, gå till nästa steg. Om du inte har ett Recovery Services-valvet som är öppna i Azure-portalen väljer du **alla tjänster**.

   * I listan över resurser, anger du **återställningstjänster**.
   * När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv**, markera den.

      ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

   * Listan över Recovery Services-valv visas. Välj ett valv i listan över Recovery Services-valv.

     Instrumentpanelen för det valda valvet öppnas.
2. I listan med objekt som visas under valvet under den **övervakning och rapporter** väljer **Backup-rapporter** konfigurerar lagringskontot för rapporter.

      ![Välj Backup-rapporter menyn objekt steg 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. På den **Backup-rapporter** bladet och välja den **diagnostikinställningar** länk. Den här länken öppnas den **diagnostikinställningar** användargränssnitt, vilket används för att skicka data till ett lagringskonto för kunden.

      ![Aktivera diagnostik steg 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Välj **slå på diagnostik** att öppna ett gränssnitt som du använder för att konfigurera ett lagringskonto. 

      ![Aktivera diagnostik steg 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. I den **namn** anger du ett namn på inställningen. Välj den **arkivet till ett lagringskonto** kryssrutan så att reporting data kan börjar flöda till lagringskontot.

      ![Aktivera diagnostik steg 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Välj **lagringskonto**, väljer du relevant prenumerationen och storage-konto i listan för att lagra rapporteringsdata och välj **OK**.

      ![Välj storage-konto steg 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Under den **Log** väljer den **AzureBackupReport** markerar du kryssrutan. Flytta skjutreglaget för att välja en kvarhållningsperiod för den här rapporteringsdata. Rapportdata i storage-kontot sparas för perioden som valts med hjälp av det här skjutreglaget.

      ![Spara storage-konto steg 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Granska alla ändringar och välj **spara**. Den här åtgärden säkerställer att alla ändringar har sparats och storage-konto har nu konfigurerats för att lagra rapporteringsdata.

9. Den **diagnostikinställningar** tabell nu visas den nya inställningen aktiverad för valvet. Om den inte visas kan du uppdatera tabellen om du vill se den uppdaterade inställningen.

      ![Visa diagnostikinställning steg 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> När du har konfigurerat rapporter genom att spara storage-konto, *vänta i 24 timmar* för ursprungliga data-push-installation att slutföra. Importera Azure Backup-App i Power BI endast efter den tidpunkten. Mer information finns i den [vanliga frågor och svar](#frequently-asked-questions). 
>
>

## <a name="view-reports-in-power-bi"></a>Visa rapporter i Power BI 
När du konfigurerar ett lagringskonto för rapporter med hjälp av en Recovery Services-valv, tar det cirka 24 timmar för rapporteringsdata att börjar flöda i. Följ dessa steg om du vill visa rapporter i Power BI efter 24 timmar för att skapa ett lagringskonto.
Om du vill anpassa och dela rapporten, skapa en arbetsyta och utför följande steg

1. [Logga in](https://powerbi.microsoft.com/landing/signin/) till Powerbi.
2. Välj **Hämta data**. I den **fler sätt att skapa ditt eget innehåll**väljer **tjänsten innehållspaket**. Följ stegen i den [Power BI-dokumentationen för att ansluta till en tjänst](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. I den **Search** stapeln, ange **Azure Backup** och välj **Hämta nu**.

      ![Hämta Innehållspaketet](./media/backup-azure-configure-reports/content-pack-get.png)
4. Ange namnet på det lagringskonto som konfigurerades i föregående steg 5 och välj **nästa**.

    ![Ange ett lagringskontonamn](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Med hjälp av autentiseringsmetod ”nyckel”, ange lagringskontots åtkomstnyckel för lagringskontot. Att [visa och kopiera åtkomstnycklar för lagring](../storage/common/storage-account-manage.md#access-keys)går du till ditt lagringskonto i Azure-portalen. 

     ![Ange storage-konto](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Välj **logga in**. När inloggningen är klar, visas ett meddelande om importera data.

    ![Importerar Innehållspaketet](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    När importen är klar visas en **lyckades** meddelande. Om mängden data i lagringskontot är stor kan ta det lite längre tid att importera Innehållspaketet.
    
    ![Importen lyckades Innehållspaketet](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. När data importeras ordentligt, den **Azure Backup** Innehållspaketet är synliga i **appar** i navigeringsfönstret. Under **instrumentpaneler**, **rapporter**, och **datauppsättningar**, i listan visas nu Azure Backup.
     
8. Under **instrumentpaneler**väljer **Azure Backup**, som visar en uppsättning Fäst viktiga rapporter.

      ![Azure Backup-instrumentpanel](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Om du vill visa en fullständig uppsättning med rapporter väljer du en rapport i instrumentpanelen.

      ![Azure Backup-jobbet hälsotillstånd](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Välj varje flik i rapporterna för att visa rapporter i det området.

      ![Azure Backup-rapporter flikar](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Hur kontrollerar jag om rapportering av data har börjat flödar till ett lagringskonto?**
    
   Gå till det lagringskonto som du har konfigurerat och Välj behållare. Om behållaren har en post för insights-logs-azurebackupreport, indikerar det att rapportdata har startat flödar i.

**Vad är frekvensen för data-push till ett lagringskonto och Azure Backup-Innehållspaketet i Power BI?**

   Det tar cirka 24 timmar för att skicka data till ett lagringskonto för dag 0 användare. När den här inledande push är klar, uppdateras data med vilken frekvens som visas i följande bild. 
      
* Data som är relaterade till **jobb**, **aviseringar**, **Säkerhetskopieringsobjekt**, **valv**, **skyddade servrar**, och  **Principer** skickas till en kundens lagringskonto och när den är inloggad.
      
* Data som är relaterade till **Storage** skickas till ett lagringskonto för kunden var 24: e timme.
   
   ![Azure Backup-rapporter push datafrekvensen](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* Powerbi har en [schemalagd uppdatering en gång om dagen](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Du kan utföra en manuell uppdatering av data i Power BI för Innehållspaketet.

**Hur länge kan jag behålla rapporter?**

   När du konfigurerar ett lagringskonto kan välja du en kvarhållningsperiod för rapportdata i lagringskontot. Följ steg 6 i avsnittet ”Konfigurera storage-konto för rapporter” ovan. Du kan också [analysera rapporter i Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) och spara dem under en längre period, utifrån dina behov.

**Ser jag mina data i rapporter när du har konfigurerat storage-konto?**

   Alla data som genereras när du har konfigurerat en storage-konto skickas till lagringskontot och är tillgänglig i rapporter. Pågående jobb skickas inte för rapportering. När jobbet har slutförts eller misslyckas, skickas den till rapporter.

**Om jag redan har konfigurerat storage-konto om du vill visa rapporter kan jag ändra konfigurationen för att använda ett annat lagringskonto?**

   Ja, du kan ändra konfiguration och referera till ett annat lagringskonto. Använd det nyligen konfigurerade lagringskontot när du ansluter till Azure Backup-Innehållspaketet. När ett annat lagringskonto har konfigurerats flödar också nya data i det här lagringskontot. Äldre data (innan du ändrar konfigurationen) finns fortfarande kvar i äldre storage-konto.

**Kan jag visa rapporter över valv och prenumerationer?**

   Ja, du kan konfigurera samma lagringskonto över olika valv cross-valv visas. Du kan också konfigurera samma lagringskonto för valv mellan prenumerationer. Du kan sedan använda det här lagringskontot när du ansluter till Azure Backup-Innehållspaketet i Power BI för att visa rapporterna. Det valda lagringskontot måste vara i samma region som Recovery Services-valvet.
   
## <a name="troubleshooting-errors"></a>Felsöka fel
| Felinformation | Lösning |
| --- | --- |
| När du har skapat lagringskontot för Backup-rapporter, **lagringskonto** visas fortfarande **inte konfigurerad**. | Om du har konfigurerat ett lagringskonto, flödar din reporting data i trots problemet. Lös problemet genom att gå till Azure-portalen och väljer **alla tjänster** > **diagnostikinställningar** > **Recovery Services-valv**  >  **Redigera inställning**. Ta bort den tidigare konfigurerade inställningen och skapa en ny inställning på samma blad. Den här gången i den **namn** väljer **service**. Nu visas det konfigurerade lagringskontot. |
|När du har importerat i Azure Backup-Innehållspaketet i Power BI, ”404-container är inte hitta” meddelandet visas. | Som tidigare nämnts, måste du vänta 24 timmar när du har konfigurerat rapporter i Recovery Services-valvet för att se dem korrekt i Power BI. Om du försöker komma åt rapporten innan 24 timmar, visas detta felmeddelande eftersom kompletta data är ännu inte finnas för att visa giltig rapporter. |

## <a name="next-steps"></a>Nästa steg
När du konfigurerar storage-konto och importera Azure Backup-innehållspaket, är nästa steg att anpassa rapporter och använda en reporting datamodell för att skapa rapporter. Mer information finns i följande artiklar.

* [Använda en Azure-Backup reporting datamodell](backup-azure-reports-data-model.md)
* [Filtrera rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Skapa rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

