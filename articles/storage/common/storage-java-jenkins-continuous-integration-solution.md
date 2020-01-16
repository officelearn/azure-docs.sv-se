---
title: Använda Azure Storage med en Jenkins-lösning för kontinuerlig integrering
description: Den här självstudien visar hur du använder Azure Blob service som en lagrings plats för bygg artefakter som skapats av en Jenkins-lösning för kontinuerlig integrering.
ms.topic: article
ms.author: tarcher
author: tarcher
services: devops
ms.service: storage
custom: jenkins
ms.date: 08/13/2019
ms.subservice: common
ms.openlocfilehash: 0e426dcead5d1f315717fbc19cf7f7bdac62d563
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75970176"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Använda Azure Storage med en Jenkins-lösning för kontinuerlig integrering

Den här artikeln beskriver hur du använder Blob Storage som en lagrings plats för bygg artefakter som skapats av en Jenkins-lösning för kontinuerlig integrering (CI) eller som en källa till nedladdnings bara filer som ska användas i en build-process. Ett av scenarier där du kan hitta den här lösningen är när du kodar i en smidig utvecklings miljö (med Java eller andra språk), versioner körs baserat på kontinuerlig integrering och du behöver en lagrings plats för dina Bygg artefakter, så att Du kan till exempel dela dem med andra medlemmar i organisationen, dina kunder eller underhålla ett arkiv. Ett annat scenario är när ditt build-jobb själva kräver andra filer, till exempel beroenden som ska laddas ned som en del av den inmatade versionen.

I den här självstudien kommer du att använda Azure Storage plugin-programmet för Jenkins-CI som gjorts tillgängligt av Microsoft.

## <a name="jenkins-overview"></a>Översikt över Jenkins
Jenkins möjliggör kontinuerlig integrering av ett program projekt genom att göra det möjligt för utvecklare att enkelt integrera sina kod ändringar och skapa och ofta skapa byggen automatiskt och därmed öka produktiviteten hos utvecklarna. Versioner av versioner och build-artefakter kan överföras till olika lagrings platser. Den här artikeln visar hur du använder Azure Blob Storage som lagrings plats för bygg artefakterna. Det visar också hur du hämtar beroenden från Azure Blob Storage.

