---
title: Katastrofåterställning för FarmBeats
description: I den här artikeln beskrivs hur dataåterställning skyddar mot att förlora dina data.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: d64735e683ba1133e7d381a68611d204c4068026
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313076"
---
# <a name="disaster-recovery-for-farmbeats"></a>Katastrofåterställning för FarmBeats

Dataåterställning skyddar dig från att förlora dina data i en händelse som kollaps i Azure-regionen. I ett sådant fall kan du starta redundans och återställa data som lagras i distributionen Av FarmBeats.

Dataåterställning är inte en standardfunktion i Azure FarmBeats. Du kan konfigurera den här funktionen manuellt genom att konfigurera de nödvändiga Azure-resurserna som används av FarmBeats för att lagra data i en Azure-kopplad region. Använd Aktiv – Passiv metod för att aktivera återställning.

I följande avsnitt finns information om hur du kan konfigurera dataåterställning i Azure FarmBeats:

- [Aktivera dataredundans](#enable-data-redundancy)
- [Återställa tjänsten från säkerhetskopiering online](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Aktivera dataredundans

FarmBeats lagrar data i tre Azure-tjänster i första part, som är **Azure storage,** **Cosmos DB** och **Time Series Insights**. Gör så här för att aktivera dataredundans för dessa tjänster till en parkopplad Azure-region:

1.  **Azure Storage** – Följ den här riktlinjen för att aktivera dataredundans för varje lagringskonto i farmbbeats-distributionen.
2.  **Azure Cosmos DB** – Följ den här riktlinjen för att aktivera dataredundans för Cosmos DB-konto som din FarmBeats-distribution.
3.  **Azure Time Series Insights (TSI)** – TSD erbjuder för närvarande inte dataredundans. Om du vill återställa Time Series Insights-data går du till din sensor/väderpartner och skickar data igen till FarmBeats-distributionen.

## <a name="restore-service-from-online-backup"></a>Återställa tjänsten från säkerhetskopiering online

Du kan initiera redundans och återställa data som lagrats för vilka, var och en av de ovan nämnda data lagrar för din FarmBeats-distribution. När du har återställt data för Azure storage och Cosmos DB skapar du en annan FarmBeats-distribution i azure-parkopplad region och konfigurerar sedan den nya distributionen för att använda data från återställda datalager (dvs. Azure Storage och Cosmos DB) med hjälp av stegen nedan:

1. [Konfigurera Cosmos DB](#configure-cosmos-db)
2. [Konfigurera lagringskonto](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Konfigurera Cosmos DB

Kopiera åtkomstnyckeln för den återställda Cosmos DB och uppdatera det nya FarmBeats Datahub Key Vault.


  ![Haveriberedskap](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Kopiera URL:en för återställd Cosmos DB och uppdatera den i den nya Konfigurationen av FarmBeats Datahub-apptjänst. Du kan nu ta bort Cosmos DB-konto i den nya FarmBeats-distributionen.

  ![Haveriberedskap](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Konfigurera lagringskonto

Kopiera åtkomstnyckeln för det återställda lagringskontot och uppdatera det i det nya FarmBeats Datahub Key Vault.

![Haveriberedskap](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Kontrollera att uppdatera lagringskontonamnet i den nya FarmBeats Batch VM-konfigurationsfilen.

![Haveriberedskap](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Om du har aktiverat dataåterställning för ditt acceleratorlagringskonto följer du steg 2 för att uppdatera åtkomstnyckeln och namnet för acceleratorlagringskontot i den nya FarmBeats-instansen.
