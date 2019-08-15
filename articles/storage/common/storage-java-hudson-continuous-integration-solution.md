---
title: Använda Hudson med Blob Storage | Microsoft Docs
description: Beskriver hur du använder Hudson med Azure Blob Storage som en lagrings plats för bygg artefakter.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: 10bfc3ce4666ee1653110099a3c8d22a58d80f35
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985304"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Använda Azure Storage med en Hudson-baserad CI-lösning
## <a name="overview"></a>Översikt
Följande information visar hur du använder Blob Storage som en lagrings plats för bygg artefakter som skapats av en Hudson-lösning för kontinuerlig integrering (CI) eller som en källa till nedladdnings bara filer som ska användas i en build-process. Ett av scenarier där du kan använda det här är när du kodar i en smidig utvecklings miljö (med Java eller andra språk), versioner körs baserat på kontinuerlig integrering och du behöver en lagrings plats för dina Bygg artefakter, så att du kan, Du kan till exempel dela dem med andra medlemmar i organisationen, dina kunder eller underhålla ett arkiv.  Ett annat scenario är när ditt build-jobb själva kräver andra filer, till exempel beroenden som ska laddas ned som en del av den inmatade versionen.

I den här självstudien kommer du att använda Azure Storage plugin-programmet för Hudson-CI som gjorts tillgängligt av Microsoft.

## <a name="introduction-to-hudson"></a>Introduktion till Hudson
Hudson möjliggör kontinuerlig integrering av ett program projekt genom att göra det möjligt för utvecklare att enkelt integrera sina kod ändringar och skapa och ofta skapa byggen automatiskt och därmed öka produktiviteten hos utvecklarna. Versioner av versioner och build-artefakter kan överföras till olika lagrings platser. I den här artikeln visas hur du använder Azure Blob Storage som lagrings plats för bygg artefakterna. Det visar också hur du hämtar beroenden från Azure Blob Storage.

