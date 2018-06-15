---
title: Med hjälp av Azure Storage med en Jenkins kontinuerlig integrationslösning | Microsoft Docs
description: Den här kursen visar hur du använder Azure blob-tjänsten medan en lagringsplats för skapar artefakter som skapats av en lösning för kontinuerlig integrering av Jenkins.
services: storage
documentationcenter: java
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: f4e5ca75-f6cb-4f74-981b-2aa06bb8de45
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 02/28/2017
ms.author: seguler
ms.openlocfilehash: 174ac449e803ed5327468a38ea7264cb9923a877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874138"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Använda Azure Storage med en Jenkins-baserad CI-lösning
## <a name="overview"></a>Översikt
Följande information visar hur du använder Blob storage som en databas av build artefakter som skapats av en lösning för Jenkins kontinuerlig Integration (KO) eller som en källa för nedladdningsbara filer som ska användas i en build-process. En av de scenarier där det skulle vara praktiskt är när du kodning i en flexibel utvecklingsmiljö (med Java eller andra språk), versioner som körs baserat på kontinuerlig integration och du behöver en lagringsplats för build-artefakter, så att du kan, till exempel dela dem med andra organisationsmedlemmar i, dina kunder eller skapa ett arkiv. Ett annat scenario är när din build-jobbet kräver andra filer, till exempel beroendena om du vill hämta som en del av build som indata.

I den här självstudiekursen kommer du använder Azure Storage plugin-programmet för Jenkins CI som gjorts tillgängliga av Microsoft.

## <a name="overview-of-jenkins"></a>Översikt över Jenkins
Jenkins aktiverar kontinuerlig integration av programvara projektet genom att låta utvecklare lätt kan integrera sina kodändringar och har producerat versioner automatiskt och ofta, vilket ökar produktiviteten för utvecklare. Versioner är en ny version och build artefakter kan överföras till olika databaser. Det här avsnittet visar hur du använder Azure blob storage som databasen av build-artefakter. Den visas också hur du hämtar beroenden från Azure blob storage.

