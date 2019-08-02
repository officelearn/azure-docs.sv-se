---
title: Konfigurera rapporter för Azure Backup
description: Konfigurera Power BI rapporter för Azure Backup med hjälp av ett Recovery Services-valv.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: dacurwin
ms.openlocfilehash: 169ce73ead52d6a275f13f084c681e14c89ab606
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689361"
---
# <a name="configure-azure-backup-reports"></a>Konfigurera Azure Backup-rapporter
Den här artikeln visar hur du konfigurerar rapporter för Azure Backup med hjälp av ett Recovery Services-valv. Den visar också hur du får åtkomst till rapporter med hjälp av Power BI. När du har slutfört de här stegen kan du gå direkt till Power BI för att visa, anpassa och skapa rapporter.

> [!IMPORTANT]
> Från den 1 november 2018 kan det uppstå problem för vissa kunder vid inläsning av data i Azure Backup-appen i Power BI, med meddelandet ”Extra tecken påträffades i slutet av JSON-indata. Undantaget returnerades av gränssnittet IDataReader.”
Det beror på en ändring i formatet som används när data läses in på lagringskontot.
Undvik det här problemet genom att ladda ned den senaste appen (version 1,8).
>
>

## <a name="supported-scenarios"></a>Scenarier som stöds
- Azure Backup rapporter stöds för säkerhets kopiering av virtuella Azure-datorer och säkerhets kopiering av filer och mappar till molnet med hjälp av Azure Recovery Services-agenten.
- Rapporter för Azure SQL Database, Azure-filresurser, Data Protection Manager och Azure Backup Server stöds inte för tillfället.
- Du kan visa rapporter i valv och prenumerationer om samma lagrings konto har kon figurer ATS för varje valv. Det valda lagrings kontot måste finnas i samma region som Recovery Services-valvet.
- Frekvensen för schemalagd uppdatering av rapporter är 24 timmar i Power BI. Du kan också utföra en ad hoc-uppdatering av rapporterna i Power BI. I det här fallet används den senaste informationen i kundens lagrings konto för att återge rapporter.

