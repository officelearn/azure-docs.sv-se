---
title: Hantera Azure Data Lake Store-resurser i Azure Storage Explorer
description: "Ger användare möjlighet att komma åt och hantera ADLS-data och -resurser i Azure Storage Explorer"
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: 
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 4c69acebceac6719cf3f12ff0a93879f1b5a8943
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-data-lake-store-resources-with-storage-explorer-preview"></a>Hantera Azure Data Lake Store-resurser med Storage Explorer (förhandsversion)

[Azure Data Lake Store (ADLS)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview) är en tjänst som används för att lagra stora mängder ostrukturerade data, som text eller binära data. Användare kan komma åt data var de än är via HTTP eller HTTPS. ADLS i Azure Storage Explorer ger användare möjlighet att komma åt och hantera ADLS-data och -resurser med andra Azure-entiteter som blob och kö. Användare kan nu använda samma verktyg för att hantera olika Azure-entiteter på ett och samma ställe.

En annan fördel är att användare inte behöver ha prenumerationsbehörighet för att hantera ADLS-data. I Storage Explorer kan användare koppla ADLS-sökvägen till noden ”Lokal och ansluten” så länge som andra tilldelar behörigheten.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du behöver följande för att slutföra stegen i den här artikeln:

*   En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial).
*   Ett Azure Data Lake Store-konto. Mer information om hur du skapar ett finns i [Kom igång med Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="installation"></a>Installation

Installera den senaste versionen av Azure Storage Explorer här: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Nu finns det versioner för Windows, Linux och MAC.

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

1. När du har installerat **Azure Storage Explorer** ska du klicka på **plugin**-ikonen till vänster, som visas i följande bild.
       
   ![Plugin-ikon](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Välj **Lägg till ett Azure-konto** och klicka sedan på **Logga in**.

   ![Ansluta till Azure-prenumerationen](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. Välj **Logga in** i dialogrutan **Azure-inloggning** och ange dina autentiseringsuppgifter för Azure.

    ![Logga in](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Välj din prenumeration i listan och klicka sedan på **Använd**.

    ![Använd](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    Explorer-fönstret uppdateras och visar konton i den valda prenumerationen.

    ![Kontolista](./media/data-lake-store-in-storage-explorer/account-list.png)

    Du har anslutit din **Azure Data Lake Store** till din Azure-prenumeration.

## <a name="connect-to-data-lake-store"></a>Ansluta till Data Lake Store
Om du vill komma åt resurser som inte finns i din prenumeration. Men andra ger dig behörighet att komma åt resursernas URI. I det här fallet kan du ansluta till Data Lake Store med denna URI när du loggat in. Se följande steg.
1. Öppna Lagringsutforskaren (förhandsversion).
2. I det vänstra fönstret expanderar du **Lokala och ansluten**.
3. Högerklicka på **Data Lake Store** och från snabbmenyn väljer du **Anslut till Data Lake Store...**.

      ![snabbmenyn anslut till Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Ange den URI du fått, verktyget går till den URL du angett.

      ![dialogen anslut till Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![resultat för ansluta till Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Visa innehållet i ett Azure Data Lake Store-konto
Ett Azure Data Lake Store-kontos resurser innehåller mappar och filer.

Följande steg illustrerar hur du kan visa innehållet i ett ADLS-konto i Storage Explorer (förhandsversion):

1. Öppna Lagringsutforskaren (förhandsversion).
2. Expandera prenumerationen som innehåller Azure Data Lake Store-kontot som du vill visa i den vänstra rutan.
3. Expandera **Data Lake Store**.
4. Högerklicka på den Azure Data Lake Store-kontonod som du vill visa och välj **Öppna** från snabbmenyn. Du kan även dubbelklicka på ADLS-kontot du vill öppna. 
5. Huvudfönstret visar ADLS-kontots innehåll.

     ![huvudfönster](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="azure-data-lake-store-resources-management"></a>Resurshantering för Azure Data Lake Store

Du kan hantera Azure Data Lake Store-resurser med följande åtgärder:
*   Navigera bland ADLS-resurser i flera ADL-konton.  
*   Använda en anslutningssträng för att ansluta och hantera ADLS direkt. 
*   Visa ADLS-resurser som delas av andra via ACL under Lokal och Ansluten.
*   Utför CRUD-åtgärder på filer eller mappar: stöd för rekursiva mappar och val av flera filer. 
*   Dra, släpp och lägg till mapp i snabbåtkomst och nyligen använda platser på ett sätt som liknar användning av utforskaren. 
*   Kopiera och öppna ADL-hyperlänk med Storage Explorer med ett klick. 
*   Visa aktivitetslogg i det undre högra fönstret för att hålla reda på aktivitetsstatusen.
*   Visa mappstatistik och filegenskaper.

## <a name="manage-resources-in-azure-storage-explorer"></a>Hantera resurser i Azure Storage Explorer
När du skapat ett Azure Data Lake Store-konto kan du ladda upp mappar och filer, ladda ned och öppna resurser på din lokala dator. Du kan fästa på snabbåtkomst, skapa en ny mapp, kopiera URL och välja alla. Du kan dessutom kopiera, klistra in, ta bort, visa mappstatistik och uppdatera.

Följande anvisningar visar hur du hanterar resurser i ett Azure Data Lake Store-konto. Gör följande beroende på vilken aktivitet du vill utföra:
   * **Överföra filer**

     1. Gå till verktygsfältet i huvudfönstret och välj **Ladda upp**, och sedan **Ladda upp filer** i den nedrullningsbara menyn.

        ![Menyn Ladda upp filer](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

     2. I dialogrutan **Välj filer att ladda upp** väljer du filerna du vill ladda upp.

        ![Dialogrutan Ladda upp mapp](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

     3. Välj **Öppna** för att börja ladda upp.
   * **Ladda upp en mapp**

     1. Gå till verktygsfältet i huvudfönstret och klicka på **Ladda upp**, och sedan på **Ladda upp mapp...** i den nedrullningsbara menyn.

        ![Menyn för mappöverföring](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     2. I dialogrutan **Välj mapp att ladda upp** väljer du mappen du vill ladda upp.

        ![Dialogrutan Ladda upp mapp](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

     3. Välj **Välj mapp** och börja ladda upp mappar.

        ![Dialogrutan Ladda upp mapp](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

      > [!NOTE] 
          > Du kan dra filerna och mapparna på den lokala datorn för att ladda upp. 
       
   * **Ladda ned mappar eller filer till den lokala datorn**

     1. Markera de mappar eller filer du vill ladda ned.
     2. Gå till verktygsfältet i huvudfönstret och klicka på **Hämta**.
     3. I dialogrutan **Välj mapp där nedladdade filer ska sparas** anger du den plats där nedladdade filer eller mappar ska sparas. Ange också ett namn.
     4. Välj **Spara**.
   * **Öppna mapp eller fil från den lokala datorn**

     1. Markera den mapp eller fil som du vill öppna.
     2. I huvudfönstrets verktygsfält väljer du **Öppna** eller så högerklickar du på den valda mappen eller filen och klickar på **Öppna** från snabbmenyn.
     3. Filen laddas ned och öppnas med det program som är kopplat till filens underliggande filtyp. Eller också öppnas mappen i huvudfönstret.

        ![öppna fil](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

   * **Kopiera mappar och filer till Urklipp**

     1. Markera de mappar eller filer du vill kopiera.
     2. I huvudfönstrets verktygsfält väljer du **Kopiera** eller så högerklickar du på den valda mappen eller filen och klickar på **Kopiera** från snabbmenyn.
     3. Navigera till ett annat ADLS-konto i den vänstra rutan och dubbelklicka på det för att visa det i huvudfönstret.
     4. Gå till verktygsfältet i huvudfönstret och klicka på **Klistra in** för att skapa en kopia. Eller klicka på **Klistra in** i snabbmenyn på destinationen.

        ![kopiera och klistra in mapp eller fil](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

      > [!NOTE] 
          > + Kopiera och klistra in mellan olika lagringstyper **stöds inte**. Det går att kopiera ADLS-mappar eller -filer och klistra in i ett annat ADLS-konto. Det går **inte** att kopiera ADLS-mappar eller -filer och klistra in dem till blob-lagring eller tvärtom. 
          > + Kopieringen och inklistringen fungerar genom att ladda ned mapparna eller filerna lokalt och sedan ladda upp dem till destinationen. Verktyget **utför inte** åtgärden i bakgrunden. Det är långsamt att kopiera och klistra in stora filer. Optimering av högpresterande kopiering och flytt av filer pågår.
   * **Ta bort mappar eller filer**

     1. Markera de mappar eller filer du vill ta bort.
     2. I huvudfönstrets verktygsfält väljer du **Ta bort** eller så högerklickar du på den valda mappen eller filen och klickar på **Ta bort** från snabbmenyn.
     3. Välj **Ja** i bekräftelsedialogen.

        ![kopiera och klistra in mapp eller fil](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

   * **Fäst i Snabbåtkomst**

     1. Markera den mapp som du vill fästa.
     2. Gå till verktygsfältet i huvudfönstret och välj **Fäst i Snabbåtkomst**.
     3. I det vänstra fönstret ser du att den valda mappen lagts till i noden **Snabbåtkomst**.

        ![fäst i snabbåtkomst](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)
     4. När du skapat snabbåtkomsten är det lätt att komma åt resurserna.
   * **Djupa länkar**
     1. Om du har en URL behöver du bara ange webbadressen i adressfältet i **Utforskaren** eller en webbläsare.
     2. **Storage Explorer.exe** öppnas automatiskt och går till den URL du angett.

        ![djup länk i utforskaren](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>Nästa steg
* Visa [ viktig information och videor för den senaste Storage Explorer-versionen (förhandsutgåva)](http://www.storageexplorer.com).
* Lär dig hantera [Manage Azure Cosmos DB i Azure Storage Explorer](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer)
* Läs mer om Storage Explorer i [Kom igång med Storage Explorer (förhandsversion)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* Kom igång med Azure Data Lake Store (ADLS)[Översikt över Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview)
* Titta på följande videoklipp om du vill se [hur du använder Azure Cosmos DB i Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)