Mer information om Jenkins finns på [Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Fördelar med att använda Blob Service
Fördelarna med att använda Blob Service som värd för dina Agile-utvecklings artefakter är:

* Hög tillgänglighet för dina build-artefakter och/eller nedladdnings bara beroenden.
* Prestanda när din Jenkins CI-lösning laddar upp dina build-artefakter.
* Prestanda när dina kunder och partners laddar ned dina build-artefakter.
* Kontroll över användar åtkomst principer, med ett val mellan anonym åtkomst, utgångs-baserade signaturer för delad åtkomst, privat åtkomst osv.

## <a name="prerequisites"></a>Krav
* En lösning för kontinuerlig integrering med Jenkins.
  
    Om du för närvarande inte har en Jenkins CI-lösning kan du köra en Jenkins CI-lösning med hjälp av följande metod:
  
  1. Hämta Jenkins. War från <https://jenkins-ci.org>på en Java-aktiverad dator.
  2. Kör följande i en kommando tolk som öppnas i mappen som innehåller Jenkins. War:
     
      `java -jar jenkins.war`

  3. `http://localhost:8080/` öppna Jenkins i webbläsaren och öppna instrument panelen för som du kommer att använda för att installera och konfigurera Azure Storage-plugin-programmet.
     
      Även om en typisk Jenkins CI-lösning skulle konfigureras att köras som en tjänst, räcker det att köra Jenkins-kriget på kommando raden för den här självstudien.
* Ett Azure-konto. Du kan registrera dig för ett Azure-konto på <https://www.azure.com>.
* Ett Azure-lagringskonto. Om du inte redan har ett lagrings konto kan du skapa ett med hjälp av stegen i [skapa ett lagrings konto](../common/storage-account-create.md).
* Du rekommenderas att använda Jenkins CI-lösningen men det är inte obligatoriskt, eftersom följande innehåll kommer att använda ett grundläggande exempel för att visa de steg som krävs när du använder Blob Service som lagrings plats för Jenkins CI build-artefakter.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Använda Blob Service med Jenkins CI
Om du vill använda Blob Service med Jenkins måste du installera Azure Storage-plugin-programmet, konfigurera plugin-programmet så att det använder ditt lagrings konto och sedan skapa en åtgärd efter build som laddar upp dina build-artefakter till ditt lagrings konto. De här stegen beskrivs i följande avsnitt.

## <a name="how-to-install-the-azure-storage-plugin"></a>Så här installerar du Azure Storage-plugin-programmet
1. I Jenkins-instrumentpanelen väljer du **Hantera Jenkins**.
2. På sidan **Hantera Jenkins** väljer du **Hantera plugin**-program.
3. Välj fliken **Tillgängliga**.
4. I avsnittet **artefakt uppladdning** kontrollerar du **Microsoft Azure Storage plugin-programmet**.
5. Välj antingen **Installera utan omstart** eller **Ladda ned nu och installera efter omstart**.
6. Starta om Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Så här konfigurerar du Azure Storage-plugin-programmet så att det använder ditt lagrings konto
1. I Jenkins-instrumentpanelen väljer du **Hantera Jenkins**.
2. På sidan **Hantera Jenkins** väljer du **Konfigurera system**.
3. I avsnittet **Microsoft Azure Storage konto konfiguration** :
   1. Ange namnet på ditt lagrings konto, som du kan hämta från [Azure Portal](https://portal.azure.com).
   2. Ange din lagrings konto nyckel, som också kan hämtas från [Azure Portal](https://portal.azure.com).
   3. Använd standardvärdet för **BLOB-tjänstens slut punkts-URL** om du använder det globala Azure-molnet. Om du använder ett annat Azure-moln använder du slut punkten som anges i [Azure Portal](https://portal.azure.com) för ditt lagrings konto. 
   4. Välj **Verifiera autentiseringsuppgifter för lagring** för att verifiera ditt lagrings konto. 
   5. Valfritt Om du har ytterligare lagrings konton som du vill ska vara tillgängliga för Jenkins-CI väljer du **Lägg till fler lagrings konton**.
   6. Spara inställningarna genom att välja **Spara** .

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Så här skapar du en åtgärd efter bygge som laddar upp dina build-artefakter till ditt lagrings konto
I instruktions syfte måste du först skapa ett jobb som ska skapa flera filer och sedan lägga till i åtgärden efter build för att överföra filerna till ditt lagrings konto.

1. I Jenkins-instrumentpanelen väljer du **nytt objekt**.
2. Namnge jobbet **MyJob**, Välj **Bygg ett program med kostnads fri stil**och välj sedan **OK**.
3. I avsnittet **build** i jobb konfigurationen väljer du **Lägg till build-steg** och väljer **kör Windows batch-kommando**.
4. I **kommando**använder du följande kommandon:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. I avsnittet **efter build-åtgärder** i jobb konfigurationen väljer du **Lägg till åtgärd efter skapande** och väljer **Ladda upp artefakter till Azure Blob Storage**.
6. För **lagrings konto namn**väljer du det lagrings konto som ska användas.
7. Ange behållar namnet för **container namn**. (Behållaren kommer att skapas om den inte redan finns när bygg artefakterna laddas upp.) Du kan använda miljövariabler, så i det här exemplet anger du `${JOB_NAME}` som behållar namn.
   
    **Tips**
   
    Under **kommando** avsnittet där du har angett ett skript för **kommando kommandot kör Windows** , är en länk till miljövariablerna som identifieras av Jenkins. Välj den länken om du vill lära dig om miljö variabel namn och beskrivningar. Miljövariabler som innehåller specialtecken, till exempel miljövariabeln **BUILD_URL** , tillåts inte som ett behållar namn eller en gemensam virtuell sökväg.
8. Välj **gör ny behållare offentlig som standard** i det här exemplet. (Om du vill använda en privat behållare måste du skapa en signatur för delad åtkomst för att tillåta åtkomst, vilket ligger utanför den här artikelns omfattning. Du kan läsa mer om signaturer för delad åtkomst i [använda signaturer för delad åtkomst (SAS)](storage-sas-overview.md).)
9. Valfritt Välj **Rensa behållare innan** du överför om du vill att behållaren ska rensas efter innehåll innan Bygg artefakter laddas upp (lämna den omarkerad om du inte vill rensa innehållet i behållaren).
10. Ange `text/*.txt`för **lista över artefakter som ska överföras**.
11. I den här självstudien anger du `${BUILD\_ID}/${BUILD\_NUMBER}`för **vanliga virtuella Sök vägar för uppladdade artefakter**.
12. Spara inställningarna genom att välja **Spara** .
13. I Jenkins-instrumentpanelen väljer du **Skapa nu** för att köra **MyJob**. Granska konsolens utdata för status. Status meddelanden för Azure Storage tas med i konsolens utdata när åtgärden efter build börjar ladda upp Bygg artefakter.
14. När jobbet har slutförts kan du undersöka build-artefakterna genom att öppna den offentliga blobben.
    1. Logga in på [Azure-portalen](https://portal.azure.com).
    2. Välj **Lagring**.
    3. Välj det lagrings konto namn som du använde för Jenkins.
    4. Välj **behållare**.
    5. Välj behållaren med namnet **myjob**, som är den gemena versionen av det jobb namn som du tilldelade när du skapade Jenkins-jobbet. Behållar namn och blob-namn är gemener (och Skift läges känsliga) i Azure Storage. I listan över blobbar för behållaren med namnet **myjob**bör du se **Hej. txt** och **date. txt**. Kopiera URL: en för något av dessa objekt och öppna den i webbläsaren. Text filen som överfördes som en Bygg artefakt visas.

Endast en åtgärd efter build-åtgärd som laddar upp artefakter till Azure Blob Storage kan skapas per jobb. Åtgärden för att överföra artefakter till Azure Blob Storage kan ange olika filer (inklusive jokertecken) och sökvägar till filer i **listan över artefakter som ska överföras** med hjälp av semikolon som avgränsare. Om din Jenkins-version t. ex **. skapar jar** -filer och txt-filer i arbets ytans installationsmapp och du vill överföra både till Azure Blob Storage, använder du följande värde för **listan över artefakter som ska överföras** : `build/\*.jar;build/\*.txt`. Du kan också använda Double-kolon-syntax för att ange en sökväg som ska användas i BLOB-namnet. Om du till exempel vill att jar v7 ska laddas upp med **binärfiler** i BLOB-sökvägen och txt-filerna för att laddas upp med **meddelanden** i BLOB-sökvägen använder du följande värde för **listan över artefakter som ska överföras** : `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Så här skapar du ett build-steg som hämtas från Azure Blob Storage
Följande steg illustrerar hur du konfigurerar ett build-steg för att ladda ned objekt från Azure Blob Storage, vilket är användbart om du vill inkludera objekt i din version. Ett exempel på att använda det här mönstret är jar v7 som du kanske vill spara i Azure Blob Storage.

1. I avsnittet **build** i jobb konfigurationen väljer du **Lägg till build-steg** och väljer **Hämta från Azure Blob Storage**.
2. För **lagrings konto namn**väljer du det lagrings konto som ska användas.
3. För **container namn**anger du namnet på den behållare som har de blobbar som du vill ladda ned. Du kan använda miljövariabler.
4. Ange BLOB-namnet för **BLOB-namn**. Du kan använda miljövariabler. Du kan också använda en asterisk som jokertecken när du har angett den första bokstaven i BLOB-namnet. **Project\\** * skulle till exempel ange alla blobbar vars namn börjar med **Project**.
5. Valfritt För **hämtnings Sök väg**anger du sökvägen på den Jenkins-dator där du vill hämta filer från Azure Blob Storage. Miljövariabler kan också användas. (Om du inte anger ett värde för **hämtnings Sök vägen**laddas filerna från Azure Blob Storage ned till jobbets arbets yta.)

Om du har fler objekt som du vill ladda ned från Azure Blob Storage kan du skapa ytterligare build-steg.

När du har kört en version kan du kontrol lera utdata från versions historik konsolen eller titta på hämtnings platsen för att se om de blobbar du förväntade dig har laddats ned.  

## <a name="components-used-by-the-blob-service"></a>Komponenter som används av Blob Service
Det här avsnittet innehåller en översikt över Blob Service-komponenterna.

* **Lagringskonto**: All åtkomst till Azure Storage görs genom ett lagringskonto. Ett lagrings konto är den högsta nivån i namn området för åtkomst till blobbar. Ett konto kan innehålla ett obegränsat antal behållare, så länge den totala storleken är under 100 TB.
* **Behållare**: en behållare ger en gruppering av en uppsättning blobbar. Alla blobar måste vara i en container. Ett konto kan innehålla ett obegränsat antal containrar. En container kan lagra ett obegränsat antal blobar.
* **BLOB**: en fil av valfri typ och storlek. Det finns två typer av blobbar som kan lagras i Azure Storage: block-och sid-blobar. De flesta filer är block-blobar. En enda Block-Blob kan vara upp till 200 GB. I den här självstudien används block blobbar. Page blobbar, en annan Blob-typ, kan vara upp till 1 TB i storlek och är mer effektivt när intervall med byte i en fil ändras ofta. Mer information om blobbar finns i [förstå block-blobbar, bifoga blobbar och sid-blobar](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **URL-format**: blobbar är adresser bara med följande URL-format:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Formatet ovan gäller för det globala Azure-molnet. Om du använder ett annat Azure-moln använder du slut punkten i [Azure Portal](https://portal.azure.com) för att fastställa URL-slutpunkten.)
  
    I formatet ovan representerar `storageaccount` namnet på ditt lagrings konto, `container_name` representerar namnet på din behållare och `blob_name` representerar namnet på din BLOB. I behållarens namn kan du ha flera sökvägar, avgränsade med ett snedstreck, **/** . Exempel namnet på den behållare som används för den här självstudien var **MyJob**, och **$ {build\_ID}/$ {build\_Number}** användes för den gemensamma virtuella sökvägen, vilket resulterade i att bloben har en URL med följande format:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg
* [Möta Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java)
* [Azure Storage Client SDK-referens](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage Teamblogg](https://blogs.msdn.com/b/windowsazurestorage/)

Mer information finns i [Azure för Java-utvecklare](/java/azure).