Mer information om Hudson finns på [Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Fördelar med att använda Blob Service
Fördelarna med att använda Blob Service som värd för dina Agile-utvecklings artefakter är:

* Hög tillgänglighet för dina build-artefakter och/eller nedladdnings bara beroenden.
* Prestanda när din Hudson CI-lösning laddar upp dina build-artefakter.
* Prestanda när dina kunder och partners laddar ned dina build-artefakter.
* Kontroll över användar åtkomst principer, med ett val mellan anonym åtkomst, utgångs-baserade signaturer för delad åtkomst, privat åtkomst osv.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att kunna använda Blob Service med din Hudson CI-lösning:

* En lösning för kontinuerlig integrering med Hudson.
  
    Om du för närvarande inte har en Hudson CI-lösning kan du köra en Hudson CI-lösning med hjälp av följande metod:
  
  1. På en Java-aktiverad dator laddar du ned Hudson-KRIGen från <http://hudson-ci.org/>.
  2. Kör Hudson-KRIGet i en kommando tolk som öppnas i den mapp som innehåller Hudson-KRIGet. Om du till exempel har hämtat version 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. Öppna `http://localhost:8080/`i webbläsaren. Då öppnas Hudson-instrumentpanelen.
  4. Vid första användningen av Hudson slutför du den första installationen på `http://localhost:8080/`.
  5. När du har slutfört den inledande installationen avbryter du den aktiva instansen av Hudson-krig, startar Hudson-kriget igen och öppnar `http://localhost:8080/`Hudson-instrumentpanelen igen, som du kommer att använda för att installera och konfigurera Azure Storage-plugin-programmet.
     
      Även om en typisk Hudson CI-lösning skulle konfigureras att köras som en tjänst, räcker det att köra Hudson-kriget på kommando raden för den här självstudien.
* Ett Azure-konto. Du kan registrera dig för ett Azure-konto <https://www.azure.com>på.
* Ett Azure-lagringskonto. Om du inte redan har ett lagrings konto kan du skapa ett med hjälp av stegen i [skapa ett lagrings konto](../common/storage-quickstart-create-account.md).
* Du rekommenderas att använda Hudson CI-lösningen men det är inte obligatoriskt, eftersom följande innehåll kommer att använda ett grundläggande exempel för att visa de steg som krävs när du använder Blob Service som lagrings plats för Hudson CI build-artefakter.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Använda Blob Service med Hudson CI
Om du vill använda Blob Service med Hudson måste du installera Azure Storage-plugin-programmet, konfigurera plugin-programmet så att det använder ditt lagrings konto och sedan skapa en åtgärd efter build som laddar upp dina build-artefakter till ditt lagrings konto. De här stegen beskrivs i följande avsnitt.

## <a name="how-to-install-the-azure-storage-plugin"></a>Så här installerar du Azure Storage-plugin-programmet
1. I Hudson-instrumentpanelen klickar du på **Hantera Hudson**.
2. På sidan **Hantera Hudson** klickar du på **Hantera plugin**-program.
3. Klicka på fliken **tillgänglig** .
4. Klicka på **andra**.
5. I avsnittet **artefakt uppladdning** väljer du **Microsoft Azure Storage plugin-program**.
6. Klicka på **Installera**.
7. När installationen är klar startar du om Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Så här konfigurerar du Azure Storage-plugin-programmet så att det använder ditt lagrings konto
1. I Hudson-instrumentpanelen klickar du på **Hantera Hudson**.
2. Klicka på **Konfigurera system**på sidan **Hantera Hudson** .
3. I avsnittet **Microsoft Azure Storage konto konfiguration** :
   
    a. Ange namnet på ditt lagrings konto, som du kan hämta från [Azure Portal](https://portal.azure.com).
   
    b. Ange din lagrings konto nyckel, som också kan hämtas från [Azure Portal](https://portal.azure.com).
   
    c. Använd standardvärdet för **BLOB-tjänstens slut punkts-URL** om du använder det globala Azure-molnet. Om du använder ett annat Azure-moln använder du slut punkten som anges i [Azure Portal](https://portal.azure.com) för ditt lagrings konto.
   
    d. Verifiera ditt lagrings konto genom att klicka på **Verifiera autentiseringsuppgifter för lagring** .
   
    e. Valfritt Om du har ytterligare lagrings konton som du vill ska vara tillgängliga för Hudson-CI klickar du på **Lägg till fler lagrings konton**.
   
    f. Klicka på **Spara** för att spara inställningarna.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Så här skapar du en åtgärd efter bygge som laddar upp dina build-artefakter till ditt lagrings konto
För instruktions syfte måste du först skapa ett jobb som ska skapa flera filer och sedan lägga till i åtgärden efter build för att överföra filerna till ditt lagrings konto.

1. I Hudson-instrumentpanelen klickar du på **nytt jobb**.
2. Namnge jobbet **MyJob**, klicka på **Bygg ett program med en kostnads fri stil**och klicka sedan på **OK**.
3. I avsnittet **build** i jobb konfigurationen klickar du på **Lägg till build-steg** och väljer **kör Windows batch-kommando**.
4. I **kommando**använder du följande kommandon:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. I avsnittet **efter build-åtgärder** i jobb konfigurationen klickar **du på överför artefakter för att Microsoft Azure Blob Storage**.
6. För **lagrings konto namn**väljer du det lagrings konto som ska användas.
7. Ange behållar namnet för **container namn**. (Behållaren kommer att skapas om den inte redan finns när bygg artefakterna laddas upp.) Du kan använda miljövariabler, så i det här exemplet anger du **$ {JOB_NAME}** som behållar namn.
   
    **Tips**
   
    Under **kommando** avsnittet där du har angett ett skript för **kommando kommandot kör Windows** , är en länk till miljövariablerna som identifieras av Hudson. Klicka på länken för att lära dig om miljö variabel namn och beskrivningar. Miljövariabler som innehåller specialtecken, till exempel miljövariabeln **BUILD_URL** , tillåts inte som ett behållar namn eller en gemensam virtuell sökväg.
8. Klicka på **gör ny behållare offentlig som standard** i det här exemplet. (Om du vill använda en privat behållare måste du skapa en signatur för delad åtkomst för att tillåta åtkomst. Det ligger utanför den här artikelns omfattning. Du kan läsa mer om signaturer för delad åtkomst i [använda signaturer för delad åtkomst (SAS)](storage-sas-overview.md).)
9. Valfritt Klicka på **Rensa behållare innan** du överför om du vill att behållaren ska rensas efter innehåll innan Bygg artefakter laddas upp (lämna den omarkerad om du inte vill rensa innehållet i behållaren).
10. Ange **text/*. txt**för **lista över artefakter som ska överföras**.
11. Ange **$\_{build ID}/$ {build\_Number}** för **gemensam virtuell sökväg för överförda artefakter**.
12. Klicka på **Spara** för att spara inställningarna.
13. I Hudson-instrumentpanelen klickar du på **Skapa nu** för att köra **MyJob**. Granska konsolens utdata för status. Status meddelanden för Azure Storage tas med i konsolens utdata när åtgärden efter build börjar överföra Bygg artefakter.
14. När jobbet har slutförts kan du undersöka build-artefakterna genom att öppna den offentliga blobben.
    
    a. Logga in på [Azure Portal](https://portal.azure.com).
    
    b. Klicka på **lagring**.
    
    c. Klicka på det lagrings konto namn som du använde för Hudson.
    
    d. Klicka på **behållare**.
    
    e. Klicka på behållaren med namnet **myjob**, som är den gemena versionen av det jobb namn som du tilldelade när du skapade Hudson-jobbet. Behållar namn och blob-namn är gemener (och Skift läges känsliga) i Azure Storage. I listan över blobbar för behållaren med namnet **myjob** bör du se **Hej. txt** och **date. txt**. Kopiera URL: en för något av dessa objekt och öppna den i webbläsaren. Text filen som överfördes som en Bygg artefakt visas.

Endast en åtgärd efter build-åtgärd som laddar upp artefakter till Azure Blob Storage kan skapas per jobb. Åtgärden för att överföra artefakter till Azure Blob Storage kan ange olika filer (inklusive jokertecken) och sökvägar till filer i **listan över artefakter som ska överföras** med hjälp av semikolon som avgränsare. Om din Hudson-version t. ex. skapar jar-filer och txt-filer i din arbets ytas installationsmapp och du vill överföra både till Azure Blob Storage, använder du följande för att **Visa en lista över artefakter att överföra** värdet: **build/\*. jar; Build/\*. txt**. Du kan också använda Double-kolon-syntax för att ange en sökväg som ska användas i BLOB-namnet. Om du till exempel vill att jar v7 ska laddas upp med **binärfiler** i BLOB-sökvägen och txt-filerna för att laddas upp med hjälp av **meddelanden** i BLOB-sökvägen använder du följande för **listan över artefakter som du vill överföra** värde: **build/\*. jar: : binärfiler; build/\*. txt:: meddelanden**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Så här skapar du ett build-steg som hämtas från Azure Blob Storage
Följande steg visar hur du konfigurerar ett build-steg för att ladda ned objekt från Azure Blob Storage. Detta är användbart om du vill inkludera objekt i din version, till exempel jar v7 som du behåller i Azure Blob Storage.

1. I avsnittet **build** i jobb konfigurationen klickar du på **Lägg till build-steg** och väljer **Hämta från Azure Blob Storage**.
2. För **lagrings konto namn**väljer du det lagrings konto som ska användas.
3. För **container namn**anger du namnet på den behållare som har de blobbar som du vill ladda ned. Du kan använda miljövariabler.
4. Ange BLOB-namnet för **BLOB-namn**. Du kan använda miljövariabler. Du kan också använda en asterisk som jokertecken när du har angett den första bokstaven i BLOB-namnet. **Project\\** * skulle till exempel ange alla blobbar vars namn börjar med **Project**.
5. Valfritt För **hämtnings Sök väg**anger du sökvägen på den Hudson-dator där du vill hämta filer från Azure Blob Storage. Miljövariabler kan också användas. (Om du inte anger ett värde för **hämtnings Sök vägen**laddas filerna från Azure Blob Storage ned till jobbets arbets yta.)

Om du har fler objekt som du vill ladda ned från Azure Blob Storage kan du skapa ytterligare build-steg.

När du har kört en version kan du kontrol lera utdata från versions historik konsolen eller titta på hämtnings platsen för att se om de blobbar du förväntade dig har laddats ned.

## <a name="components-used-by-the-blob-service"></a>Komponenter som används av Blob Service
Här följer en översikt över Blob Service-komponenterna.

* **Lagringskonto**: All åtkomst till Azure Storage görs via ett lagrings konto. Detta är den högsta nivån i namn området för åtkomst till blobbar. Ett konto kan innehålla ett obegränsat antal behållare, så länge den totala storleken är under 100 TB.
* **Behållare**: En behållare ger gruppering av en uppsättning blobbar. Alla blobar måste vara i en container. Ett konto kan innehålla ett obegränsat antal containrar. En container kan lagra ett obegränsat antal blobar.
* **BLOB**: En fil av valfri typ och storlek. Det finns två typer av blobbar som kan lagras i Azure Storage: block-och sid-blobar. De flesta filer är block-blobar. En enda Block-Blob kan vara upp till 200 GB. I den här självstudien används block blobbar. Page blobbar, en annan Blob-typ, kan vara upp till 1 TB i storlek och är mer effektivt när intervall med byte i en fil ändras ofta. Mer information om blobbar finns i [förstå block-blobbar, bifoga blobbar och sid](https://msdn.microsoft.com/library/azure/ee691964.aspx)-blobar.
* **URL-format**: Blobbar är adresser bara med följande URL-format:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Formatet ovan gäller för det globala Azure-molnet. Om du använder ett annat Azure-moln använder du slut punkten i [Azure Portal](https://portal.azure.com) för att fastställa URL-slutpunkten.)
  
    I formatet ovan `storageaccount` representerar namnet på ditt lagrings konto, `container_name` representerar namnet på din behållare och `blob_name` representerar namnet på din BLOB. I behållarens namn kan du ha flera sökvägar, avgränsade med ett **/** snedstreck. Exempel namnet på containern i den här självstudien var **MyJob**, och **\_$ {build ID}/$ {build\_Number}** användes för den gemensamma virtuella sökvägen, vilket resulterade i att bloben har en URL med följande format:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Nästa steg
* [Möta Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java)
* [Azure Storage Client SDK-referens](http://dl.windowsazure.com/storage/javadoc/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage Teamblogg](https://blogs.msdn.com/b/windowsazurestorage/)

Mer information finns i [Azure för Java-utvecklare](/java/azure).
