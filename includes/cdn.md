# <a name="using-cdn-for-azure"></a>Med hjälp av CDN för Azure
Den Azure innehåll innehållsleveransnätverk (CDN) ger utvecklare en global för att leverera innehåll med hög bandbredd genom cachelagring av blobbar och compute-instanser på fysiska noder i USA, Europa, Asien, Australien och Sydamerika statiskt innehåll. En aktuell lista över CDN-nodplatser finns [Azure CDN-Nodplatser].

Den här uppgiften innehåller följande steg:

* [Steg 1: Skapa ett lagringskonto](#Step1)
* [Steg 2: Skapa en ny CDN-slutpunkt för ditt lagringskonto](#Step2)
* [Steg 3: Komma åt din CDN-innehåll](#Step3)
* [Steg 4: Ta bort CDN-innehåll](#Step4)

Fördelarna med att använda CDN kan cachelagra Azure data omfattar:

* Bättre prestanda och användarens upplevelse för slutanvändare som är långt från en innehållskälla och använder program där flera internet resor' krävs för att läsa in innehåll
* Distribuerade storskaliga bättre hantera omedelbar hög belastning, exempelvis i början av en händelse, till exempel en produktlanseringen

Befintliga CDN-kunder kan nu använda Azure CDN i den [klassiska Azure-portalen]. CDN är en funktion i tillägg till din prenumeration och har en separat [faktureringsavtal].

<a id="Step1"> </a>

<h2>Steg 1: Skapa ett lagringskonto</h2>

Använd följande procedur för att skapa ett nytt lagringskonto för en Azure-prenumeration. Ett lagringskonto ger åtkomst till Azure storage-tjänster. Lagringskontot representerar den högsta nivån av namnområdet för åtkomst till alla komponenter för Azure storage-tjänsten: Blob-tjänster, Queue-tjänster och tabellen tjänster. Mer information om Azure-lagringstjänster finns [med hjälp av Azure Storage-tjänster](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Du måste vara tjänstadministratör eller en medadministratör för den associera prenumerationen om du vill skapa ett lagringskonto.

> [!NOTE]
> Information om hur du utför den här åtgärden med hjälp av Azure Service Management API finns i [skapa Lagringskonto](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) referensavsnittet.
> 
> 

**Skapa ett lagringskonto för en Azure-prenumeration**

1. Logga in på den [klassiska Azure-portalen].
2. I det nedre vänstra hörnet, klickar du på **ny**. I den **ny** väljer **datatjänster**, klicka på **lagring**, sedan **Snabbregistrering**.
   
   Den **skapa Lagringskonto** visas.
   
   ![Skapa Lagringskonto][create-new-storage-account]
3. I den **URL** skriver du ett underdomännamn. Den här posten kan innehålla mellan 3 till 24 gemena bokstäver och siffror.
   
    Det här värdet blir värdnamnet inom URI: N som används för att adressera Blob, kö eller tabell resurser för prenumerationen. För att lösa en behållare resurs i Blob-tjänsten har du använder en URI i följande format, där  *&lt;StorageAccountLabel&gt;*  refererar till värdet du angav i **ange en URL**:
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;minbehållare&gt;*
   
    **Viktigt:** URL: en etikett formulär underdomänen lagringskontot URI och måste vara unika bland alla värdbaserade tjänster i Azure.
   
    Det här värdet används också som namn på det här lagringskontot i portalen eller vid åtkomst till det här kontot programmässigt.
4. Från den **Region/Tillhörighetsgrupp** listrutan, Välj en region eller mappning mellan gruppen för lagringskontot. Välj en tillhörighetsgrupp i stället för en region om du vill storage-tjänster i samma datacenter med andra Windows Azure-tjänster som du använder. Detta kan förbättra prestanda och inga avgifter tas ut utgående.  
   
   **Obs:** för att skapa en tillhörighetsgrupp, öppna den **inställningar** område i hanteringsportalen klickar du på **Tillhörighetsgrupper**, och klicka sedan på antingen **Lägg till en tillhörighetsgrupp** eller **lägga till**. Du kan också skapa och hantera tillhörighetsgrupper med hjälp av Windows Azure Service Management API. Mer information finns i [åtgärder med Tillhörighetsgrupper].
5. Från den **prenumeration** nedrullningsbara listan, Välj den prenumeration som lagringskontot kommer att användas med.
6. Klicka på **Create Storage Account** (Skapa lagringskonto). Processen att skapa lagringskontot kan ta flera minuter att slutföra.
7. Kontrollera att lagringskontot har skapats, kontrollera att kontot visas i de objekt som visas för **lagring** med statusen **Online**.

<a id="Step2"> </a>

<h2>Steg 2: Skapa en ny CDN-slutpunkt för ditt lagringskonto</h2>

När du aktiverar CDN åtkomst till ett lagringskonto eller värdtjänsten är alla offentligt tillgängliga objekt berättigade för cachelagring av CDN kant. Om du ändrar ett objekt som för tillfället är cachelagrade i CDN nytt innehåll inte tillgängliga via CDN förrän CDN uppdateras dess innehåll när den cachelagrade innehållet time to live-perioden har löpt ut.

**Skapa en ny CDN-slutpunkt för ditt lagringskonto**

1. I den [klassiska Azure-portalen], i navigeringsfönstret klickar du på **CDN**.
2. Klicka på menyfliken **ny**. I den **ny** markerar **Apptjänster**, sedan **CDN**, sedan **Snabbregistrering**.
3. I den **ursprungsdomän** listrutan, Välj lagring kontot du skapade i föregående avsnitt från listan över konton tillgängligt lagringsutrymme. 
4. Klicka på den **skapa** för att skapa en ny slutpunkt.
5. När slutpunkten har skapats visas den i en lista över slutpunkter för prenumerationen. I listvyn visas bara URL:en som ska användas för att komma åt cachelagrat innehåll, samt ursprungsdomänen. 
   
    Den ursprungliga domänen är den plats där CDN cachelagrar innehåll. Den ursprungliga domänen kan vara antingen ett lagringskonto eller en tjänst i molnet; storage-konto används för det här exemplet. Lagring innehåll cachelagras edge servrar enligt en cache-control-inställning som du anger, eller i standard heuristik för cachelagring nätverk. 

    > [AZURE.NOTE]Konfigurationen för slutpunkten blir omedelbart inte tillgängliga; Det kan ta upp till 60 minuter för registreringen ska spridas via nätverket CDN. Användare som försöker använda domännamnet CDN direkt får statuskod: 400 (felaktig begäran) tills innehållet är tillgängligt via CDN.

<a id="Step3"> </a>

<h2>Steg 3: Åtkomst CDN innehåll</h2> 

Använd CDN-URL som anges i portalen för att komma åt cachelagrade innehåll på CDN. Adressen för en cachelagrade blob ska vara liknar följande:

http://<*CDNNamespace*\>.vo.msecnd.net/ <*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>

<h2>Steg 4: Ta bort innehåll från CDN</h2>

Om du inte längre vill cachelagra ett objekt i Azure Content Delivery innehållsleveransnätverk (CDN) kan du göra något av följande steg:

* Du kan ta bort blobben från offentliga behållare för en Azure blob.
* Du kan göra behållaren privat i stället för allmänheten. Se [begränsa åtkomsten till behållare och Blobbar](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs) för mer information.
* Du kan inaktivera eller ta bort CDN-slutpunkten med hanteringsportalen.
* Du kan ändra din värdbaserade tjänst för att inte längre svarar på begäranden för objektet.

Ett objekt som redan har cachelagrats i CDN förblir cachelagrade tills time to live-period för objektet har löpt ut. När time to live-period har löpt ut ska CDN kontrollera om CDN-slutpunkten är fortfarande giltigt och anonymt fortfarande komma åt objektet. Om det inte är det, kommer inte längre objektet cachelagras.

Möjlighet att rensa omedelbart innehåll stöds för närvarande inte på Azure-hanteringsportalen. Kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) om du vill rensa innehållet omedelbart. 

## <a name="additional-resources"></a>Ytterligare resurser
* [Så här skapar du en Tillhörighetsgrupp i Azure]
* [Så här: hantera Lagringskonton för en Azure-prenumeration]
* [Om Service Management API]
* [Mappa CDN-innehåll till en anpassad domän]

[Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[Azure CDN-Nodplatser]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
[klassiska Azure-portalen]: https://manage.windowsazure.com/
[faktureringsavtal]: /pricing/calculator/?scenario=full
[Så här skapar du en Tillhörighetsgrupp i Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
[Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
[Om Service Management API]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
[Mappa CDN-innehåll till en anpassad domän]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
