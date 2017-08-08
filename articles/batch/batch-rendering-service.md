---
title: "Rendera i molnet med tjänsten Azure Batch Rendering | Microsoft Docs"
description: "Rendera jobb på virtuella datorer i Azure direkt från Maya med betalning per användning."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: hero-article
ms.date: 07/31/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 4d22f92cafdbceee5213361d6d2b2f38904d12c6
ms.contentlocale: sv-se
ms.lasthandoff: 08/01/2017

---

# <a name="get-started-with-the-batch-rendering-service"></a>Komma igång med tjänsten Batch Rendering

Tjänsten Azure Batch Rendering erbjuder renderingsfunktioner i molnskala med betalning per användning. Tjänsten Batch Rendering hanterar jobbschemaläggning och jobbköer, med funktioner för felhantering, omförsök och automatisk skalning för renderingsjobb. Tjänsten Batch Rendering stöder Autodesk Maya, 3ds Max och Arnold. Stöd för andra program kommer inom kort. Med Batch-plugin-programmet för Maya 2017 kan du enkelt starta ett renderingsjobb i Azure direkt från skrivbordet. 

## <a name="supported-applications"></a>Program som stöds

För närvarande har tjänsten Batch Rendering stöd för följande program:

- Autodesk Maya
- Autodesk 3ds Max
- Autodesk Arnold

## <a name="prerequisites"></a>Krav

För att använda tjänsten Batch Rendering behöver du:

