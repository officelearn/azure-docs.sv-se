---
title: Azure Batch-renderingstjänst – rendering i molnskala | Microsoft Docs
description: Rendera jobb på virtuella datorer i Azure direkt från Maya med betalning per användning.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: danlep
ms.openlocfilehash: df1b2da7628e6c3f9f4bcbb02a936c33aad49698
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="get-started-with-the-batch-rendering-service"></a>Komma igång med tjänsten Batch Rendering

Tjänsten Azure Batch Rendering erbjuder renderingsfunktioner i molnskala med betalning per användning. Tjänsten Batch Rendering hanterar jobbschemaläggning och jobbköer, med funktioner för felhantering, omförsök och automatisk skalning för renderingsjobb. Tjänsten Batch Rendering stöder renderingsappar som [Autodesk Maya](https://www.autodesk.com/products/maya/overview), [3ds Max](https://www.autodesk.com/products/3ds-max/overview), [Arnold](https://www.autodesk.com/products/arnold/overview) och [V-Ray](https://www.chaosgroup.com/vray/maya). Med Batch-plugin-programmet för Maya 2017 kan du enkelt starta ett renderingsjobb i Azure direkt från skrivbordet.

Med Maya och 3ds Max kan du köra jobb med datorprogrammet [BatchLabs](https://github.com/Azure/BatchLabs) eller [CLI för Batch-mallar](batch-cli-templates.md). Med Azure Batch CLI kan du köra Batch-jobb utan att skriva kod. Istället kan du använda mallfilerna för att skapa Batch-pooler, -jobb och -uppgifter. Mer information finns i [Använda Azure Batch CLI-mallar och filöverföring](batch-cli-templates.md).


## <a name="supported-applications"></a>Program som stöds

För närvarande har tjänsten Batch Rendering stöd för följande program:

På CentOS 7 återgivningsnoder:
- Autodesk Maya I/O 2017 uppdatering 5 (version 201708032230)
- Autodesk Maya I/O 2018 uppdatering 2 version 201711281015
- Autodesk Arnold för Maya 2017 (Arnold version 5.0.1.1) MtoA-2.0.1.1-2017
- Autodesk Arnold för Maya 2018 (Arnold version 5.0.1.4) MtoA-2.1.0.3-2018
- Chaos Group V-Ray för Maya 2017 (version 3.60.04) 
- Chaos Group V-Ray för Maya 2018 (version 3.60.04) 
- Blender (2.68)

På Windows Server 2016 återgivningsnoder:
- Autodesk Maya I/O 2017 uppdatering 5 (version 17.4.5459) 
- Autodesk Maya I/O 2018 uppdatering 2 (version 18.2.0.6476) 
- Autodesk Maya 3ds I/O 2018 uppdatering 4 (version 20.4.0.4254) 
- Autodesk Arnold för Maya (Arnold version 5.0.1.1) MtoA-2.0.1.1-2017
- Autodesk Arnold för Maya (Arnold version 5.0.1.4) MtoA-2.0.2.3-2018
- Autodesk Arnold för 3ds Max (Arnold version 5.0.2.4 )(version 1.2.926) 
- Chaos Group V-Ray för Maya (version 3.52.03) 
- Chaos Group V-Ray för 3ds Max (version 3.60.02)
- Blender (2.79)


## <a name="prerequisites"></a>Nödvändiga komponenter

För att använda tjänsten Batch Rendering behöver du:

- [Azure-konto](https://azure.microsoft.com/free/).
- **Azure Batch-konto.** Anvisningar för hur du skapar ett Batch-konto på Azure-portalen finns i [Skapa ett Batch-konto med Azure-portalen](batch-account-create-portal.md).
- **Azure Storage-konto.** De resurser som används för dina återgivningsjobb lagras vanligtvis i Azure Storage. Du kan skapa ett lagringskonto automatiskt när du skapar ditt Batch-konto. Du kan också använda ett befintligt lagringskonto. För olika alternativ för lagringskonton, se [Översikt över Batch-funktionen](batch-api-basics.md#azure-storage-account).
- **Miljövariabler.** Om din lösning ändrar miljövariabler kontrollerar du att värdena för `AZ_BATCH_ACCOUNT_URL` och `AZ_BATCH_SOFTWARE_ENTITLEMENT_TOKEN` hålls intakta och tillgängliga när något av ovanstående licensierade program anropas. Annars är det troligt att det uppstår aktiveringsproblem med programvaran.
- **BatchLabs** (valfritt). [BatchLabs](https://azure.github.io/BatchLabs) är ett kostnadsfritt, fristående klientverktyg med omfattande funktioner för att skapa, felsöka och övervaka Azure Batch-program. Även om det inte krävs för att använda renderingstjänsten så är det användbart för att utveckla och felsöka dina Batch-lösningar.

För att använda Batch-plugin-programmet för Maya behöver du:

- [Autodesk Maya 2017](https://www.autodesk.com/products/maya/overview).
- Ett renderingsprogram som stöds som Arnold för Maya eller V-Ray för Maya.

## <a name="basic-batch-concepts"></a>Grundläggande begrepp för Batch

Innan du börjar använda tjänsten Batch Rendering är det bra att känna till några viktiga Batch-begrepp, t.ex. beräkningsnoder, pooler och jobb. Mer allmän information om Azure Batch finns i [Köra parallella arbetsbelastningar med Batch](batch-technical-overview.md).

### <a name="pools"></a>Pooler

Batch är en plattformstjänst för att köra beräkningsintensivt arbete, t.ex. rendering, i en **pool** med **beräkningsnoder**. Varje beräkningsnod i poolen är en virtuell Azure-dator (VM) som kör Linux eller Windows. 

Mer information om Batch-pooler och beräkningsnoder finns i avsnitten [Pool](batch-api-basics.md#pool) och [Beräkningsnod](batch-api-basics.md#compute-node) i [Utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).

### <a name="jobs"></a>Jobb

Ett Batch-**jobb** är en samling uppgifter som körs på beräkningsnoderna i en pool. När du skickar ett renderingsjobb delar Batch upp jobbet i olika uppgifter och distribuerar uppgifterna till beräkningsnoderna i poolen som ska köra dem.

Från [Azure Portal](https://ms.portal.azure.com/) kan du övervaka jobb och diagnostisera uppgifter som misslyckats genom att hämta programloggarna och genom att fjärransluta till enskilda virtuella datorer med RDP eller SSH. Du kan också hantera, övervaka och felsöka med hjälp av [BatchLabs-verktyget](https://azure.github.io/BatchLabs).

Mer information om Batch-jobb finns i avsnittet [Jobb](batch-api-basics.md#job) i [Utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).

## <a name="options-for-provisioning-required-applications"></a>Alternativ för att etablera nödvändiga program

Flera program kan krävas för att återge ett jobb, t.ex. en kombination av Maya och Arnold eller 3ds Max och V-Ray samt andra tredjeparts-plugin-program, om det behövs. Vissa kunder kan dessutom kräva specifika versioner av dessa program. Därför finns det flera metoder för att etablera nödvändiga program:

### <a name="pre-configured-vm-images"></a>Förkonfigurerade VM-avbildningar

Azure tillhandahåller Windows- och Linux-avbildningar med en förinstallerad version av Maya 3ds Max och Arnold V-Ray som är klar att användas. Du kan välja de här avbildningarna i [Azure Portal](https://portal.azure.com), Maya-plugin-programmet eller [BatchLabs](https://azure.github.io/BatchLabs) när du skapar en pool.

I Azure Portal och i BatchLabs kan du installera någon av VM-avbildningarna med de förinstallerade programmen enligt följande: I avsnittet pooler på ditt Batch-konto väljer du **nytt** och i **lägg till pool** väljer du **grafik och rendering (Linux/Windows)** från listrutan **avbildningstyp**:

![Välj avbildningstyp för Batch-kontot](./media/batch-rendering-service/add-pool.png)

Bläddra nedåt och under **Licensiering grafik och återgivning** klickar du på **Välj program och prissättning**. Välj en eller flera av programvarulicenserna:

![Välj grafik- och renderingslicens för poolen](./media/batch-rendering-service/graphics-licensing.png)

Specifika licensversioner som tillhandahålls med versionerna i avsnittet ”Program som stöds” ovan.

### <a name="custom-images"></a>Anpassade avbildningar

Med Azure Batch kan du tillhandahålla en egen anpassad avbildning. Med det här alternativet kan du konfigurera din virtuella dator med de exakta program och specifika versioner du behöver. Mer information finns i [Use a custom image to create a pool of virtual machines](https://docs.microsoft.com/azure/batch/batch-custom-images) (Använda en anpassad avbildning för att skapa en pool med virtuella datorer). Observera att Autodesk och Chaos Group har ändrat Arnold respektive V-Ray för att verifiera mot vår egen licensieringstjänst. Du måste kontrollera att du har versionerna av dessa program som har det här stödet, annars fungerar inte licensieringen där du betalar utifrån hur mycket du använder. Licensverifieringen krävs inte för Maya eller 3ds Max eftersom de publicerade versionerna inte kräver någon licensserver när de körs med fjärradministrering (i batch/kommandoradsläge). Kontakta Azure-supporten om du inte vet hur du fortsätter med det här alternativet.

## <a name="options-for-submitting-a-render-job"></a>Alternativ för att skicka ett renderingsjobb

Beroende på vilket 3D-program du använder finns det olika alternativ för att skicka renderingsjobb till tjänsten:

### <a name="maya"></a>Maya

Med Maya kan du använda:

- [Batch-plugin-programmet för Maya](https://docs.microsoft.com/azure/batch/batch-rendering-service#use-the-batch-plug-in-for-maya-to-submit-a-render-job)
- [BatchLabs](https://azure.github.io/BatchLabs)-datorprogrammet
- [CLI för Batch-mallar](batch-cli-templates.md)

### <a name="3ds-max"></a>3ds Max

Med 3ds Max kan du använda:

- [BatchLabs](https://azure.github.io/BatchLabs)-datorprogrammet (se [BatchLabs-data](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) för hjälp med att använda 3ds Max BatchLabs-mallar)
- [CLI för Batch-mallar](batch-cli-templates.md)

Med 3ds Max Batch Labs-mallar kan du återge VRay- och Arnold-bakgrunder med tjänsten Azure Batch Rendering. Det finns två varianter av mallen för VRay och Arnold, en för standardbakgrunder och en för mer komplexa bakgrunder som kräver en 3ds Max-sökvägsfil till tillgångar och strukturer (.mxp-fil). Mer information om 3ds Max BatchLabs-mallar finns i lagringsplatsen [BatchLabs-data](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) på GitHub.

Du kan också använda [Batch Python SDK](https://docs.microsoft.com/azure/batch/batch-python-tutorial) för att integrera tjänsten med din befintliga pipeline.


## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Skicka ett renderingsjobb med Batch-plugin-programmet för Maya

Med Batch-plugin-programmet för Maya kan du skicka ett jobb till Batch Rendering-tjänsten direkt från Maya. Följande avsnitt beskriver hur du konfigurerar jobbet från plugin-programmet och hur du sedan skickar det. 

### <a name="load-the-batch-plug-in-for-maya"></a>Läsa in Batch-plugin-programmet för Maya

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
|Linux     |Batch CentOS |
|Windows     |Batch Windows |

#### <a name="choose-a-vm-size"></a>Välja storlek för virtuella datorer

Du kan ange storleken för virtuella datorer på fliken **Env** (Miljö). Mer information om tillgängliga storlekar för virtuella datorer finns i [Linux VM sizes in Azure](../virtual-machines/linux/sizes.md) (Storlekar för virtuella Linux-datorer i Azure) och [Windows VM sizes in Azure](../virtual-machines/windows/sizes.md) (Storlekar för virtuella Windows-datorer i Azure). 

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
