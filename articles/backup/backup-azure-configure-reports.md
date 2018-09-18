---
title: Konfigurera rapporter för Azure Backup
description: Konfigurera Power BI-rapporter för Azure Backup med Recovery Services-valvet.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c59653bf3709f7798fd92a44fa420b99f2cbc6b6
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733596"
---
# <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter
Den här artikeln handlar om åtgärder för att konfigurera rapporter för Azure Backup med Recovery Services-valvet och till dessa rapporter med Power BI. När du har utfört dessa steg kan du direkt Gå till Power BI för att visa alla rapporter, anpassa och skapa rapporter. 

## <a name="supported-scenarios"></a>Scenarier som stöds
1. Azure Backup-rapporter har stöd för säkerhetskopiering av Azure virtuella datorer och säkerhetskopiering av filer/mappar till molnet med Azure Recovery Services Agent.
2. Rapporter för Azure SQL, DPM och Azure Backup Server stöds inte just nu.
3. Du kan visa rapporter över valv och mellan prenumerationer, om samma lagringskonto har konfigurerats för varje valv. Valda lagringskontot ska vara i samma region som recovery services-valv.
4. Frekvensen för schemalagd uppdatering för rapporterna är 24 timmar i Power BI. Du kan också utföra en ad hoc-uppdatering av rapporter i Power BI, i vilket fall senaste data i kundens lagringskonto används för återgivning av rapporter. 

