Följ dessa steg om du vill installera och köra MongoDB på en virtuell dator som kör Windows Server.

> [!IMPORTANT]
> MongoDB säkerhetsfunktioner, till exempel autentisering och IP-adress bindningen är inte aktiverade som standard. Säkerhetsfunktioner ska aktiveras innan du distribuerar MongoDB till en produktionsmiljö.  Mer information finns i [säkerhets- och](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
>
>

1. När du har anslutit till den virtuella datorn via fjärrskrivbord, öppna Internet Explorer från den **starta** menyn på den virtuella datorn.
2. Välj den **verktyg** i det övre högra hörnet.  I **Internetalternativ**, Välj den **säkerhet** och välj sedan den **tillförlitliga platser** ikon, och slutligen på den **platser** knappen. Lägg till *https://\*. mongodb.org* i listan över betrodda platser.
3. Gå till [hämtas - MongoDB](https://www.mongodb.com/download-center#community).
4. Hitta den **aktuella stabil versionen** av **Community Server**, Välj senast **64-bitars** version i Windows-kolumnen. Hämta och kör sedan installationen av MSI.
5. MongoDB installeras normalt i C:\Program Files\MongoDB. Sök efter miljövariabler på skrivbordet och lägga till sökvägen till MongoDB-binärfiler i PATH-variabeln. Du kan till exempel hitta binärfilerna på C:\Program Files\MongoDB\Server\3.4\bin på din dator.
6. Skapa MongoDB data och loggfilen kataloger i datadisken (till exempel enhet **F:**) du skapade i föregående steg. Från **starta**väljer **kommandotolk** att öppna ett kommandotolksfönster.  Ange:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. Om du vill köra databasen kör du:

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Alla loggmeddelanden dirigeras till den *F:\MongoLogs\mongolog.log* filen som mongod.exe servern startar och preallocates journal-filer. Det kan ta flera minuter för MongoDB Förallokera journal-filer och starta lyssna efter anslutningar. Kommandotolken förblir fokuserar på den här uppgiften när MongoDB-instansen körs.
8. Om du vill starta det administrativa gränssnittet MongoDB, öppnar du en annan kommandofönster från **starta** och Skriv följande kommandon:

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

    En tjänst har installerats med namnet MongoDB med en beskrivning av ”Mongo databas”. Den `--logpath` alternativet måste användas för att ange en loggfil, eftersom tjänsten körs inte har ett kommandofönster om du vill visa utdata.  Den `--logappend` alternativet anger att en omstart av tjänsten gör att utdata ska läggas till i den befintliga loggfilen.  Den `--dbpath` alternativet anger platsen för datakatalog. För mer tjänstrelaterade kommandoradsalternativ, se [tjänstrelaterade kommandoradsalternativ][MongoWindowsSvcOptions].

    Om du vill starta tjänsten, kör du kommandot:

        C:\> net start MongoDB
10. Nu när MongoDB är installerad och körs, måste du öppna en port i Windows-brandväggen så att du kan fjärransluta till MongoDB.  Från den **starta** väljer du **Administrationsverktyg** och sedan **Windows-brandväggen med avancerad säkerhet**.
11. en) i den vänstra rutan, Välj **regler för inkommande trafik**.  I den **åtgärder** rutan på höger, Välj **ny regel...** .

    ![Windows-brandväggen][Image1]

    b) i den **ny inkommande regel för**väljer **Port** och klicka sedan på **nästa**.

    ![Windows-brandväggen][Image2]

    c) Välj **TCP** och sedan **specifika lokala portar**.  Ange en port för ”27017” (standardporten MongoDB lyssnar på) och klicka på **nästa**.

    ![Windows-brandväggen][Image3]

    d) Välj **tillåter anslutningen** och på **nästa**.

    ![Windows-brandväggen][Image4]

    e) Klicka på **nästa** igen.

    ![Windows-brandväggen][Image5]

    f) anger ett namn för regeln, till exempel ”MongoPort” och klickar på **Slutför**.

    ![Windows-brandväggen][Image6]

12. Om du inte konfigurerar en slutpunkt för MongoDB när du skapade den virtuella datorn kan göra du det nu. Behöver du både brandväggsregeln och slutpunkten ska kunna fjärransluta till MongoDB.

  I Azure-portalen klickar du på **virtuella datorer (klassisk)**, klicka på namnet på den nya virtuella datorn och klicka sedan på **slutpunkter**.

    ![Slutpunkter][Image7]

13. Klicka på **Lägg till**.

14. Lägg till en slutpunkt med namnet ”Mongo”, protokollet **TCP**, och båda **offentliga** och **privata** portar inställd på ”27017”. Öppnar den här porten kan MongoDB för fjärråtkomst.

    ![Slutpunkter][Image9]

> [!NOTE]
> Porten 27017 är standardporten som används av MongoDB. Du kan ändra den här standardporten genom att ange den `--port` parameter när mongod.exe servern startas. Se till att ge samma portnummer i brandväggen och slutpunkten ”Mongo” i ovanstående instruktioner.
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
