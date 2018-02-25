---
title: "Konfigurera rapporter för Azure-säkerhetskopiering"
description: "Den här artikeln handlar om hur du konfigurerar Power BI-rapporter för Azure Backup med Recovery Services-valvet."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 86e465f1-8996-4a40-b582-ccf75c58ab87
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/10/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c6cc4ba95f440f09f11a93927fd67873f8813e8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter
Den här artikeln handlar om åtgärder för att konfigurera rapporter för Azure Backup med Recovery Services-valvet och att få åtkomst till dessa rapporter med hjälp av Power BI. När du utför dessa steg måste gå du till Power BI att visa alla rapporter, anpassa och skapa rapporter direkt. 

## <a name="supported-scenarios"></a>Scenarier som stöds
1. Azure Backup-rapporter har stöd för säkerhetskopiering av virtuella Azure-datorn och filen/mappen till molnet med Azure Recovery Services-agenten.
2. Azure SQL, DPM och Azure Backup Server stöds inte just nu.
3. Du kan visa rapporter över valv och över prenumerationer, om samma lagringskonto har konfigurerats för var och en av valv. Lagringskonto har valts måste vara i samma region som recovery services-valvet.
4. Frekvensen för schemalagd uppdatering för rapporterna är 24 timmar i Power BI. Du kan också utföra en ad hoc-uppdatering av rapporterna i Power BI, där case senaste data i kundlagringskontot används för att återge rapporter. 
5. Azure Backup-rapporter stöds för närvarande inte i nationella moln.