## <a name="prerequisites"></a>Förutsättningar
1. Skapa en [Azure storage-konto](../storage/common/storage-quickstart-create-account.md) konfigureras för rapporter. Det här lagringskontot används för att lagra relaterade data i rapporter.
2. [Skapa en Power BI-konto](https://powerbi.microsoft.com/landing/signin/) för att visa, anpassa och skapa egna rapporter med Power BI-portalen.
3. Registrera resursprovidern **Microsoft.insights** om inte är registrerad redan med prenumerationen för storage-konto och prenumerationen för Recovery Services-valvet för att aktivera rapportering data kan flöda till lagringen konto. Om du vill göra samma sak, måste du gå till Azure portal > prenumeration > resursprovidrar och kontrollera den här leverantören att registrera den. 

## <a name="configure-storage-account-for-reports"></a>Konfigurera lagringskonto för rapporter
Använd följande steg för att konfigurera storage-konto för recovery services-valv med hjälp av Azure portal. Detta är en engångskonfiguration och när lagringskontot har konfigurerats kan du gå till Power BI direkt till Visa innehållspaket och dra nytta av rapporter.
1. Om du redan har ett Recovery Services-valv öppnar du gå vidare till nästa steg. Om du inte har ett Recovery Services-valv är öppet, men är i Azure portal, klickar du på **alla tjänster**.

   * I listan över resurser skriver du **Recovery Services**.
   * När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv** klickar du på det.

      ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Listan över Recovery Services-valv visas. Välj ett valv i listan över Recovery Services-valv.

     Instrumentpanelen för det valda valvet öppnas.
2. I listan över objekt som visas under valv, klickar du på **Backup-rapporter** under övervakning och rapporter avsnitt för att konfigurera lagringskontot för rapporter.

      ![Välj Backup-rapporter menyn objekt steg 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Backup-rapporter-bladet och klicka på **diagnostikinställningar** länk. Då öppnas diagnostikinställningar Användargränssnittet som används för att skicka data till kundens lagringskonto.

      ![Aktivera diagnostik steg 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Klicka på länken **slå på diagnostik**. Detta öppnar Användargränssnittet för att konfigurera storage-konto. 

      ![Aktivera diagnostik steg 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Ange namn i fältet **namn** och välj **arkivet till ett Lagringskonto** kryssrutan så att reporting data kan börjar flöda i till lagringskontot.

      ![Aktivera diagnostik steg 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Klicka på Storage-kontot datumväljare och välj relevant prenumeration och storage-konto i listan för att lagra reporting data och klicka på **OK**.

      ![Välj storage-konto steg 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Välj **AzureBackupReport** kryssrutan under avsnittet Log och flytta skjutreglaget till väljer kvarhållningsperioden för den här rapportdata. Rapportdata i storage-kontot sparas för den perioden som valts med hjälp av det här skjutreglaget.

      ![Spara storage-konto steg 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Granska ändringarna och klicka på **spara** knappen överst, som visas i bilden ovan. Den här åtgärden säkerställer att alla ändringar har sparats och storage-konto har nu konfigurerats för att lagra rapporteringsdata.

9. Tabellen diagnostikinställningar bör nu visa den nya inställningen aktiverad för valvet. Om det inte visas, uppdatera tabellen om du vill se den uppdaterade inställningen.

      ![Visa diagnostikinställning steg 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> När du har konfigurerat rapporter genom att spara storage-konto bör du **vänta i 24 timmar** för ursprungliga data-push-installation att slutföra. Azure Backup-Innehållspaketet i Power BI bör du importera endast efter den tidpunkten. Se [vanliga frågor och svar](#frequently-asked-questions) för mer information. 
>
>

## <a name="view-reports-in-power-bi"></a>Visa rapporter i Power BI 
När du konfigurerar lagringskontot för rapporter med hjälp av recovery services-valv, tar det cirka 24 timmar för rapporteringsdata att börjar flöda. Använd följande steg för att visa rapporter i Power BI efter 24 timmar för att ställa in storage-konto:
1. [Logga in](https://powerbi.microsoft.com/landing/signin/) till Powerbi.
2. Klicka på **hämta Data** och klicka på **hämta** under **Services** i innehållsbiblioteket Pack. Använd åtgärderna som nämns i [Power BI-dokumentationen för att komma åt Innehållspaketet](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

     ![Importera Innehållspaketet](./media/backup-azure-configure-reports/content-pack-import.png)
3. Typ **Azure Backup** i sökfältet och klicka på **Hämta nu**.

      ![Hämta Innehållspaketet](./media/backup-azure-configure-reports/content-pack-get.png)
4. Ange namnet på lagringskontot konfigurerade i steg 5 ovan och klicka på **nästa** knappen.

    ![Ange ett lagringskontonamn](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Ange lagringskontonyckeln för det här lagringskontot. Du kan [visa och kopiera åtkomstnycklar för lagring](../storage/common/storage-account-manage.md#access-keys) genom att gå till ditt lagringskonto i Azure-portalen. 

     ![Ange storage-konto](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Klicka på **logga in** knappen. När inloggningen har slutförts, får du **dataimport** meddelande.

    ![Importerar Innehållspaketet](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    När du har tid **lyckades** meddelande när importen är klar. Det kan ta längre tid att importera Innehållspaketet, om det finns stora mängder data i lagringskontot.
    
    ![Importen lyckades Innehållspaketet](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. När data importeras, **Azure Backup** Innehållspaketet är synliga i **appar** i navigeringsfönstret. I listan visas nu Azure Backup-instrumentpaneler, rapporter och datauppsättning med en gul stjärna som anger nyligen importerade rapporter. 

     ![Azure Backup-innehållspaket](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Klicka på **Azure Backup** under instrumentpaneler, som visar en uppsättning fästa viktiga rapporter.

      ![Azure Backup-instrumentpanel](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Om du vill visa en fullständig uppsättning med rapporter, klickar du på valfri rapport på instrumentpanelen.

      ![Azure Backup-jobbet hälsotillstånd](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Klicka på varje flik i rapporterna för att visa rapporter i det området.

      ![Azure Backup-rapporter flikar](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
1. **Hur kontrollerar jag om rapportering av data har startat flöda i till storage-konto?**
    
    Du kan gå till det lagringskonto som har konfigurerats och Välj behållare. Om behållaren har en post för insights-logs-azurebackupreport, indikerar det att rapportdata har startat flödar i.

2. **Vad är frekvensen för data-push till storage-konto och Azure Backup-Innehållspaketet i Power BI?**

   För dag 0 användare tar det cirka 24 timmar att skicka data till lagringskontot. När den här inledande push är klar, uppdateras data med följande frekvens som på bilden nedan. 
      * Data som är relaterade till **jobb, aviseringar, Säkerhetskopieringsobjekt, valv, skyddade servrar och principer** skickas till kundens lagringskonto som och när den är inloggad.
      * Data som är relaterade till **Storage** skickas till kundens lagringskonto var 24: e timme.
   
    ![Azure Backup-rapporter push datafrekvensen](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Powerbi har en [schemalagd uppdatering en gång om dagen](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Du kan utföra en manuell uppdatering av data i Power BI för Innehållspaketet.

3. **Hur länge kan jag behålla rapporter?** 

   Du kan välja kvarhållningsperiod för rapportering av data i storage-konto (med steg 6 i Konfigurera storage-konto för rapporter ovan) när du konfigurerar storage-konto. Förutom att det kan du [analysera rapporter i excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) och spara dem under en längre period, enligt dina behov. 

4. **Kommer jag att få Mina data i rapporter när du har konfigurerat storage-konto?**

   Alla data som genereras när **”konfigurera storage-konto”** ska skickas till storage-kontot och blir tillgängliga i rapporter. Dock **i pågående jobb skickas inte** för rapportering. När jobbet har slutförts eller misslyckas, skickas den till rapporter.

5. **Om jag redan har konfigurerat storage-konto om du vill visa rapporter kan jag ändra konfigurationen för att använda ett annat lagringskonto?** 

   Ja, du kan ändra konfiguration och referera till ett annat lagringskonto. Du bör använda det nyligen konfigurerade lagringskontot vid anslutning till Azure Backup-Innehållspaketet. Dessutom när ett annat lagringskonto har konfigurerats, skulle nya data flödar i det här lagringskontot. Men äldre data (innan du ändrar konfigurationen) skulle fortfarande är kvar i äldre storage-kontot.

6. **Kan jag visa rapporter över valv och mellan prenumerationer?** 

   Ja, du kan konfigurera samma lagringskonto över olika valv cross-valv visas. Du kan också konfigurera samma lagringskonto för valv mellan prenumerationer. Du kan sedan använda det här lagringskontot vid anslutning till Azure Backup-Innehållspaketet i Power BI för att visa rapporterna. Det valda lagringskontot bör dock vara i samma region som recovery services-valv.
   
## <a name="troubleshooting-errors"></a>Felsöka fel
| Felinformation | Lösning |
| --- | --- |
| När du har installerat storage-konto för Backup-rapporter, **Lagringskonto** visas fortfarande **inte konfigurerad**. | Om du har konfigurerat storage-konto har flödar din reporting data i trots problemet. Lös problemet genom att gå till Azure portal > alla tjänster > diagnostikinställningar > RS valvet > Redigera inställning. Ta bort den tidigare konfigurerade inställningen och skapa en ny inställning från samma blad. Ändra fältet **namn** till **service**. Detta ska visa de konfigurerade lagringskontot. |
|När du har importerat Azure Backup-innehållspaket i Power BI, felet **det gick inte att hitta 404-container** visas. | Enligt förslaget i det här dokumentet, måste du vänta i 24 timmar när du har konfigurerat rapporter i Recovery Services-valvet ska kunna se dem korrekt i Power BI. Om du försöker komma åt rapporten innan 24 timmar, får du det här felet eftersom kompletta data inte finns ännu att visa giltig rapporter. |

## <a name="next-steps"></a>Nästa steg
Nu när du har konfigurerat storage-konto och importerade Azure Backup-Innehållspaketet, är nästa steg att anpassa rapporterna och använda reporting datamodell för att skapa rapporter. Mer information finns i följande artiklar.

* [Med Azure Backup reporting datamodell](backup-azure-reports-data-model.md)
* [Filtrera rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Skapa rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

