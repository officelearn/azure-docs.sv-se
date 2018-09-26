Följ dessa steg för att installera och kör MongoDB på en virtuell dator som kör Windows Server.

> [!IMPORTANT]
> MongoDB säkerhetsfunktioner, till exempel autentisering och IP-adress bindningen är inte aktiverade som standard. Säkerhetsfunktioner måste vara aktiverad innan du distribuerar MongoDB till en produktionsmiljö.  Mer information finns i [säkerhet och autentisering](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
>
>

1. När du har anslutit till den virtuella datorn med hjälp av fjärrskrivbord, öppna Internet Explorer från den **starta** menyn på den virtuella datorn.
2. Välj den **verktyg** knappen i det övre högra hörnet.  I **Internetalternativ**väljer den **säkerhet** fliken och välj sedan den **tillförlitliga platser** ikon, och klicka sedan på **platser** knappen. Lägg till *https://\*. mongodb.org* i listan över betrodda platser.
3. Gå till [Downloads - MongoDB](https://www.mongodb.com/download-center#community).
4. Hitta den **aktuella stabila versionen** av **gruppservern**, Välj senast **64-bitars** version i Windows-kolumnen. Ladda ned och kör MSI-installationsprogrammet.
5. MongoDB installeras vanligtvis i C:\Program Files\MongoDB. Sök efter miljövariabler på skrivbordet och lägga till MongoDB-binärfiler sökvägen i PATH-variabeln. Till exempel kanske du upptäcker binärfilerna på c:\Program\Microsoft Files\MongoDB\Server\3.4\bin på din dator.
6. Skapa en MongoDB-data och loggfiler kataloger i datadisken (till exempel enhet **F:**) du skapade i föregående steg. Från **starta**väljer **kommandotolk** att öppna ett kommandotolksfönster.  Ange:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. För att köra databasen, kör du:

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Alla loggmeddelanden dirigeras till den *F:\MongoLogs\mongolog.log* filen när mongod.exe server startas och preallocates journalfiler. Det kan ta flera minuter för MongoDB Förallokera journal-filer och starta lyssna efter anslutningar. Kommandotolken förblir fokuserad på den här uppgiften när MongoDB-instansen körs.
8. Starta administrativa MongoDB-gränssnittet genom att öppna en annan kommandofönster från **starta** och Skriv följande kommandon:

        C:\> cd \my_mongo_dir\bin  
        C:\my_mongo_dir\bin> mongo  
        >db  
        test
        > db.foo.insert( { a : 1 } )  
        > db.foo.find()  
        { _id : ..., a : 1 }  
        > show dbs  
        ...  
        > show collections  
        ...  
        > help  

    Databasen har skapats genom att infoga.
9. Du kan också installera mongod.exe som en tjänst:

        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install

    En tjänst är installerad med namnet MongoDB med en beskrivning av ”Mongo DB”. Den `--logpath` alternativet måste användas för att ange en loggfil, eftersom tjänsten som körs inte har ett kommandofönster för att visa utdata.  Den `--logappend` alternativet anger att en omstart av tjänsten gör att utdata ska läggas till den befintliga loggfilen.  Den `--dbpath` alternativet anger platsen för data-katalog. För mer tjänstrelaterade kommandoradsalternativ, se [tjänstrelaterade kommandoradsalternativ][MongoWindowsSvcOptions].

    Om du vill starta tjänsten kör du följande kommando:

        C:\> net start MongoDB
10. Nu när MongoDB är installerad och körs, måste du öppna en port i Windows-brandväggen så att du kan fjärransluta till MongoDB.  Från den **starta** menyn och välj **Administrationsverktyg** och sedan **Windows-brandväggen med avancerad säkerhet**.
11. (a) i den vänstra rutan väljer **regler för inkommande trafik**.  I den **åtgärder** fönstret till höger väljer **ny regel...** .

    ![Windows-brandväggen][Image1]

    (b) i den **guiden Ny inkommande regel**väljer **Port** och klicka sedan på **nästa**.

    ![Windows-brandväggen][Image2]

    c) Välj **TCP** och sedan **specifika lokala portar**.  Ange en port för ”27017” (standardporten MongoDB lyssnar på) och klicka på **nästa**.

    ![Windows-brandväggen][Image3]

    d) Välj **Tillåt anslutningen** och klicka på **nästa**.

    ![Windows-brandväggen][Image4]

    e) Klicka på **nästa** igen.

    ![Windows-brandväggen][Image5]

    f) anger ett namn för regeln, till exempel ”MongoPort”, och klickar på **Slutför**.

    ![Windows-brandväggen][Image6]

12. Om du inte konfigurerar en slutpunkt för MongoDB när du skapade den virtuella datorn kan göra du det nu. Du behöver både brandväggsregeln och slutpunkten för att kunna fjärransluta till MongoDB.

  I Azure-portalen klickar du på **virtuella datorer (klassiska)**, klicka på namnet på din nya virtuella dator och klicka sedan på **slutpunkter**.

    ![Slutpunkter][Image7]

13. Klicka på **Lägg till**.

14. Lägg till en slutpunkt med namnet ”Mongo”, protokoll **TCP**, och båda **offentliga** och **privata** portar som är inställt på ”27017”. Om du öppnar den här porten kan MongoDB nåbara via fjärranslutning.

    ![Slutpunkter][Image9]

> [!NOTE]
> Port 27017 är standardporten som används av MongoDB. Du kan ändra den här standardporten genom att ange den `--port` parametern när mongod.exe servern startas. Se till att ge samma portnummer i brandväggen och ”Mongo”-slutpunkten i instruktionerna ovan.
>
>

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/menusendpointadd.png
<!-- Removed 03/08/2017. Not in new portal. -->
<!-- [Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
-->
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/newendpointdetails.png