## <a name="prerequisites"></a>Förutsättningar
- Skapa ett [Azure Storage-konto](../storage/common/storage-quickstart-create-account.md) för att konfigurera det för-rapporter. Det här lagrings kontot används för att lagra rapporter relaterade data.
- [Skapa ett Power BI konto](https://powerbi.microsoft.com/landing/signin/) om du vill visa, anpassa och skapa egna rapporter med hjälp av Power BI portalen.
- Registrera resurs leverantören **Microsoft. Insights**, om den inte redan har registrerats. Använd prenumerationerna för lagrings kontot och Recovery Services valvet så att rapporterings data kan flöda till lagrings kontot. För att göra det här steget går du till Azure Portal, > väljer prenumerations**resurs leverantörer**och söker efter den här providern för att registrera den.

## <a name="configure-storage-account-for-reports"></a>Konfigurera lagrings konto för rapporter
Följ dessa steg om du vill konfigurera lagrings kontot för ett Recovery Services valv med hjälp av Azure Portal. Detta är en engångs konfiguration. När lagrings kontot har kon figurer ATS kan du gå direkt till Power BI för att Visa innehålls paketet och använda rapporter.

1. Om du redan har ett Recovery Services-valv öppet går du till nästa steg. Om du inte har ett Recovery Services-valv öppet går du till Azure Portal och väljer **alla tjänster**.

   * Ange **Recovery Services**i listan över resurser.
   * När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services valv**väljer du det.
   * Listan över Recovery Services-valv visas. Välj ett valv i listan över Recovery Services-valv.

     Instrumentpanelen för det valda valvet öppnas.
2. I listan med objekt som visas under valvet går du till avsnittet **övervakning och rapporter** och väljer **säkerhets kopierings rapporter** för att konfigurera lagrings kontot för-rapporter.

      ![Välj meny alternativet säkerhets kopierings rapporter steg 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. På bladet **säkerhets kopierings rapporter** väljer du länken **diagnostikinställningar** . Den här länken öppnar användar gränssnittet för **diagnostikinställningar** , som används för att skicka data till ett kund lagrings konto.

      ![Aktivera diagnostik steg 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Välj **Aktivera diagnostik** för att öppna ett användar gränssnitt som används för att konfigurera ett lagrings konto.

      ![Aktivera diagnostik steg 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. I rutan **namn** anger du ett inställnings namn. Markera kryss rutan **arkivera till ett lagrings konto** så att rapporterings data kan börja flöda in på lagrings kontot.

      ![Aktivera diagnostik steg 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Välj **lagrings konto**, Välj den relevanta prenumerationen och lagrings kontot i listan för lagring av rapporterings data och välj **OK**.

      ![Välj lagrings konto steg 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Under avsnittet **logg** markerar du kryss rutan **AzureBackupReport** . Flytta skjutreglaget för att välja en kvarhållningsperiod för denna rapport data. Rapporterings data i lagrings kontot behålls under den period som väljs med hjälp av det här skjutreglaget.

      ![Spara lagrings konto steg 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Granska alla ändringar och välj **Spara**. Den här åtgärden säkerställer att alla ändringar sparas och att lagrings kontot nu har kon figurer ATS för att lagra rapporterings data.

9. Tabellen **diagnostiska inställningar** visar nu den nya inställningen som är aktive rad för valvet. Om den inte visas uppdaterar du tabellen för att se den uppdaterade inställningen.

      ![Visa diagnostisk inställning steg 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> När du har konfigurerat rapporter genom att spara lagrings kontot väntar du i *24 timmar* på att den första data-push-installationen ska slutföras. Importera Azure Backup-appen i Power BI endast efter den tiden. Mer information finns i [avsnittet Vanliga frågor och svar](backup-azure-monitor-alert-faq.md).
>
>

## <a name="view-reports-in-power-bi"></a>Visa rapporter i Power BI
När du har konfigurerat ett lagrings konto för rapporter med hjälp av ett Recovery Services-valv tar det cirka 24 timmar innan rapporterings data börjar flöda i. Följ stegen nedan om du vill visa rapporter i Power BI efter 24 timmar efter att du ställt in ett lagrings konto.
Om du vill anpassa och dela rapporten skapar du en arbets yta och utför följande steg

1. [Logga in](https://powerbi.microsoft.com/landing/signin/) på Power BI.
2. Välj **Hämta data**. På **fler sätt att skapa ditt eget innehåll**väljer du **tjänst innehålls paket**. Följ stegen i [Power BI-dokumentationen för att ansluta till en tjänst](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. I **Sök** fältet skriver du **Azure Backup** och väljer **Hämta nu**.

      ![Hämta innehålls paket](./media/backup-azure-configure-reports/content-pack-get.png)
4. Ange namnet på det lagrings konto som har kon figurer ATS i föregående steg 5 och välj **Nästa**.

    ![Ange ett lagringskontonamn](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Använd autentiseringsmetoden "Key" och ange lagrings konto nyckeln för det här lagrings kontot. Om du vill [Visa och kopiera åtkomst nycklar för lagring](../storage/common/storage-account-manage.md#access-keys)går du till ditt lagrings konto i Azure Portal.

     ![Ange lagrings konto](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Välj **Logga**in. När inloggningen är klar visas ett meddelande om att importera data.

    ![Innehålls paketet importeras](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    När importen är klar visas ett meddelande om att det är **klart** . Om mängden data i lagrings kontot är stor kan det ta lite längre tid att importera innehålls paketet.

    ![Importera lyckade innehålls paket](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. När data har importer ATS visas **Azure Backup** innehålls paketet i **appar** i navigerings fönstret. Under **instrument paneler**, **rapporter**och **data uppsättningar**visar listan nu Azure Backup.

8. Under **instrument paneler**väljer du **Azure Backup**som visar en uppsättning med fästa nyckel rapporter.

      ![Azure Backup instrument panel](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Om du vill visa en fullständig uppsättning rapporter väljer du valfri rapport på instrument panelen.

      ![Azure Backup jobbets hälsa](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Välj varje flik i rapporterna för att visa rapporter i det här avsnittet.

      ![Flikar för Azure Backup rapporter](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="troubleshooting-errors"></a>Felsöka fel
| Felinformation | Lösning |
| --- | --- |
| När du har konfigurerat lagrings kontot för säkerhets kopierings rapporter visas inte **lagrings kontot** som **har kon figurer ATS**. | Om du har konfigurerat ett lagrings konto flödar dina rapporterings data på trots det här problemet. Lös problemet genom att gå till Azure Portal och välja **alla tjänster** > **diagnostikinställningar inställningar** > **Recovery Services valv** > **Redigera inställning**. Ta bort den tidigare konfigurerade inställningen och skapa en ny inställning på samma blad. Den här gången väljer du **tjänst**i rutan **namn** . Nu visas det konfigurerade lagrings kontot. |
|När du har importerat Azure Backup innehålls paketet i Power BI visas fel meddelandet "404-container hittas inte". | Som tidigare nämnts måste du vänta 24 timmar efter att du har konfigurerat rapporter i Recovery Services valvet för att se dem korrekt i Power BI. Om du försöker komma åt rapporterna före 24 timmar visas det här fel meddelandet eftersom fullständiga data ännu inte är tillgängliga för att visa giltiga rapporter. |

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat lagrings kontot och importerat Azure Backup innehålls paketet, är nästa steg att anpassa rapporter och använda en rapporterings data modell för att skapa rapporter. Mer information finns i följande artiklar.

* [Använda en Azure Backup rapporterings data modell](backup-azure-reports-data-model.md)
* [Filtrera rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Skapa rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