## <a name="prerequisites"></a>Förutsättningar
1. Skapa en [Azure storage-konto](../storage/common/storage-create-storage-account.md#create-a-storage-account) konfigureras för rapporter. Det här lagringskontot används för att lagra rapporter relaterade data.
2. [Skapa en Power BI-konto](https://powerbi.microsoft.com/landing/signin/) för att visa, anpassa och skapa egna rapporter med Power BI-portalen.
3. Registrera resursprovidern **Microsoft.insights** om inte registrerad redan, med prenumerationen för storage-konto och prenumeration på Recovery Services-valvet för att aktivera rapportering data kan flöda till lagring konto. Om du vill göra det, måste du gå till Azure-portalen > prenumeration > resursproviders och Sök efter den här providern att registrera den. 

## <a name="configure-storage-account-for-reports"></a>Konfigurera lagringskonto för rapporter
Använd följande steg för att konfigurera lagringskonto för recovery services-ventilen med hjälp av Azure portal. Detta är en engångskonfiguration och när lagringskontot har konfigurerats kan du gå till Power BI direkt till Visa Innehållspaketet och utnyttja rapporter.
1. Om du redan har ett Recovery Services-valv öppna vidare till nästa steg. Om du inte behöver öppna en Recovery Services-valvet, men är i Azure-portalen, klickar du på **alla tjänster**.

   * I listan över resurser skriver du **Recovery Services**.
   * När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv** klickar du på det.

      ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Listan över Recovery Services-valv visas. Välj ett valv i listan över Recovery Services-valv.

     Instrumentpanelen för det valda valvet öppnas.
2. Från listan över objekt som visas under valv, klickar du på **säkerhetskopiering rapporter** under avsnittet för övervakning och rapporter att konfigurera lagringskonto för rapporter.

      ![Välj säkerhetskopiering rapporter menyn objektet steg 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Klicka på bladet säkerhetskopiering rapporter **diagnostikinställningar** länk. Diagnostikinställningar användargränssnitt som används för att skicka data till kundlagringskontot öppnas.

      ![Aktivera diagnostik steg 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Klicka på länken **aktivera diagnostiken**. Detta öppnar gränssnitt för att konfigurera storage-konto. 

      ![Aktivera diagnostik steg 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Ange namnet på inställningen i fältet **namn** och välj **arkivet till ett Lagringskonto** kryssrutan så att reporting data kan börjar flöda i till lagringskontot.

      ![Aktivera diagnostik steg 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Klicka på väljaren för Storage-konto och välj relevant prenumeration och storage-konto i listan för att lagra data och klicka på **OK**.

      ![Välj kontot steg 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Välj **AzureBackupReport** kryssrutan under avsnittet loggen och flytta skjutreglaget till väljer kvarhållningsperiod för detta rapportdata. Rapporterar data i lagringskontot sparas under den period som valts med hjälp av skjutreglaget.

      ![Spara storage-konto steg 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Granska alla ändringar och klicka på **spara** knappen överst, som visas i bilden ovan. Den här åtgärden säkerställer att alla dina ändringar sparas och storage-konto har nu konfigurerats för att lagra rapportinformationen.

9. Diagnostikinställningar för tabellen bör nu visas den nya inställningen aktiverad för valvet. Om det inte visas, uppdatera tabellen om du vill visa den uppdaterade inställningen.

      ![Visa diagnostikinställningen steg 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> När du konfigurerar rapporter genom att spara storage-konto, bör du **vänta 24 timmar** för inledande data-push att slutföra. Efter denna tid bör du importera Azure Backup-Innehållspaketet i Power BI. Se [frågor och svar](#frequently-asked-questions) för mer information. 
>
>

## <a name="view-reports-in-power-bi"></a>Visa rapporter i Power BI 
När konfigurera lagringskonto för rapporter med hjälp av recovery services-ventilen, tar cirka 24 timmar för att rapportera data till börjar flöda. Använd följande steg för att visa rapporter i Power BI efter 24 timmar för att skapa lagringskonto:
1. [Logga in](https://powerbi.microsoft.com/landing/signin/) till Powerbi.
2. Klicka på **hämta Data** och på **hämta** under **Services** i innehållsbiblioteket Pack. Använd steg som nämns i [Power BI-dokumentationen till Innehållspaketet](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/).

     ![Importera Innehållspaketet](./media/backup-azure-configure-reports/content-pack-import.png)
3. Typen **Azure Backup** i sökfältet och klicka på **blir det nu**.

      ![Hämta Innehållspaketet](./media/backup-azure-configure-reports/content-pack-get.png)
4. Ange namnet på lagringskontot konfigurerade i steg 5 ovan och klicka på **nästa** knappen.

    ![Ange ett lagringskontonamn](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Ange lagringskontonyckel för det här lagringskontot. Du kan [visa och kopiera åtkomstnycklar för lagring](../storage/common/storage-create-storage-account.md#manage-your-storage-account) genom att navigera till ditt lagringskonto i Azure-portalen. 

     ![Ange storage-konto](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Klicka på **inloggning** knappen. När inloggningen är klar, hämta **dataimport** meddelande.

    ![Importera Innehållspaketet](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    När du har tid, **lyckade** meddelande när importen är klar. Det kan ta lite längre tid att importera Innehållspaketet, om det finns stora mängder data i lagringskontot.
    
    ![Importera lyckade Innehållspaketet](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. När data har importerats **Azure Backup** Innehållspaketet är synliga i **appar** i navigeringsfönstret. I listan visas nu Azure Backup-instrumentpanelen, rapporter och dataset med en gul stjärna som anger nyligen importerade rapporter. 

     ![Azure Backup-Innehållspaketet](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Klicka på **Azure Backup** under instrumentpaneler, vilket visar att en uppsättning fästa viktiga rapporter.

      ![Azure Backup-instrumentpanelen](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Klicka på rapport i instrumentpanelen för att visa en fullständig uppsättning rapporter.

      ![Azure Backup-jobbet hälsa](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Klicka på varje flik i rapporterna för att visa rapporter i detta område.

      ![Azure Backup rapporter flikar](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
1. **Hur jag kontrollera om reporting data har startat flödar i till storage-konto?**
    
    Du kan gå till lagringskonto som har konfigurerats och Välj behållare. Om behållaren har en post för insights-loggar-azurebackupreport, innebär det att rapportera data har startats flödar i.

2. **Vad är frekvensen för data-push till lagringskontot och Azure Backup-Innehållspaketet i Power BI?**

   För användare i dag 0, tar det cirka 24 timmar för pusha data till lagringskontot. När den här första push compelete kan uppdateras data med följande frekvens som visas i bilden nedan. 
      * Data som rör **jobb, aviseringar, säkerhetskopiering objekt, valv, skyddade servrar och principer** skickas till kundlagringskontot som och när du är inloggad.
      * Data som rör **lagring** skickas till kundlagringskontot var 24: e timme.
   
    ![Azure Backup rapporter data push frekvens](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Powerbi har en [en gång om dagen för schemalagd uppdatering](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Du kan utföra en manuell uppdatering av data i Power BI för Innehållspaketet.

3. **Hur länge behålla rapporterna?** 

   Du kan välja period av rapportdata i storage-konto (med steg 6 i Konfigurera storage-konto för rapporter ovan) när du konfigurerar storage-konto. Förutom att du kan [analysera rapporter i excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) och spara dem under en längre period, enligt dina behov. 

4. **Ser jag mina data i rapporter när du har konfigurerat storage-konto?**

   Alla data som genereras när **”konfigurera storage-konto”** ska skickas till lagringskontot och kommer att vara tillgängliga i rapporter. Dock **inte push-pågående jobb** för rapportering. När jobbet har slutförts eller misslyckas, skickas till rapporter.

5. **Om det redan har konfigurerat storage-konto om du vill visa rapporter kan jag ändra konfigurationen för att använda ett annat lagringskonto?** 

   Ja, du kan ändra konfigurationen så att den pekar till ett annat lagringskonto. Du bör använda det nyligen konfigurerade lagringskontot vid anslutning till Azure Backup-Innehållspaketet. Dessutom när ett annat lagringskonto har konfigurerats kan skulle nya dataflöde i det här lagringskontot. Men äldre data (innan du ändrar konfigurationen) skulle kvar i äldre lagringskontot.

6. **Kan jag visa rapporter över valv och över prenumerationer?** 

   Ja, du kan konfigurera samma lagringskonto över olika valv att visa rapporter för cross-valvet. Du kan också konfigurera samma lagringskonto för valv över prenumerationer. Du kan sedan använda det här lagringskontot vid anslutning till Azure Backup-Innehållspaketet i Power BI för att visa rapporterna. Det lagringskonto som valts bör dock i samma region som recovery services-valvet.
   
## <a name="troubleshooting-errors"></a>Felsökning av fel
| Felinformation | Lösning |
| --- | --- |
| När du har skapat lagringskontot för säkerhetskopiering rapporter **Lagringskonto** visas fortfarande **inte konfigurerad**. | Om du har konfigurerat storage-konto, flödar reporting data i trots problemet. Lös problemet genom att gå till Azure-portalen > alla tjänster > diagnostikinställningar > RS valv > Redigera inställning. Ta bort den tidigare konfigurerade inställningen och skapa en ny inställning från samma bladet. Ändra fältet **namn** till **tjänsten**. Detta ska visa konfigurerade storage-konto. |
|När du har importerat Azure Backup innehåll pack i Power BI, felet **404-behållaren finns inte** kommer. | Enligt förslaget i det här dokumentet, måste du vänta 24 timmar efter att konfigurera rapporter i Recovery Services-valvet se dem på rätt sätt i Power BI. Om du försöker komma åt rapporterna innan 24 timmar, får du felet eftersom fullständiga data inte kan finnas för att visa giltig rapporter. |

## <a name="next-steps"></a>Nästa steg
Nu när du har konfigurerat storage-konto och importerade Azure Backup-Innehållspaketet, är nästa steg att anpassa rapporterna och använda reporting datamodell för att skapa rapporter. Mer information finns i följande artiklar.

* [Med Azure Backup reporting datamodellen](backup-azure-reports-data-model.md)
* [Filtrera rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Skapa rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