Mer information om Jenkins kan hittas på [uppfyller Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Fördelarna med att använda Blob-tjänsten
Fördelarna med att använda Blob-tjänsten som värd för din flexibel utveckling build artefakter inkluderar:

* Hög tillgänglighet av build artefakter och/eller nedladdningsbara beroenden.
* Prestanda när Jenkins CI-lösningen överför build-artefakter.
* Prestanda när kunder och partners hämtar build-artefakter.
* Kontroll över principer för åtkomst, välja mellan anonym åtkomst, förfallodatum-baserad delad åtkomst signatur åtkomst, privat åtkomst, osv.

## <a name="prerequisites"></a>Krav
Du behöver följande för att använda Blob-tjänsten med Jenkins CI-lösningen:

* Lösning för Jenkins kontinuerlig integrering.
  
    Du kan köra en Jenkins CI-lösning med hjälp av följande metod om du för närvarande inte har en Jenkins CI-lösning:
  
  1. På en dator i Java-aktiverade hämta jenkins.war från <http://jenkins-ci.org>.
  2. I Kommandotolken som öppnas till den mapp som innehåller jenkins.war, kör du:
     
      `java -jar jenkins.war`

  3. Öppna i din webbläsare `http://localhost:8080/`. Jenkins instrumentpanelen, som du använder för att installera och konfigurera Azure Storage plugin-programmet öppnas.
     
      När en typisk Jenkins CI-lösning skulle ställas in att köras som en tjänst som körs på Jenkins war på kommandoraden är tillräckliga för den här kursen.
* Ett Azure-konto. Du kan registrera dig för ett Azure-konto på <http://www.azure.com>.
* Ett Azure-lagringskonto. Om du inte redan har ett lagringskonto, kan du skapa ett med hjälp av stegen i [skapa ett Lagringskonto](../common/storage-create-storage-account.md#create-a-storage-account).
* Om du är bekant med Jenkins CI-lösningen rekommenderas men krävs inte, eftersom följande innehåll kommer att använda ett grundläggande exempel visas de steg som krävs när du använder Blob-tjänsten som en lagringsplats för Jenkins CI skapa artefakter.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Hur du använder Blob-tjänsten med Jenkins CI
Om du vill använda Blob-tjänsten med Jenkins behöver du installera plugin-program för Azure Storage, konfigurera plugin-programmet för att använda ditt lagringskonto och sedan skapa en efter build-åtgärd som överför build-artefakter till ditt lagringskonto. Dessa steg beskrivs i följande avsnitt.

## <a name="how-to-install-the-azure-storage-plugin"></a>Så här installerar du plugin-programmet för Azure Storage
1. I instrumentpanelen Jenkins klickar du på **hantera Jenkins**.
2. I den **hantera Jenkins** klickar du på **hantera plugin-program**.
3. Klicka på den **tillgänglig** fliken.
4. I den **artefakt Uploaders** avsnitt, kontrollera **plugin-program för Microsoft Azure Storage**.
5. Klicka på antingen **installera utan att starta om** eller **nu hämta och installera efter omstart**.
6. Starta om Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Så här konfigurerar du plugin-programmet för Azure Storage för att använda ditt lagringskonto
1. I instrumentpanelen Jenkins klickar du på **hantera Jenkins**.
2. I den **hantera Jenkins** klickar du på **konfigurera systemet**.
3. I den **Microsoft Azure Storage-kontokonfigurationen** avsnitt:
   1. Ange namnet på ditt lagringskonto, som du kan hämta från den [Azure Portal](https://portal.azure.com).
   2. Ange din lagringskontonyckel, även kan erhållas från den [Azure Portal](https://portal.azure.com).
   3. Använd standardvärdet för **slutpunkts-URL för Blob-tjänsten** om du använder offentliga Azure-molnet. Om du använder en annan Azure-molnet, använda slutpunkten som anges i den [Azure Portal](https://portal.azure.com) för ditt lagringskonto. 
   4. Klicka på **verifiera autentiseringsuppgifterna för lagring** att verifiera ditt lagringskonto. 
   5. [Valfritt] Om du har ytterligare storage-konton som du vill få tillgång till din Jenkins CI klickar du på **lägga till flera Lagringskonton**.
   6. Klicka på **spara** att spara dina inställningar.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Så här skapar du en efter build-åtgärd som överför build-artefakter till ditt lagringskonto
För instruktioner, först måste du skapa ett jobb som skapar flera filer, och Lägg sedan till i efter build-åtgärden för att överföra filer till ditt lagringskonto.

1. I instrumentpanelen för Jenkins klickar du på **Nytt objekt**.
2. Namnge jobbet **MyJob**, klickar du på **ett ledigt format programvara projekt**, och klicka sedan på **OK**.
3. I den **skapa** avsnitt av jobb-konfigurationen klickar du på **Lägg till build steg** och välj **köra Windows batch-kommandot**.
4. I **kommandot**, Använd följande kommandon:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. I den **efter build åtgärder** avsnitt av jobb-konfigurationen klickar du på **Lägg till efter build-åtgärd** och välj **överför artefakter till Azure Blob storage**.
6. För **lagringskontonamnet**, Välj lagringskontot för att använda.
7. För **behållarnamn**, ange behållarnamnet på. (Behållaren skapas om den inte redan finns när build-artefakter överförs.) Du kan använda miljövariabler, så det här exemplet anger **${JOB_NAME}** som behållarens namn.
   
    **Tips**
   
    Under den **kommandot** avsnitt som du angav ett skript för **köra Windows batch-kommandot** är en länk till miljövariabler som identifieras av Jenkins. Klicka på länken Läs miljövariabelnamn och beskrivningar. Observera att miljövariabler som innehåller särskilda tecken, som den **BUILD_URL** miljövariabeln, tillåts inte som en behållare eller vanliga virtuella sökvägen.
8. Klicka på **offentliggöra ny behållare som standard** för det här exemplet. (Om du vill använda en privata behållare du behöver skapa en signatur för delad åtkomst för att tillåta åtkomst. Det är utanför omfattningen för det här avsnittet. Du kan lära dig mer om signaturer för delad åtkomst på [med delad åtkomst signaturer (SAS)](../storage-dotnet-shared-access-signature-part-1.md).)
9. [Valfritt] Klicka på **ren behållare innan du laddar upp** om du vill att behållaren ska rensas innehåll innan build artefakter överförs (lämna den oskyddad om du inte vill att rensa behållarens innehåll).
10. För **lista artefakter att överföra**, ange  **text /*.txt**.
11. För **vanliga virtuella sökvägen för överförda artefakter**, för den här kursen ange **${skapa\_ID} / ${skapa\_nummer}**.
12. Klicka på **spara** att spara dina inställningar.
13. I instrumentpanelen Jenkins klickar du på **skapa nu** att köra **MyJob**. Granska konsolen utdata för status. Statusmeddelanden för Azure storage inkluderas i konsolens utdata när åtgärden efter build börjar överför build-artefakter.
14. Du kan undersöka build-artefakter genom att öppna den offentliga blobben vid slutförande av jobbet.
    1. Logga in på den [Azure-portalen](https://portal.azure.com).
    2. Klicka på **lagring**.
    3. Klicka på namnet på lagringskontot som du använde för Jenkins.
    4. Klicka på **behållare**.
    5. Klicka på den behållare som heter **myjob**, vilket är en gemen version av namnet på det jobb som du tilldelade när du skapade Jenkins jobbet. Namn på behållare och blobbnamnen är gemener (och skiftlägeskänsligt) i Azure-lagring. I listan över blobbar för behållaren med namnet **myjob** bör du se **hello.txt** och **date.txt**. Kopiera Webbadressen för något av dessa objekt och öppna den i webbläsaren. Textfilen som överfördes visas som en build-artefakt.

Endast en efter build-åtgärd som överför artefakter till Azure blob storage kan skapas per jobb. Observera att den enda efter build åtgärden att överföra artefakter till Azure blob storage kan ange olika filer (inklusive jokertecken) och sökvägar för filer i **lista artefakter att överföra** med ett semikolon som avgränsare. Producerar till exempel om din Jenkins skapa JAR-filer och TXT-filer i din arbetsyta **skapa** och du vill överföra båda till Azure blob storage, använder du följande för den **lista artefakter att överföra** värde: **skapa /\*.jar; skapa /\*.txt**. Du kan också använda dubbla kolon syntax för att ange en sökväg som ska användas i blob-namnet. Om du vill burkar som får överföras med till exempel **binärfiler** i blob-sökväg och TXT-filer som får överföras med **meddelanden** i blob-sökväg, använder du följande för den **lista artefakter att överföra** värde: **skapa /\*. jar::binaries; skapa /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Så här skapar du ett build-steg som kan hämtas från Azure blob storage
Följande steg visar hur du konfigurerar ett build-steg för att hämta objekt från Azure blob storage. Detta kan vara användbart om du vill ta med objekt i din build exempelvis burkar som du behåller i Azure blob storage.

1. I den **skapa** avsnitt av jobb-konfigurationen klickar du på **Lägg till build steg** och välj **ladda ned från Azure Blob storage**.
2. För **lagringskontonamnet**, Välj lagringskontot för att använda.
3. För **behållarnamn**, ange namnet på behållaren som innehåller blob som du vill hämta. Du kan använda miljövariabler.
4. För **blobbnamnet**, ange blobbnamnet på. Du kan använda miljövariabler. Du kan också använda en asterisk som jokertecken när du har angett den första bokstaven i blob-namnet. Till exempel **projekt\***  anger alla BLOB vars namn börjar med **projekt**.
5. [Valfritt] För **hämtningssökvägen**, ange sökvägen på den Jenkins datorn där du vill hämta filer från Azure blob storage. Miljövariabler kan också användas. (Om du inte anger ett värde för **hämtningssökvägen**, hämtas filer från Azure blob storage till jobbets arbetsyta.)

Om du har ytterligare objekt som du vill hämta från Azure blob storage kan skapa du ytterligare build steg.

När du kör en version måste du söka version historia konsolens utdata eller titta på din nedladdningsplatsen för att se om BLOB som du förväntade dig som hämtats.  

## <a name="components-used-by-the-blob-service"></a>Komponenter som används av Blob-tjänsten
Följande innehåller en översikt över tjänstkomponenter Blob.

* **Lagringskonto**: All åtkomst till Azure Storage görs genom ett lagringskonto. Det här är den högsta nivån av namnområdet för åtkomst till blobbar. Ett konto kan innehålla ett obegränsat antal behållare, så länge som deras sammanlagda storlek är under 100TB.
* **Behållaren**: en behållare grupperar en uppsättning blobbar. Alla blobbar måste vara i en behållare. Ett konto kan innehålla ett obegränsat antal behållare. En behållare kan lagra ett obegränsat antal blobbar.
* **BLOB**: en fil av valfri typ och storlek. Det finns två typer av blobbar som kan lagras i Azure Storage: block- och sidblobbar. De flesta filer är blockblobbar. En enda blockblobb kan vara upp till 200GB i storlek. Den här kursen använder blockblobar. Sidblobar, en annan blob-datatyp kan vara upp till 1TB i storlek och är mer effektivt när adressintervall byte i en fil ändras ofta. Mer information om BLOB finns [förstå Blockblobbar, Tilläggsblobbar och Sidblobbar](http://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Webbadressformatet**: Blobbar är adresserbara via följande URL-format:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (Formatet ovan gäller offentliga Azure-molnet. Om du använder en annan Azure-molnet använder slutpunkten inom den [Azure Portal](https://portal.azure.com) att bestämma URL-slutpunkt.)
  
    I formatet ovan `storageaccount` representerar namnet på ditt lagringskonto `container_name` representerar namnet på din behållare och `blob_name` representerar namnet på din blob respektive. Du kan ha flera sökvägar, avgränsade med ett snedstreck inom behållarnamn,  **/** . Exempel behållarens namn i den här självstudiekursen har **MyJob**, och **${skapa\_ID} / ${skapa\_nummer}** användes för vanliga virtuell sökväg, vilket resulterar i blob med URL: en följande format:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="next-steps"></a>Nästa steg
* [Uppfyller Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
* [Azure Storage SDK för Java](https://github.com/azure/azure-storage-java)
* [Referens för Azure Storage Client SDK](http://dl.windowsazure.com/storage/javadoc/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage Teamblogg](http://blogs.msdn.com/b/windowsazurestorage/)

Mer information finns i [Azure för Java-utvecklare](/java/azure).