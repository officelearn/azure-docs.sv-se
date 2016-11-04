---
title: Köra en Windows-app på valfri enhet med Azure RemoteApp | Microsoft Docs
description: Lär dig hur du delar en Windows-app med dina användare via Azure RemoteApp.
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin
editor: ''

ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 08/15/2016
ms.author: elizapo

---
# Köra en Windows-app på valfri enhet med Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp upphör att gälla. Läs [meddelandet](https://go.microsoft.com/fwlink/?linkid=821148) för mer information.
> 
> 

Du kan köra ett Windows-program var som helst på valfri enhet genom att helt enkelt använda Azure RemoteApp. Vare sig det gäller ett anpassat program som skrevs för 10 år sedan eller en Office-app, behöver användarna inte längre vara bundna till ett visst operativsystem (som Windows XP) för några få program.

Med Azure RemoteApp kan användarna även använda sina egna Android- eller Apple-enheter och få samma användarupplevelse som på Windows (eller på Windows Phone-enheter). Du åstadkommer detta genom att värdhantera ditt Windows-program i en samling virtuella Windows-datorer på Azure – vilket innebär att användarna kan komma åt dem från valfri plats med en internetanslutning. 

Längre ner finns ett exempel som visar hur du gör det.

I den här artikeln ska vi dela Access med alla våra användare. Men du kan använda VALFRI app. Så länge som du kan installera appen på en Windows Server 2012 R2-dator kan du dela den genom att följa stegen nedan. Du kan granska [appkraven](remoteapp-appreqs.md) för att se till att appen fungerar.

Observera att eftersom Access är en databas, och vi vill att databasen ska vara användbar, går vi igenom några extra steg för att låta användarna få tillgång till Access-dataresursen. Om din app inte är en databas, eller om användarna inte behöver komma åt en filresurs, kan du hoppa över de stegen i den här kursen.

> [!NOTE]
> <a name="note"></a>Du behöver ett Azure-konto för att kunna slutföra den här guiden:
> 
> * Du kan [kostnadsfritt registrera ett Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): du får då krediter som kan användas för att pröva Azure-betaltjänster och när du har använt upp dem så kan du behålla kontot och använda kostnadsfria Azure-tjänster, till exempel Websites. Ditt kreditkort kommer aldrig att debiteras om du inte specifikt ändrar dina inställningar och ber om det.
> * Du kan [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): din MSDN-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.
> 
> 

## Skapa en samling i RemoteApp
Börja med att skapa en samling. Samlingen fungerar som en behållare för dina appar och användare. Varje samling är baserad på en avbildning – du kan skapa en egen eller använda en som ingår i din prenumeration. I den här kursen använder vi Office 2013-utvärderingsavbildningen. Den innehåller den app som vi vill dela.

1. Rulla nedåt i det vänstra navigeringsträdet i Azure Portal tills du ser RemoteApp. Öppna sidan.
2. Klicka på **Create a RemoteApp collection** (Skapa en RemoteApp-samling).
3. Klicka på **Quick create** (Snabbregistrering) och ange ett namn för samlingen.
4. Välj den region som du vill använda för att skapa samlingen. Bäst resultat får du om du väljer den region som geografiskt sett ligger närmast den plats där användarna ska använda appen. I den här kursen finns användarna till exempel i Redmond i delstaten Washington. Den närmaste Azure-regionen är **West US** (USA, västra).
5. Välj den faktureringsplan som du vill använda. Den grundläggande faktureringsplanen har 16 användare på en stor Azure VM, medan standardfaktureringsplanen har 10 användare på en stor Azure VM. I allmänhet fungerar den grundläggande planen bra för ett arbetsflöde med datainmatning. Men för en produktivitetsapp som Office passar standardplanen bättre.
6. Till sist väljer du Office 2013 Professional-avbildningen. Den här avbildningen innehåller Office 2013-appar. Kom ihåg att den här avbildningen enbart kan användas för utvärderingssamlingar och POC. Det går inte att använda den här avbildningen i en produktionssamling.
7. Klicka på **Create RemoteApp collection** (Skapa RemoteApp-samling).