- Ett [Azure-konto](https://azure.microsoft.com/free/). 
- **Ett Azure Batch-konto.** Anvisningar för hur du skapar ett Batch-konto på Azure-portalen finns i [Skapa ett Batch-konto med Azure-portalen](batch-account-create-portal.md).
- **Ett Azure Storage-konto.** De resurser som används för dina renderingsjobb lagras i Azure Storage. Du kan skapa ett lagringskonto automatiskt när du skapar ditt Batch-konto. Du kan också använda ett befintligt lagringskonto. Mer information om lagringskonton finns i [Skapa, hantera eller ta bort ett lagringskonto på Azure-portalen](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

För att använda Batch-plugin-programmet för Maya behöver du:

- **Maya 2017**
- **Arnold för Maya**

Du kan också använda [Azure-portalen](https://portal.azure.com) för att skapa pooler med virtuella datorer som är förkonfigurerade med Maya, 3ds Max och Arnold. Från portalen kan du övervaka jobb och diagnostisera uppgifter som misslyckats genom att hämta programloggarna och genom att fjärransluta till enskilda virtuella datorer med RDP eller SSH.

## <a name="basic-batch-concepts"></a>Grundläggande begrepp för Batch

Innan du börjar använda tjänsten Batch Rendering är det bra att känna till några viktiga Batch-begrepp, t.ex. beräkningsnoder, pooler och jobb. Mer allmän information om Azure Batch finns i [Köra parallella arbetsbelastningar med Batch](batch-technical-overview.md).

### <a name="pools"></a>Pooler

Batch är en plattformstjänst för att köra beräkningsintensivt arbete, t.ex. rendering, i en **pool** med **beräkningsnoder**. Varje beräkningsnod i poolen är en virtuell Azure-dator (VM) som kör Linux eller Windows. 

Mer information om Batch-pooler och beräkningsnoder finns i avsnitten [Pool](batch-api-basics.md#pool) och [Beräkningsnod](batch-api-basics.md#compute-node) i [Utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).

### <a name="jobs"></a>Jobb

Ett Batch-**jobb** är en samling uppgifter som körs på beräkningsnoderna i en pool. När du skickar ett renderingsjobb delar Batch upp jobbet i olika uppgifter och distribuerar uppgifterna till beräkningsnoderna i poolen som ska köra dem.

Mer information om Batch-jobb finns i avsnittet [Jobb](batch-api-basics.md#job) i [Utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).

## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Skicka ett renderingsjobb med Batch-plugin-programmet för Maya

Med Batch-plugin-programmet för Maya kan du skicka ett jobb till Batch Rendering-tjänsten direkt från Maya. Följande avsnitt beskriver hur du konfigurerar jobbet från plugin-programmet och hur du sedan skickar det. 

### <a name="load-the-batch-plug-in-in-maya"></a>Läsa in Batch-plugin-programmet i Maya

Batch-plugin-programmet är tillgängligt på [GitHub](https://github.com/Azure/azure-batch-maya/releases). Packa upp arkivet till valfri katalog. Du kan läsa in plugin-programmet direkt från katalogen *azure_batch_maya*.

Så här läser du in plugin-programmet i Maya:

1. Kör Maya.
2. Öppna **Window** (Fönster)  > **Settings/Preferences** (Inställningar)  > **Plug-in Manager** (Plugin-hanteraren).
3. Klicka på **Browse** (Bläddra).
4. Navigera till och välj *azure_batch_maya/plug-in/AzureBatch.py*.

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>Autentisera åtkomsten till dina Batch- och Storage-konton

Du måste autentisera med dina Azure Batch- och Azure Storage-kontonycklar för att kunna använda plugin-programmet. Så här hämtar du dina kontonycklar:

1. Visa plugin-programmet i Maya och välj fliken **Config** (Konfiguration).
2. Navigera till [Azure-portalen](https://portal.azure.com).
3. Välj **Batch-konton** på menyn till vänster. Om det behövs klickar du på **Fler tjänster** och filtrerar på _Batch_.
4. Leta upp Batch-kontot i listan.
5. Välj menyalternativet **Nycklar** för att visa kontonamnet, kontots URL-adress och åtkomstnycklarna:
    - Klistra in URL:en för Batch-kontot i fältet **Tjänst** i Batch-plugin-programmet.
    - Klistra in kontonamnet i fältet **Batch-konto**.
    - Klistra in den primära kontonyckeln i fältet **Batch Key** (Batch-nyckel).
7. Välj Lagringskonton på menyn till vänster. Om det behövs klickar du på **Fler tjänster** och filtrerar på _Storage_.
8. Leta upp önskat lagringskonto i listan.
9. Välj menyalternativet **Åtkomstnycklar** för att visa lagringskontots namn och nycklar.
    - Klistra in lagringskontots namn i fältet **Lagringskonto** i Batch-plugin-programmet.
    - Klistra in den primära kontonyckeln i fältet **Lagringsnyckel**.
10. Kontrollera att plugin-programmet kan komma åt båda kontona genom att klicka på **Autentisera**.

När autentiseringen har lyckats ändras statusfältet i plugin-programmet till **Autentiserad**: 

![Autentisera dina Batch- och Storage-konton](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>Konfigurera en pool för ett renderingsjobb

När du har autentiserat dina Batch- och Storage-konton skapar du en pool för renderingsjobbet. Plugin-programmet sparar dina val mellan sessioner. När du har skapat konfigurationen behöver du inte modifiera den såvida den inte ändras.

Följande avsnitt beskriver de tillgängliga alternativen på fliken **Skicka**:

#### <a name="specify-a-new-or-existing-pool"></a>Ange en ny eller befintlig pool

Du anger vilken pool som renderingsjobbet ska köras i genom att först välja fliken **Skicka**. På den här fliken kan du välja mellan att skapa en ny pool eller använda en befintlig:

- Du kan **etablera en pool automatiskt för det här jobbet** (standardalternativet). Om du väljer det här alternativet skapar Batch poolen exklusivt för det aktuella jobbet och tar automatiskt bort poolen när renderingsjobbet har slutförts. Det här alternativet är bäst när du ska köra ett enskilt renderingsjobb.
- Du kan **återanvända en befintlig beständig pool**. Om du har en befintlig pool som är inaktiv kan du välja den poolen för att köra renderingsjobbet genom att välja poolen i listrutan. Du sparar tid genom att återanvända en befintlig beständig pool eftersom du inte behöver etablera poolen.  
- Du kan **skapa en ny beständiga pool**. Om du väljer det här alternativet skapas en ny pool för att köra jobbet. Poolen tas inte bort när jobbet har slutförts, så att du kan återanvända den för framtida jobb. Välj det här alternativet om du behöver köra renderingsjobb regelbundet. För efterföljande jobb kan du välja att **återanvända en befintlig beständig pool** om du vill använda den beständiga poolen som du skapade för det första jobbet.

![Ange pool, OS-avbildning, VM-storlek och licens](./media/batch-rendering-service/submit.png)

Mer information om hur kostnader påförs för virtuella Azure-datorer finns i avsnittet med [vanliga frågor och svar om priser för Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq) och avsnittet med [vanliga frågor och svar om priser för Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq).

#### <a name="specify-the-os-image-to-provision"></a>Ange den operativsystemsavbildning som ska etableras

Du kan ange vilken typ av operativsystemsavbildning som du vill använda för att etablera beräkningsnoder i poolen på fliken **Env** (Miljö). För närvarande stöder Batch följande avbildningsalternativ för renderingsjobb:

|Operativsystem  |Bild  |
|---------|---------|
|Linux     |Batch CentOS Preview |
|Windows     |Batch Windows Preview |

#### <a name="choose-a-vm-size"></a>Välja storlek för virtuella datorer

Du kan ange storleken för virtuella datorer på fliken **Env** (Miljö). Mer information om tillgängliga storlekar för virtuella datorer finns i [Linux VM sizes in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) (Storlekar för virtuella Linux-datorer i Azure) och [Windows VM sizes in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) (Storlekar för virtuella Windows-datorer i Azure). 

![Ange operativsystemsavbildning och storlek för virtuella datorer på fliken Env (Miljö)](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>Ange licensieringsalternativ

Du kan ange vilka licenser du vill använda på fliken **Env** (Miljö). Alternativen är:

- **Maya**, som är aktiverat som standard.
- **Arnold**, som är aktiverat om Arnold har identifierats som den aktiva renderingsmotorn i Maya.

 Om du vill använda en egen licens för renderingen kan du konfigurera slutpunkten för din licens genom att lägga till lämpliga miljövariablerna i tabellen. Avmarkera standardlicensalternativen om du gör det.

> [!IMPORTANT]
> Du debiteras för användning av licenserna när virtuella datorer körs i poolen, även om de virtuella datorerna inte används för rendering för tillfället. Du kan undvika extra kostnader genom att gå till fliken **Pooler** och ändra storleken på poolen till 0 noder tills det är dags att köra nästa renderingsjobb. 
>
>

#### <a name="manage-persistent-pools"></a>Hantera beständiga pooler

Du kan hantera en befintlig beständiga pool på fliken **Pooler**. Om du väljer en pool i listan visas poolens aktuella status.

Från fliken **Pooler** kan du även ta bort poolen och ändra antalet virtuella datorer i poolen. Du kan ändra storlek på poolen till 0 noder för att undvika kostnader mellan arbetsbelastningar.

![Visa, ändra storlek på och ta bort pooler](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>Konfigurera ett renderingsjobb för att skicka det

När du har angett parametrarna för poolen som ska köra renderingsjobbet, konfigurerar du själva jobbet. 

#### <a name="specify-scene-parameters"></a>Ange scenparametrar

Batch-plugin-programmet identifierar vilken renderingsmotor som du för närvarande använder i Maya och visar lämpliga renderingsinställningar på fliken **Submit** (Skicka). Du kan bland annat ange inställningar för första bildruta, sista bildruta, utdataprefix och bildrutesteg. Du kan åsidosätta renderingsinställningarna för scenfilen genom att ange olika inställningar i plugin-programmet. De ändringar du gör i plugin-programmets inställningar är inte beständiga i scenfilens renderingsinställningar. Det betyder att du kan göra ändringar för varje enskilt jobb utan att behöva ladda upp scenfilen.

Plugin-programmet varnar dig om den renderingsmotor som du valt i Maya inte stöds.

Om du läser in en ny scen när plugin-programmet är öppet klickar du på knappen **Uppdatera** för att se till att inställningarna uppdateras.

#### <a name="resolve-asset-paths"></a>Matcha sökvägar för resurser

När du läser in plugin-programmet genomsöks scenfilen efter eventuella externa filreferenser. Dessa referenser visas på fliken **Assets** (Resurser). Om det inte går att matcha en refererad sökväg försöker plugin-programmet hitta filen på ett antal standardplatser, inklusive:

- platsen för scenfilen 
- det aktuella projektets _sourceimages_-katalog
- den aktuella arbetskatalogen. 

Om det fortfarande inte går att hitta resursen visas den med en varningsikon:

![Resurser som saknas visas med en varningsikon](./media/batch-rendering-service/missing_assets.png)

Om du känner till platsen för en filreferens som inte kan matchas, kan du klicka på varningsikonen så uppmanas du att lägga till sökvägen. I så fall använder plugin-programmet den här sökvägen för att försöka matcha resurser som saknas. Du kan lägga till valfritt antal ytterligare sökvägar.

När en referens har matchats visas den med en grön knappikon:

![Matchade resurser visas med en grön knappikon](./media/batch-rendering-service/found_assets.png)

Om din scen kräver andra filer som plugin-programmet inte har identifierat kan du lägga till ytterligare filer eller kataloger. Använd knapparna **Lägg till filer** och **Lägg till katalog**. Om du läser in en ny scen när plugin-programmet är öppet klickar du på **Uppdatera** så att scenens referenser uppdateras.

#### <a name="upload-assets-to-an-asset-project"></a>Ladda upp resurser till ett resursprojekt

När du skickar ett renderingsjobb överförs automatiskt de refererade filerna som visas på fliken **Assets** (Resurser) till Azure Storage som ett resursprojekt. Du kan också ladda upp resursfiler oberoende av ett renderingsjobb genom att använda knappen **Överför** på fliken **Assets** (Resurser). Resursprojektets namn anges i fältet **Projekt**. Projektet namnges som standard baserat på det aktuella Maya-projektet. Den lokala filstrukturen bevaras när resursfilerna laddas upp. 

När resurserna har laddats upp kan ett obegränsat antal renderingsjobb referera till dem. Alla överförda resurser är tillgängliga för alla jobb som refererar till resursprojektet, oavsett om de finns med i scenen eller inte. Om du vill ändra resursprojektet som ditt nästa jobb refererar till ändrar du namnet i fältet **Projekt** på fliken **Assets** (Resurser). Om det finns refererade filer som du vill utesluta från uppladdningen avmarkerar du dem genom att klicka på den gröna knappen bredvid posten i listan.

#### <a name="submit-and-monitor-the-render-job"></a>Skicka och övervaka renderingsjobbet

När du har konfigurerat renderingsjobbet i plugin-programmet klickar du på knappen **Skicka jobb** på fliken **Skicka** för att skicka jobbet till Batch:

![Skicka renderingsjobbet](./media/batch-rendering-service/submit_job.png)

Du kan övervaka ett pågående jobb från fliken **Jobb** i plugin-programmet. Välj ett jobb i listan för att visa jobbets aktuella status. Du kan också använda den här fliken för att avbryta och ta bort jobb, samt för att hämta utdata och renderingsloggar. 

Om du vill hämta utdata ändrar du fältet **Utdata** till önskad målkatalog. Klicka på kugghjulsikonen om du vill starta en bakgrundsprocess som bevakar jobbet och hämtar utdata i takt med att jobbet fortskrider: 

![Visa jobbstatus och hämta utdata](./media/batch-rendering-service/jobs.png)

Du kan stänga Maya utan att hämtningsprocessen avbryts.

## <a name="next-steps"></a>Nästa steg

Mer information om Batch finns i [Köra parallella arbetsbelastningar med Batch](batch-technical-overview.md).
