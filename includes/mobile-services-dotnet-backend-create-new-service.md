
Följ de här stegen om du vill skapa en ny mobiltjänst.

1.  Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com/). Längst ned i navigationsfönstret klickar du på **+NY**. Expandera **Compute** och **Mobiltjänst** och klicka sedan på **Skapa**.
    
    ![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create.png)

    Dialogrutan **Skapa en mobiltjänst** visas.

2.  I dialogrutan **Skapa en mobiltjänst** väljer du **Skapa en kostnadsfri 20 MB SQL Database** och **.NET**-runtime och anger sedan ett namn på underdomänen för den nya mobiltjänsten i **URL**-textrutan. Klicka på högerpilen för att gå till nästa sida.
    
    ![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page1.png)

    Sidan **Ange databasinställningar** visas.

    > [AZURE.NOTE] Som en del av den här guiden kommer du att skapa en ny SQL Database-instans och -server. Du kan återanvända den nya databasen och administrera den som vilken som helst annan SQL Database-instans. Om du redan har en databas i samma region som den nya mobiltjänsten, kan du istället välja **Använd befintlig databas** och sedan välja den databasen. Det är inte rekommenderat att använda en databas i en annan region på grund av ökade kostnader för bandbredd och ökad latens.

3.  I **Namn** skriver du namnet på den nya databasen och skriver sedan **Inloggningsnamn**, vilket är inloggningsnamnet för administratören för den nya SQL Database-servern. Skriv och bekräfta lösenordet och klicka på knappen med en bock för att slutföra processen.
    ![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page2.png)

Nu har du skapat en ny mobiltjänst som kan användas av dina mobilappar.



<!--HONumber=Jun16_HO2-->