![Skapa en molnsamling i RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Den här åtgärden börjar skapa samlingen, men det kan ta upp till en timme.

Sedan kan du lägga till användare.

## Dela appen med användare
När din samling har skapats, är det dags att publicera Access till användarna och lägga till de användare som ska ha åtkomst till den.

Om du har navigerat bort från Azure RemoteApp-noden medan samlingen skapades, börjar du med att gå tillbaka till den från Azure-startsidan.

1. Klicka på samlingen som du skapade tidigare för att komma åt ytterligare alternativ och konfigurera samlingen.
   ![En ny RemoteApp-molnsamling](./media/remoteapp-anyapp/ra-anyappcollection.png)
2. Gå till fliken **Publishing** (Publicering) och klicka på **Publish** (Publicera) längst ned på skärmen. Klicka sedan på **Publish Start menu programs** (Publicera program på Start-menyn).
   ![Publicera ett RemoteApp-program](./media/remoteapp-anyapp/ra-anyapppublish.png)
3. Välj de appar som du vill publicera i listan. I det här exemplet väljer vi Access. Klicka på **Complete** (Slutför). Vänta tills apparna har publicerats.
   ![Publicera Access i RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)
4. När appen har publicerats ska du gå till fliken **User Access** (Användaråtkomst) där du kan lägga till alla användare som ska ha tillgång till dina appar. Ange användarnas användarnamn (e-postadress) och klicka sedan på **Save** (Spara).

![Lägga till användare i RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)

1. Nu är det dags att informera användarna om de nya apparna och hur de kommer åt dem. Gör det genom att skicka ett mejl till dem och hänvisa dem till fjärrskrivbordsklientens nedladdnings-URL.
   ![Klientens nedladdnings-URL för RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## Konfigurera åtkomst till Access
Vissa appar behöver ytterligare konfiguration efter att du har distribuerat dem via RemoteApp. I synnerhet för Access ska vi skapa en filresurs på Azure som alla användare kan komma åt. (Om du inte vill göra det kan du skapa en [hybridsamling](remoteapp-create-hybrid-deployment.md) [i stället för molnsamlingen] som låter användarna komma åt filer och information på det lokala nätverket.) Sedan måste du säga åt användarna att de ska mappa en lokal enhet på sin dator till Azure-filsystemet.

Det första gör du i egenskap av administratör. Sedan har vi några steg för användarna.

1. Börja med att publicera kommandoradsgränssnittet (cmd.exe). Gå till fliken **Publishing** (Publicering), välj **cmd** och klicka sedan på **Publish > Publish program using path** (Publicera > Publicera program med sökväg).
2. Ange namnet på appen och sökvägen. I det här exemplet använder vi ”File Explorer” (Utforskaren) som namn och ”%SYSTEMDRIVE%\windows\explorer.exe” som sökväg.
   ![Publicera filen cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. Nu måste du skapa ett [Azure-lagringskonto](../storage/storage-create-storage-account.md). Vi valde namnet ”accessstorage”, så välj ett namn som är meningsfullt för dig. (För att felcitera Highlander – det kan bara finnas ett ”accessstorage”.) ![Vårt Azure-lagringskonto](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Gå tillbaka till instrumentpanelen så att du kan hämta sökvägen till ditt lagringsutrymme (slutpunktens plats). Var noga med att kopiera och spara sökvägen eftersom du kommer att använda den om en liten stund.
   ![Sökvägen för lagringskontot](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. När lagringskontot har skapats behöver du den primära åtkomstnyckeln. Klicka på **Manage access keys** (Hantera åtkomstnycklar) och kopiera sedan den primära åtkomstnyckeln.
6. Ange sedan kontexten för lagringskontot och skapa en ny filresurs för Access. Kör följande cmdlets i ett upphöjt Windows PowerShell-fönster:
   
        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx
   
    Så för vår resurs kör vi följande cmdlets:
   
        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx

Nu är det användarens tur. Uppmana först användarna att installera en [RemoteApp-klient](remoteapp-clients.md). Sedan måste användarna mappa en enhet från sitt konto till den Azure-filresursen du skapade och lägga till sina Access-filer. Det gör de på följande sätt:

1. Öppna de publicerade apparna i RemoteApp-klienten. Starta programmet cmd.exe.
2. Kör följande kommando för att mappa en enhet från datorn till filresursen:
   
        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>
   
    Om du anger yes för parametern **/persistent**, är den mappade enheten beständig i alla sessioner.
3. Starta appen Utforskaren från RemoteApp. Kopiera alla Access-filer som du vill använda i den delade appen till filresursen.
   ![Placera Access-filer i en Azure-resurs](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
4. Öppna till sist Access och öppna sedan den databas som du precis har delat. Då bör du kunna se att dina Access-data körs från molnet.
   ![En verklig Access-databas som körs från molnet](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Nu kan du använda Access på valfri enhet där en RemoteApp-klient finns installerad.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nästa steg
Nu när du har lärt dig att skapa en samling kan du försöka skapa en [samling som använder Office 365](remoteapp-tutorial-o365anywhere.md). Du kan även skapa en [hybridsamling](remoteapp-create-hybrid-deployment.md) som kan komma åt ditt lokala nätverk.

<!--Image references-->




<!--HONumber=Sep16_HO3-->


